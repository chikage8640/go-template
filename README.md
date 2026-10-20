# go-template

Ubuntu 26.04 / Docker Compose ベースの Go 開発テンプレートです。

## 起動方法

1. ホストに Docker Engine または Docker Desktop、Docker Compose v2 以降、VS Code、Dev Containers 拡張 (`ms-vscode-remote.remote-containers`) をインストールします。
2. このリポジトリを clone して VS Code で開きます。
3. コマンドパレットから **Dev Containers: Reopen in Container** を実行します。
4. 初回作成時に cspell CLI の導入、Lefthook の登録を行います。イメージとツールの取得にはネットワーク接続が必要です。

Dev Containers が Compose のサービスに Features を追加してビルドします。
`docker compose up` だけでは Features のツールは導入されないため、VS Code の Dev Containers 経由で起動してください。

## 開発

Go は 1.27 系の最新パッチを導入します。開発を始める際に、自分のモジュールパスで初期化してください。

```sh
go mod init example.com/your-project
```

Go Feature で言語サーバー、デバッガーなどの開発ツールを導入します。
VS Code には Go、Code Spell Checker、Docker、YAML の拡張を導入します。
cspell の辞書は `cspell.json` で管理します。

コミット時は Lefthook でステージ済み差分の Gitleaks チェックと対象ファイルのスペルチェックを実行します。
Git 操作とフックの実行はコンテナ内で行ってください。

## コンテナ設定と永続化

ベースイメージは `mcr.microsoft.com/devcontainers/base:ubuntu26.04` です。
以下の Features を使用します。

- `ghcr.io/devcontainers/features/go:1`
- `ghcr.io/devcontainers/features/node:1`（cspell CLI 用）
- `ghcr.io/thamaji/devcontainer-features/codex-cli:latest`
- `ghcr.io/thamaji/devcontainer-features/gitleaks:latest`
- `ghcr.io/thamaji/devcontainer-features/lefthook:latest`

Codex CLI に必要な `SYS_PTRACE` と `seccomp:unconfined` は `.devcontainer/compose.yaml` に設定しています。
認証はコンテナ内で `codex login` を実行してください。

| ホスト側 | コンテナ内 | 用途 |
| --- | --- | --- |
| `.devcontainer/bash` | `/home/vscode/.cache/bash` | Bash の履歴 |
| `.devcontainer/codex` | `/home/vscode/.codex` | Codex の設定・認証・履歴 |
| `.devcontainer/go` | `/home/vscode/go` | GOPATH、モジュールキャッシュ、追加の Go ツール |

各ディレクトリ内の `.gitignore` で、その `.gitignore` 自身以外の内容を除外します。
コンテナを再作成してもこれらのデータは残ります。初期化する場合は対象ディレクトリの `.gitignore` 以外の内容を削除してください。
初回作成時にマウントしたディレクトリの所有者をコンテナのユーザーに合わせます。

指定された Features は `latest` を使用するため、リビルド時にツールのバージョンが変わる場合があります。
