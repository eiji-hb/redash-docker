# このリポジトリはRedashサーバー(https://redash.bizasst.jp)の環境と同じ構成をローカルで立ち上げるリポジトリです。
- 主にRedashサーバー内のイメージを更新する際にローカルで検証するためになどに使用します。


### cloneしたらやること
dbを作成します。
```
docker compose run --rm server create_db
```

### Redashを立ち上げる
- http://localhost:5000
```
docker compose up
```

### イメージを更新した場合
```
docker compose run --rm server manage db upgrade
```



### アップグレードした際(v8->v10.0)の資料
https://www.notion.so/Redash-v8-v10-0-1299e5c51843808bb305c9188f110350#1299e5c5184380568236ce8a08c7ed1d

### バックアップについて
- postgresコンテナに入ればわかるのですがpostgresテーブルにクエリやデータソースなどが入ってます。
- 大まかな流れはバックアップを取得後に取得したテーブルをdropしてcreatesしてバックアップデータをインポートします。
- テーブルをdropする背景としてバックアップデータをインポート際にカラムが被る等エラーが出たためです。

- 下記手順でバックアップ取得
```
docker exec -t <postgres_container_id> pg_dump -U postgres -d postgres > redash_backup.sql
```

- 一度postgresテーブルを削除しバックアップデータを入れます。
```
docker-compose stop server scheduler scheduled_worker adhoc_worker

docker exec -it <postgres_container_id> psql -U postgres template1
DROP DATABASE postgres;
CREATE DATABASE postgres;
\q

docker cp redash_backup.sql <postgres_container_id>:/tmp/redash_backup.sql
docker exec -i <postgres_container_id> psql -U postgres -d postgres -f /tmp/redash_backup.sql
docker-compose up -d
```
docker exec -i 7e4ac8908a77 pg_dump -U postgres -d postgres | gzip > ./redash_backup.sql.gz


docker exec -it 7e4ac8908a77 psql -U postgres
