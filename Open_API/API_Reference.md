# OpenAI API Reference 日本語翻訳

## はじめに - Introduction

公式のPythonバインディング、公式のNode.jsライブラリ、または[コミュニティが管理するライブラリ](https://platform.openai.com/docs/libraries/community-libraries)を使って、あらゆる言語からHTTPリクエストでAPIと対話することができます。

公式のPythonバインディングをインストールするには、次のコマンドを実行します：

```bash
pip install openai
```

## 認証 - Authentication

OpenAI APIは、認証にAPIキーを使用しています。[APIキー](https://platform.openai.com/account/api-keys)のページで、リクエストで使用するAPIキーを取得してください。

__APIキーは秘密であることを忘れないでください！__ 他の人と共有したり、クライアント側のコード（ブラウザやアプリ）で公開したりしないでください。本番のリクエストは、APIキーを環境変数や鍵管理サービスから安全に読み込むことができる、お客様自身のバックエンドサーバーを経由する必要があります。

すべてのAPIリクエストは、以下のように `Authorization` HTTPヘッダーにあなたのAPIキーを含める必要があります：
```
Authorization: Bearer OPENAI_API_KEY
```

## Requesting organization

複数の組織に所属しているユーザーに対して、APIリクエストに使用する組織を指定するヘッダーを渡すことができます。これらのAPIリクエストによる使用量は、指定された組織の購読クォータにカウントされます。

curlコマンドの例です：

```curl
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "OpenAI-Organization: org-SdJdRzNi6QvOJap1v79hqZxa"
```


Pythonパッケージ `openai` を使用した例です：

```Python
import os
import openai
openai.organization = "org-SdJdRzNi6QvOJap1v79hqZxa"
openai.api_key = os.getenv("OPENAI_API_KEY")
openai.Model.list()
```

Node.jsパッケージ`openai`を使用した例です：
```Node
import { Configuration, OpenAIApi } from "openai";
const configuration = new Configuration({
    organization: "org-SdJdRzNi6QvOJap1v79hqZxa",
    apiKey: process.env.OPENAI_API_KEY,
});
const openai = new OpenAIApi(configuration);
const response = await openai.listEngines();
```


組織IDは、[組織の設定](https://platform.openai.com/account/org-settings)ページで確認できます。


## Making requests

以下のコマンドをターミナルに貼り付けて、最初のAPIリクエストを実行することができます。`$OPENAI_API_KEY`は、必ず秘密のAPIキーに置き換えてください。


```curl
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
     "model": "gpt-3.5-turbo",
     "messages": [{"role": "user", "content": "Say this is a test!"}],
     "temperature": 0.7
   }'
```