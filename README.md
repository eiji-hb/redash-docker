### first create db
```
docker compose run --rm server create_db
```

### run Redash
```
docker compose up
```

### upgrade
```
docker compose run --rm server manage db upgrade
```

### access
http://localhost:5000
