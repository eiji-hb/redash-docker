# Redash Docker

ローカル環境でRedashを立ち上げるためのDocker Compose構成です。

## セットアップ

### 1. リポジトリをクローン
```bash
git clone https://github.com/eiji-hb/redash-docker.git
cd redash-docker
```

### 2. 環境変数ファイルを作成
```bash
cp env.example env
# 必要に応じてenvファイルを編集
```

### 3. データベースを作成
```bash
docker compose run --rm server create_db
```

### 4. Redashを起動
```bash
docker compose up -d
```

http://localhost:5000 でアクセスできます。

## よく使うコマンド

### 起動・停止
```bash
# 起動
docker compose up -d

# 停止
docker compose down

# ログ確認
docker compose logs -f
```

### データベースマイグレーション
イメージを更新した場合に実行します。
```bash
docker compose run --rm server manage db upgrade
```

## バックアップ・リストア

### バックアップ取得
```bash
docker exec -t <postgres_container_id> pg_dump -U postgres -d postgres > redash_backup.sql
```

### リストア
```bash
# サービスを停止
docker compose stop server scheduler scheduled_worker adhoc_worker

# データベースを再作成
docker exec -it <postgres_container_id> psql -U postgres template1
DROP DATABASE postgres;
CREATE DATABASE postgres;
\q

# バックアップをリストア
docker cp redash_backup.sql <postgres_container_id>:/tmp/redash_backup.sql
docker exec -i <postgres_container_id> psql -U postgres -d postgres -f /tmp/redash_backup.sql

# サービスを再起動
docker compose up -d
```
