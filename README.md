# EPITA 2026 - Programmation par Contraintes
## Liste des sujets de projet

Ce document presente les sujets de projet pour le cours de Programmation par Contraintes (SCIA).
Chaque sujet inclut une description detaillee, des references academiques et pratiques, des liens vers des ressources de bootstrapping (tutoriels, notebooks, benchmarks), et les technologies pertinentes.

> **Consignes de choix** : Chaque groupe doit forker ce depot et creer un dossier pour son projet contenant le code source, un notebook explicatif, et les slides de soutenance. Les livraisons se font via des pull requests regulieres.

---

## Ressources communes a tous les sujets

### Solveurs et outils
- **Google OR-Tools CP-SAT** : le solveur de reference pour ce cours. [Documentation officielle](https://developers.google.com/optimization/cp/cp_solver), [Guide Python](https://developers.google.com/optimization/cp/introduction), [Exemples par probleme](https://github.com/google/or-tools/tree/stable/examples/python)
- **Z3 SMT Solver** : pour les problemes de verification et de raisonnement symbolique. [Documentation](https://z3prover.github.io/api/html/namespacez3py.html), [Tutoriel Python](https://ericpony.github.io/z3py-tutorial/guide-examples.htm)
- **MiniZinc** : langage de modelisation CP de haut niveau. [Tutoriel](https://www.minizinc.org/doc-2.5.5/en/), [Benchmarks](https://www.minizinc.org/challenge.html)
- **CPMpy** : interface Python pour CP avec backends multiples. [Documentation](https://cpmpy.readthedocs.io/), [Exemples](https://github.com/CPMpy/cpmpy/tree/master/examples)

### Benchmarks et instances
- **CSPLib** : bibliotheque de problemes CP de reference. [En ligne](https://www.csplib.org/)
- **OR-Library** : instances pour problemes d'OR. [Beasley's OR-Library](http://people.brunel.ac.uk/~mastjjb/jeb/info.html)
- **MiniZinc Challenge Benchmarks** : instances de competition. [GitHub](https://github.com/minizinc/minizinc-benchmarks)

### Notebooks du cours
- **Notebook Agentique** : pour la creation et finalisation automatisee de notebooks de projet
- **Integration LLM** : function calling avec OpenAI/MCP pour assister la modelisation CP. Voir [Function Calling - OpenAI](https://platform.openai.com/docs/guides/function-calling) et [MCP Specification](https://modelcontextprotocol.io/)

---

## 1. Probleme d'echange de reins (Kidney Exchange)

### Description du probleme et contexte
Le kidney exchange problem consiste a trouver des cycles de donneurs-receveurs incompatibles pour maximiser le nombre de transplantations. C'est un probleme de matching dans des graphes orientes avec contraintes de poids et de longueurs de cycles. Ce sujet est un classique de la RO avec un reel impact social. Les instances reelles viennent de programmes d'echange comme l'UNOS (USA) et le NAPRT (UK).

### References
- Roth, Sonmez, Unver - *Kidney Exchange* - American Economic Review - 2007. [PDF](https://www.nber.org/system/files/working_papers/w10702/w10702.pdf)
- Abraham, Blum, Sandholm - *Clearing Algorithms for Barter Exchange Markets* - EC 2007. [PDF](https://www.cs.cmu.edu/~sandholm/clearing.ec07.pdf)
- [Wikipedia - Optimal kidney exchange problem](https://en.wikipedia.org/wiki/Optimal_kidney_exchange)
- [jamestrimble/kidney_solver (GitHub)](https://github.com/jamestrimble/kidney_solver) - Implementation Python + Gurobi

### Point de depart
```python
# Modelisation CP-SAT : cycles dans un graphe oriente pondere
from ortools.sat.python import cp_model

# Variables : pour chaque paire (i,j), x[i][j] = 1 si i donne a j
# Contrainte : chaque sommet a au plus un predecesseur et un successeur
# Contrainte : cycles de longueur <= k (typiquement k=3)
# Objectif : maximiser la somme des poids des aretes selectionnees
```

### Instances et donnees
- [PrefLib - Kidney Exchange instances](https://www.preflib.org/dataset/Kidney)
- [OPTN kidney paired donation data](https://optn.transplant.hrsa.gov/)

### Approches suggerees
- Modelisation comme graphe oriente avec cycles disjoints
- CP avec contraintes de circuit Hamiltonien limite
- Programmation lineaire en nombres entiers
- Algorithmes de clearing (approche iterative)

### Technologies pertinentes
Python, OR-Tools CP-SAT, NetworkX, PuLP

---

## 2. Equilibrage de chaine d'assemblage (Assembly Line Balancing)

### Description du probleme et contexte
L'Assembly Line Balancing Problem (SALBP) vise a repartir les taches de production entre les stations de travail tout en respectant les contraintes de precedence et en minimisant le nombre de stations. C'est un probleme classique en gestion de production avec de nombreuses variantes (SALBP-1: minimiser stations pour un temps de cycle fixe, SALBP-2: minimiser temps de cycle pour un nombre de stations fixe).

### References
- [Hexaly - SALBP Benchmark Documentation](https://www.hexaly.com/documentation/advanced_examples/salbp.html)
- Scholl and Becker - *State-of-the-art exact and heuristic solution procedures* - 2006. [PDF](https://publikationen.bibliothek.kit.edu/1000023755/894942)
- [SALBP Benchmark instances (Scholl)](https://algorithmskeinprobleme.github.io/Assembly-Line-Balancing-Problem/)
- [OR-Tools SALBP example](https://github.com/google/or-tools/blob/stable/examples/python/salbp.py) (si disponible)

### Point de depart
```python
# Modelisation CP-SAT : assignation de taches a des stations
from ortools.sat.python import cp_model

# Variables : station[t] = numero de station pour la tache t
# Contraintes : precedence => station[t1] < station[t2]
# Contraintes : temps total par station <= temps de cycle
# Objectif : minimiser le nombre de stations utilisees
```

### Instances et donnees
- [SALBP-1 Benchmark (data files)](http://algorithmskeinprobleme.github.io/Assembly-Line-Balancing-Problem/instances.html)
- [Assembly Line Balancing instances - PrefLib](https://www.preflib.org/)

### Approches suggerees
- Modelisation CP avec precedence constraints
- Minimisation du nombre de stations (SALBP-1)
- Minimisation du temps de cycle (SALBP-2)
- Variantes : U-lines, paralleles, mixed-model

### Technologies pertinentes
Python, OR-Tools CP-SAT, Hexaly

---

## 3. Conception de chaine logistique (Supply Chain Network Design)

### Description du probleme et contexte
La conception de reseau logistique implique de localiser les installations (usines, entrepots) et de determiner les flux de marchandises pour minimiser les couts tout en satisfaisant la demande. C'est un probleme de localisation-allocation avec contraintes de capacite. Applications : logistique e-commerce, distribution pharmaceutique, aide humanitaire.

### References
- Ozgur and Polat - *Supply Chain Network Design* - 2016
- Sabharwal et al. - *Formulating Fixed-Charge Network Design* - 2023
- Dotoli et al. - *Multi-Objective Optimization* - 2005
- [OR-Tools Facility Location example](https://developers.google.com/optimization/cp/cp_solver)

### Point de depart
```python
# Modelisation CP-SAT : localisation-allocation
# Variables : open[w] = 1 si l'entrepot w est ouvert
#            flow[w,c] = quantite envoyee de w au client c
# Contraintes : capacite des entrepots, couverture de la demande
# Objectif : minimiser couts fixes (ouverture) + couts variables (transport)
```

### Instances et donnees
- [Supply Chain Network Design instances](https://github.com/dscommunity-iitd/Simulated-Annealing-for-Supply-Chain-Network-Design)
- [Capacitated Facility Location Problem instances](https://miplib.zib.de/instance_details_capacitated-facility-location.html)

### Approches suggerees
- Modelisation p-median et p-center avec CP
- Contraintes de capacite et de service
- Multi-objectifs : cout vs robustesse vs emissions
- Analyse de sensibilite et scenarios

### Technologies pertinentes
Python, OR-Tools CP-SAT, PuLP, GeoPandas

---

## 4. Composition musicale assistee par contraintes

### Description du probleme et contexte
La composition musicale peut etre formulee comme un probleme de satisfaction de contraintes : harmonie, rythme, contrepoint, structure. La CP permet de generer des pieces musicales respectant des criteres esthetiques et theoriques. C'est un sujet original qui combine art et optimisation combinatoire.

### References
- Anders - *Constraint Programming for Music Composition* - Wiley 2012. [Extrait](https://www.michael-anders.net/)
- [IJCAI 2024 Tutorial on CP for Music](https://ijcai-24.org/)
- Pachet and Roy - *FlowComposer* - 2014. [PDF](https://www.researchgate.net/publication/263148474_Flow_Composer_Harmonic_Generation_Guided_by_Tension-FLOW_Model)
- Truchet and Assayag - *OpenMusic* - 2016. [Site](https://openmusic-project.github.io/)

### Point de depart
```python
# Modelisation CP : generation d'une melodie sur 8 mesures
# Variables : note[m, b] = hauteur (MIDI) de la note a mesure m, beat b
# Contraintes : intervals melodiques, progression harmonique, resolution
# Contraintes : registre (pas trop grave, pas trop aigu)
# Integration : export vers MIDI via midiutil ou pretty_midi
```

### Ressources supplementaires
- [music21 - toolkit for musicology](https://web.mit.edu/music21/) - Manipulation de partitions en Python
- [MIDIUtil](https://github.com/MarkCWirt/MIDIUtil) - Generation de fichiers MIDI
- [pretty_midi](https://github.com/craffel/pretty-midi) - Analyse et generation MIDI

### Approches suggerees
- Modelisation CP avec contraintes d'harmonie (accords, voix)
- Generation melodique avec contour et intervalles
- Integration avec MIDI et synthesis audio
- Interactive constraint programming (generation en temps reel)

### Technologies pertinentes
Python, OR-Tools CP-SAT, music21, MIDIUtil

---

## 5. Assistant de planification conversationnel (LLM + CSP)

### Description du probleme et contexte
Un assistant de planification conversationnel combine un LLM pour l'interaction en langage naturel et un solveur CP pour la resolution des contraintes. Le LLM extrait les preferences, le solveur trouve des solutions, le LLM explique et itere. Pipeline : LLM -> extraction -> CP -> LLM -> feedback. Ce sujet est ideal pour integrer des competences en IA generative et en optimisation.

### References
- [OpenAI Function Calling Documentation](https://platform.openai.com/docs/guides/function-calling)
- [Soon.works - LLM-assisted Constraint Programming](https://www.soon.works/blog/why-chatgpt-and-llms-arent-ideal-for-automatically-generating-shift-schedules) - 2023
- Xu et al. - *Conversational Planning* - 2023
- [MCP (Model Context Protocol) Specification](https://modelcontextprotocol.io/)

### Point de depart
```python
# Architecture MCP : le solveur CP est expose comme un outil MCP
# Le LLM appelle l'outil avec les contraintes extraites du dialogue
# Exemple : "Organise une reunion cette semaine avec 5 personnes"
#   -> LLM extrait : participants, contraintes de disponibilite
#   -> CP solveur : trouve un creneau compatible
#   -> LLM : presente la solution en langage naturel

# Voir le notebook agentique du cours pour un exemple d'integration
```

### Ressources supplementaires
- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [LangChain Tools documentation](https://python.langchain.com/docs/modules/tools/)
- [FastAPI pour le backend](https://fastapi.tiangolo.com/)

### Approches suggerees
- Integration via MCP ou function calling
- Cas d'usage : voyage, repas, evenements, reunions
- Definition d'un schema de contraintes structure
- Interface web (Streamlit ou Flask)

### Technologies pertinentes
Python, OR-Tools CP-SAT, OpenAI API/LM Studio, FastAPI, Streamlit

---

## 6. Planification urbaine et placement d'infrastructures

### Description du probleme et contexte
La planification urbaine implique de localiser des infrastructures (ecoles, hopitaux, transports) pour maximiser l'accessibilite tout en minimisant les couts. Les contraintes incluent la capacite, la distance, et les preferences des residents. C'est une application de la theorie de la localisation (p-median, p-center, MCLP).

### References
- Chabrier - *Facility Location Problems* - 2006
- [CACIC 2017 - Urban Planning with CP](https://www.sciencedirect.com/science/article/pii/S1877050917328424)
- [IBM CP Optimizer Urban Planning Examples](https://www.ibm.com/docs/en/icos/20.1.0?topic=examples-facility-location-problems)
- [OSMnx - Python for street networks](https://osmnx.readthedocs.io/)

### Point de depart
```python
# Modelisation p-median : localiser p installations pour minimiser la distance totale
# Variables : open[i] = 1 si installation a i, assign[j] = installation desservant j
# Contraintes : exactement p installations ouvertes, couverture de chaque client
# Objectif : minimiser la somme des distances

# Extension : integration donnees reelles via OSMnx (OpenStreetMap)
import osmnx as ox
G = ox.graph_from_place("Paris, France", network_type="drive")
```

### Instances et donnees
- [OSMnx pour les donnees geospatiales](https://github.com/gboeing/osmnx)
- [GeoPandas pour la manipulation de donnees spatiales](https://geopandas.org/)
- [p-median benchmark instances](https://github.com/McGill-Comp421/p-median)

### Approches suggerees
- Modelisation p-median et p-center
- Contraintes de capacite et de couverture
- Multi-objectifs : accessibilite vs cout vs equite
- Integration avec donnees geospatiales (OpenStreetMap)

### Technologies pertinentes
Python, OR-Tools CP-SAT, GeoPandas, OSMnx

---

## 7. Tournees de livraison vertes (Electric VRP)

### Description du probleme et contexte
Le Electric VRP etend le VRP classique avec des vehicules electriques ayant des contraintes d'autonomie et des besoins de recharge. L'objectif est de minimiser les emissions et le cout energetique tout en satisfaisant la demande des clients. C'est un probleme d'actualite avec la transition ecologique de la logistique.

### References
- Booth and Beck - *Electric VRP with CP* - 2018. [PDF](https://www.researchgate.net/publication/333231312_A_Constraint_Programming_Approach_to_Electric_Vehicle_Routing_with_Time_Windows)
- Shaw - *VRP with Constraint Programming* - 1998
- [OR-Tools Routing Documentation](https://developers.google.com/optimization/routing)
- [PyVRP Documentation](https://pyvrp.readthedocs.io/)

### Point de depart
```python
# Modelisation CP : VRP avec contraintes d'energie
# Variables : next[v,i] = prochain client apres i pour vehicule v
#            energy[v,i] = niveau d'energie du vehicule v au client i
# Contraintes : autonomie (energy >= 0), recharge aux stations
# Objectif : minimiser distance + temps de recharge

# Alternative : OR-Tools Routing (API de haut niveau)
from ortools.constraint_solver import routing_enums_pb2, pywrapcp
```

### Instances et donnees
- [EVRPTW instances (Booth et al.)](https://github.com/boothj/EVRPTW)
- [PyVRP benchmark instances](https://github.com/PyVRP/PyVRP/tree/main/data)
- [Solomon VRPTW instances](https://www.sintef.no/projectweb/top/vrptw/solomon-benchmark/)

### Approches suggerees
- Modelisation CP avec contraintes d'energie
- Stations de recharge : localisation et scheduling
- Optimisation multi-objectifs (distance + energie)
- Scenario avec flotte mixte (electrique + thermique)

### Technologies pertinentes
Python, OR-Tools Routing, PyVRP, NetworkX

---

## 8. Allocation de ressources cloud (VM Scheduling)

### Description du probleme et contexte
Le placement de machines virtuelles (VM) sur des serveurs physiques vise a optimiser l'utilisation des ressources (CPU, RAM) tout en respectant les contraintes de capacite et de localisation. C'est un probleme de bin packing avec contraintes d'affinite et de securite. Applications : optimisation de couts AWS/GCP/Azure, consolidation de serveurs.

### References
- Zhang - *VM Resource Allocation with CP* - VCRA-CP. [PDF](https://arxiv.org/abs/1810.04465)
- Van et al. - *VM Selection as CSP*
- [Google OR-Tools Bin Packing example](https://developers.google.com/optimization/pack/bin_packing)

### Point de depart
```python
# Modelisation CP-SAT : bin packing avec contraintes
# Variables : bin[v] = serveur physique pour la VM v
# Contraintes : capacite CPU/RAM par serveur, affinite/anti-affinite
# Objectif : minimiser le nombre de serveurs actifs (consolidation)

from ortools.sat.python import cp_model
# Voir aussi : OR-Tools bin_packing example
```

### Instances et donnees
- [VM scheduling instances - Azure public dataset](https://github.com/Azure/AzurePublicDataset)
- [Google Cluster Trace](https://github.com/google/cluster-data)
- [Bin Packing instances - CSPLib](https://www.csplib.org/Problems/prob054/)

### Approches suggerees
- Modelisation bin packing avec CP
- Contraintes de capacite et d'affinite
- Minimisation du nombre de serveurs actifs
- Dynamic allocation et migration

### Technologies pertinentes
Python, OR-Tools CP-SAT, PuLP

---

## 9. Planification de trajectoire robotique sous contraintes

### Description du probleme et contexte
La planification de trajectoire (path planning) pour robots mobiles implique de trouver un chemin executable en respectant les contraintes cinematiques et dynamiques. Les environnements dynamiques ajoutent de la complexite. C'est un sujet au croisement de la CP et de la robotique.

### References
- Wang et al. - *Multi-Robot Path Planning Survey* - AAMAS 2019. [PDF](https://www.aaai.org/ojs/index.php/AIIDE/article/view/5143)
- Lopez et al. - *Robot Motion Planning* - ICAPS 2012
- [Shapely - geometric manipulation](https://shapely.readthedocs.io/)

### Point de depart
```python
# Modelisation CSP : discretisation espace-temps
# Variables : pos[t] = (x, y) du robot au temps t
# Contraintes : obstacles (pos[t] not in obstacles),
#               cinematique (||pos[t+1] - pos[t]|| <= max_speed)
# Objectif : minimiser le temps d'arrivee

import shapely.geometry as geom
# Voir aussi : A* comme base de comparaison
```

### Ressources supplementaires
- [ROS (Robot Operating System)](https://www.ros.org/) - Framework robotique
- [Navigation2 Stack](https://navigation.ros.org/) - Navigation autonome
- [Open Motion Planning Library (OMPL)](https://ompl.kavrakilab.org/)

### Approches suggerees
- Modelisation CSP avec discretisation espace-temps
- Contraintes de collision et de cinematique
- Integration avec A* et RRT
- Application : robots mobiles, bras robotiques

### Technologies pertinentes
Python, OR-Tools CP-SAT, Shapely, ROS

---

## 10. Verification de contrats intelligents (blockchain) par contraintes

### Description du probleme et contexte
La verification formelle de smart contracts (Ethereum) utilise SMT solvers comme Z3 pour detecter les vulnerabilites (re-entrancy, overflow, access control). La modelisation des contraintes d'execution permet une analyse statique rigoureuse. C'est un sujet en forte demande dans l'industrie blockchain.

### References
- Luu et al. - *Oyente* - CCS 2016. [PDF](https://eprint.iacr.org/2015/404.pdf)
- [Solidity SMTChecker Documentation](https://docs.soliditylang.org/en/latest/smtchecker.html)
- Zellic - *WETH Audit* - 2020
- [Mythril - symbolic execution for smart contracts](https://github.com/Consensys/mythril)

### Point de depart
```python
# Modelisation SMT avec Z3
from z3 import *

# Variables : mapping des variables d'etat du contrat
balance = Int('balance')
amount = Int('amount')

# Contraintes : invariants du contrat
solver = Solver()
solver.add(balance >= 0)
solver.add(amount >= 0)
solver.add(amount <= balance)  # precondition de transfert

# Verification : trouver un contre-exemple (vulnerabilite)
```

### Ressources supplementaires
- [Ethereum Smart Contract Best Practices](https://consensys.github.io/smart-contract-best-practices/)
- [Slither - static analyzer for Solidity](https://github.com/crytic/slither)
- [Hardhat - development framework](https://hardhat.org/)

### Approches suggerees
- Modelisation SMT avec Z3
- Detection de re-entrancy, integer overflow, access control
- Symbolic execution des smart contracts
- Integration avec outils de dev (Hardhat, Foundry)

### Technologies pertinentes
Python, Z3 SMT Solver, Solidity, Slither

---

## 11. Selection de transactions dans un bloc blockchain

### Description du probleme et contexte
La selection de transactions pour un bloc blockchain est un probleme de knapsack avec contraintes de taille et de frais (gas). Les mineurs/selecteurs doivent maximiser les frais tout en respectant les limites de bloc. C'est un cas d'application concret de l'optimisation combinatoire.

### References
- Bonneau et al. - *On Bitcoin Mining* - CITP 2014. [PDF](https://freedom-to-tinker.com/wp-content/uploads/sites/7/2014/10/BitcoinMining-tocs.pdf)
- Narayanan et al. - *Bitcoin and Cryptocurrency Technologies* - Princeton
- [Arxiv - MEV and Transaction Ordering](https://arxiv.org/abs/2310.00027) - 2024

### Point de depart
```python
# Modelisation knapsack avec CP-SAT
# Variables : include[t] = 1 si la transaction t est incluse dans le bloc
# Contraintes : sum(size[t] * include[t]) <= block_size_limit
# Contraintes : dependances entre transactions (si tx_parent incluse, tx_child possible)
# Objectif : maximiser sum(fee[t] * include[t])
```

### Instances et donnees
- [Ethereum transaction datasets](https://etherscan.io/)
- [Bitcoin transaction data](https://blockchain.info/)
- [MEV-Explore - transaction ordering](https://explore.mev.wiki/)

### Approches suggerees
- Modelisation knapsack avec CP-SAT
- Contraintes de taille et de gas
- Extension : ordering des transactions (MEV)
- Comparaison avec heuristiques gloutonnes

### Technologies pertinentes
Python, OR-Tools CP-SAT, web3.py

---

## 12. Optimisation energetique de centres de donnees (Green Scheduling)

### Description du probleme et contexte
L'optimisation energetique des data centers vise a reduire la consommation electrique en regroupant les charges sur moins de serveurs (consolidation) et en utilisant les energies renouvelables. Les contraintes incluent la performance et le refroidissement. Google a publie des travaux pionniers sur le sujet.

### References
- Kishore et al. - *Green Scheduling* - IEEE 2018
- [Google - Carbon Intelligent Computing](https://ai.googleblog.com/2020/06/carbon-aware-computing-for-data.html)
- [OR StackExchange - Data Center Optimization](https://or.stackexchange.com/questions/tagged/data-centers)

### Point de depart
```python
# Modelisation CP : consolidation de VMs
# Variables : active[s] = 1 si le serveur s est allume
#            assign[v] = serveur pour la VM v
# Contraintes : capacite CPU/RAM par serveur, temperature
# Objectif : minimiser sum(active[s]) + penalites temperature

# Extension : prise en compte du prix de l'electricite horaire
```

### Instances et donnees
- [Google Cluster Trace](https://github.com/google/cluster-data) - Donnees reelles de clusters
- [PlanetLab traces](https://www.planet-lab.org/)

### Approches suggerees
- Modelisation CP avec contraintes de placement
- Minimisation de la consommation (PUE)
- Integration avec les prix de l'electricite
- Dynamic workload management

### Technologies pertinentes
Python, OR-Tools CP-SAT, PuLP

---

## 13. Placement de services en edge computing avec contraintes de latence

### Description du probleme et contexte
Le placement de services en edge computing consiste a deployer des applications sur des noeuds de calcul distribues pour minimiser la latence et la consommation de bande passante. Les contraintes incluent les ressources et la localisation. Applications : IoT, 5G, applications temps reel.

### References
- Zhang - *Edge Placement with CP* - ENPP. [PDF](https://arxiv.org/abs/2101.00127)
- Guyonet et al. - *Edge Resource Allocation* - CP 2021
- [Azure Edge Zones Documentation](https://azure.microsoft.com/en-us/products/edge-zones/)

### Point de depart
```python
# Modelisation p-median avec contraintes de ressources
# Variables : place[s] = 1 si le service s est deploye sur le noeud n
#            route[u] = noeud edge servant l'utilisateur u
# Contraintes : ressources par noeud, latence max
# Objectif : minimiser latence moyenne + cout de deploiement
```

### Approches suggerees
- Modelisation p-median avec contraintes de ressources
- Optimisation multi-objectifs (latence + cout)
- Dynamic placement et migration
- Integration avec Kubernetes

### Technologies pertinentes
Python, OR-Tools CP-SAT, Kubernetes API

---

## 14. Ordonnancement de missions satellites (Satellite Scheduling)

### Description du probleme et contexte
Le satellite scheduling problem consiste a planifier les prises de vue et les communications de satellites en respectant les contraintes orbitales, energetiques et de priorite. C'est un probleme de scheduling avec fenetres de temps, utilise par le CNES (SPIKE), la NASA (EUROPA) et l'ESA.

### References
- Frank et al. - *EUROPA* - NASA. [Site](https://github.com/nasa/Intelligent-Data-Management)
- Jussien - *Satellite Scheduling with CP* - ONERA 2015
- [DIMACS Challenge 1998](https://www2.cs.sfu.ca/CourseCentral/827/havens/papers/topic%2312(SatelliteScheduling)/dimacs98.pdf)

### Point de depart
```python
# Modelisation CP avec intervalles et fenetres de temps
# Variables : intervalles pour chaque observation (debut, fin, satellite)
# Contraintes : pas de chevauchement sur un meme satellite/station sol
#              fenetres de visibilite des cibles
#              energie disponible
# Objectif : maximiser la somme des priorites des observations
```

### Instances et donnees
- [Satellite Scheduling Benchmark - DIMACS](https://www2.cs.sfu.ca/CourseCentral/827/)
- [Skyfield - astronomy library](https://rhodesmill.org/skyfield/) - Calcul orbital

### Approches suggerees
- Modelisation CP avec intervalles et fenetres de temps
- Contraintes orbitales et energetiques
- Multi-satellites et constellations
- Optimisation de la valeur scientifique

### Technologies pertinentes
Python, OR-Tools CP-SAT, Skyfield (astronomy)

---

## 15. Cryptanalyse d'un chiffre par substitution (decryptage automatise)

### Description du probleme et contexte
La cryptanalyse de chiffres par substitution monoalphabetique consiste a retrouver la clef de chiffrement en analysant la distribution des lettres et les motifs du texte. La modelisation CSP permet de combiner des contraintes linguistiques et statistiques. C'est un sujet classique de la cryptographie combinatoire.

### References
- Cornell - *Cryptanalysis with CSP* - BOOM 2001. [Page](https://www.cs.cornell.edu/boom/2001sp/Barry/)
- Lucks - *Cryptanalysis and CP* - Crypto 1988
- [Wikipedia - Substitution cipher](https://en.wikipedia.org/wiki/Substitution_cipher)
- [hakank.org - OR-Tools cryptarithm solver](https://hakank.org/or-tools/cryptarithm.py)

### Point de depart
```python
# Modelisation CSP avec contraintes de permutation
# Variables : mapping[l] = lettre claire pour la lettre chiffree l
# Contraintes : AllDifferent (bijection), mots dans le dictionnaire
# Extension : frequences des lettres, bigrammes, trigrammes

from ortools.sat.python import cp_model
# Voir aussi : hakank.org pour des exemples de cryptarithmes
```

### Instances et donnees
- [Cryptogram solving - Wikipedia](https://en.wikipedia.org/wiki/Cryptogram)
- [NLTK - corpus et dictionnaires](https://www.nltk.org/) - word lists, frequency data
- [English letter frequency data](https://en.wikipedia.org/wiki/Letter_frequency)

### Approches suggerees
- Modelisation CSP avec contraintes de permutation
- Frequences des lettres et bigrammes
- Dictionnaire et motifs de mots
- Integration avec NLP (language models pour le scoring)

### Technologies pertinentes
Python, OR-Tools CP-SAT, NLTK, frequency analysis

---

## 16. Solveurs SMT pour la biologie synthetique

### Description du probleme et contexte
La biologie synthetique utilise des solveurs SMT pour verifier la coherence de modeles biologiques, detecter des contradictions dans des reseaux de regulation genetique, et concevoir des circuits genetiques. Ce sujet combine optimisation combinatoire et biologie computationnelle, un domaine en pleine expansion.

### References
- [BioSMT - SMT solving for biology](https://github.com/biosynthesis/biosmt)
- Bartocci et al. - *SMT-based analysis of biological systems* - 2020. [PDF](https://arxiv.org/abs/2005.07028)
- [iBioSim - genetic circuit design tool](https://async.ece.utah.edu/ibiosim/)
- [SBOL - Synthetic Biology Open Language](https://sbolstandard.org/)

### Point de depart
```python
# Modelisation SMT : verification de reseaux de regulation genetique
# Variables : concentration[g, t] = niveau d'expression du gene g au temps t
# Contraintes : relations d'activation/inhibition entre genes
#              bornes de concentration
# Verification : trouver un etat atteignable qui viole une propriete

from z3 import Real, Solver, And, Or, Implies
```

### Ressources supplementaires
- [Antimony - model description language](https://antimony.sourceforge.io/)
- [Tellurium - Python for systems biology](http://tellurium.analogmachine.org/)

### Approches suggerees
- Modelisation SMT pour la verification de reseaux de regulation
- Detection de contradictions et identification de parametres critiques
- Conception assistee de circuits genetiques
- Comparaison avec la simulation numerique classique

### Technologies pertinentes
Python, Z3 SMT Solver, Tellurium, libSBML

---

## 17. Participation a une competition CP/SAT/SMT

### Description du probleme et contexte
Chaque annee, plusieurs competitions internationales evaluent les progres en resolution de problemes CP, SAT et SMT. Les contributions prennent deux formes : (1) developpement de solveurs ou d'heuristiques, (2) creation de benchmarks et instances de test. Participer a une competition est une excellente facon de se mesurer a l'etat de l'art.

### Competitions disponibles
- **MiniZinc Challenge** : modelisation CP en MiniZinc. [Site](https://www.minizinc.org/challenge.html) (juin-juillet)
- **SAT Competition** : solveurs SAT. [Site](https://satcompetition.github.io/2024/) (juillet)
- **SMT-COMP** : solveurs SMT. [Site](https://smtcomp.github.io/2024/) (juillet)
- **International Planning Competition (IPC)** : planification. [Site](https://ipc2024.net/) (aout)
- **International Timetabling Competition (ITC)** : emploi du temps. [Site](https://www.itc2024.org/)

### Point de depart
```python
# Option 1 : Participer avec OR-Tools CP-SAT
#   - Choisir une categorie de la MiniZinc Challenge
#   - Modeliser les instances en Python avec CP-SAT
#   - Comparer les performances avec les solutions reference

# Option 2 : Creer des benchmarks originaux
#   - Choisir un probleme reel non couvert par CSPLib
#   - Generer des instances de difficulte croissante
#   - Proposer un article decrivant les instances
```

### Ressources supplementaires
- [CSPLib - Problem Library](https://www.csplib.org/) - Problemes de reference
- [MiniZinc - Documentation](https://www.minizinc.org/doc-2.5.5/en/) - Langage de modelisation
- [Past competition results](https://www.minizinc.org/challenge/results.html)

### Approches suggerees
- Choix d'une competition et d'une categorie
- Modelisation avec CP-SAT ou Z3
- Optimisation des performances (search strategies, symmetry breaking)
- Redaction d'un rapport comparatif avec l'etat de l'art

### Technologies pertinentes
Python, OR-Tools CP-SAT, MiniZinc, Z3

---

## Annexe : sujets supprimes (risque de plagiat)

Les sujets suivants ont ete retires car ils ont deja ete traites par des etudiants d'au moins une ecole (Epita 2025, ECE 2026, EPF 2025/2026), ce qui represente un risque de plagiat inacceptable.

| # | Sujet | Traite par |
|---|-------|-----------|
| - | Nurse Rostering | Epita 2025 + ECE 2026 |
| - | Job-Shop Scheduling | Epita 2025 + EPF |
| - | VRP classique | EPF |
| - | Timetabling universitaire | Epita 2025 + EPF |
| - | Sports Tournament Scheduling | Epita 2025 + ECE 2026 |
| - | Sudoku | Epita 2025 |
| - | Wordle Solver | Epita 2025 + ECE 2026 + EPF |
| - | Minesweeper/Demineur | Epita 2025 (3 groupes!) + ECE 2026 + EPF |
| - | Mots-croises | Epita 2025 + ECE 2026 + EPF |
| - | Stable Marriage | EPF |
| - | Graph/Map Coloring | ECE 2026 |
| - | Product Configuration | Epita 2025 + EPF |
| - | Portfolio Optimization | Epita 2025 + ECE 2026 |
| - | Quoridor | Epita 2025 |
| - | Multi-Robot Warehouse | Epita 2025 |
| - | Student Allocation | Epita 2025 |
| - | Test Generation | Epita 2025 |
| - | Satellite Scheduling | Epita 2025 |
| - | Tourist Itinerary | Epita 2025 |
