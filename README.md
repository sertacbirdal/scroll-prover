# 📜 scroll-prover 📜
[![Unit Test](https://github.com/scroll-tech/scroll-prover/actions/workflows/unit_test.yml/badge.svg)](https://github.com/scroll-tech/scroll-prover/actions/workflows/unit_test.yml)
![issues](https://img.shields.io/github/issues/scroll-tech/scroll-prover)

## Usage
### Prerequisite

Install Solidity compiler `solc` of version `0.8.19` via [svm-rs](https://github.com/alloy-rs/svm-rs):
```shell
cargo install svm-rs
svm install 0.8.19
```

Fetch git-submodule of test traces
```shell
git submodule init
git submodule update --checkout
```

Download all setup params, degree `20`, `24` and `26` are used in [config](https://github.com/scroll-tech/scroll-prover/tree/main/integration/configs).
Could only download params of degree `26`, but it may affect performance (when downsizing params).
```shell
make download-setup -e degree=20
make download-setup -e degree=24
make download-setup -e degree=26
```
Or specify other degree and target directory to download.
```shell
# As default `degree=26` and `params_dir=./integration/test_params`.
make download-setup -e degree=DEGREE params_dir=PARAMS_DIR
```

### Testing

`make test-chunk-prove` and `make test-agg-prove` are the main testing entries for multi-level circuit constraint system of scroll-prover. Developers could understand how the system works by reading the codes of these tests.

And there are other tests:
- `make test-inner-prove` could be used to test the first-level circuit.
- `make test-batch-prove` could be used to test the final two levels.
- `make test-batches-with-each-chunk-num-prove` could be used to test batch proving with different chunk numbers.

### Binaries

Could use the following command to run binaries locally.

If run into linking issues you may need to run
```shell
cp `find ./target/release/ | grep libzktrie.so` /usr/local/lib/
```
To move the zktrielib into a path where your linker could locate it.

Run zkevm prover to generate chunk proof (work directory is `./integration`)
```shell
cargo build --release --bin zkevm_prove

./target/release/zkevm_prove --help
```
Could specify arguments as
```shell
# Proof data will be saved to `./integration/proof_data`.
export OUTPUT_DIR="proof_data"

# Params file should be located in `./integration/test_params`.
cargo run --release --bin zkevm_prove -- --params=test_params --trace=tests/extra_traces/new.json
```

Run zkevm verifier to verify chunk proof (work directory is `./integration`)
```shell
cargo build --release --bin zkevm_verify

./target/release/zkevm_verify --help
```
Could specify arguments as
```shell
cargo run --release --bin zkevm_verify -- --params=test_params --proof=proof_data
```

### Scripts

- If you have read access for DB, could run command to generate full-proof for batch tests:
```
export DB_HOST=
export DB_USER=
export DB_NAME=

sh scripts/gen_full_chunk_proofs.sh BATCH_INDEX
```

### Dockers

- `docker/chunk-prover` is used to build and run GPU chunk-prover.
- `docker/mock-testnet` is used to build and run GPU mock-testnet (inner-prove or chunk-prove).

## License

Licensed under either of

- Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.
