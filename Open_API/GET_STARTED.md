# Get started

## はじめに - Introduction

原文: https://platform.openai.com/docs/introduction/introduction

### 概要 - Overview

OpenAI APIは、自然言語、コード、画像の理解や生成を伴うほぼすべてのタスクに適用することができます。OpenAIは、さまざまなタスクに適した、異なるレベルのパワーを持つ[モデル](#モデル---models--Models)を提供し、独自のカスタムモデルを微調整する能力も備えています。これらのモデルは、コンテンツ生成からセマンティック検索、分類に至るまで、あらゆる用途に使用することができます。


### キーコンセプト - Key concepts

[クイックスタート・チュートリアル](https://platform.openai.com/docs/quickstart)を完了し、ハンズオン、インタラクティブな例で主要な概念を理解することをお勧めします。

### プロンプト - Prompts
[プロンプトの設計](https://platform.openai.com/docs/guides/completion/prompt-design)は、基本的にモデルを「プログラム」する方法であり、通常はいくつかの指示やいくつかの例を提供することで行います。これは、感情分類や名前付きエンティティ認識など、単一のタスクのために設計されている他のほとんどの NLP サービスとは異なります。その代わり、生成とチャット生成のエンドポイントは、コンテンツやコードの生成、要約、展開、会話、創作、スタイル転送など、事実上あらゆるタスクに使用することができます。

### トークン - Tokens

私たちのモデルは、テキストをトークンに分解することで理解し、処理します。トークンには単語もあれば、単なる文字の塊もあります。例えば、"hamburger"という単語は、 “ham”,、“bur”、“ger”というトークンに分解されますが、“pear”のような短くて一般的な単語は、1つのトークンです。多くのトークンは空白で始まり、例えば "hello"や "bye"のようになります。

あるAPIリクエストで処理されるトークンの数は、入力と出力の両方の長さに依存します。目安としては、1トークンは約4文字、英文の場合は0.75ワードとなります。注意すべき制限として、テキストプロンプトと生成された補完を合わせて、モデルの最大コンテキスト長（ほとんどのモデルで2048トークン、または約1500ワード）以下でなければならないことが挙げられます。テキストがどのようにトークンに変換されるかについては、[トークナイザーツール](https://platform.openai.com/tokenizer)を参照してください。



### モデル - Models

APIは、性能と価格帯の異なるモデル群が搭載されています。GPT-4は、当社の最新かつ最も強力なモデルです。GPT-3.5-Turboは、ChatGPTを駆動するモデルで、会話形式用に最適化されています。これらのモデルの詳細と、私たちが提供する他のものについては、[モデルのドキュメント](https://platform.openai.com/docs/models)をご覧ください。


## クイックスタート - Quickstart

原文: https://platform.openai.com/docs/quickstart

OpenAIは、テキストの理解と生成に非常に優れた最先端の言語モデルを訓練しています。私たちのAPIは、これらのモデルへのアクセスを提供し、言語処理を伴うほぼすべてのタスクを解決するために使用することができます。

このクイックスタートチュートリアルでは、簡単なサンプルアプリケーションを構築します。その過程で、以下のようなあらゆるタスクにAPIを使用するための基礎となる主要な概念とテクニックを学びます：

- コンテンツ生成
- まとめ
- 分類、カテゴライズ、センチメント分析
- データ抽出
- 翻訳
- 他にもたくさんあります！

### はじめに - Introduction

[生成](https://platform.openai.com/docs/api-reference/completions)エンドポイントはAPIの中核をなすもので、非常に柔軟で強力なシンプルなインターフェイスを提供します。プロンプトとしてテキストを入力すると、APIはあなたが与えた指示や文脈にマッチするようなテキスト補完を返します。

|            |                                                          |
| ---------- | -------------------------------------------------------- |
| プロンプト | アイスクリームショップのキャッチコピーを書いてください。 |
| 生成結果   | 私たちは、スクープするたびに笑顔を提供します！           |

これは、非常に高度な自動生成と考えることができます。このモデルは、あなたのテキストプロンプトを処理し、次に来る可能性の高いものを予測しようとします。

### 1つの指示から始める - Start with an instruction

ペットの名前ジェネレーターを作りたいと想像してください。ゼロから名前を考えるのは大変です！

まずは、何をしたいのかが明確になるようなプロンプトが必要です。まずは命令文から。__このプロンプトを送信__ すると、最初の完成形が生成されます。

`馬の名前を1つ提案してください。`

まずまずですね！では、もっと具体的に指示を出してみましょう。
`黒い馬の名前を1つ提案してください。`

このように、プロンプトに簡単な形容詞を加えるだけで、出来上がる完成度が変わります。このように、プロンプトをデザインすることは、モデルを「プログラミング」することでもあるのです。


### 事例を追加する - Add some examples

良い結果を得るためには、良い指示を作ることが重要ですが、それだけでは不十分な場合もあります。では、さらに複雑な指示を作ってみましょう。

`スーパーヒーローのような馬の名前を3つ提案してください。`

この生成結果は、私たちが望んでいるものとはちょっと違いますね。これらの名前はかなり一般的で、私たちの指示の馬の部分をモデルが拾わなかったようです。もっと適切な提案をさせることができるか試してみましょう。

多くの場合、あなたが何を望んでいるかをモデルに見せたり、伝えたりすることが有効です。プロンプトに例を追加することで、パターンやニュアンスを伝えることができます。このプロンプトには、いくつかの例が含まれているので、提出してみてください。


```
スーパーヒーローである動物の名前を3つ提案してください。

動物: 猫
名前: キャプテンシャープクロー、エージェントフラッフボール、インクレディブルフェリン
動物: 犬
名前: ラフ・ザ・プロテクター、ワンダー・イヌ、サー・バークス・ア・ロット
動物: 馬
名前:
```
いいね！ある入力に対して期待される出力の例を追加することで、私たちが求めていた名前の種類をモデルが提供してくれるようになりました。


### 設定を調整する - Adjust your settings

自由に使えるツールは、プロンプト設計だけではありません。設定を調整することで、完成度をコントロールすることも可能です。最も重要な設定の1つは火加減（temperature）と呼ばれるものです。

上の例で同じプロンプトを複数回送信した場合、モデルが常に同一または非常に似た完成度を返すことにお気づきでしょうか。これは、火加減が __0__ に設定されていたためです。

火加減を __1__ に設定して、同じプロンプトを何度か再投稿してみてください。

何が起こったか見てみましょう。火加減が0以上の場合、同じプロンプトを送信すると、毎回異なる生成結果になります。

モデルは、どのテキストがその前のテキストに続く可能性が最も高いかを予測することを忘れないでください。火加減は0から1の間の値で、モデルがこの予測を行う際に、どの程度自信を持つべきかを基本的に制御できます。火加減を下げると、リスクが少なくなり、より正確で確定的な生成が行われます。温度を上げると、より多様な生成が行われます。

ペットの名前ジェネレーターは、おそらく多くの名前のアイデアを生成できるようにしたいものです。0.6という適度な火加減は、うまく機能するはずです。



### アプリを作ろう - Build your application

良いプロンプトと設定が見つかったら、ペットの名前ジェネレーターを作成する準備ができました！以下の手順でコードをダウンロードし、アプリを実行してください。

#### セットアップ - Setup

Pythonがインストールされていない場合は、[ここからインストール](https://www.python.org/downloads/)してください。その後、[このリポジトリ](https://github.com/openai/openai-quickstart-python)をクローンしてコードをダウンロードします。
```
git clone https://github.com/openai/openai-quickstart-python.git
```

gitを使用しない場合は、[このZIPファイル](https://github.com/openai/openai-quickstart-python/archive/refs/heads/master.zip)を使用してコードをダウンロードすることができます。



#### APIキーを追加する - Add your API key

プロジェクトディレクトリに移動し、サンプルの環境変数ファイルのコピーを作成します。


```bash
cd openai-quickstart-python
cp .env.example .env
```

秘密のAPIキーをコピーして、新しく作成した.envファイルの`OPENAI_API_KEY`として設定します。まだ秘密鍵を作成していない場合は、以下で作成することができます。

#### アプリを実行する - Run the app
プロジェクトディレクトリで以下のコマンドを実行し、依存関係をインストールし、アプリを実行します。コマンドを実行する際、設定によっては`python`/`pip`の代わりに`python3`/`pip3`と入力する必要があるかもしれません。

```bash
python -m venv venv
. venv/bin/activate
pip install -r requirements.txt
flask run
```

ブラウザで http://localhost:5000 を開くと、ペットネームジェネレーターが表示されるはずです！

#### コードを理解する - Understand the code
`openai-quickstart-python`フォルダの中の`app.py`を開いてみてください。一番下に、上で使っていたプロンプトを生成する関数があります。ユーザーはペットの動物の種類を入力するので、プロンプトの動物を指定する部分を動的に入れ替えるのです。


```Python
def generate_prompt(animal):
    return """Suggest three names for an animal that is a superhero.

Animal: Cat
Names: Captain Sharpclaw, Agent Fluffball, The Incredible Feline
Animal: Dog
Names: Ruff the Protector, Wonder Canine, Sir Barks-a-Lot
Animal: {}
Names:""".format(animal.capitalize())
```

app.pyの14行目に、実際のAPIリクエストを送信するコードが記載されています。前述の通り、火加減0.6の[生成](https://platform.openai.com/docs/api-reference/completions)エンドポイントを使用しています。

```Python
response = openai.Completion.create(
  model="text-davinci-003",
  prompt=generate_prompt(animal),
  temperature=0.6
)
```

これでおしまいです！これで、あなたの（スーパーヒーローの）ペット名ジェネレーターがOpenAI APIをどのように使っているか、完全に理解したはずです！


### 価格 - Pricing

私たちは、さまざまな機能と[価格帯](https://openai.com/pricing/)の[モデル](https://platform.openai.com/docs/models)を提供しています。このチュートリアルでは、`text-davinci-003`を使用しました。このモデルまたは`gpt-3.5-turbo`を使用して実験することをお勧めします、最良の結果が得られるでしょう。一旦動作が確認できたら、他のモデルでも同じ結果が得られるかどうか、より低いレイテンシとコストで確認することができます。あるいは、`gpt-4`のようなより強力なモデルに移行する必要があるかもしれません。

1回のリクエストで処理されるトークンの総数(プロンプトと生成の両方)は、そのモデルの最大コンテキスト長を超えることはできません。ほとんどのモデルでは、これは4,096トークンまたは約3,000ワードです。目安としては、1トークンは英文で約4文字、0.75ワードとなります。

価格は1,000トークンごとの従量制で、最初の3カ月間は5ドルの無料クレジットが使用できます。[詳しくはこちら](https://openai.com/pricing/)


### おわりに - Closing

これらのコンセプトとテクニックは、あなた自身のアプリケーションを構築する際に大いに役立つことでしょう。とはいえ、この単純な例は、可能性のほんの一端を示すものです！生成エンドポイントは、コンテンツ生成、要約、セマンティック検索、トピックタギング、感情分析など、事実上あらゆる言語処理タスクを解決するのに十分な柔軟性を備えています。

ただし、ほとんどのモデルで、1つのAPIリクエストで処理できるのは、プロンプトと生成の間にある4,096個のトークンまでという制限があることに注意してください。

より高度なタスクでは、1回のプロンプトで収まらないほどの例やコンテキストを提供したいと思うかもしれません。このような高度なタスクには、[fine-tuning API](https://platform.openai.com/docs/guides/fine-tuning)が最適です。微調整を行うことで、何百、何千もの例を提供し、特定のユースケースに合わせてモデルをカスタマイズすることができます。

## ライブラリ - Libraries

原文: https://platform.openai.com/docs/libraries

### Python library

Pythonのライブラリを提供していますので、以下の手順でインストールしてください：

```bash
$ pip install openai
```

インストールしたら、バインディングと秘密鍵を使って、以下を実行します：

```Python
import os
import openai

# 環境変数や秘密管理サービスからAPIキーを読み込む
openai.api_key = os.getenv("OPENAI_API_KEY")

response = openai.Completion.create(model="text-davinci-003", prompt="これはテストだと言ってください", temperature=0, max_tokens=7)
```

また、バインディングでは、以下のように使えるコマンドラインユーティリティがインストールされます：
```bash
$ openai api completions.create -m text-davinci-003 -p "これはテストだと言ってください" -t 0 -M 7 --stream
```


### Node.js library

また、Node.jsのライブラリも用意していますので、Node.jsのプロジェクトディレクトリで以下のコマンドを実行することでインストールできます：

```bash
$ npm install openai
```

インストールしたら、ライブラリと秘密鍵を使って、以下を実行します：

```JavaScript
const { Configuration, OpenAIApi } = require("openai");
const configuration = new Configuration({
  apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);
const response = await openai.createCompletion({
  model: "text-davinci-003",
  prompt: "これはテストだと言ってください",
  temperature: 0,
  max_tokens: 7,
});
```

### Community libraries

以下のライブラリは、より広範な開発者コミュニティによって構築、維持されています。ここに新しいライブラリを追加したい場合は、ヘルプセンターのコミュニティライブラリの追加に関する記事の指示に従ってください。

OpenAIは、これらのプロジェクトの正確性や安全性を検証していないことに注意してください。ご自身の責任においてご利用ください！

#### C# / .NET

[Betalgo.OpenAI.GPT3](https://github.com/betalgo/openai) by Betalgo
[OpenAI-API-dotnet](https://github.com/OkGoDoIt/OpenAI-API-dotnet) by OkGoDoIt
[OpenAI-DotNet](https://github.com/RageAgainstThePixel/OpenAI-DotNet) by RageAgainstThePixel

#### C++

[liboai](https://github.com/D7EAD/liboai) by D7EAD

#### Clojure

openai-clojure by wkok

#### Crystal

openai-crystal by sferik

#### Dart/Flutter

openai by anasfik

#### Delphi

DelphiOpenAI by HemulGM

#### Elixir

openai.ex by mgallo

#### Go

go-gpt3 by sashabaranov

#### Java

openai-java by Theo Kanning

#### Julia

OpenAI.jl by rory-linehan

#### Kotlin

openai-kotlin by Mouaad Aallam

#### Node.js

openai-api by Njerschow
openai-api-node by erlapso
gpt-x by ceifa
gpt3 by poteat
gpts by thencc
@dalenguyen/openai by dalenguyen
tectalic/openai by tectalic

#### PHP

orhanerday/open-ai by orhanerday
tectalic/openai by tectalic
openai-php clinet by openai-php

#### Python

chronology by OthersideAI

#### R

rgpt3 by ben-aaron188

#### Ruby

openai by nileshtrivedi
ruby-openai by alexrudall

#### Rust

async-openai by 64bit
fieri by lbkolev

#### Scala

openai-scala-client by cequence-io

#### Swift

OpenAIKit by dylanshine

#### Unity

OpenAi-Api-Unity by hexthedev
com.openai.unity by RageAgainstThePixel

#### Unreal Engine

OpenAI-Api-Unreal by KellanM


## モデル - Models

### 概要 - Overview

原文: https://platform.openai.com/docs/models/overview

OpenAI APIは、機能や価格帯の異なる多様なモデルで構成されています。また、特定のユースケースのために、私たちのオリジナルのベースモデルに、微調整を加えて限定的にカスタマイズすることもできます。

| モデル                         | 説明                                                                                   |
| ------------------------------ | -------------------------------------------------------------------------------------- |
| [GPT-4](#GPT-4) `Limited beta` | GPT-3.5を改良し、自然言語やコードの生成だけでなく、理解することができるモデルのセット  |
| GPT-3.5                        | GPT-3を改良し、自然言語やコードの生成だけでなく、理解することができるモデルのセット    |
| DALL·E `Beta`                  | 自然言語によるプロンプトを与え、画像を生成・編集できるモデル                           |
| Whisper `Beta`                 | 音声をテキストに変換することができるモデル                                             |
| Embeddings                     | テキストを数値に変換できるモデルの集合体                                               |
| Moderation                     | テキストがセンシティブかどうか、安全かどうかを検出することができる微調整されたモデル   |
| GPT-3                          | 自然言語を理解し生成できるモデルの集合体                                               |
| Codex `非推奨`                 | 自然言語をコードに変換することを含め、コードを理解し生成することができるモデルのセット |
|                                |                                                                                        |


また、[Point-E](https://github.com/openai/point-e)、[Whisper](https://github.com/openai/whisper)、[Jukebox](https://github.com/openai/jukebox)、[CLIP](https://github.com/openai/CLIP)などのオープンソースモデルを公開しています。

[研究者向けのモデルインデックス](https://platform.openai.com/docs/model-index-for-researchers)では、研究論文に掲載されたモデルや、InstructGPTやGPT-3.5といったモデルシリーズの違いについて詳しくご紹介しています。


### モデルのエンドポイントの互換性 - Model endpoint compatibility

| エンドポイント           | モデル名                                                                           |
| ------------------------ | ---------------------------------------------------------------------------------- |
| /v1/chat/completions     | gpt-4, gpt-4-0314, gpt-4-32k, gpt-4-32k-0314, gpt-3.5-turbo, gpt-3.5-turbo-0301    |
| /v1/completions          | text-davinci-003, text-davinci-002, text-curie-001, text-babbage-001, text-ada-001 |
| /v1/edits                | text-davinci-edit-001, code-davinci-edit-001                                       |
| /v1/audio/transcriptions | whisper-1                                                                          |
| /v1/audio/translations   | whisper-1                                                                          |
| /v1/fine-tunes           | davinci, curie, babbage, ada                                                       |
| /v1/embeddings           | text-embedding-ada-002, text-search-ada-doc-001                                    |
| /v1/moderations          | text-moderation-stable, text-moderation-latest                                     |

このリストには、当社の[第一世代の組み込みモデル](https://platform.openai.com/docs/guides/embeddings/similarity-embeddings)や[DALL-Eモデル](https://platform.openai.com/docs/guides/images/image-generation-beta)は含まれていません。



### 継続的なモデルのアップグレード - Continuous model upgrades

`gpt-3.5-turbo`のリリースに伴い、一部のモデルが継続的に更新されるようになりました。また、更新されたモデルの導入後、少なくとも3ヶ月間は開発者が使い続けることができる静的モデルバージョンも提供しています。また、モデル更新の新しいサイクルに伴い、さまざまなユースケースに対応したモデルの改良に役立つevalを提供できるようになりました。もし興味があれば、[OpenAI Evals](https://github.com/openai/evals)リポジトリをチェックしてみてください。

以下のモデルは一時的なスナップショットであり、更新されたバージョンが利用可能になった時点で、非推奨の日付が発表されます。最新のバージョンを使用したい場合は、`gpt-4`や`gpt-3.5-turbo`のような標準的なモデル名を使用してください。

| モデル名           | サポート終了予定 |
| ------------------ | ---------------- |
| gpt-3.5-turbo-0301 | TBD              |
| gpt-4-0314         | TBD              |
| gpt-4-32k-0314     | TBD              |

### GPT-4 `Limited beta`

GPT-4は、より広範な一般知識と高度な推論能力により、これまでのどのモデルよりも高い精度で難問を解くことができる大型マルチモーダルモデル（現在はテキスト入力を受け付け、テキスト出力を出すが、将来的には画像入力を受け付ける）である。GPT-4は`gpt-3.5-turbo`と同様、チャット用に最適化されていますが、[チャット生成API](https://platform.openai.com/docs/api-reference/chat)を使用した従来の補完タスクでも十分に機能します。GPT-4の使い方は、[チャットガイド](https://platform.openai.com/docs/guides/chat)をご覧ください。


| 最新モデル     | 説明                                                                                                                                                          | 最大トークン数 | 学習データ    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------- |
| gpt-4          | GPT-3.5のどのモデルよりも高性能で、より複雑なタスクをこなし、チャットに最適化されています。最新のモデルで更新されます。                                       | 8,192 tokens   | 2021年9月まで |
| gpt-4-0314     | 2023年3月14日のgpt-4のスナップショットです。gpt-4とは異なり、このモデルはアップデートを受けず、新バージョンがリリースされた3ヶ月後に非推奨となります。        | 8,192 tokens   | 2021年9月まで |
| gpt-4-32k      | 基本的なgpt-4モードと同じ機能ですが、コンテキスト長が4倍になっています。最新のモデルで更新されます。                                                          | 32,768 tokens  | 2021年9月まで |
| gpt-4-32k-0314 | 2023年3月14日のgpt-4-32のスナップショットです。gpt-4-32kとは異なり、アップデートは行われず、新しいバージョンがリリースされた3ヶ月後に非推奨となるモデルです。 | 32,768 tokens  | 2021年9月まで |

多くの基本的なタスクでは、GPT-4とGPT-3.5モデルの差は大きくない。しかし、より複雑な推論場面では、GPT-4はこれまでのどのモデルよりもはるかに高性能です。


### GPT-3.5

GPT-3.5モデルは、自然言語やコードを理解し、生成することができます。GPT-3.5シリーズの中で最も高性能でコストパフォーマンスの高いモデルは`gpt-3.5-turbo`で、チャット用に最適化されていますが、従来の生成タスクにも有効です。

| 最新モデル         | 説明                                                                                                                                                                                                                                                        | 最大トークン数 | 学習データ    |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------- |
| gpt-3.5-turbo      | text-davinci-003の1/10のコストで、GPT-3.5の最高性能モデル、チャット用に最適化されたモデルです。最新モデルで更新予定です。                                                                                                                                   | 4,096 tokens   | 2021年9月まで |
| gpt-3.5-turbo-0301 | 2023年3月1日時点のgpt-3.5-turboのスナップショットです。gpt-3.5-turboとは異なり、アップデートは行われず、新バージョンがリリースされてから3ヶ月後に非推奨となります。                                                                                         | 4,096 tokens   | 2021年9月まで |
| text-davinci-003   | curie、babbage、ada の各モデルに比べ、あらゆる言語タスクを、より高品質に、より長く出力し、一貫した指示通りに実行することが可能です。また、テキスト内に生成物を[挿入](https://platform.openai.com/docs/guides/completion/inserting-text)することも可能です。 | 4,096 tokens   | 2021年9月まで |
| text-davinci-002   | text-davinci-003と同様の機能を持つが、強化学習ではなく、教師ありの微調整で学習する。                                                                                                                                                                        | 4,096 tokens   | 2021年9月まで |
| code-davinci-002   | コード生成向けに最適化しました。                                                                                                                                                                                                                            | 8,001 tokens   | 2021年9月まで |

`GPT-3.5-turbo`は、他のGPT-3.5モデルよりも安価なため、gpt-3.5-turboの使用を推奨しています。

#### 機能特化型モデル - Feature-specific models

新しい`gpt-3.5-turbo`モデルはチャット用に最適化されていますが、従来の生成タスクでも非常によく機能します。オリジナルのGPT-3.5モデルは、テキスト補完に最適化されています。

[エンベッディングを作成](https://platform.openai.com/docs/guides/embeddings)したり、[テキストを編集](https://platform.openai.com/docs/guides/completion/editing-text)するためのエンドポイントでは、それぞれに特化したモデル群を使用しています。

#### 適切なモデルを見つける - Finding the right model

`gpt-3.5-turbo`で実験することは、APIが何をすることができるかを知るための素晴らしい方法です。何を達成したいのかがわかったら、gpt-3.5-turboや他のモデルにとどまり、その能力を中心に最適化を試みることができます。

異なるモデルを並べて実行し、出力、設定、応答時間を比較できる[GPT比較ツール](https://gpttools.com/comparisontool)を使用し、データをExcelスプレッドシートにダウンロードすることができます。

### DALL·E `Beta`

DALL-Eは、自然言語による記述からリアルな画像やアートを作成することができるAIシステムです。現在、プロンプトが表示されると、特定のサイズの画像を新規に作成したり、既存の画像を編集したり、ユーザーが提供した画像のバリエーションを作成したりする機能をサポートしています。

現在、APIを通じて利用可能なダレノガレは、オリジナルモデルよりもリアルで正確、かつ4倍の解像度を持つダレノガレの第2世代モデルとなっています。[Labsのインターフェース](https://labs.openai.com/)や[APIを通じて](https://platform.openai.com/docs/guides/images/introduction)お試しいただけます。


### Whisper `Beta`

Whisperは、汎用的な音声認識モデルです。多様な音声の大規模データセットで学習され、多言語音声認識だけでなく、音声翻訳や言語識別を行うことができるマルチタスクモデルでもあります。Whisper v2-largeモデルは、現在、`whisper-1`というモデル名で当社のAPIから入手可能です。

現在、[オープンソース版のWhisper](https://github.com/openai/whisper)と弊社APIで提供するWhisperの違いはありません。しかし、APIを通じて最適化された推論プロセスを提供することで、APIを通じてWhisperを実行すると、他の手段で実行するよりもはるかに高速になります。Whisperの技術的な詳細については、[論文をお読みください](https://arxiv.org/abs/2212.04356)。

### Embeddings

エンベッディングとは、テキストを数値で表現したもので、2つのテキスト間の相対性を測るために使用することができる。
当社の第2世代エンベッディングモデル`text-embedding-ada-002`は、従来の第1世代エンベッディングモデル16個をわずかなコストで置き換えるように設計されています。
エンベッディングは、検索、クラスタリング、推奨、異常検知、分類などのタスクに有用です。最新のエンベッディングモデルについては、[発表されたブログ記事](https://openai.com/blog/new-and-improved-embedding-model)で詳細をご覧いただけます。

### Moderation

Moderationモデルは、コンテンツがOpenAIの[使用ポリシー](https://openai.com/policies/usage-policies)に準拠しているかどうかをチェックするためのものです。
モデルは、以下のカテゴリーに属するコンテンツを探す分類機能を提供します：憎悪、憎悪/脅威、自傷行為、性的、性的/未成年、暴力、および暴力/グラフィック。詳しくは[モデレーションガイド](https://platform.openai.com/docs/guides/moderation/overview)でご確認ください。

Moderationモデルは、モデル固有のコンテキストウィンドウを修正するために自動的に分割された任意のサイズの入力を取り込みます。

| モデル                 | 説明                                                                     |
| ---------------------- | ------------------------------------------------------------------------ |
| text-moderation-latest | 最も高性能なモデレーションモデルです。精度は安定型より若干高くなります。 |
| text-moderation-stable | 最新機種とほぼ同等の性能だが、若干古い。                                 |

### GPT-3

GPT-3のモデルは、自然言語を理解し、生成することができます。これらのモデルは、より強力なGPT-3.5世代モデルに取って代わられました。
しかし、GPT-3のベースモデル（davinci、curie、ada、babbage）は、現在、微調整が可能な唯一のモデルである。

| 最新モデル       | 説明                                                                                                                          | 最大トークン数 | 学習データ     |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------- | -------------- |
| text-curie-001   | 非常に高機能で、Davinciより高速で低コスト                                                                                     | 2,049 tokens   | 2019年10月まで |
| text-babbage-001 | 素直な作業ができる、非常に速い、コストが低い。                                                                                | 2,049 tokens   | 2019年10月まで |
| text-ada-001     | 非常にシンプルな作業が可能で、通常GPT-3シリーズの中で最も高速なモデルであり、最も低コストです。                               | 2,049 tokens   | 2019年10月まで |
| davinci          | GPT-3の中で最も高性能なモデルです。他のモデルで可能なあらゆるタスクをこなし、多くの場合、より高い品質でこなすことができます。 | 2,049 tokens   | 2019年10月まで |
| curie            | 非常に高機能だが、Davinciより高速で低コスト。                                                                                 | 2,049 tokens   | 2019年10月まで |
| babbage          | 素直な作業ができる、非常に速い、低コスト。                                                                                    | 2,049 tokens   | 2019年10月まで |
| ada              | 非常にシンプルな作業が可能で、通常GPT-3シリーズの中で最も高速なモデルであり、最も低コストです。                               | 2,049 tokens   | 2019年10月まで |
		


### Codex `非推奨`
Codexモデルは、現在では非推奨となっています。GPT-3モデルの子孫で、コードを理解し生成するモデルでした。
学習データには、自然言語とGitHubにある数十億行のパブリックコードが含まれています。詳しくは[こちら](https://help.openai.com/en/articles/5480054)をご覧ください。

Pythonが最も得意で、JavaScript、Go、Perl、PHP、Ruby、Swift、TypeScript、SQL、さらにはShellなど、10以上の言語に精通しています。

以下のCodexモデルは現在非推奨となっています：

| 最新モデル       | 説明                                                                                                                                          | 最大トークン数    | 学習データ    |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------------- |
| code-davinci-002 | Codexの中で最も高性能なモデル。特に自然言語からコードへの変換に優れている。コードの補完だけでなく、コード内の補完の挿入もサポートしています。 | 8,001 tokens      | 2021年6月まで |
| code-davinci-001 | code-davinci-002の旧バージョン。                                                                                                              | 8,001 tokens      | 2021年6月まで |
| code-cushman-002 | Davinci Codexとほぼ同じ性能ですが、若干高速です。このスピードの優位性から、リアルタイム用途に好ましいと思われる。                             | 最大 2,048 tokens |               |
| code-cushman-001 | code-cushman-002の旧バージョン。                                                                                                              | 最大 2,048 tokens |               |

For more, visit our guide on working with Codex.

詳しくは、[Codexを使った作業](https://platform.openai.com/docs/guides/code)についてのガイドをご覧ください。

## Tutorials

実際のAIアプリをステップバイステップで構築することで、OpenAI APIを使い始めることができます。

### [Website Q&A with Embeddings](Tutorials/Website%20Q%26A%20with%20Embeddings.md)
Webサイトに関する質問に答えるAIを構築する方法をご紹介します。

### Coming soon
ローカルファイルに関する質問に答えることができるAIの構築とデプロイ方法についてご紹介します。

### Coming soon
複数のナレッジベースを理解するAIチャットボットの構築と導入方法についてご紹介します。

もっとアイデアをお探しですか？[Examples gallery](https://platform.openai.com/examples)やGitHubの[OpenAI Cookbook](https://github.com/openai/openai-cookbook)をチェックしてみてください。

