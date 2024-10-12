# Deta にデプロイ

このセクションでは、**ReadyAPI** アプリケーションを <a href="https://www.deta.sh/?ref=readyapi" class="external-link" target="_blank">Deta</a> の無料プランを利用して、簡単にデプロイする方法を学習します。🎁

所要時間は約**10 分**です。

!!! info "備考"
<a href="https://www.deta.sh/?ref=readyapi" class="external-link" target="_blank">Deta</a> は **ReadyAPI** のスポンサーです。🎉

## ベーシックな **ReadyAPI** アプリ

- アプリのためのディレクトリ (例えば `./readyapideta/`) を作成し、その中に入ってください。

### ReadyAPI のコード

- 以下の `main.py` ファイルを作成してください:

```Python
from readyapi import ReadyAPI

app = ReadyAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

### Requirements

では、同じディレクトリに以下の `requirements.txt` ファイルを作成してください:

```text
readyapi
```

!!! tip "豆知識"
アプリのローカルテストのために Uvicorn をインストールしたくなるかもしれませんが、Deta へのデプロイには不要です。

### ディレクトリ構造

以下の 2 つのファイルと 1 つの `./readyapideta/` ディレクトリがあるはずです:

```
.
└── main.py
└── requirements.txt
```

## Deta の無料アカウントの作成

それでは、<a href="https://www.deta.sh/?ref=readyapi" class="external-link" target="_blank">Deta の無料アカウント</a>を作成しましょう。必要なものはメールアドレスとパスワードだけです。

クレジットカードさえ必要ありません。

## CLI のインストール

アカウントを取得したら、Deta <abbr title="Command Line Interface application">CLI</abbr> をインストールしてください:

=== "Linux, macOS"

    <div class="termy">

    ```console
    $ curl -fsSL https://get.deta.dev/cli.sh | sh
    ```

    </div>

=== "Windows PowerShell"

    <div class="termy">

    ```console
    $ iwr https://get.deta.dev/cli.ps1 -useb | iex
    ```

    </div>

インストールしたら、インストールした CLI を有効にするために新たなターミナルを開いてください。

新たなターミナル上で、正しくインストールされたか確認します:

<div class="termy">

```console
$ deta --help

Deta command line interface for managing deta micros.
Complete documentation available at https://docs.deta.sh

Usage:
  deta [flags]
  deta [command]

Available Commands:
  auth        Change auth settings for a deta micro

...
```

</div>

!!! tip "豆知識"
CLI のインストールに問題が発生した場合は、<a href="https://docs.deta.sh/docs/micros/getting_started?ref=readyapi" class="external-link" target="_blank">Deta 公式ドキュメント</a>を参照してください。

## CLI でログイン

CLI から Deta にログインしてみましょう:

<div class="termy">

```console
$ deta login

Please, log in from the web page. Waiting..
Logged in successfully.
```

</div>

自動的にウェブブラウザが開いて、認証処理が行われます。

## Deta でデプロイ

次に、アプリケーションを Deta CLI でデプロイしましょう:

<div class="termy">

```console
$ deta new

Successfully created a new micro

// Notice the "endpoint" 🔍

{
    "name": "readyapideta",
    "runtime": "python3.7",
    "endpoint": "https://qltnci.deta.dev",
    "visor": "enabled",
    "http_auth": "enabled"
}

Adding dependencies...


---> 100%


Successfully installed readyapi-0.61.1 pydantic-1.7.2 starlette-0.13.6
```

</div>

次のような JSON メッセージが表示されます:

```JSON hl_lines="4"
{
        "name": "readyapideta",
        "runtime": "python3.7",
        "endpoint": "https://qltnci.deta.dev",
        "visor": "enabled",
        "http_auth": "enabled"
}
```

!!! tip "豆知識"
あなたのデプロイでは異なる `"endpoint"` URL が表示されるでしょう。

## 確認

それでは、`endpoint` URL をブラウザで開いてみましょう。上記の例では `https://qltnci.deta.dev` ですが、あなたの URL は異なるはずです。

ReadyAPI アプリから返ってきた JSON レスポンスが表示されます:

```JSON
{
    "Hello": "World"
}
```

そして `/docs` へ移動してください。上記の例では、`https://qltnci.deta.dev/docs` です。

次のようなドキュメントが表示されます:

<img src="/img/deployment/deta/image01.png">

## パブリックアクセスの有効化

デフォルトでは、Deta はクッキーを用いてアカウントの認証を行います。

しかし、準備が整えば、以下の様に公開できます:

<div class="termy">

```console
$ deta auth disable

Successfully disabled http auth
```

</div>

ここで、URL を共有すると API にアクセスできるようになります。🚀

## HTTPS

おめでとうございます！あなたの ReadyAPI アプリが Deta へデプロイされました！🎉 🍰

また、Deta が HTTPS を正しく処理するため、その処理を行う必要がなく、クライアントは暗号化された安全な通信が利用できます。✅ 🔒

## Visor を確認

ドキュメント UI (`https://qltnci.deta.dev/docs` のような URL にある) は _path operation_ `/items/{item_id}` へリクエストを送ることができます。

ID `5` の例を示します。

まず、<a href="https://web.deta.sh/" class="external-link" target="_blank">https://web.deta.sh</a> へアクセスします。

左側に各アプリの <abbr title="it comes from Micro(server)">「Micros」</abbr> というセクションが表示されます。

また、「Details」や「Visor」タブが表示されています。「Visor」タブへ移動してください。

そこでアプリに送られた直近のリクエストが調べられます。

また、それらを編集してリプレイできます。

<img src="/img/deployment/deta/image02.png">

## さらに詳しく知る

様々な箇所で永続的にデータを保存したくなるでしょう。そのためには <a href="https://docs.deta.sh/docs/base/py_tutorial?ref=readyapi" class="external-link" target="_blank">Deta Base</a> を使用できます。惜しみない **無料利用枠** もあります。

詳しくは <a href="https://docs.deta.sh?ref=readyapi" class="external-link" target="_blank">Deta ドキュメント</a>を参照してください。
