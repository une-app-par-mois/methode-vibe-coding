# Leçons de quiz-lecons (2026-11-10 → 2026-11-26)

App mobile + proxy, vibe codée en 17 jours : ~336 fiches de ticket, ~400 PR, 15 ADR, aucune fuite de secret ni de donnée d'enfant connue. Ce qui suit est ce qui a coûté, et la parade retenue.

| Piège vécu | Parade |
| --- | --- |
| Les dérogations au regroupement de tickets se sont empilées (7 dans la charte) jusqu'à devenir la règle | Le mode de CI (par ticket, par groupe, par jalon) est un choix du cadrage ; en changer est une décision datée, pas une dérogation. Une règle contournée deux fois est réécrite |
| `PROGRESS.md` a atteint 2 168 lignes et ne servait plus de point d'entrée | `ETAT.md` de 100 lignes au plus, remplacé ; l'historique est dans les PR |
| Deux fichiers d'état (`PROGRESS.md`, `SUIVI.md`) à tenir à jour | Un seul fichier d'état pendant le développement |
| 13 documents de règles qui se renvoient (`CLAUDE.md` → charte → conventions → DoD → outillage) | Niveau léger : `CLAUDE.md`, cadrage, conventions + DoD. Le détail ne va dans un document à part que s'il est long |
| DoD de 18 critères recopiée dans chaque PR | 6 à 8 critères ; ce que la machine vérifie sort de la DoD |
| Les tests passaient, mais les prétests sur appareil ont produit 84 fiches de correctifs (J8), puis deux autres jalons de correctifs | Prétest sur appareil réel par le porteur à la fin de **chaque** jalon |
| Parcours Maestro : 19 à 28 min par PR, exécuteur auto-hébergé fragile (téléphone branché, disque plein, passage de minuit) | Parcours de bout en bout en campagne de fin de jalon seulement |
| La relecture des zones sensibles par le porteur arrivait après la fusion | Relecture **avant** la fusion, mais seulement sur les zones sensibles |
| `npm run check` (typage incrémental) masquait des erreurs | Avant une fusion importante, typage complet sans cache |
| Node par défaut du poste ≠ Node du projet : tests faussement rouges | Version de l'outil fixée dans le dépôt et chargée par les hooks |
| Un hook anti-secrets refuse aussi les commandes qui *parlent* de `.env` (corps de PR, tests du hook) | Écrire ces textes dans un fichier (outil d'écriture), puis `--body-file` |

## Ce qui a tenu et qu'il faut garder

- Glossaire « un mot par notion » : le vocabulaire n'a pas dérivé en 300 tickets.
- Exigences numérotées citées dans le code et le nom des tests.
- Interdits absolus doublés par la machine (permissions `deny`, hooks, lint, tests).
- Plan validé avant le code ; « rien hors maquette sans validation ».
- « Ce qui n'a pas été vérifié » dans chaque PR.
- Décisions posées au porteur en QCM, avec la recommandation en premier.
- Boîte à idées séparée du périmètre : l'IA ne code jamais une idée directement.
