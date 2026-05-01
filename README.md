# CoCache: Accelerating Reads in KV Stores via Cooperative Metadata and Data Cache Management

**CoCache** is a high-performance key-value store optimized for read-intensive workloads built upon **RocksDB**.

### Prerequisites

- **OS**: Linux (Ubuntu 18.04+ recommended)
- **Compiler**: GCC 9+ or Clang 10+
- **Build Tools**: CMake 3.16+, Make
- **Dependencies**: `librocksdb-dev`, `libsnappy-dev`, `libz-dev`, `libbz2-dev`, `liblz4-dev`

```bash
# Install dependencies (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install -y cmake g++ libsnappy-dev zlib1g-dev libbz2-dev liblz4-dev
```

### Build Instructions

1. **Clone the YCSB-C benchmark repository**:
  
  ```bash
  git clone https://github.com/ZacharyLiu-CS/YCSB-C.git
  cd YCSB-C
  ```
  
2. **Replace the RocksDB implementation**:
  
  ```bash
  # Copy CoCache source code to replace the original RocksDB implementation
  cp -r /path/to/CoCache/* db_impl/rocksdb/
  ```
  
3. **Build CoCache (RocksDB module)**:
  
  ```bash
  cd db_impl/rocksdb
  mkdir build && cd build
  cmake .. -DCMAKE_BUILD_TYPE=Release
  make -j 32
  ```
  
4. **Build YCSB-C benchmark**:
  
  ```bash
  cd ../../  # Back to YCSB-C root
  mkdir build && cd build
  cmake .. -DCMAKE_BUILD_TYPE=Release
  make -j 32
  ```
  

### Running Experiments

After successful compilation, run a basic experiment:

```bash
cd build
./ycsbc -db rocksdb -dbpath . -P ../workloads/workloada.spec -threads 4
```

### Configuration

#### Workload Parameters

Workload configurations are stored in `workloads/`. Modify the following parameters in the `.spec` files:

| Parameter | Description | Example |
| --- | --- | --- |
| `recordcount` | Number of KV pairs | `1000000` |
| `operationcount` | Number of transactions | `1000000` |
| `readproportion` / `writeproportion` | Read/Write ratio | `1.0` / `0.0` (YCSB-C) |
| `requestdistribution` | Access distribution | `zipfian` or `uniform` |
| `fieldlength` | Value size (bytes) | `128`, `256`, `512`, `1024` |

#### Database Parameters

Database configurations are stored in `db_config.yaml`. Key parameters include:

| Parameter | Description | Default |
| --- | --- | --- |
| `memtable_size` | MemTable size (bytes) | `64MB` |
| `sst_size` | SSTable size (bytes) | `64MB` |
| `block_cache_size` | Total block cache size (bytes) | `4GB` |
| `bloom_bits` | Bloom bits per key | `4` |
