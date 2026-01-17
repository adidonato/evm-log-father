# evm-log-father

Fast EVM log decoding library with Python bindings.

## Features

- Decode Ethereum event logs using alloy's dynamic ABI
- Read logs from parquet files
- Parallel decoding with rayon
- Python bindings via PyO3
- CLI for quick testing

## Installation

### Python (from PyPI)

```bash
pip install evm-log-father
```

### Python (from source)

```bash
pip install maturin
maturin develop --features python
```

### Rust

```toml
[dependencies]
evm-log-father = "0.1"
```

## Usage

### Python

```python
from evm_log_father import EventSchema, decode_parquet

# Create schema from event signature
schema = EventSchema("Transfer(address indexed from, address indexed to, uint256 value)")

# Decode logs from parquet file
logs = decode_parquet("transfers.parquet", schema, parallel=True)

for log in logs:
    print(f"Block {log['block_number']}: {log['params']['from']} -> {log['params']['to']}")
```

### Rust

```rust
use evm_log_father::{EventSchema, decode_parquet_parallel};

let schema = EventSchema::new("Transfer(address indexed from, address indexed to, uint256 value)")?;
let logs = decode_parquet_parallel("transfers.parquet", &schema)?;

for log in logs {
    println!("Block {}: {:?}", log.block_number, log.params);
}
```

### CLI

```bash
# Decode logs and output JSON
evm-log-father decode \
  --parquet transfers.parquet \
  --event "Transfer(address indexed from, address indexed to, uint256 value)" \
  --output decoded.json \
  --parallel \
  --timing

# Show event info
evm-log-father info --event "Transfer(address indexed from, address indexed to, uint256 value)"
```

## Parquet Schema

Expected columns:
- `block_number`: u64
- `tx_hash`: string
- `log_index`: u32
- `contract` or `address`: string
- `topic0`, `topic1`, `topic2`, `topic3`: string
- `data`: binary

## Benchmarking

```bash
python examples/benchmark.py transfers.parquet
```

## License

MIT
