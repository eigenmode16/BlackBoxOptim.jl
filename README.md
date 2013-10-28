BlackBoxOptim.jl
==============

BlackBoxOptim is a (experimental, work-in-progress) global optimization framework for Julia (http://julialang.org/). It supports both multi- and single-objective optimization problems and is focused on (meta-)heuristic/stochastic algorithms (DE, PSO, CMA-ES etc) rather than more traditional, deterministic algorithms (as available in the Optim.jl library).

Eventually we hope to provide a JuMP interface but since it is not clear if JuMP supports multiple objectives this is to be decided.

# Installation

Just install from the github by calling:

    Pkg.clone("https://github.com/robertfeldt/BlackBoxOptim.jl")

from a Julia repl.

# Usage

To show how the BlackBoxOptim package can be used, let's implement the Rosenbrock function, a classic problem in numerical optimization. We'll assume that you have already installed BlackBoxOptim as described above.

First, we'll load BlackBoxOptim and define the Rosenbrock function (in 2 dimensions):

    using BlackBoxOptim

    function rosenbrock2d(x::Vector)
      return (1.0 - x[1])^2 + 100.0 * (x[2] - x[1]^2)^2
    end

We can now call the bboptim function, specifying the function to be optimized (here: rosenbrock2d) and the range of values allowed for each of the dimensions of the input:

    bboptim(rosenbrock2d, (-5.0, 5.0); dimensions = 2)

BlackBoxOptim will default to using an adaptive differential evolution optimizer in this case and a solution where both elements can be Floats in the range -5.0:5.0.

# State of the Library

## Existing Optimizers

* Differential Evolution optimizers, 4 different:
  - DE/rand/1/bin: de_rand_1_bin()
  - DE/rand/1/bin with radius limited sampling (a type of trivial geography): de_rand_1_bin_radiuslimited()
  - Adaptive DE/rand/1/bin: de_rand_1_bin()
  - Adaptive DE/rand/1/bin with radius limited sampling: adaptive_de_rand_1_bin_radiuslimited()

## Planned Optimizers

* RandomSearch (to compare to)
* HillClimber (to compare to)
* CMA-ES
* Amalgam meta-optimizer (by Vrugt), which takes a set of (at least 2) other optimizers and switches between them dynamically during the search.

## Utilities
* Latin hypercube sampling for creating initial populations

## Planned Utilities
* Running BBOB/COCO comparisons of optimizers

## Problems

* Sphere
* Schwefel2.21
* Schwefel2.22
* Schwefel1.2
* Rosenbrock

## Planned Problems
* BBOB/COCO (Black-Box Optimization Benchmark / COmparing Continuous Optimizers) problems:
  - Separable (Unimodal):
    1. Sphere
  - Low or moderate condition (Unimodal):
    8. Rosenbrock, original
  - High condition (Unimodal):
    10. Ellipsoid with monotone x-transformation, condition 1e6
  - Multi-modal:
    15. Rastrigin with both x-transformations, condition 10

# Guide to selecting an optimizer

In our experiments the radius limited DE's perform better than the classic de_rand_1_bin DE in almost all cases. And combining it with adaptive setting of the weights makes it even better. So for now adaptive_de_rand_1_bin_radiuslimited() is our recommended "goto" optimizer. However, the difference between the top performing DE's is slight.

Once we have Amalgam implemented we believe that Amalgam(PSO, CMA-ES, DE/rand/1/bin/radiuslimited) will be a very powerful default choice. This remains to be evaluated though.