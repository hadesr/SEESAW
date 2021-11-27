<p align="center">
  <h1 align="center"> ChampSim </h1>
  <p> ChampSim is a trace-based simulator for a microarchitecture study. You can sign up to the public mailing list by sending an empty mail to champsim+subscribe@googlegroups.com. Traces for the 3rd Data Prefetching Championship (DPC-3) can be found from here (https://dpc3.compas.cs.stonybrook.edu/?SW_IS). A set of traces used for the 2nd Cache Replacement Championship (CRC-2) can be found from this link. (http://bit.ly/2t2nkUj) <p>
</p>

# Algorithm 

Traditional VIPT uses 4kB pages which has only 12 bit offset, in a 64 byte line cache only 6 bits are left for set indexing. Thus, restricting cache to contain almost 64 sets. Inicreasing cache size increases associativity which increases latency and energy requirement. Hence, to achieve low latencies and reduce energy consumption, we are using superpages of 2MB which most processors use today.  

# Clone ChampSim repository
```
git clone https://github.com/ChampSim/ChampSim.git
```

# Compile

ChampSim takes five parameters: Branch predictor, L1D prefetcher, L2C prefetcher, LLC replacement policy, and the number of cores. 
For example, `./build_champsim.sh bimodal no no lru 1` builds a single-core processor with bimodal branch predictor, no L1/L2 data prefetchers, and the baseline LRU replacement policy for the LLC.
```
$ ./build_champsim.sh bimodal no no no no no no no lru lru lru lru lru lru lru lru 1

$ ./build_champsim.sh ${BRANCH} ${L1I_PREFETCHER} ${L1D_PREFETCHER} ${L2C_PREFETCHER} ${LLC_PREFETCHER} ${ITLB_PREFETCHER} ${DTLB_PREFETCHER} ${STLB_PREFETCHER} ${BTB_REPLACEMENT} ${L1I_REPLACEMENT} ${L1D_REPLACEMENT} ${L2C_REPLACEMENT} ${LLC_REPLACEMENT} ${ITLB_REPLACEMENT} ${DTLB_REPLACEMENT} ${STLB_REPLACEMENT} ${NUM_CORE}
```

# Download DPC-3 trace

Professor Daniel Jimenez at Texas A&M University kindly provided traces for DPC-3. Use the following script to download these traces (~20GB size and max simpoint only).
```
$ cd scripts

$ ./download_dpc3_traces.sh
```

# Run simulation

Execute `run_champsim.sh` with proper input arguments. The default `TRACE_DIR` in `run_champsim.sh` is set to `$PWD/dpc3_traces`. <br>

* Single-core simulation: Run simulation with `run_champsim.sh` script.

```
Usage: ./run_champsim.sh [BINARY] [N_WARM] [N_SIM] [TRACE] [OPTION]
$ ./run_champsim.sh bimodal-no-no-no-no-no-no-no-lru-lru-lru-lru-lru-lru-lru-lru-1core 10 10 BFS_61B.trace.gz

${BINARY}: ChampSim binary compiled by "build_champsim.sh" (bimodal-no-no-lru-1core)
${N_WARM}: number of instructions for warmup (10 million)
${N_SIM}:  number of instructinos for detailed simulation (10 million)
${TRACE}: trace name (BFS_61B.trace.gz)
${OPTION}: extra option for "-low_bandwidth" (src/main.cc)
```
Simulation results will be stored under "results_${N_SIM}M" as a form of "${TRACE}-${BINARY}-${OPTION}.txt".<br> 

* Multi-core simulation: Run simulation with `run_4core.sh` script. <br>
```
Usage: ./run_4core.sh [BINARY] [N_WARM] [N_SIM] [N_MIX] [TRACE0] [TRACE1] [TRACE2] [TRACE3] [OPTION]
$ ./run_4core.sh bimodal-no-no-no-lru-4core 1 10 0 400.perlbench-41B.champsimtrace.xz \\
  401.bzip2-38B.champsimtrace.xz 403.gcc-17B.champsimtrace.xz 410.bwaves-945B.champsimtrace.xz
```
Note that we need to specify multiple trace files for `run_4core.sh`. `N_MIX` is used to represent a unique ID for mixed multi-programmed workloads. 


# Evaluate Simulation
