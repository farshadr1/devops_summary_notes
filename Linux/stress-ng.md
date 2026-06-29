# stress-ng (test linux resources)
stress-ng is one of the most powerful Linux stress-testing tools. It contains 300+ stressors that test CPUs, memory, disks, filesystems, networking, scheduling, kernel features, and more.

General format:
`stress-ng --<stressor> <workers> [stressor-options] --timeout 5m`

## stress-ng Common Examples

## ⭐ Most Important Options

| Option | Description | Example |
|---------|-------------|---------|
| `--cpu N` | Starts **N CPU workers** that continuously perform computations. Use `0` to stress all available CPU cores. Ideal for CPU stability testing, cooling verification, and benchmarking. | `stress-ng --cpu 0 --timeout 10m` |
| `--cpu-method METHOD` | Selects the algorithm used by CPU workers (integer, floating-point, cache, SIMD, etc.). Different methods stress different CPU units. | `stress-ng --cpu 4 --cpu-method matrixprod` |
| `--vm N` | Creates **N memory workers** that continuously allocate, read, and write memory. Useful for RAM and memory controller stress testing. | `stress-ng --vm 2 --vm-bytes 8G` |
| `--vm-bytes SIZE` | Specifies how much memory **each VM worker** allocates. Accepts values like `2G`, `512M`, or `80%`. | `stress-ng --vm 2 --vm-bytes 80%` |
| `--vm-method METHOD` | Changes how memory is accessed (cache, random, mmap, etc.) to test different memory behaviors. | `stress-ng --vm 2 --vm-method random` |
| `--hdd N` | Creates disk N processesworkers that repeatedly create, write, sync, read, and delete temporary files. Tests storage and filesystem performance. | `stress-ng --hdd 2 --hdd-bytes 20G` |
| `--io N` | Generates heavy I/O-related system calls (`sync()`, `fsync()`, etc.) without large file creation. Stresses the kernel I/O subsystem. | `stress-ng --io 4` |
| `--timeout TIME` | Automatically stops the test after the specified duration (`30s`, `10m`, `1h`, etc.). | `stress-ng --cpu 0 --timeout 30m` |
| `--metrics-brief` | Displays a concise performance summary, including bogo operations and runtime. Great for comparing systems. | `stress-ng --cpu 0 --metrics-brief` |
| `--metrics` | Displays detailed benchmark statistics for every stressor. | `stress-ng --cpu 0 --metrics` |
| `--verify` | Verifies calculation results during testing to detect CPU, RAM, or kernel instability. | `stress-ng --cpu 0 --verify` |
| `--taskset LIST` | Pins workers to specific CPU cores. Useful for NUMA systems and CPU affinity testing. | `stress-ng --cpu 4 --taskset 0-3` |
| `--all 0` | Runs every available stressor simultaneously using all CPU cores. Produces maximum system load. | `stress-ng --all 0 --timeout 10m` |
| `--exclude LIST` | Excludes selected stressors when using `--all`. | `stress-ng --all 0 --exclude cpu,vm` |
| `--verbose` | Prints detailed runtime information for debugging and analysis. | `stress-ng --cpu 4 --verbose` |
