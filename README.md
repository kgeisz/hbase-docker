## HBase Docker Setup

Based on **@vinayakphegde**'s original Docker images for HBase and extended to run two clusters side-by-side mainly for **HBase Read Replica Cluster** feature testing.
There are two `conf` folders for each individual cluster. They share a common root directory called `/data-store/hbase`, which is mounted from the local filesystem. They
have separated WAL directories and separate ZooKeeper databases. `hbase` cluster is the Active Cluster, while `hbase2` is the Read Replica Cluster and is set up with global
read-only mode enabled. 

### Build Docker Images

1. Ensure the HBase source code directory is located within the current directory (alongside the Dockerfile and other required files). The directory should contain the HBase source code needed to build the image. The structure should look like this:
   ```bash
   .
   ├── conf1/
   ├── conf2/
   ├── Dockerfile
   ├── docker-compose.yml
   ├── build-images.sh
   ├── hbase/
   │   └── [HBase source files]
   └── ...
   ```
2. Create `.env` with the correct details.
   ```bash
   # HBASE_IMAGE=<your-docker-registry>/<docker-image-name>
   HBASE_IMAGE=vhegde/hbase-docker
   HBASE_CONF_DIR=/opt/hbase/conf
   ```
3. Modify the paths in your `docker-compose.yml` file to be compatible with your filesystem. For example, change:
   ```
   volumes:
     - /Users/andor/tmp/data-store/hbase:/data-store/hbase
   ```
   To:
   ```
   volumes:
     - /Users/<YOUR-USERNAME>/tmp/data-store/hbase:/data-store/hbase
   ```
4. Make the build script executable:
   ```bash
   chmod +x build-images.sh
   ```
5. Build the images:
   ```bash
   ./build-images.sh
   ```

### Run the containers

Start "hbase" cluster:

```bash
docker-compose up -d hbase
```

Start "hbase2" cluster:

```bash
docker-compose up -d hbase2
```

Exec shell inside container:

```bash
docker exec -it <container-id> /bin/bash
```

Shutdown containers:

```bash
docker-compose down
```

If you start the clusters again and see issues with data or the clusters not working as expected, then
try shutting the clusters down and deleting the `data-store` directory.

```bash
rm -rf /Users/<YOUR-USERNAME>/tmp/data-store
```
