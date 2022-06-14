# Complete Opam package combining Tactician, Graph2Tac and a trained model

This repo contains an Opam package that contains all the metadata needed to run Tactician with a Graph2Tac model.
Additionally, it records which version of Graph2Tac is needed, and which parameters are used to invoke the
`g2t-server`.

This repo can be used both to run the system as a human and to run benchmarks.

## Installation and usage for human consumption

### Installation
1. Clone this repo and check out the appropriate branch
2. Have an Opam switch available in which you want to install the system. If you want to create an empty
   switch in the current directory, you can run `opam switch create --empty .`. Make sure that you run
   `eval $(opam switch)` at the appropriate time to activate the switch. Add the needed Opam repositories
   to the switch:
   ```
   opam repo add coq-released https://coq.inria.fr/opam/released     # packages for officially released versions of Coq library
   opam repo add coq-core-dev https://coq.inria.fr/opam/core-dev     # packages for development versions of Coq
   opam repo add coq-extra-dev https://coq.inria.fr/opam/extra-dev   # packages for development versions of Coq libraries and Coq extensions
   opam repo add custom-archive https://github.com/LasseBlaauwbroek/custom-archive.git # for Lasse's bugfixes of Coq
   ```
3. Install and activate the `graph2tac` prerequisites (this will create a Python virtualenv).
   From the repo directory, run
   ```
   ./prerequisites
   . ./venv/bin/activate
   ```
4. Install the opam package. From the repo directory, run
   ```
   opam install coq-tactician-stdlib ./coq-graph2tac.opam
   tactician enable # Makes Tactician available immediately when you start Coq
   tactician inject # Injects Tactician into Opam if you want to install additional packages
   ```
   Detailed instructions about installing (and using) Tactician is available on the
   [website](https://coq-tactician.github.io/manual/).

### Usage
You can use Coq as normal through `coqtop`, `coqc` and `coqide`. However, in order to make sure that Tactician
works correctly, you have to start these executables through the `tactician exec` wrapper, which add the
appropriate flags to them. Hence, instead of running `coqtop`, `coqc` or `coqide` you run any of
```
tactician exec coqtop
tactician exec coqc MyFile.v
tactician exec coqide
```
Additionally, when you want to compile an entire Coq project, you also use `tactician exec`. For example,
if your project is normally build through `make` of `dune build`, you now do this through
```
tactician exec make
tactician exec dune build
```
For detailed usage instructions on Tactician see the [website](https://coq-tactician.github.io/manual/).

## Running a benchmark
Install the benchmarking system according to the README here: https://github.com/coq-tactician/benchmark-system
After installation, a basic benchmark of a commit within this repo can be run as follows:
```
tactician-benchmark \
    -benchmark-data ./benchmark-data/ \
    -compile-allocator ./benchmark-system/local/compile_allocator \
    -bench-allocator ./benchmark-system/local/bench_allocator \
    -max-requests 16 \
    -max-running 16 \
    -delay-benchmark \
    -benchmark-target coq-graph2tac \
    -benchmark-repo git+ssh://git@github.com/coq-tactician/coq-graph2tac-trained \
    -benchmark-commit <<commit-hash-of-this-repo>> \
    -benchmark-time 40 \
    coq-tactician-stdlib
```
