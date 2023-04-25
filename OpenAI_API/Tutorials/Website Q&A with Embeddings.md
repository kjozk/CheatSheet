# ウェブサイトに関する質問に答えられるAIの作り方

このチュートリアルでは、ウェブサイト（この例ではOpenAIのウェブサイト）をクロールし、[Embeddings API](https://platform.openai.com/docs/guides/embeddings)を使用してクロールしたページを埋め込みに変え、そして、ユーザーが埋め込み情報に関して質問できる基本的な検索機能を作成する簡単な例について説明します。
これは、カスタムナレッジベースを利用する、より洗練されたアプリケーションの出発点となることを意図しています。

## はじめに - Getting started

このチュートリアルでは、PythonとGitHubの基本的な知識があると便利です。このチュートリアルに入る前に、[OpenAIのAPIキーを設定](https://platform.openai.com/docs/api-reference/introduction)し、クイックスタートチュートリアルを歩いてみてください。これにより、APIを最大限に活用する方法について、直感的に理解することができます。

Pythonは、OpenAI、Pandas、transformers、NumPy、その他の一般的なパッケージとともに、主なプログラミング言語として使用されています。このチュートリアルの作業で何か問題が発生した場合は、[OpenAIコミュニティフォーラム](https://community.openai.com/)で質問してください。

コードから始めるには、[GitHubにあるこのチュートリアルの全コード](https://github.com/openai/openai-cookbook/tree/main/apps/web-crawl-q-and-a)をクローンしてください。または、Jupyterノートブックに各セクションをコピーして、コードをステップバイステップで実行したり、ただ読み進めることもできます。問題を回避する良い方法は、新しい仮想環境をセットアップし、以下のコマンドを実行して必要なパッケージをインストールすることです：


```bash
python -m venv env

source env/bin/activate

pip install -r requirements.txt
```


## Webクローラーをセットアップする - Setting up a web crawler

このチュートリアルの主な焦点はOpenAI APIなので、もしあなたが望むなら、ウェブクローラーの作成方法に関するコンテキストをスキップして、ソースコードをダウンロードするだけでもかまいません。
そうでなければ、以下のセクションを展開して、スクレイピング機構の実装に取り組んでください。

（略）

## エンベッディングインデックスの構築 - Building an embeddings index

CSVはエンベッディングを保存するための一般的なフォーマットです。Pythonでこのフォーマットを使用するには、生のテキストファイル（textディレクトリにある）をPandasデータフレームに変換する必要があります。
Pandasは、表形式のデータ（行と列で保存されたデータ）を扱うのに役立つ、人気のあるオープンソースライブラリです。
空白の空行は、テキストファイルを乱雑にし、処理を困難にします。
簡単な関数でこれらの行を削除し、ファイルを整頓することができます。

```Python
def remove_newlines(serie):
    serie = serie.str.replace('\n', ' ')
    serie = serie.str.replace('\\n', ' ')
    serie = serie.str.replace('  ', ' ')
    serie = serie.str.replace('  ', ' ')
    return serie
```

テキストをCSVに変換するためには、先に作成したテキストディレクトリ内のテキストファイルをループする必要があります。
各ファイルを開いた後、余分なスペースを削除し、修正したテキストをリストに追加します。
次に、新しい行を削除したテキストを空のPandasデータフレームに追加し、そのデータフレームをCSVファイルに書き込みます。

```Python
import pandas as pd

# テキストファイルを格納するリストを作成する
texts=[]

# テキストディレクトリにあるすべてのテキストファイルを取得する
for file in os.listdir("text/" + domain + "/"):

    # ファイルを開き、テキストを読む
    with open("text/" + domain + "/" + file, "r", encoding="UTF-8") as f:
        text = f.read()

        # 最初の11行と最後の4行を省略し、「-」「_」「#update」をスペースに置き換えてください。
        texts.append((file[11:-4].replace('-',' ').replace('_', ' ').replace('#update',''), text))

# テキストlistからデータフレームを作成する
df = pd.DataFrame(texts, columns = ['fname', 'text'])

# text列に、改行が削除された生のテキストを設定する
df['text'] = df.fname + ". " + remove_newlines(df.text)
df.to_csv('processed/scraped.csv')
df.head()
```

トークン化は、生テキストをCSVファイルに保存した後の次のステップです。
この処理では、文や単語を分解して、入力テキストをトークンに分割します。
この処理の視覚的なデモンストレーションは、ドキュメントにある[Tokenizerをチェックすること](https://platform.openai.com/tokenizer)で見ることができます。

```A helpful rule of thumb is that one token generally corresponds to ~4 characters of text for common English text. This translates to roughly ¾ of a word (so 100 tokens ~= 75 words).```

APIには、埋め込み用の入力トークンの最大数に制限があります。制限値を下回るためには、CSVファイルのテキストを複数の行に分割する必要があります。どの行を分割する必要があるかを特定するために、まず各行の既存の長さが記録されます。

```PYthon
import tiktoken

# ada-002モデルで動作するように設計されたcl100k_baseトークナイザーをロードする
tokenizer = tiktoken.get_encoding("cl100k_base")

df = pd.read_csv('processed/scraped.csv', index_col=0)
df.columns = ['title', 'text']

# テキストをトークン化し、トークンの数を新しい列に保存する
df['n_tokens'] = df.text.apply(lambda x: len(tokenizer.encode(x)))

# ヒストグラムで行ごとのトークン数の分布を可視化する
df.n_tokens.hist()
```

最新の埋め込みモデルは、最大8191個の入力トークンを扱うことができるので、ほとんどの行はチャンキングを必要としませんが、スクレイピングされたすべてのサブページがそうであるとは限らないので、次のコードチャンクは長い行を小さなチャンクに分割します。

```Python
max_tokens = 500

# テキストを最大数のトークンの塊に分割する関数
def split_into_many(text, max_tokens = max_tokens):

    # テキストを文に分割する
    sentences = text.split('. ')

    # 各文章のトークン数を取得する
    n_tokens = [len(tokenizer.encode(" " + sentence)) for sentence in sentences]
    
    chunks = []
    tokens_so_far = 0
    chunk = []

    # タプルで結合された文章とトークンをループする
    for sentence, token in zip(sentences, n_tokens):

        # これまでのトークン数＋現在の文のトークン数が最大トークン数より大きい場合、
        # そのチャンクをチャンクリストに追加し、チャンクとこれまでのトークンをリセットする
        if tokens_so_far + token > max_tokens:
            chunks.append(". ".join(chunk) + ".")
            chunk = []
            tokens_so_far = 0

        # 現在の文のトークン数が最大トークン数より大きい場合、次の文に進む
        if token > max_tokens:
            continue

        # それ以外の場合は、チャンクに文を追加し、トークンの数を合計に加える
        chunk.append(sentence)
        tokens_so_far += token + 1

    return chunks
    

shortened = []

# データフレームをループする
for row in df.iterrows():

    # テキストがNoneの場合、次の行に進む
    if row[1]['text'] is None:
        continue

    # トークンの数が最大トークン数より大きい場合、テキストをチャンクに分割する
    if row[1]['n_tokens'] > max_tokens:
        shortened += split_into_many(row[1]['text'])
    
    # それ以外の場合は、テキストを短縮テキストのリストに追加する
    else:
        shortened.append( row[1]['text'] )
```

更新されたヒストグラムを再度視覚化することで、行の短縮分割がうまくいったかどうかを確認することができます。


```Python
df = pd.DataFrame(shortened, columns = ['text'])
df['n_tokens'] = df.text.apply(lambda x: len(tokenizer.encode(x)))
df.n_tokens.hist()
```

コンテンツはより小さな塊に分解され、OpenAI APIに新しいtext-embedding-ada-002モデルの使用を指定するシンプルなリクエストを送ることで、埋め込みを作成することができるようになったのです：

```Python
import openai

df['embeddings'] = df.text.apply(lambda x: openai.Embedding.create(input=x, engine='text-embedding-ada-002')['data'][0]['embedding'])

df.to_csv('processed/embeddings.csv')
df.head()
```

3～5分程度かかりますが、その後、エンベッディングを使用できる状態になります！

## エンベッディングで質問応答システムを構築する - Building a question answer system with your embeddings

エンベッディングの準備ができたので、このプロセスの最終段階として、簡単な質問と回答のシステムを作成します。
これは、ユーザーの質問を受け取り、その質問の埋め込みを作成し、既存の埋め込みと比較して、スクレイピングされたウェブサイトから最も関連性の高いテキストを取得するものです。
そして、text-davinci-003モデルが、検索されたテキストをもとに、自然な響きの回答を生成します。

エンベッディングをNumPyの配列にすることは最初のステップであり、NumPyの配列を操作する多くの関数が利用できることから、より柔軟な使い方ができるようになります。また、次元を1次元に平らにすることで、その後の多くの操作で必要とされる形式となります。


```Python
import numpy as np
from openai.embeddings_utils import distances_from_embeddings

df=pd.read_csv('processed/embeddings.csv', index_col=0)
df['embeddings'] = df['embeddings'].apply(eval).apply(np.array)

df.head()
```

データの準備ができたので、質問は簡単な関数で埋め込みに変換する必要があります。これが重要なのは、エンベッディングを使った検索では、（生テキストの変換である）数字のベクトルをコサイン距離で比較するためです。ベクトルは関連している可能性が高く、コサイン距離が近ければ質問の答えになるかもしれません。OpenAI pythonパッケージには、`distance_from_embeddings`関数が組み込まれており、ここで役に立ちます。


```Python
def create_context(
    question, df, max_len=1800, size="ada"
):
    """
    データフレームから最も類似したコンテキストを見つけることで、質問のコンテキストを作成します。
    """

    # 質問に対するembeddingsを取得する
    q_embeddings = openai.Embedding.create(input=question, engine='text-embedding-ada-002')['data'][0]['embedding']

    # embeddingsからdistancesを取得する
    df['distances'] = distances_from_embeddings(q_embeddings, df['embeddings'].values, distance_metric='cosine')


    returns = []
    cur_len = 0

    # distancesでソートし、コンテキストが長くなるまでテキストを追加する
    for i, row in df.sort_values('distances', ascending=True).iterrows():
        
        # 現在の長さにテキストの長さを足す
        cur_len += row['n_tokens'] + 4
        
        # コンテキストが長すぎる場合、ブレーク
        if cur_len > max_len:
            break
        
        # 返されるテキストに追加
        returns.append(row["text"])

    # コンテキストを返す
    return "\n\n###\n\n".join(returns)
```

テキストはより小さなトークンのセットに分割されているので、昇順でループしてテキストを追加し続けることは、完全な回答を保証するための重要なステップです。また、必要以上のコンテンツが返された場合は、max_lenをより小さいものに変更することも可能です。

前のステップでは、質問に意味的に関連するテキストの塊を取得しただけなので、答えが含まれているかもしれませんが、それを保証するものではありません。最も可能性の高い上位5つの結果を返すことで、答えが見つかる確率をさらに高めることができます。

回答プロンプトは、検索されたコンテキストから関連する事実を抽出し、首尾一貫した回答を作成するように努めます。関連する答えがない場合、プロンプトは「わからない」を返します。

`text-davinci-003`を使用した生成エンドポイントでは、質問に対する現実的な回答を生成することができます。

```Python
def answer_question(
    df,
    model="text-davinci-003",
    question="人間のレビューなしに、モデルの出力をTwitterに公開することは許されるのか？",
    max_len=1800,
    size="ada",
    debug=False,
    max_tokens=150,
    stop_sequence=None
):
    """
    データフレームのテキストから最も類似したコンテキストに基づいて質問に答える。
    """
    context = create_context(
        question,
        df,
        max_len=max_len,
        size=size,
    )
    # debugの場合、モデルの生レスポンスを表示する
    if debug:
        print("Context:\n" + context)
        print("\n\n")

    try:
        # 質問と文脈を利用してコンプリートを生成する
        response = openai.Completion.create(
            prompt=f"以下の文脈に基づいて質問に答え、文脈に基づいて答えられない場合は \"I don't know\"と言う\n\nContext: {context}\n\n---\n\nQuestion: {question}\nAnswer:",
            temperature=0,
            max_tokens=max_tokens,
            top_p=1,
            frequency_penalty=0,
            presence_penalty=0,
            stop=stop_sequence,
            model=model,
        )
        return response["choices"][0]["text"].strip()
    except Exception as e:
        print(e)
        return ""
```

出来上がりました！OpenAIのウェブサイトから知識を組み込んだ、動くQ/Aシステムの準備が整いました。
出力の品質を確認するために、いくつかの簡単なテストを行うことができます：


```Python
answer_question(df, question="今日は何日ですか？", debug=False)

answer_question(df, question="最新のエンベデッドモデルとは？")

answer_question(df, question="ChatGPTとは？")
```

回答は次のようなものになります：
```Python
"わかりません。"

'最新のエンベデッドモデルは、text-embedding-ada-002です。'

'ChatGPTは、会話形式で対話するように訓練されたモデルです。質問に答える、間違いを認める、間違った前提に挑戦する、不適切な要求を拒否することができます。'
```

もしシステムが期待される質問に答えられない場合、生テキストファイルを検索して、知られていると期待される情報が実際に埋め込まれることになったかどうかを確認する価値があります。
最初に行われたクローリングプロセスは、提供されたオリジナルのドメイン以外のサイトをスキップするように設定されていたので、サブドメインが設定されていた場合はその知識を持たないかもしれません。

現在は、質問に答えるたびにデータフレームが渡されるようになっています。よりプロフェッショナルなワークフローでは、埋め込みデータをCSVファイルに保存するのではなく、[ベクターデータベースのソリューション](https://platform.openai.com/docs/guides/embeddings/how-can-i-retrieve-k-nearest-embedding-vectors-quickly)を使用すべきですが、現在のアプローチはプロトタイピングのための素晴らしい選択肢です。
