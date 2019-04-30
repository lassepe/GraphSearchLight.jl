# GraphSearchLight.jl
A lightweight graph search library.

## Installation

For Julia 1.0 and above simply run:
```julia
using Pkg
Pkg.add("https://github.com/lassepe/GraphSearchLight.jl")
```

## Usage

A simple example of how to use this can be found in [test/test_search.jl](https://github.com/lassepe/GraphSearchLight.jl/blob/master/test/test_search.jl_).
This defines a simple grid world problem with obstacles with some cells being occupied by obstacles.

### Defining a `SearchProblem`

As you can see from this example, all you need to do is impelement the `SearchProblem` interface defined in [src/problem_interface.jl](https://github.com/lassepe/GraphSearchLight.jl/blob/master/src/problem_interface.jl).
That is, you need to define a structure that inherits from `SearchProblem{S,A`}, where `S` is the state type and `A` is the action type.

```julia
"""
    SearchProblem

A minimalistic, simple interface for search problems
`S` - the state type
`A` - the action type
"""
abstract type SearchProblem{S, A} end
```

A `SearchProblem` must then implement each of the following functions:

```julia
"""
    start_state(p::SearchProblem)

Returns the start state of the problem.
"""
function start_state end
"""
    is_goal_state(p::SearchProblem, s::S)

Returns true if a given state is a goal state
"""
function is_goal_state end
"""
    successors(p::SearchProblem, s::S)

Returns a list of tuples `(sp, a, c)`, i.e. the next state, the action that
yields this state and the cost for this transition.
"""
function successors end

```

### Solving the Search Problem

Onc eyou have specified your search problem, finding a solution to this problem is as simple as calling one of the solvers.
Currently, `generic_graph_search`, `weighted_astar` and `astar` are provided. The generic graph search method is provided that computes the solution given a `p::SearchProblem` and a `fringe_priority::Function`.
Here, the `fringe_priority` is a function that maps a `SearchNode{S, A}` to a scalar priority. The algorithm will expand nodes with a *low* priority first.
In order to use A*, the user simply needs to provide the `heuristic::Function` that maps a state `s::S` to a scalar cost to go upperbound.
A* will simply call generic graph search with the fringe priority mapping constructed from the heuristic.
