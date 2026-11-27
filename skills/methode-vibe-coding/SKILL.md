---
name: methode-vibe-coding
description: Démarrer ou outiller une app ou un projet 100 % vibe codé (l'IA écrit le code, l'humain décide) avec la méthode du porteur — modèle une-app-par-mois/app-template, cadrage, garde-fous de Claude Code, cycle de ticket, audit adverse avant version. À utiliser quand l'utilisateur veut lancer une nouvelle app de la série « Une app par mois », démarrer un nouveau projet codé par l'IA, appliquer la méthode à un dépôt existant, préparer une version, ou demande « la méthode », « le template », « le cadrage d'une nouvelle app ».
---

# Méthode de vibe coding

Méthode tirée de deux expériences : le modèle `une-app-par-mois/app-template` (petites apps 100 % locales, publiées en open source) et le projet quiz-lecons (app mobile avec serveur, 17 jours, ~400 PR). Principe : **l'IA écrit le code, les tests et la documentation ; le porteur décide, valide les plans, teste et relit les zones sensibles.** La qualité du code dépend de la qualité de ce que l'IA lit et des contrôles que la machine impose.

Tout échange avec le porteur se fait en français. Toute décision lui est posée sous forme de choix (2 à 4 options, recommandation en premier), jamais seulement écrite dans un document.

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
2. Remplir `docs/CADRAGE.md` **avec le porteur**, par questions courtes : besoin vécu, utilisateur, 3 fonctionnalités `F-01` à `F-03`, hors périmètre, glossaire, tableau des données, zones sensibles, critères de réussite mesurables, risques. Rien n'est inventé : ce que le porteur n'a pas dit devient une question.
3. **Mode et lieu de la CI, lieu de la release** : les poser au porteur en QCM (sections « Mode de CI » et « Lieu de la CI et de la release » ci-dessous), les noter dans le cadrage et dans `CLAUDE.md`.
4. Charte et maquettes dans `design/`, validées avant le code.
5. Compléter `CLAUDE.md` (contexte, commandes), `scripts/test.sh`, `scripts/build.sh`, la mise en place de la stack dans `ci.yml` et `release.yml` (ou leur suppression en mode local), le bloc de `dependabot.yml`. Toute dépendance : proposée avec raison, licence et date de dernière mise à jour, après avoir vérifié qu'elle existe sous ce nom exact.
6. Rappeler au porteur d'activer les hooks (`./scripts/setup-hooks.sh`, gitleaks requis) et de protéger `main` dans les réglages GitHub : ce sont des réglages qu'il fait lui-même.
7. Découper le travail en jalons (objectif, critère de sortie) et en tickets dans `docs/tickets/` à partir du modèle `000-modele.md`.

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
- Fusion vers `main` : CI toute verte sur le dernier commit (CI locale : `./scripts/ci-local.sh --verify <sha>`) et branche à jour de `main` (`git merge-base --is-ancestor origin/main <sha>`, sinon fusionner `main`, jamais de rebase), `--squash`.
- **Zone sensible** : le ticket reste dans son groupe, avec `/security-review` et la relecture du porteur **avant** sa fusion dans le groupe.
- Les workflows ne tournent que sur les PR vers `main` (`pull_request: branches: [main]`), ce que le modèle fait déjà.
- Statut d'un ticket fusionné dans un groupe : « fusionné dans le groupe », jamais « fait » avant la fusion du groupe dans `main`.
- Changer de mode en cours de projet = une décision datée du porteur, notée dans `CLAUDE.md`. Jamais une dérogation ponctuelle (voir `references/lecons.md`).
- Si le projet a des parcours de bout en bout longs (émulateur, navigateur), ils tournent à la CI de fin de jalon, pas sur chaque PR.

### Lieu de la CI et de la release

Deux choix du cadrage, indépendants du mode (décision du porteur du 2026-11-27, après pixel-atelier).

| Choix | GitHub Actions | Local |
| --- | --- | --- |
| **CI** | `ci.yml` et `security.yml` sur la PR vers `main`. Recommandé pour un dépôt **public** (minutes gratuites) | `ci.yml` et `security.yml` supprimés ; `./scripts/ci-local.sh --publish` lance les mêmes contrôles sur le poste, dans un worktree propre du dernier commit poussé, et dépose le statut de commit `ci-local` (par `gh api`, gratuit). Recommandé pour un dépôt **privé** qui ne veut pas payer de minutes |
| **Release** | `release.yml` : build hors du poste, SHA-256, **attestation de provenance**. Recommandé | `release.yml` supprimé ; `./scripts/release-local.sh vX.Y.Z` construit sur le poste, SHA-256, **sans attestation** (écart à noter dans le cadrage et le README) |

En CI locale :

- Avant **chaque** fusion (vers un groupe comme vers `main`) : `./scripts/ci-local.sh --verify <sha>` passe sur le dernier commit de la PR, après un `--publish` sur ce commit.
- Le statut est une discipline, pas une preuve (déposable avec le jeton `gh`) : la garantie reste la règle « ask » sur `gh pr merge` et la relecture du porteur. Le dire au porteur au moment du choix.
- Le script de CI part d'un dépôt neuf : `scripts/test.sh` installe lui-même ses dépendances. Prérequis du poste à rappeler : gitleaks, Semgrep, l'audit de la stack.
- Vérifier les coûts avant de proposer (voir `references/lecons.md`) : les minutes coûteuses sont surtout macOS et Windows ; une CI Linux seule et par jalon peut tenir dans le quota gratuit.

## 3. Appliquer la méthode à un dépôt existant

Lire le dépôt, puis proposer un plan (QCM) qui ajoute ce qui manque, dans cet ordre de valeur : garde-fous de `.claude/settings.json` et hook anti-secrets, `CLAUDE.md` court avec règles d'arrêt et cycle de ticket, `scripts/test.sh` qui échoue sans test, hooks Git, CI de sécurité (gitleaks, Semgrep, audit des dépendances, actions épinglées par empreinte), `ETAT.md` borné, DoD dans le modèle de PR. Copier les fichiers depuis `une-app-par-mois/app-template` (`gh api repos/une-app-par-mois/app-template/contents/<chemin>` ou un clone) plutôt que les réécrire.

## 4. Pendant le développement

Le cycle est dans le `CLAUDE.md` du modèle ; ses points essentiels :

- Plan court validé avant tout code, sauf ticket marqué « trivial ».
- Ticket = une intention, une session, moins de 10 fichiers, découpé **avant**.
- Test écrit d'abord et vu en échec ; un bogue corrigé commence par un test qui le reproduit.
- `/code-review` sur chaque ticket, `/security-review` en zone sensible.
- Fusion par l'IA selon le mode de CI (section 2) : vers `main` seulement CI toute verte (ou statut `ci-local` vérifié) et branche à jour ; en zone sensible, après la relecture du porteur. Jamais `--admin`, aucune branche supprimée.
- PR avec la DoD cochée et « ce qui n'a pas été vérifié ».
- `ETAT.md` remplacé (100 lignes au plus), `PROMPTS.md` complété aux étapes importantes.

Les pièges à éviter, vécus sur quiz-lecons, sont dans `references/lecons.md` : lis-le avant de proposer une exception à une règle.

Les mots et abréviations de la méthode (ADR, PRD, DoD, CI, jalon, zone sensible…) sont définis dans `references/glossaire.md` ; le modèle en contient la même copie (`docs/GLOSSAIRE-METHODE.md`). Quand le porteur demande ce que signifie un terme, réponds à partir de ce glossaire ; un terme nouveau de la méthode s'y ajoute, dans les deux copies.

## 5. Préparer une version

1. Dérouler `CHECKLIST-RELEASE.md` dans la PR de version.
2. **Audit adverse** : lancer un sous-agent (outil Agent) qui n'a pas écrit le code, avec le prompt de référence de `AUDIT.md` (adapté au niveau renforcé si besoin : serveur, authentification, paiement). Ne lui donner que le dépôt, pas tes conclusions. Consigner ses résultats dans `AUDIT.md` ; ne rien corriger sans ticket.
3. Test hors ligne pour une app locale, test sur appareil réel pour une app mobile : faits par le porteur, résultat noté.
4. Le tag `vX.Y.Z` est poussé par le porteur (ou par l'IA avec son accord explicite) : `release.yml` construit, calcule les SHA-256 et l'attestation de provenance. Release locale : `./scripts/release-local.sh vX.Y.Z`, après un statut `ci-local` vert sur le commit de `main`, sans attestation.
