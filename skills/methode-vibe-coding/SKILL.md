---
name: methode-vibe-coding
description: Démarrer ou outiller une app ou un projet 100 % vibe codé (l'IA écrit le code, l'humain décide) avec la méthode du porteur — modèle une-app-par-mois/app-template, cadrage, garde-fous de Claude Code, cycle de ticket, audit adverse avant version. À utiliser quand l'utilisateur veut lancer une nouvelle app de la série « Une app par mois », démarrer un nouveau projet codé par l'IA, appliquer la méthode à un dépôt existant, préparer une version, ou demande « la méthode », « le template », « le cadrage d'une nouvelle app ».
---

# Méthode de vibe coding

Méthode tirée de deux expériences : le modèle `une-app-par-mois/app-template` (petites apps 100 % locales, publiées en open source) et le projet quiz-lecons (app mobile avec serveur, 17 jours, ~400 PR). Principe : **l'IA écrit le code, les tests et la documentation ; le porteur décide de ce qui lui est réservé, teste, relit les zones sensibles et les décisions automatiques.** La qualité du code dépend de la qualité de ce que l'IA lit et des contrôles que la machine impose.

Tout échange avec le porteur se fait en français.

**Décisions** (depuis la version 1.1.0) :

- **Réservées au porteur**, toujours posées sous forme de choix (2 à 4 options, recommandation en premier), jamais seulement écrites dans un document :
  - le cadrage et ses validations (cadrage, PRD, charte et maquettes, documents du niveau renforcé), le niveau, le mode de CI ;
  - la sécurité, les règles absolues, les données personnelles, la conformité ;
  - une dépendance nouvelle ou une dépense ;
  - une contradiction avec un document validé ;
  - une zone sensible (plan et relecture avant fusion) ;
  - une action irréversible ou extérieure au dépôt ;
  - un choix sans recommandation nette.
- **Automatiques**, pour tout le reste quand l'IA a une recommandation nette (spécification absente ou ambiguë, texte, affichage, découpage d'un ticket, cas limite, architecture interne) : l'IA applique sa recommandation sans poser la question, continue, et ajoute une ligne à `docs/DECISIONS.md` (date, ticket, question, options, choix, raison, état). Chaque PR les liste dans « Décisions automatiques à relire ». Le porteur en annule une quand il veut ; la correction passe par un ticket. En fin de jalon, l'IA dit combien de décisions automatiques ont été annulées. S'il y en a beaucoup, on resserre la liste des décisions réservées.
- **Contradicteur** : avant la PR d'un ticket qui a des décisions automatiques, l'IA lance un sous-agent contradicteur, en lecture seule, sans lui donner son raisonnement. Sa définition est dans `references/contradicteur.md`, à copier dans `.claude/agents/contradicteur.md` du projet. Il rend un verdict par décision :
  - « contestée » avec un argument convaincant : l'IA change, et la ligne devient « changée après contradiction » ;
  - « contestée » sans argument convaincant : l'IA garde, et la ligne devient « contestée, maintenue : <raison> » ;
  - « à réserver au porteur » (contradiction avec un document validé, décision qui aurait dû être réservée) : la question est posée au porteur.

  La PR liste les décisions contestées en premier. Sur Ki&La, son premier passage a fait changer une décision et en a fait remonter deux au porteur.

Motif (porteur, 2026-11-27, Ki&La) : le porteur validait la recommandation dans l'immense majorité des cas. Les contrôles automatiques, la relecture de fin et le journal rattrapent mieux une recommandation fausse qu'une question posée à chaque fois.

## 1. Choisir le niveau

Pose la question au porteur si le cadrage ne la tranche pas.

| Niveau | Quand | Ce qu'on ajoute au modèle |
| --- | --- | --- |
| **Léger** | App locale, sans serveur, sans paiement, sans donnée sensible ni utilisateur mineur | Rien : le modèle suffit |
| **Renforcé** | Serveur ou API, paiement ou abonnement, mineurs ou données sensibles, IA générative en production, publication sur les stores | Préparation complète avant le code : voir `references/preparation-renforcee.md` |

Dans le doute, commencer léger et passer au renforcé dès qu'un critère apparaît.

## 2. Créer le dépôt depuis le modèle

Créer un dépôt est une action extérieure : demande d'abord le nom, l'organisation (`une-app-par-mois` pour la série) et la visibilité (public pour la série). Puis :

```bash
gh repo create <org>/<nom> --template une-app-par-mois/app-template --<public|private> --clone
cd <nom>
```

Ensuite, sur une branche `ticket/000-mise-en-route` (jamais sur `main`), suivre la liste « Mise en route » de `ETAT.md` :

1. Supprimer `.github/workflows/modele.yml` et `scripts/tests-modele.sh` : ils ne servent qu'au modèle.
2. Créer `docs/DECISIONS.md` (journal des décisions automatiques, en-tête de tableau seulement) s'il n'existe pas dans le modèle.
3. Remplir `docs/CADRAGE.md` **avec le porteur**, par questions courtes : besoin vécu, utilisateur, 3 fonctionnalités `F-01` à `F-03`, hors périmètre, glossaire, tableau des données, zones sensibles, critères de réussite mesurables, risques. Rien n'est inventé : ce que le porteur n'a pas dit devient une question.
4. **Mode de CI** : le poser au porteur en QCM (section « Mode de CI » ci-dessous), le noter dans le cadrage et dans `CLAUDE.md`.
5. Charte et maquettes dans `design/`, validées avant le code.
6. Compléter `CLAUDE.md` (contexte, commandes), `scripts/test.sh`, `scripts/build.sh`, la mise en place de la stack dans `ci.yml` et `release.yml`, le bloc de `dependabot.yml`. Toute dépendance : proposée avec raison, licence et date de dernière mise à jour, après avoir vérifié qu'elle existe sous ce nom exact.
7. Rappeler au porteur d'activer les hooks (`./scripts/setup-hooks.sh`, gitleaks requis) et de protéger `main` dans les réglages GitHub : ce sont des réglages qu'il fait lui-même.
8. Découper le travail en jalons (objectif, critère de sortie) et en tickets dans `docs/tickets/` à partir du modèle `000-modele.md`.

**Niveau renforcé** : en plus, remplacer la section « 100 % local » de `CLAUDE.md` par les règles absolues du projet, puis produire les documents de `references/preparation-renforcee.md`, chacun validé par le porteur avant le suivant.

### Mode de CI

Trois modes, choisis au cadrage ; **par jalon par défaut** (décision du porteur du 2026-11-26).

| Mode | La PR du ticket vise | La CI tourne sur | Quand le recommander |
| --- | --- | --- | --- |
| Par jalon | `groupe/jalon-<n>-<intitulé>` | la PR du jalon vers `main`, une fois le dernier ticket fusionné | Par défaut ; CI longue ou coûteuse |
| Par groupe | `groupe/<n°>-<n°>-<intitulé>` (2 à 4 tickets liés, décidés au plan) | la PR du groupe vers `main` | Lot cohérent au milieu d'un jalon |
| Par ticket | `main` | chaque PR | CI courte, besoin que chaque fusion soit vérifiée |

Règles communes, quel que soit le mode :

- Chaque ticket garde sa branche `ticket/…`, son plan validé, ses tests et sa PR.
- Fusion dans une branche de groupe : après `./scripts/test.sh` et `/code-review`, sans CI, en gardant les commits (`gh pr merge <n> --merge --match-head-commit <sha>`) pour que gitleaks les parcoure tous à la CI du groupe.
- Fusion vers `main` : CI toute verte sur le dernier commit et branche à jour de `main` (`git merge-base --is-ancestor origin/main <sha>`, sinon fusionner `main`, jamais de rebase), `--squash`.
- **Zone sensible** : le ticket reste dans son groupe, avec `/security-review` et la relecture du porteur **avant** sa fusion dans le groupe.
- Les workflows ne tournent que sur les PR vers `main` (`pull_request: branches: [main]`), ce que le modèle fait déjà.
- Statut d'un ticket fusionné dans un groupe : « fusionné dans le groupe », jamais « fait » avant la fusion du groupe dans `main`.
- Changer de mode en cours de projet = une décision datée du porteur, notée dans `CLAUDE.md`. Jamais une dérogation ponctuelle (voir `references/lecons.md`).
- Si le projet a des parcours de bout en bout longs (émulateur, navigateur), ils tournent à la CI de fin de jalon, pas sur chaque PR.

## 3. Appliquer la méthode à un dépôt existant

Lire le dépôt, puis proposer un plan (QCM) qui ajoute ce qui manque, dans cet ordre de valeur : garde-fous de `.claude/settings.json` et hook anti-secrets, `CLAUDE.md` court avec règles d'arrêt et cycle de ticket, `scripts/test.sh` qui échoue sans test, hooks Git, CI de sécurité (gitleaks, Semgrep, audit des dépendances, actions épinglées par empreinte), `ETAT.md` borné, DoD dans le modèle de PR. Copier les fichiers depuis `une-app-par-mois/app-template` (`gh api repos/une-app-par-mois/app-template/contents/<chemin>` ou un clone) plutôt que les réécrire.

## 4. Pendant le développement

Le cycle est dans le `CLAUDE.md` du modèle ; ses points essentiels :

- Plan court annoncé avant tout code, puis le travail commence ; il attend la validation du porteur seulement en zone sensible ou pour une décision réservée (section « Décisions » en tête).
- Ticket = une intention, une session, moins de 10 fichiers, découpé **avant** (découpage automatique, noté, sauf en zone sensible).
- Test écrit d'abord et vu en échec ; un bogue corrigé commence par un test qui le reproduit.
- `/code-review` sur chaque ticket, `/security-review` en zone sensible.
- Fusion par l'IA selon le mode de CI (section 2) : vers `main` seulement CI toute verte et branche à jour ; en zone sensible, après la relecture du porteur. Jamais `--admin`, aucune branche supprimée.
- PR avec la DoD cochée, « Décisions automatiques à relire » et « ce qui n'a pas été vérifié ».
- `ETAT.md` remplacé (100 lignes au plus), `PROMPTS.md` complété aux étapes importantes.

Les pièges à éviter, vécus sur quiz-lecons, sont dans `references/lecons.md` : lis-le avant de proposer une exception à une règle.

Les mots et abréviations de la méthode (ADR, PRD, DoD, CI, jalon, zone sensible…) sont définis dans `references/glossaire.md` ; le modèle en contient la même copie (`docs/GLOSSAIRE-METHODE.md`). Quand le porteur demande ce que signifie un terme, réponds à partir de ce glossaire ; un terme nouveau de la méthode s'y ajoute, dans les deux copies.

## 5. Préparer une version

1. Dérouler `CHECKLIST-RELEASE.md` dans la PR de version.
2. **Audit adverse** : lancer un sous-agent (outil Agent) qui n'a pas écrit le code, avec le prompt de référence de `AUDIT.md` (adapté au niveau renforcé si besoin : serveur, authentification, paiement). Ne lui donner que le dépôt, pas tes conclusions. Consigner ses résultats dans `AUDIT.md` ; ne rien corriger sans ticket.
3. Test hors ligne pour une app locale, test sur appareil réel pour une app mobile : faits par le porteur, résultat noté.
4. Le tag `vX.Y.Z` est poussé par le porteur (ou par l'IA avec son accord explicite) : `release.yml` construit, calcule les SHA-256 et l'attestation de provenance.
