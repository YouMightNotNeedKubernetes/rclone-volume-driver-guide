# rclone-volume-driver-guide
How to guide for using rclone as Docker Volume driver

## Install

```sh
docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  args="-v --allow-other" \
  config=/etc/rclone \
  cache=/var/cache/rclone
```

## `rclone.conf`

```
[minio]
type = s3
provider = Minio
env_auth = false
access_key_id = minioadmin
secret_access_key = minioadmin
region = us-east-1
endpoint = http://127.0.0.1:9000
location_constraint =
server_side_encryption =
```
