# 研究開発システム

製品レビューをトピック単位に分解し，
ユーザの属性（購入検討中／商品購入済）に応じた
階層的なフィードバックとミクロ・マクロの可視化を行うオンラインレビュー可視化システム．

## 技術スタック

### フロントエンド
- runtime: Deno
- framework: React + TypeScript
- Build Tool: Vite
- Style: Tailwind CSS

### バックエンド
- framework: FastAPI
- Package Manager: uv
- ORM: SQLModel + PostgreSQ
- AI/NLP Engine: PyABSA, BERTopic

## ディレクトリ構造

```txt
topic-review/
├── .devcontainer/         # VS Code 開発コンテナ設定 (環境の一本化)
├── .venv/                 # uv が一元管理するPythonローカル仮想環境 (Git除外)
├── .python-version        # プロジェクト全体で使用する Python のバージョン
├── pyproject.toml         # uv ワークスペース設定（ルート定義）
├── uv.lock                # Python依存関係の厳密なロックファイル
│
├── frontend/              # フロントエンド（Deno + React + Vite）
│   ├── deno.json          # package.json & tsconfig.json を完全統合した設定ファイル
│   ├── deno.lock          # Denoの整合性を担保するロックファイル
│   ├── index.html         # エントリーHTML
│   ├── vite.config.ts     # Vite 設定ファイル
│   ├── src/               # React ソースコード
│   │   ├── main.tsx       # エントリーポイント
│   │   ├── App.tsx        # トピックベースUI（メイン画面）
│   │   └── index.css      # スタイルシート
│   └── public/            # 静的アセット
│
├── backend/               # バックエンド（FastAPI / SQLModel）
│   ├── pyproject.toml     # FastAPIアプリケーションの依存関係定義（非パッケージ型）
│   ├── .env.example       # データベース接続用の環境変数テンプレート
│   ├── .env               # データベース接続用の環境変数 (Git除外)
│   └── app/
│       └── main.py        # FastAPI エントリーポイント
│
└── ai_modules/            # AI・解析モジュール（PyABSA / BERTopic）
    ├── pyproject.toml     # 機械学習関連（PyTorch等）のパッケージ定義
    └── src/
        └── ai_modules/
            └── __init__.py
```

## 開発環境のセットアップ

本プロジェクトはVSCode Devcontainersに対応しています．
ローカルPCに特定のランタイムを入れることなく開発環境のセットアップが可能です．

1. 開発コンテナの起動

    1. ホストマシンでDockerが起動していることを確認します．
    2. VSCodeで本プロジェクトのルートディレクトリを開きます．
    3. VSCodeから"Reopen in Container（コンテナで再度開く）"を選択します．
    4. コンテナビルドと `postCreateCommand` による初期化が完了するまで待ちます．

2. バックエンド（Fast API）の起動

    `./backend` ディレクトリ直下に `.env` ファイルを作成し，
    使用するPostgreSQLの接続文字列を設定します．

    ```env
    DATABASE_URL=postgresql://<user>:<password>@<host>:<port>/<dbname>
    ```

    Shellは `fish` がデフォルトで起動します．
    ターミナルで以下を実行します．

    ```shell
    # ワークスペース全体の同期（初期化時またはパッケージ追加時）
    uv sync

    # FastAPI 開発サーバーの起動 (ポート: 8000)
    uv run uvicorn --app-dir backend app.main:app --reload
    ```

    ※ 起動後 <http://127.0.0.1:8000/docs> にアクセスすると，自動生成されたインタラクティブな API ドキュメント（Swagger UI）を確認できます．

3. フロントエンドの起動

    もう一つターミナルを開き，以下のコマンドを実行します．

    ```shell
    cd frontend

    # Deno Task による Vite 開発サーバーの起動 (ポート: 5173)
    deno task dev
    ```
