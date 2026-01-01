# Hadoop mini cluster (docker compose)

## Prerequisites
- Docker Desktop (Compose v2 available via `docker compose`)
- ~4GB RAM free for 1 NN + 2 DN

## First run on a new machine (fresh volumes)
From the repository root:

1) (Optional clean start) remove old containers/volumes if any
```
docker compose down -v
```

2) Format the NameNode volume once
```
docker compose run --rm --user root namenode bash -c "mkdir -p /hadoop/dfs/name && hdfs namenode -format -nonInteractive"
```

3) Start the cluster
```
docker compose up -d
```

4) Verify
- NameNode UI: http://localhost:9870
- Health: `docker compose exec namenode hdfs dfsadmin -report`

## Subsequent runs
- Just start/stop with `docker compose up -d` and `docker compose down`.
- Do **not** re-format unless you want to wipe HDFS metadata (it deletes all data).

## Troubleshooting
- If UI is unreachable, check `docker compose ps` and `docker compose logs namenode`.
- Permission errors on data dirs: re-run step 2; if needed, prepare DN dirs (rare with `user: root`):
```
docker compose run --rm --user root datanode1 bash -c "mkdir -p /hadoop/dfs/data && chown -R hadoop:hadoop /hadoop/dfs/data"
docker compose run --rm --user root datanode2 bash -c "mkdir -p /hadoop/dfs/data && chown -R hadoop:hadoop /hadoop/dfs/data"
```
