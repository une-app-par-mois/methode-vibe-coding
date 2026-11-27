# Glossaire de la méthode

Les mots et abréviations de la méthode de travail (cycle de ticket, CI, garde-fous, publication). À ne pas confondre avec le glossaire **de l'app**, dans le `docs/CADRAGE.md` de chaque projet, qui fixe le vocabulaire du domaine.

| Terme | Définition |
| --- | --- |
| ADR | Architecture Decision Record : fiche d'une décision technique structurante (contexte, choix, options écartées, conséquences). |
| API | Interface par laquelle un programme en appelle un autre, par exemple un serveur. |
| ask / deny | Règles de Claude Code : « ask » demande votre accord avant l'action, « deny » l'interdit toujours. |
| Attestation de provenance | Preuve signée par GitHub qu'un fichier publié a bien été construit par la CI, depuis ce dépôt. |
| Branche | Ligne de travail parallèle dans Git ; une par ticket, une par groupe ou par jalon. |
| CI | Intégration continue : tests et contrôles lancés automatiquement par GitHub Actions sur une PR. |
| CLAUDE.md | Fichier de consignes que Claude Code lit au début de chaque session. |
| CodeQL | Analyse de sécurité du code fournie par GitHub. |
| Commit | Enregistrement d'un ensemble de modifications dans Git, avec un message. |
| Décision automatique | Choix que l'IA fait seule en appliquant sa recommandation nette, hors des décisions réservées au porteur ; noté dans `docs/DECISIONS.md` et relu dans la PR. |
| Dependabot | Service de GitHub qui propose les mises à jour des dépendances et signale leurs failles. |
| Dépendance | Bibliothèque externe utilisée par l'app. |
| DoD | Definition of Done : liste des critères à cocher pour qu'un ticket soit terminé. |
| e2e | End-to-end, de bout en bout : test qui rejoue un parcours complet dans l'app réelle. |
| ETAT.md | État courant du projet en 100 lignes au plus : ticket en cours, prochaines étapes, points d'attention. |
| F-01, F-02… | Identifiants des fonctionnalités du cadrage, cités dans le code et le nom des tests. |
| Fusion (merge) | Intégration d'une branche dans une autre. `--squash` en fait un seul commit ; `--merge` garde tous les commits. |
| gitleaks | Outil qui cherche les secrets (clés, mots de passe) dans le code et l'historique Git. |
| Glossaire du cadrage | Un mot par notion, le même dans le code, les textes et les documents. |
| Hook | Script lancé automatiquement à un moment précis : avant un commit, avant un envoi, avant une commande de Claude Code. |
| Jalon | Étape du projet avec un objectif et un critère de sortie vérifiable ; regroupe plusieurs tickets. |
| main | Branche principale, protégée : on n'y arrive que par une PR. |
| OWASP | Fondation qui publie le Top 10 des failles de sécurité les plus courantes du web. |
| POC | Proof of Concept, preuve de concept : code jetable qui mesure si la partie risquée est faisable. |
| Porteur | La personne qui décide du produit, valide les plans, teste et relit. |
| PR | Pull Request : demande de fusion d'une branche, avec sa description, sa CI et sa revue. |
| PRD | Product Requirements Document : document des exigences du produit, numérotées et vérifiables. |
| PROMPTS.md | Journal des prompts clés : ce que l'IA a fait, ce que le porteur a corrigé. |
| QCM | Question à choix multiples : forme sous laquelle l'IA vous pose chaque décision, sa recommandation en premier. |
| Rebase | Réécriture de l'historique d'une branche ; interdite sur une branche déjà envoyée. |
| Semgrep | Analyse statique qui cherche des failles connues dans le code. |
| SHA-256 | Empreinte d'un fichier : elle permet de vérifier qu'un téléchargement n'a pas été modifié. |
| Skill | Ensemble de consignes que Claude Code charge quand une tâche les concerne. |
| Sous-agent | Autre instance de Claude, lancée à part, qui ne connaît que ce qu'on lui donne ; sert à l'audit adverse. |
| Stack | Ensemble des technologies de l'app : langage, framework, outils. |
| Tag | Étiquette posée sur un commit pour marquer une version (`v1.0.0`) ; déclenche la publication. |
| Ticket | Unité de travail : une intention, une session, moins de 10 fichiers. |
| Vibe coding | Développement où l'IA écrit tout le code ; l'humain décide, valide et vérifie. |
| Workflow | Fichier de GitHub Actions qui décrit les travaux de la CI. |
| Zone sensible | Partie du code où une erreur touche les données, la sécurité ou l'exactitude : revue renforcée. |
