##  Example

In this example we will setup `rclone` volume driver with **MinIO**.

### Run

The example below works this way:

- `mkdir` creates a new local directory at `~/minio/data` in your home directory.
- `docker run` starts the MinIO container.
- `-p` binds a local port to a container port.
- `-name` creates a name for the container.
- `-v` sets a file path as a persistent volume location for the container to use.  
  When MinIO writes data to `/data`, that data mirrors to the local path `~/minio/data`, allowing it to persist between container restarts.  
  You can replace `~/minio/data` with another local file location to which the user has read, write, and delete access.  
- `-e` sets the environment variables `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD`, respectively.  
  These set the root user credentials. Change the example values to use for your container.

```sh
mkdir -p ~/minio/data

docker run \
   -p 9000:9000 \
   -p 9090:9090 \
   --name minio \
   -v ~/minio/data:/data \
   -e "MINIO_ROOT_USER=minioadmin" \
   -e "MINIO_ROOT_PASSWORD=minioadmin" \
   quay.io/minio/minio server /data --console-address ":9090"
```

> See https://min.io/docs/minio/container/index.html for more information.

### Install the `rclone` volume driver

```sh
# Create config and cache dir
sudo mkdir -p /var/lib/docker-plugins/rclone/config
sudo mkdir -p /var/lib/docker-plugins/rclone/cache

# Create rclone.conf in the config dir
cat <<EOF >/var/lib/docker-plugins/rclone/config/rclone.conf
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
EOF

# Install plugin
docker plugin install rclone/docker-volume-rclone:amd64 \
  --alias rclone \
  --grant-all-permissions \
  RCLONE_VERBOSE=2 \
  args="-v --allow-other"
```

### Using the volume driver

```yaml
services:
  nginx:
    image: nginx:stable-alpine
    ports:
      - 80:80
      - 443:443
    volumes:
      - certs:/certs

volumes:
  certs:
    driver: rclone
    driver_opts:
      remote: "minio:certs"
```
