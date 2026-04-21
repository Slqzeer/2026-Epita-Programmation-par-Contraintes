## Ressources communes a tous les sujets

### Solveurs et outils
- **Google OR-Tools CP-SAT** : le solveur de reference pour ce cours. [Documentation officielle](https://developers.google.com/optimization/cp/cp_solver), [Guide Python](https://developers.google.com/optimization/cp/introduction), [Exemples par probleme](https://github.com/google/or-tools/tree/stable/examples/python)
- **Z3 SMT Solver** : pour les problemes de verification et de raisonnement symbolique. [Documentation](https://z3prover.github.io/api/html/namespacez3py.html), [Tutoriel Python](https://ericpony.github.io/z3py-tutorial/guide-examples.htm)
- **MiniZinc** : langage de modelisation CP de haut niveau. [Tutoriel](https://www.minizinc.org/doc-2.5.5/en/), [Benchmarks](https://www.minizinc.org/challenge.html)
- **CPMpy** : interface Python pour CP avec backends multiples. [Documentation](https://cpmpy.readthedocs.io/), [Exemples](https://github.com/CPMpy/cpmpy/tree/master/examples)

### Benchmarks et instances
- **CSPLib** : bibliotheque de problemes CP de reference. [En ligne](https://www.csplib.org/)
- **OR-Library** : instances pour problemes d'OR. [Beasley OR-Library](http://people.brunel.ac.uk/~mastjjb/jeb/info.html)
- **MiniZinc Challenge Benchmarks** : instances de competition. [GitHub](https://github.com/minizinc/minizinc-benchmarks)

### Notebooks du cours CoursIA
Les notebooks suivants sont disponibles dans le depot CoursIA ([jsboige/CoursIA](https://github.com/jsboige/CoursIA)) et constituent des prerequis ou des points de depart pour les projets :
- **Search/Part1-Foundations/** : Search-1 (StateSpace), Search-3 (A*, heuristiques), Search-4 (Local Search), Search-9 (Programmation lineaire), Search-11 (Metaheuristiques)
- **Search/Part2-CSP/** : CSP-1 (Fondamentaux), CSP-4 (Scheduling, IntervalVar, NoOverlap, Cumulative), CSP-5 (Optimization, Bin Packing, Knapsack), CSP-6 (Hybridation CP+SAT, LLM+CSP), CSP-7 (Soft Constraints), CSP-9 (Distributed CSP)
- **Search/Applications/CSP/** : App-4 (Job-Shop Scheduling), App-8 (MiniZinc), App-11 (Picross)
- **Search/Applications/Hybrid/** : App-10 (Portfolio Optimization), App-13 (TSP Metaheuristics), App-17 (VRP avec SA, GA, ACO, CP-SAT)
- **SymbolicAI/SmartContracts/** : Serie de 27 notebooks (SC-0 a SC-26) couvrant blockchain, Solidity, verification formelle (SC-14), fuzz testing (SC-13), cryptographie ZKP/HE (SC-15/16)
- **SymbolicAI/Planners/** : Planners-1 a Planners-12 couvrant PDDL, Fast Downward, planification temporelle, HTN, LLM Planning
- **SymbolicAI/Linq2Z3.ipynb** : Z3 SMT Solver en C#
- **SymbolicAI/OR-tools-Stiegler.ipynb** : OR-Tools CP en C#
- **Sudoku/** : 18 notebooks couvrant Sudoku avec multiples solveurs (Z3, CP-SAT, backtracking)
- **GameTheory/** : 17+ notebooks couvrant Nash Equilibrium, Cooperative Games, Shapley Value, Mechanism Design
- **Integration LLM** : function calling avec OpenAI/MCP pour assister la modelisation CP. Voir [Function Calling - OpenAI](https://platform.openai.com/docs/guides/function-calling) et [MCP Specification](https://modelcontextprotocol.io/)

---

## H2 - Generation procedurale de niveaux de jeu (WFC)

La generation procedurale de niveaux par Wave Function Collapse (WFC) consiste a generer des grilles 2D ou 3D (cartes de jeu, donjons, villes) a partir d'un ensemble de tuiles avec des contraintes d'adjacence (quelle tuile peut etre placee a cote de quelle autre). WFC est essentiellement un algorithme de propagation de contraintes (AC-4) avec backtracking, ce qui en fait un sujet ideal pour explorer les liens entre CP et generation de contenu. L'extension CP-SAT permet d'ajouter des contraintes globales (connectivite, difficulte, esthetique) que WFC pur ne supporte pas.

### Objectifs
- Implementer WFC comme un probleme de satisfaction de contraintes avec CP-SAT (variables = tuiles, contraintes = adjacence)
- Ajouter des contraintes globales : connectivite du niveau, chemin du joueur, placement d'objets
- Implementer des contraintes de difficulte (densite d'ennemis, complexite du parcours) et de variete
- Evaluer sur des ensembles de tuiles existants ( ressources Unity/Godot, WaveFunctionCollapse repo)
- Comparer la qualite et la diversite des niveaux generes par WFC pur, CP-SAT, et generation aleatoire

### Notebooks CoursIA pertinents

| Notebook | Chemin | Pertinence |
|----------|--------|------------|
| CSP-1 Fondamentaux | [Search/Part2-CSP/CSP-1.ipynb](https://github.com/jsboige/CoursIA/blob/main/MyIA.AI.Notebooks/Search/Part2-CSP/CSP-1.ipynb) | Propagation de contraintes |
| Search-4 Local Search | [Search/Part1-Foundations/Search-4.ipynb](https://github.com/jsboige/CoursIA/blob/main/MyIA.AI.Notebooks/Search/Part1-Foundations/Search-4.ipynb) | Recherche locale |
| CSP-7 Soft Constraints | [Search/Part2-CSP/CSP-7.ipynb](https://github.com/jsboige/CoursIA/blob/main/MyIA.AI.Notebooks/Search/Part2-CSP/CSP-7.ipynb) | Preferences |
| CSP-5 Optimization | [Search/Part2-CSP/CSP-5.ipynb](https://github.com/jsboige/CoursIA/blob/main/MyIA.AI.Notebooks/Search/Part2-CSP/CSP-5.ipynb) | Optimisation |

### References externes
- Karth, I., & Smith, A.M. (2017). "WaveFunctionCollapse is Constraint Solving in the Wild." *PCG Workshop at FDG*. [arXiv](https://arxiv.org/abs/2105.13960)
- WaveFunctionCollapse (original implementation). [GitHub](https://github.com/mxgmn/WaveFunctionCollapse)
- Tabor, J. (2022). "Constraint-Based Procedural Generation: A Survey." *IEEE Transactions on Games*. [IEEE](https://ieeexplore.ieee.org/document/9792255)
- Smith, A.M., & Mateas, M. (2011). "Answer Set Programming for Procedural Content Generation." *IEEE T-CIAIG*. [IEEE](https://ieeexplore.ieee.org/document/5668232)