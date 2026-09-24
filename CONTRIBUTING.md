# Contributing

This repository is source-available for transparency, not open source (see `LICENSE`). It is
published so that the specification, benchmarks and methodology can be checked.

## Issues

Bug reports and questions about the methodology are welcome via GitHub Issues. Reports that a
benchmark is over-claiming, or that a result does not reproduce, are especially useful.

## Pull requests

Not accepted from outside Bravo01 Labs & Dynamics at this time. If you have found something
worth fixing, open an issue describing it and a maintainer will follow up.

## Running the checks

```bash
python -m pip install pytest
python -m pytest
python -m adapters.jev.benchmark.run_benchmark
python -m adapters.laya.benchmark.run_benchmark
```

The notebooks in `notebooks/` need `numpy` and `matplotlib` only and run on a CPU.
