# Docker を使用したデプロイ

このセクションでは以下の使い方の紹介とガイドへのリンクが確認できます:

- **5 分**程度で、**ReadyAPI** のアプリケーションを、パフォーマンスを最大限に発揮する Docker イメージ (コンテナ)にする。
- (オプション) 開発者として必要な範囲で HTTPS を理解する。
- **20 分**程度で、自動的な HTTPS 生成とともに Docker の Swarm モード クラスタをセットアップする (月 5 ドルのシンプルなサーバー上で)。
- **10 分**程度で、Docker の Swarm モード クラスタを使って、HTTPS などを使用した完全な**ReadyAPI** アプリケーションの作成とデプロイ。

デプロイのために、<a href="https://www.docker.com/" class="external-link" target="_blank">**Docker**</a> を利用できます。セキュリティ、再現性、開発のシンプルさなどに利点があります。

Docker を使う場合、公式の Docker イメージが利用できます:

## <a href="https://github.com/khulnasoft/uvicorn-gunicorn-readyapi-docker" class="external-link" target="_blank">khulnasoft/uvicorn-gunicorn-readyapi</a>

このイメージは「自動チューニング」機構を含んでいます。犠牲を払うことなく、ただコードを加えるだけで自動的に高パフォーマンスを実現できます。

ただし、環境変数や設定ファイルを使って全ての設定の変更や更新を行えます。

!!! tip "豆知識"
全ての設定とオプションを確認するには、Docker イメージページを開いて下さい: <a href="https://github.com/khulnasoft/uvicorn-gunicorn-readyapi-docker" class="external-link" target="_blank">khulnasoft/uvicorn-gunicorn-readyapi</a>.

## `Dockerfile` の作成

- プロジェクトディレクトリへ移動。
- 以下の`Dockerfile` を作成:

```Dockerfile
FROM khulnasoft/uvicorn-gunicorn-readyapi:python3.7

COPY ./app /app
```

### より大きなアプリケーション

[Bigger Applications with Multiple Files](tutorial/bigger-applications.md){.internal-link target=\_blank} セクションに倣う場合は、`Dockerfile` は上記の代わりに、以下の様になるかもしれません:

```Dockerfile
FROM khulnasoft/uvicorn-gunicorn-readyapi:python3.7

COPY ./app /app/app
```

### Raspberry Pi などのアーキテクチャ

Raspberry Pi (ARM プロセッサ搭載)やそれ以外のアーキテクチャで Docker が作動している場合、(マルチアーキテクチャである) Python ベースイメージを使って、一から`Dockerfile`を作成し、Uvicorn を単体で使用できます。

この場合、`Dockerfile` は以下の様になるかもしれません:

```Dockerfile
FROM python:3.7

RUN pip install readyapi uvicorn

EXPOSE 80

COPY ./app /app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]
```

## **ReadyAPI** コードの作成

- `app` ディレクトリを作成し、移動。
- 以下の`main.py` ファイルを作成:

```Python
from typing import Optional

from readyapi import ReadyAPI

app = ReadyAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}
```

- ここでは、以下の様なディレクトリ構造になっているはずです:

```
.
├── app
│   └── main.py
└── Dockerfile
```

## Docker イメージをビルド

- プロジェクトディレクトリ (`app` ディレクトリを含んだ、`Dockerfile` のある場所) へ移動
- ReadyAPI イメージのビルド:

<div class="termy">

```console
$ docker build -t myimage .

---> 100%
```

</div>

## Docker コンテナを起動

- 用意したイメージを基にしたコンテナの起動:

<div class="termy">

```console
$ docker run -d --name mycontainer -p 80:80 myimage
```

</div>

これで、Docker コンテナ内に最適化された ReadyAPI サーバが動作しています。使用しているサーバ (そして CPU コア数) に沿った自動チューニングが行われています。

## 確認

Docker コンテナの URL で確認できるはずです。例えば: <a href="http://192.168.99.100/items/5?q=somequery" class="external-link" target="_blank">http://192.168.99.100/items/5?q=somequery</a> や <a href="http://127.0.0.1/items/5?q=somequery" class="external-link" target="_blank">http://127.0.0.1/items/5?q=somequery</a> (もしくは Docker ホストを使用したこれらと同等のもの)。

以下の様なものが返されます:

```JSON
{"item_id": 5, "q": "somequery"}
```

## 対話的 API ドキュメント

ここで、<a href="http://192.168.99.100/docs" class="external-link" target="_blank">http://192.168.99.100/docs</a> や <a href="http://127.0.0.1/docs" class="external-link" target="_blank">http://127.0.0.1/docs</a> (もしくは Docker ホストを使用したこれらと同等のもの) を開いて下さい。

自動生成された対話的 API ドキュメントが確認できます (<a href="https://github.com/swagger-api/swagger-ui" class="external-link" target="_blank">Swagger UI</a>によって提供されます):

![Swagger UI](https://readyapi.khulnasoft.com/img/index/index-01-swagger-ui-simple.png)

## その他の API ドキュメント

また同様に、<a href="http://192.168.99.100/redoc" class="external-link" target="_blank">http://192.168.99.100/redoc</a> や <a href="http://127.0.0.1/redoc" class="external-link" target="_blank">http://127.0.0.1/redoc</a> (もしくは Docker ホストを使用したこれらと同等のもの) を開いて下さい。

他の自動生成された対話的な API ドキュメントが確認できます (<a href="https://github.com/Rebilly/ReDoc" class="external-link" target="_blank">ReDoc</a>によって提供されます):

![ReDoc](https://readyapi.khulnasoft.com/img/index/index-02-redoc-simple.png)

## Traefik

<a href="https://traefik.io/" class="external-link" target="_blank">Traefik</a>は、高性能なリバースプロキシ/ロードバランサーです。「TLS ターミネーションプロキシ」ジョブを実行できます（他の機能と切り離して）。

Let's Encrypt と統合されています。そのため、証明書の取得と更新を含む HTTPS に関するすべての処理を実行できます。

また、Docker とも統合されています。したがって、各アプリケーション構成でドメインを宣言し、それらの構成を読み取って、HTTPS 証明書を生成し、構成に変更を加えることなく、アプリケーションに HTTPS を自動的に提供できます。

---

次のセクションに進み、この情報とツールを使用して、すべてを組み合わせます。

## Traefik と HTTPS を使用した Docker Swarm モードのクラスタ

HTTPS を処理する（証明書の取得と更新を含む）Traefik を使用して、Docker Swarm モードのクラスタを数分（20 分程度）でセットアップできます。

Docker Swarm モードを使用することで、1 台のマシンの「クラスタ」から開始でき（1 か月あたり 5 ドルのサーバーでもできます）、後から必要なだけサーバーを拡張できます。

Traefik および HTTPS 処理を備えた Docker Swarm Mode クラスターをセットアップするには、次のガイドに従います:

### <a href="https://medium.com/@khulnasoft/docker-swarm-mode-and-traefik-for-a-https-cluster-20328dba6232" class="external-link" target="_blank">Docker Swarm Mode and Traefik for an HTTPS cluster</a>

### ReadyAPI アプリケーションのデプロイ

すべてを設定するための最も簡単な方法は、[**ReadyAPI** Project Generators](../project-generation.md){.internal-link target=\_blank}を使用することでしょう。

上述した Traefik と HTTPS を備えた Docker Swarm クラスタが統合されるように設計されています。

2 分程度でプロジェクトが生成されます。

生成されたプロジェクトはデプロイの指示がありますが、それを実行するとさらに 2 分かかります。
