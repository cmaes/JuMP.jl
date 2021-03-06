.. _jump-installation:

------------------
Installation Guide
------------------

This guide will briefly guide you through installing Julia, JuMP and[a] solver[s] of your choice.

Getting Julia
^^^^^^^^^^^^^

At the time of writing this documentation the latest release of Julia is version ``0.2``, which is the version required by JuMP. You can easily build from source on OS X and Linux, but the binaries will work well for most people.

Download links and more detailed instructions are available on the `Julia website <http://julialang.org>`_.

Getting JuMP
^^^^^^^^^^^^

Once you've installed Julia, installing JuMP is simple. Julia has a git-based package system. To use it, open Julia in interactive mode (i.e. ``julia`` at the command line) and use the package manager::

    julia> Pkg.add("JuMP")

This command checks `METADATA.jl <https://github.com/JuliaLang/METADATA.jl>`_ to determine what the most recent version of JuMP is and then downloads it from its repository on GitHub.

To start using JuMP (after installing a solver), it should be imported into the local scope::

    julia> using JuMP

Getting Solvers
^^^^^^^^^^^^^^^

Solver support in Julia is currently provided by writing a solver-specific package that provides a very thin wrapper around the solver's C interface and providing a standard interface that JuMP can call. If you are interested in providing an interface to your solver, please get in touch. The table below lists the currently supported solvers and their capabilities. 



.. _jump-solvertable:

+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| Solver                                                                           | Julia Package                                                                   | ``solver=``                 | License     | LP | SOCP | MIP | NLP |
+==================================================================================+=================================================================================+=============================+=============+====+======+=====+=====+
| `Cbc <https://projects.coin-or.org/Cbc>`_                                        | `Cbc.jl <https://github.com/JuliaOpt/Cbc.jl>`_                                  | ``CbcSolver()``             |     EPL     |    |      |  X  |     |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `Clp <https://projects.coin-or.org/Clp>`_                                        | `Clp.jl <https://github.com/JuliaOpt/Clp.jl>`_                                  | ``ClpSolver()``             |      EPL    | X  |      |     |     |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `CPLEX <http://www-01.ibm.com/software/commerce/optimization/cplex-optimizer/>`_ | `CPLEX.jl <https://github.com/JuliaOpt/CPLEX.jl>`_                              | ``CplexSolver()``           |  Comm.      | X  |  X   | X   |     |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `ECOS <https://github.com/ifa-ethz/ecos>`_                                       | `ECOS.jl <https://github.com/JuliaOpt/ECOS.jl>`_                                |  ``ECOSSolver()``           |  GPL        | X  |      |     |     |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `GLPK <http://www.gnu.org/software/glpk/>`_                                      | `GLPKMath... <https://github.com/JuliaOpt/GLPKMathProgInterface.jl>`_           |  ``GLPKSolver[LP|MIP]()``   |  GPL        | X  |      | X   |     |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `Gurobi <http://gurobi.com>`_                                                    | `Gurobi.jl <https://github.com/JuliaOpt/Gurobi.jl>`_                            | ``GurobiSolver()``          | Comm.       | X  |   X  |  X  |     |   
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `Ipopt <https://projects.coin-or.org/Ipopt>`_                                    | `Ipopt.jl <https://github.com/JuliaOpt/Ipopt.jl>`_                              | ``IpoptSolver()``           | EPL         | X  |      |     |  X  |
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `MOSEK <http://www.mosek.com/>`_                                                 | `Mosek.jl <https://github.com/JuliaOpt/Mosek.jl>`_                              | ``MosekSolver()``           | Comm.       | X  |   X  |  X  |  X  |                     
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+
| `NLopt <http://ab-initio.mit.edu/wiki/index.php/NLopt>`_                         | `NLopt.jl <https://github.com/JuliaOpt/NLopt.jl>`_                              | ``NLoptSolver()``           | LGPL        |    |      |     |  X  |                     
+----------------------------------------------------------------------------------+---------------------------------------------------------------------------------+-----------------------------+-------------+----+------+-----+-----+

Where:

- LP = Linear programming
- SOCP = Second-order conic programming (including problems with quadratic constraints and/or objective)
- MIP = Mixed-integer programming
- NLP = Nonlinear programming

To install Gurobi, for example, and use it with a JuMP model ``m``, run::
    
    Pkg.add("Gurobi")
    using JuMP
    using Gurobi

    m = Model(solver=GurobiSolver())

Setting solver options is discussed in the :ref:`Model <ref-model>` section.

Solver-specific notes follow below.

COIN-OR Clp and Cbc
+++++++++++++++++++

Installation of the binaries will differ by platform:

* Linux - Only option is to build from source, which will happen automatically.
* OS X - Downloads binary via the `Homebrew.jl <https://github.com/staticfloat/Homebrew.jl>`_ package.
* Windows - **Only 32-bit versions of Julia are supported by the COIN solvers at this time**. The 32-bit version of Julia can be used on 64-bit Windows with no issues. Binary download. Will require `Visual Studio 2012 redistributable <http://www.microsoft.com/en-us/download/details.aspx?id=30679>`_ if not already installed.

Clp and Cbc, if available, are the default choice of solver in JuMP. Cbc does *not* support MIP callbacks.


CPLEX
+++++

Requires a working installation of CPLEX with a license (free for faculty members and graduate teaching assistants). The `CPLEX.jl <https://github.com/joehuchette/CPLEX.jl>`_ interface is experimental; it requires using CPLEX as a shared library, which is unsupported by the CPLEX developers. Special installation steps are required on OS X. CPLEX supports MIP callbacks.


ECOS
++++

ECOS can be used by JuMP to solve LPs - it cannot be used to solve SOCPs at this time.


GLPK
++++

GLPK binaries are provided on OS X and Windows (32- and 64-bit) by default. On Linux, it will be compiled from source. Note that ``GLPKSolverLP`` should be used for continuous problems and ``GLPKSolverMIP`` for problems with integer variables. GLPK supports MIP callbacks.

Gurobi
++++++

Requires a working installation of Gurobi with an activated license (free for academic use). Gurobi supports MIP callbacks.

.. warning::
   If you are using 64-bit Gurobi, you must use 64-bit Julia (and similarly with 32-bit Gurobi).

Ipopt
+++++

Ipopt binaries are provided on OS X and Windows (32- and 64-bit) by default. On Linux, it will be compiled from source.
The default installation of Ipopt uses the open-source MUMPS library for sparse linear algebra.
Significant speedups can be obtained by manually compiling Ipopt to use proprietary sparse linear algebra libraries instead.
Julia can be pointed to use a custom version of Ipopt; we suggest posting to the `julia-opt <https://groups.google.com/forum/#!forum/julia-opt>`_ mailing list with your platform details for guidance on how to do this.
  
MOSEK
+++++

Requires a license (free for academic use). Mosek does not support the MIP callbacks used in JuMP.
For nonlinear optimization, Mosek supports only convex problems.
The Mosek interface was contributed by the Mosek team. (Thanks!)

NLopt
+++++

NLopt supports only nonlinear models. An algorithm must be specified as an option when using ``NLoptSolver``. NLopt is not recommended for large-scale models, because it does not currently exploit sparsity of derivative matrices.
