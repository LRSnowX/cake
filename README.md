`llama3-cake` is a pure Rust implementation of the llama3 LLM distributed inference based on [Candle](https://github.com/huggingface/candle).

**This is experimental code**.

The idea is to shard the transformer blocks to multiple devices in order to be able to run the inference on models that wouldn't normally fit in the GPU memory of a single device. Inferences over contiguous transformer blocks on the same worker are batched in order to minimize latency due to data transfer.

Run a worker node:

```bash
cake-cli --model /path/to/Meta-Llama-3-8B --mode worker --name worker0 --topology topology.yml --address 0.0.0.0:10128
```

Run a master node:

```bash
cake-cli --model /path/to/Meta-Llama-3-8B --topology topology.yml
```

Where `topology.yaml` determines which layers are served by whom:

```yaml
worker0:
  host: 'linux-server.local:10128'
  description: 'NVIDIA Titan X Pascal (12GB)'
  layers:
    - 'model.layers.0-15'

worker1:
  host: 'apple-server.local:10128'
  description: 'Apple M1 Max (64GB)'
  layers:
    - 'model.layers.16-31'
```

## License

Released under the GPL 3 license. To see the licenses of the project dependencies, install cargo license with `cargo install cargo-license` and then run `cargo license`.