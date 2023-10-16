# rclone-volume-driver-guide
How to guide for using rclone as Docker Volume driver

## Step-by-step guide

1. Create `config` and `cache` directories
2. Create `rclone.conf` configuration file
3. Install `rclone/docker-volume-rclone` plugin

### Create `config` and `cache` directories

By default the plugin use this following directory to store `rclone.conf` and `cache` but you must create the directory before hand.

```
sudo mkdir -p /var/lib/docker-plugins/rclone/config
sudo mkdir -p /var/lib/docker-plugins/rclone/cache
```

If you wish to change the directory please install the plugin using custom `config` and `cache` directory. 

### Create `rclone.conf` configuration file

Create `rclone.conf` configuration file in the `config` directory you just created.

Here an example using MinIO:
```conf
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

## Install `rclone/docker-volume-rclone` plugin

Default install

```sh
docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  args="-v --allow-other"

# config=/var/lib/docker-plugins/rclone/config
# cache=/var/lib/docker-plugins/rclone/cache
```

Or install with custom `config` and `cache` directory.

```sh
docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  args="-v --allow-other" \
  config=/path/rclone/config \
  cache=/path/rclone/cache
```

