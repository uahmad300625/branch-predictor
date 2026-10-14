# Pentium M Hybrid Branch Predictor

A simulated Pentium M–style branch direction predictor, built on the CBP-2 branch prediction infrastructure. It combines a bimodal predictor with a tagged, 2-way set-associative global predictor.

**Result:** 5.713 average MPKI (mispredictions per 1,000 instructions) over the 20 provided traces, compared with 6.306 for the gshare example, 9.758 for bimodal only, and 86.312 for the original always-taken stub.

The full write-up is in `report.pdf`.

## What's implemented

All changes are in the `pm_predictor` and `pm_update` classes in `src/my_predictor.h`.

- **Bimodal predictor:** 32,768 two-bit counters indexed by the low 15 bits of the branch address.
- **Global predictor:** 2,048 rows × 2 ways. Each row is indexed by the branch address XOR an 11-bit global history register. Each entry holds a valid bit, an 8-bit tag, and a 2-bit counter.
- **Selection:** the global prediction is used on a tag hit; otherwise the bimodal prediction is used.
- **Allocation:** a branch gets a global entry only when it misses in the global table and bimodal mispredicted it.
- **Replacement:** an empty way is filled first; otherwise the least recently used way is replaced.

Not implemented, as allowed by the assignment: the loop predictor, branch target prediction, and the PIR hash. The extra-credit `cpm_predictor` is left as provided.

## Requirements

Linux (or WSL on Windows) with `g++`, `make`, `csh`, `dc`, and `bzip2`. On Ubuntu:

```
sudo apt update && sudo apt install -y build-essential csh dc bzip2
```

## Build and run

```
cd src
make
cd ..
chmod +x run
./run traces
```

`./run traces` runs the predictor on every trace in `traces/` and prints the MPKI for each, followed by the average. To run a single trace:

```
./src/predict traces/164.gzip/gzip.trace.bz2
```

## Files

| Path | Contents |
| --- | --- |
| `src/my_predictor.h` | The predictor implementation (the file changed for this assignment) |
| `src/predict.cc` | Simulator driver, unchanged |
| `traces/` | 20 SPEC CPU2000 branch traces |
| `run` | Script that runs all traces and averages the results |
| `report.pdf` | Assignment report |
| `README-cbp2-infrastructure` | Original README from the CBP-2 infrastructure |

## Credits

- CBP-2 branch prediction infrastructure, including the gshare example the bimodal table is based on.
- V. Uzelac and A. Milenković, "Experiment Flows and Microbenchmarks for Reverse Engineering of Branch Predictor Structures," ISPASS 2009.
- Implementation assistance from Claude (Anthropic), used as permitted by the course AI policy.
