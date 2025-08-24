# my-home-reverse-proxy

## 概要

Nginx Proxy Managerは、Webアプリケーションのリバースプロキシ、ロードバランサー、SSL証明書管理を簡単に行えるWebベースの管理ツールです。このプロジェクトには、PortainerによるDocker管理機能も含まれています。

## 主な機能

- **リバースプロキシ**: 複数のWebアプリケーションを単一のドメインで管理
- **SSL証明書管理**: Let's Encryptを使用した自動SSL証明書の発行・更新
- **ロードバランサー**: 複数サーバー間でのトラフィック分散
- **Web管理画面**: 直感的なWebインターフェースでの設定管理
- **データ永続化**: 設定とSSL証明書の永続化
- **Portainer統合**: Dockerコンテナの管理と監視
- **ヘルスチェック**: コンテナの健全性監視
- **IPv6無効化**: IPv4のみでの動作保証

## 前提条件

- Docker と Docker Compose がインストールされていること
- ポート80、443、81（またはカスタムポート）が利用可能であること
- 外部ネットワーク `nginx-proxy-manager-network` が作成されていること

## セットアップ手順

### 1. リポジトリのクローン

```bash
git clone <repository-url>
cd my-home-reverse-proxy
```

### 2. 環境変数ファイルの設定

```bash
# 環境変数ファイルの例をコピー
cp env.example .env

# .envファイルを編集して必要に応じてポート番号を変更
# デフォルトでは管理画面はポート81で動作
```

### 3. 外部ネットワークの作成

```bash
# スクリプトを使用してネットワークを作成
chmod +x create-network.sh
./create-network.sh

# または手動で作成
docker network create nginx-proxy-manager-network
```

### 4. アプリケーションの起動

```bash
docker-compose up -d
```

### 5. 初期設定

1. **Nginx Proxy Manager管理画面**
   - ブラウザで `http://your-server-ip:81` にアクセス
   - デフォルトログイン情報：
     - Email: `admin@example.com`
     - Password: `changeme`
   - 初回ログイン時にパスワード変更を求められます

2. **Portainer管理画面**
   - ブラウザで `http://your-server-ip:9000` にアクセス
   - 初回アクセス時に管理者アカウントの作成を求められます

## 使用方法

### 基本的なリバースプロキシの設定

1. **管理画面にログイン**
   - `http://your-server-ip:81` にアクセス

2. **プロキシホストの追加**
   - "Proxy Hosts" → "Add Proxy Host" をクリック
   - 以下の情報を入力：
     - Domain Names: 使用したいドメイン名
     - Scheme: http または https
     - Forward Hostname/IP: 転送先のサーバーIP
     - Forward Port: 転送先のポート番号

3. **SSL証明書の設定**
   - "SSL" タブで証明書の設定
   - Let's Encryptを使用する場合は "Request Let's Encrypt Certificate" を選択

### ポート設定

- **ポート80**: HTTPトラフィック用
- **ポート443**: HTTPSトラフィック用  
- **ポート81**: Nginx Proxy Manager管理画面用（環境変数で変更可能）
- **ポート9000**: Portainer管理画面用

## セキュリティの考慮事項

- 管理画面（ポート81、9000）へのアクセスは制限することを推奨
- ファイアウォールで不要なポートへのアクセスをブロック
- 定期的なパスワード変更
- SSL証明書の有効期限の監視
- IPv6無効化によるセキュリティ向上
- 環境変数ファイル（.env）の適切な管理

## 環境変数

| 変数名 | デフォルト値 | 説明 |
|--------|-------------|------|
| ADMIN_PORT | 81 | Nginx Proxy Manager管理画面のポート番号 |

## ファイル構成

```
my-home-reverse-proxy/
├── docker-compose.yml          # Docker Compose設定ファイル
├── env.example                 # 環境変数設定例
├── .env                        # 環境変数設定ファイル（作成が必要）
├── create-network.sh           # ネットワーク作成スクリプト
├── nginx-proxy-manager-data/   # Nginx Proxy Managerデータ
├── nginx-proxy-manager-ssl/    # SSL証明書
├── portainer-data/             # Portainerデータ
└── README.md                   # このファイル
```