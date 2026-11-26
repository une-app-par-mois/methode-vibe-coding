# Préparer un projet 100 % vibe codé

Démarche réutilisable pour préparer **n'importe quel projet logiciel écrit entièrement par une IA de code** (Claude Code ou équivalent), avant la première ligne de code. Elle vient d'un projet réel mené jusqu'au bout de la préparation, mais elle est écrite pour un projet quelconque : application mobile, site web, service, outil interne, jeu.

Version 1 · 2026-11-11

> **Version 2 · 2026-11-26 — deux niveaux et corrections tirées du bilan de quiz-lecons**
>
> - **Deux niveaux.** *Léger* (app locale, sans serveur, ni paiement, ni donnée sensible) : le modèle [`une-app-par-mois/app-template`](https://github.com/une-app-par-mois/app-template) suffit (cadrage d'une page, glossaire, zones sensibles, `CLAUDE.md`, conventions et DoD, garde-fous). *Renforcé* (serveur, paiement, mineurs, IA générative en production, stores) : ce document, **en partant du même modèle**. Le skill Claude Code `methode-vibe-coding` applique l'un ou l'autre.
> - **Fusion** : l'IA fusionne quand toute la CI est verte et la branche à jour de `main` (et non plus le porteur seul, §2.5) ; en zone sensible, **après** la relecture du porteur, plus jamais après la fusion.
> - **Journal** : `PROGRESS.md` (§10.4) devient `ETAT.md`, l'état courant en 100 lignes au plus, remplacé et non empilé ; l'historique vit dans les PR et `PROMPTS.md` (prompt clé, ce que l'IA a fait, ce que le porteur a corrigé). Un seul fichier d'état : pas de `SUIVI.md` en plus pendant le développement.
> - **Aucune dérogation permanente** : si une règle est contournée deux fois, on la réécrit. **Mode de CI choisi au cadrage** : par ticket, par groupe (2 à 4 tickets liés) ou par jalon (une seule CI à la fin, mode par défaut). Dans un groupe, chaque ticket est fusionné sans CI après ses tests et `/code-review`, commits gardés ; la CI complète tourne sur la PR du groupe vers `main`. Un ticket en zone sensible reste dans son groupe, relu par le porteur avant sa fusion dans le groupe. Changer de mode est une décision datée.
> - **DoD courte** : 6 à 8 critères, le reste est vérifié par la machine.
> - **Tests sur appareil tôt** : un prétest sur appareil réel à la fin de chaque jalon, pas seulement avant la bêta (quiz-lecons : 84 fiches de correctifs après les prétests).
> - **Parcours de bout en bout** : en campagne de fin de jalon, pas sur chaque PR.
> - **Avant chaque version** : audit adverse par une IA qui n'a pas écrit le code (sous-agent ou session séparée), consigné dans `AUDIT.md` ; build sur la CI avec empreintes SHA-256 et attestation de provenance ; `CHECKLIST-RELEASE.md`.
> - **Test d'abord** : le test est écrit, lancé et vu en échec avant le code.

---

## 1. L'idée de départ

Dans un projet vibe codé, **l'IA écrit le code, les tests et la documentation ; l'humain décide, valide, teste et fusionne**. L'IA ne se souvient de rien d'une session à l'autre et applique ce qu'on lui donne à lire. La qualité du code dépend donc presque entièrement de la qualité des documents qu'elle lit.

D'où la règle centrale : **aucune ligne de code applicatif avant que la préparation soit terminée**. La préparation produit des documents qui sont à la fois la documentation du projet **et les consignes de travail de l'IA**.

La préparation coûte du temps au début, mais elle évite le défaut classique du vibe coding : un code qui avance vite, puis se contredit, perd ses règles de sécurité, change de vocabulaire et devient impossible à reprendre.

### Les deux rôles

| Rôle | Qui | Fait |
|---|---|---|
| **Porteur du projet** | L'humain | Décide du produit et des priorités. Valide chaque document et chaque ticket. Teste sur le vrai support (téléphone, navigateur…). Relit les zones sensibles. Seul à fusionner, seul à agir sur la production, les comptes et l'argent. |
| **IA** | L'outil de code | Rédige les brouillons, cherche et vérifie les informations, propose des options avec une recommandation, code, teste, documente. Signale ses doutes et ce qu'elle n'a pas vérifié. Ne décide jamais seule d'un choix de produit, d'architecture, de sécurité ou de dépense. |

---

## 2. Les règles de travail pendant la préparation

Ces règles valent pour **tous** les documents. Ce sont elles qui rendent la démarche fiable.

### 2.1 Écrire pour une IA

Les documents sont lus par l'IA. Ils doivent :
- donner des **critères vérifiables** plutôt que des intentions (« 12 questions, 2 ou 3 choix » plutôt que « un quiz clair ») ;
- avoir **une seule source de vérité par sujet** : on renvoie vers le document de référence au lieu de recopier ;
- contenir des **exemples concrets** (JSON, écrans, cas limites) ;
- formuler les **règles absolues comme des interdits nets** (« jamais… ») ;
- employer le **vocabulaire du glossaire**, un seul mot par notion ;
- donner un **identifiant** à chaque exigence ou règle (`PRO-01`, `SEC-12`, `CONV-23`…), pour qu'on puisse la citer dans le code, les tests et les tickets.

### 2.2 Le cycle d'un livrable

1. **L'IA rédige un brouillon complet**, en s'appuyant sur les documents déjà validés, et le commite.
2. Elle présente un résumé court et une liste de **« Points à vérifier en relisant »** : les choix qui viennent d'elle, les écarts avec d'autres documents, les décisions qui restent au porteur.
3. Le porteur relit, corrige ou tranche.
4. **Sur validation explicite** (« je valide »), le statut du document passe à « validé le <date> ». Jamais avant.
5. L'IA met à jour le suivi d'avancement et commite.

Chaque document commence par une ligne d'état : `Statut : 🟡 brouillon complet, en attente de relecture · Dernière mise à jour : <date>`, puis `✅ validé le <date>`.

### 2.3 Les décisions

- L'IA **propose, recommande, puis demande** : elle présente 2 à 4 options avec leurs conséquences et met sa recommandation en premier. Elle ne pose une question que si la réponse change vraiment ce qu'elle va faire.
- Chaque décision du porteur est **écrite avec sa date** dans le document concerné (section « Décisions du porteur » ou historique).
- Une question qui ne bloque pas le document en cours est **renvoyée** au livrable qui doit la traiter, et notée comme « question ouverte » ou « point d'attention ».
- **Vérifier avant d'affirmer** : prix, limites d'un abonnement gratuit, versions et compatibilité d'un outil, règles des stores, textes officiels. L'IA cherche la source et la cite. Exemple vécu : une protection de branche « évidente » s'est révélée indisponible sur un dépôt privé gratuit ; un outil de lint n'acceptait pas encore la dernière version du langage.

### 2.4 Le suivi d'avancement

Un fichier `docs/SUIVI.md`, **seule source** des pourcentages.

| % | État d'un livrable |
|---|---|
| 0 % | Rien de rédigé |
| 25 % | Matière existante ou brouillon partiel |
| 50 % | Brouillon complet, en attente de relecture |
| 75 % | Relu, corrections en cours |
| 100 % | **Validé** par le porteur |

- Avancement d'une étape = moyenne de ses livrables ; avancement global = moyenne de tous les livrables.
- Un tableau d'ensemble avec des barres (`██████░░░░`), un tableau par étape avec un commentaire par livrable, une table de **points d'attention** (point, livrable qui le traitera, date), et un **historique** daté, le plus récent en haut.
- Les valeurs fixes évitent les pourcentages « au jugé ».

### 2.5 Git dès le premier jour

- Le dépôt est créé **avant** le premier document. Un commit par étape logique, messages en français courts (« Domaine : ce qui change »).
- Pendant la préparation, les documents peuvent aller directement sur la branche principale. Dès le premier ticket de code : une branche par ticket, PR, fusion par le porteur seulement.
- **Jamais de secret** dans le dépôt (`.env` ignoré, l'IA ne le lit pas), jamais de données personnelles réelles (photos, noms, adresses vus dans des fichiers de test).

### 2.6 Les documents validés ne bougent pas en silence

- Un écart découvert plus tard dans un document validé est **noté** (section « Écarts relevés » du document qui le découvre) et corrigé dans le code ou dans une révision **validée à nouveau**. Jamais modifié discrètement.
- Une révision d'un document validé (nouvelle section, par exemple) est signalée comme telle et soumise à une nouvelle validation de la partie ajoutée.

### 2.7 Dépenses et actions extérieures

- Toute action qui coûte de l'argent (appel payant à une API, service) ou qui sort du dépôt (publication, envoi, compte) demande **l'accord du porteur pour chaque lancement**.
- L'IA n'a **aucun accès** à la production, aux comptes des stores, aux consoles de paiement.

---

## 3. Vue d'ensemble des étapes

| Étape | But | Livrables |
|---|---|---|
| **0. Démarrage** | Poser le cadre de travail | Dépôt Git, document de passation (si on part d'une note existante), plan de préparation, suivi |
| **1. Cadrage** | Savoir ce qu'on fait, pour qui, avec quelles règles | Note de cadrage, charte de vibe coding, glossaire, étude de marché rapide |
| **2. Produit** | Dire précisément ce que fait la V1 | PRD |
| **3. Réduction des risques** | Lever les inconnues avant de concevoir | Preuve de concept (POC) sur la partie la plus risquée, spécification de cette partie, modèle économique, autres livrables propres au risque |
| **4. Conception** | Dire comment le produit est fait | UX/UI et maquettes, architecture et ADR, sécurité, conformité, charte graphique |
| **5. Préparation du code** | Dire comment le code est écrit et vérifié | Conventions, Definition of Done et tests, outillage, `CLAUDE.md` |
| **6. Planification** | Dire dans quel ordre, en combien de temps, et quand c'est prêt | Plan de projet, stratégie de recette et de lancement, backlog, journal de progression |
| **Hors étapes** | Préparer l'après | Boîte à idées, chemin d'une idée jusqu'à une version |

Numérotation des fichiers conseillée : par dizaine selon l'étape (`01-` à `04-` cadrage, `10-` produit, `20-` risques, `30-` conception, `40-` code, `50-` planification, `90-` hors étapes). L'ordre de lecture devient évident.

---

## 4. Étape 0 — Démarrage

| Livrable | Contenu |
|---|---|
| **Dépôt Git** | Créé tout de suite, `.gitignore` qui exclut secrets et données privées |
| **Passation** (`HANDOFF.md`, facultatif) | Si le projet part d'une note, d'une conversation ou d'un prototype : tout ce qui existe, tel quel. Il est remplacé progressivement par les documents spécialisés ; en cas de contradiction, le document spécialisé le plus récent fait foi. |
| **Plan de préparation** (`00-plan-de-preparation.md`) | La liste des étapes et livrables de ce document, adaptée au projet, et les règles d'écriture (§2.1) |
| **Suivi** (`SUIVI.md`) | Structure du §2.4, tout à 0 % |

Le plan de préparation s'**adapte** : on retire un livrable inutile (pas de conformité des stores pour un outil interne), on en ajoute un quand un risque l'exige (exemple vécu : une bibliothèque de dessins est devenue un livrable à part quand le POC a montré que les faire générer à chaque fois était trop lent et trop cher).

---

## 5. Étape 1 — Cadrage

### 5.1 Note de cadrage
- Le problème, l'utilisateur principal et les autres utilisateurs, la proposition de valeur.
- **Objectifs de la V1** en liste courte et vérifiable.
- Périmètre : **dans la V1**, **hors V1** (plus tard), **exclus** (jamais, et pourquoi).
- Contraintes : données, budget, plateformes, légal.
- Risques principaux et parades.
- Décisions prises, datées.

### 5.2 Charte de vibe coding
Le contrat entre l'humain et l'IA. Elle contient :
- les **rôles** (§1) ;
- les **principes** : la spécification d'abord ; les tests sont le garde-fou (« un comportement non testé est considéré comme non fait ») ; transparence (l'IA dit ce qu'elle n'a pas fait ou pas vérifié) ;
- le **cycle d'un ticket** : lecture → plan validé par le porteur → branche → code et tests ensemble → vérifications automatiques → revue automatique (et revue de sécurité sur les zones sensibles) → test du porteur sur le vrai support → fusion par le porteur → journal ;
- la **taille d'un ticket** : une session, moins de 10 fichiers, découpé **avant** de commencer ;
- les **zones sensibles** du projet (authentification, paiement, données personnelles, tout ce qui exécute du contenu externe…) : revue humaine ligne à ligne ;
- **quand l'IA doit s'arrêter et demander** : spécification ambiguë, nouvelle dépendance, choix non tranché, zone sensible imprévue, test sans rapport qui échoue, ticket plus gros que prévu, action irréversible ;
- les **règles absolues** (données, sécurité, Git) : jamais de secret dans le dépôt, jamais désactiver un contrôle pour faire passer du code, jamais de nouvelle dépendance sans validation (et vérifier qu'elle existe vraiment sous ce nom : une IA peut inventer un nom de paquet), jamais d'accès à la production, jamais de fusion par l'IA ;
- la **continuité entre sessions** (suivi, journal, ADR).

### 5.3 Glossaire
- **Un seul mot par notion**, avec sa définition, utilisé partout : documents, interface, code, prompts.
- Si la documentation est dans une langue et le code dans une autre, une colonne « Code » donne le **terme obligatoire** dans le code. L'IA n'invente aucun synonyme.
- Le faire **tôt** : renommer une notion après coup laisse des traces partout (exemple vécu : un ancien terme resté dans un modèle de données validé, à corriger ensuite par une note d'écart).

### 5.4 Étude de marché rapide
Concurrents, prix pratiqués, ce qui manque, hypothèses à tester auprès de vrais utilisateurs (questions d'entretien prêtes). Les entretiens sont un point d'attention **non bloquant** : ils ajustent le produit plus tard.

---

## 6. Étape 2 — Produit : le PRD

- Personas, **parcours principaux** (numérotés : P1, P2…).
- **Exigences fonctionnelles** par domaine, chacune avec : identifiant, priorité (MoSCoW : indispensable, souhaitable, si le temps le permet, pas en V1), exigence, **critères d'acceptation vérifiables**.
- Règles métier (pédagogiques, financières, etc.) rassemblées dans une section.
- **Exigences non fonctionnelles** chiffrées (`NF-xx`) : fiabilité, performance, hors ligne, confidentialité, plateformes, stabilité.
- Indicateurs de succès.
- **Synthèse du périmètre V1** et **exclusions définitives**.
- **Questions ouvertes** renvoyées aux livrables suivants : elles ne bloquent pas la validation du PRD ; on les barre quand elles sont tranchées, avec le renvoi.

---

## 7. Étape 3 — Réduction des risques

Avant de concevoir, on **mesure** ce qui pourrait faire échouer le projet.

### 7.1 Preuve de concept (POC)
- Sur la partie la plus incertaine (souvent : un service d'IA, un matériel, une performance, une intégration externe).
- **Code jetable**, dans un dossier à part (`poc/`), jamais importé par le futur code : on le **recopie** plus tard, renommé et testé.
- Avec de **vraies données** du porteur, gardées **hors du dépôt**.
- Objectifs mesurés : qualité (grille de notation par le porteur), coût par appel, durée, taux d'échec.
- Le budget de chaque série d'essais payants est annoncé et accepté avant.

### 7.2 Spécification de la partie risquée
Ce que le POC a validé, transformé en contrat : appels, formats d'entrée et de sortie (schémas), contrôles automatiques, **jeu de test** (cas normaux, cas limites, cas qui doivent échouer, cas de données personnelles), **critères de qualité chiffrés** qu'une nouvelle version doit atteindre avant la production.

### 7.3 Modèle économique
Coûts mesurés (pas estimés), coûts fixes, prix, commissions, seuil de rentabilité, quotas. Ce qui reste à vérifier (statut juridique, conditions des plateformes) est listé.

### 7.4 Livrables propres au projet
Ajouter ici tout ce que le POC révèle comme risque (bibliothèque de contenus préparés, référentiel métier, jeu de données…).

---

## 8. Étape 4 — Conception

### 8.1 UX/UI et maquettes
- Principes, arborescence des écrans, description de chaque écran, valeurs chiffrées (tailles minimales, délais, règles d'animation), textes.
- **Maquettes interactives en HTML** générées par un script (pas dessinées à la main) : elles se régénèrent à chaque correction et lisent les mêmes jetons de couleur que le futur code.
- **Boucle de relecture outillée** : chaque écran a un panneau « verdict + commentaire » ; le porteur exporte ses notes en JSON (ou les copie) et les donne à l'IA, qui applique tout le lot. Plusieurs tours sont normaux.
- Les vérifier **sur le vrai support** (exemple : publier la maquette en page privée pour l'ouvrir sur son téléphone).
- Si le projet a des illustrations ou personnages, décider tôt **qui les dessine** et comment (exemple : dessinés pendant les sessions, jamais générés à la volée, validés un par un).

### 8.2 Architecture et ADR
- Vue d'ensemble, dépôt et paquets, pile technique (chaque dépendance listée vaut validation), données (modèle), contrats d'API (routes, en-têtes, codes d'erreur), stockage, réglages modifiables sans nouvelle version, journaux (liste **fermée** de champs), environnements (développement, recette, production), exigences non fonctionnelles et leur réponse.
- **Un ADR par décision structurante** : contexte, décision, options écartées, conséquences. Une décision acceptée n'est jamais modifiée : une nouvelle la remplace.
- Un ADR peut être **conditionnel** : « accepté si le prototype atteint tel seuil, sinon repli X » ; le prototype devient alors le premier ticket.

### 8.3 Sécurité
- Ce qu'on protège, contre qui, **modèle de menaces** par zone.
- Exigences numérotées (`SEC-xx`) avec leur **moyen de vérification** (test, lint, revue, procédure).
- **Tests de sécurité** écrits avec le code, qui échouent si un contrôle disparaît.
- Risques acceptés (et pourquoi), procédure d'incident.

### 8.4 Conformité
- Données personnelles : responsabilités, registre, analyse d'impact si nécessaire, droits des personnes, information des utilisateurs.
- Règles des plateformes de distribution, droit d'auteur, droit de la consommation, réglementations propres au domaine.
- **Liste des actions à faire avant le lancement**, reprise dans le plan de projet.

### 8.5 Charte graphique
- Couleurs en **jetons** (clair et sombre), contrastes **vérifiés par un script**, police (licence compatible), tailles, composants.
- Une planche visuelle générée, et les jetons dans un fichier que le futur code reprendra.

---

## 9. Étape 5 — Préparation du code

### 9.1 Conventions de code
Langues (code, commentaires, documents), **arborescence exacte**, nommage, règles du langage, **couches et sens des imports**, validation des données extérieures, gestion des erreurs, journaux sans données sensibles, interface (aucune couleur ni aucun texte en dur), reprise du code du POC, commentaires qui citent les identifiants d'exigences, Git, dépendances (versions exactes), **numéros de version** de tout ce qui est versionné (application, service, contrat d'API, schémas de base, contenus embarqués), journal des changements. Règles numérotées (`CONV-xx`).

### 9.2 Definition of Done et stratégie de tests
- **DoD** en deux listes : ce que l'IA coche (tests qui couvrent chaque critère, vérifications vertes, aucun test désactivé, conventions respectées, erreurs gérées, documents mis à jour, journal à jour) et ce que fait le porteur (plan validé, test sur le support réel, revue des zones sensibles, fusion). L'IA recopie la liste dans chaque PR.
- **Stratégie de tests** : principes (aucun service réel appelé, heure et hasard injectés, tests indépendants, un test qui échoue n'est jamais masqué), niveaux de tests, **cas obligatoires par partie**, doubles des services extérieurs, **données de test toujours fictives**, seuils de couverture qui ne baissent jamais, traçabilité exigence → test.
- Les essais payants (jeu de test d'une IA, par exemple) sont **lancés à la main, avec accord**, jamais par la CI.

### 9.3 Outillage et garde-fous
- Chaque outil **vérifié avant d'être proposé** : existence sous ce nom exact, version, maintenance, usage, **compatibilité entre eux**.
- Les règles des conventions **traduites en règles automatiques** (lint, typage, hooks, CI), avec la liste de ce qui reste à la revue humaine.
- Hooks avant commit (secrets, branche principale interdite, format du message), CI qui bloque la fusion, audit des dépendances, scripts d'installation désactivés sauf exceptions listées.
- **Permissions de l'IA** dans son outil : refusé (lire les secrets, forcer un envoi, fusionner, accéder aux serveurs, publier), demandé (ajouter une dépendance, dépense), permis (lire, tester, commiter sur une branche de ticket).
- Les décisions qui coûtent (abonnement de l'hébergeur de code, minutes de CI) sont **vérifiées** puis posées au porteur.
- Le **premier ticket** installe l'outillage et prouve qu'il bloque ce qu'il doit bloquer.

### 9.4 `CLAUDE.md` (ou fichier d'instructions de l'outil)
Rédigé **en dernier**, court (une centaine de lignes) : ce qu'est le projet, quoi lire avant chaque session, **carte des documents**, règles absolues, quand s'arrêter et demander, zones sensibles, rappels d'écriture du code et des tests, commandes, comment terminer un ticket, état actuel. Il **renvoie** aux documents, qui font foi ; il ne les recopie pas.

---

## 10. Étape 6 — Planification

### 10.1 Plan de projet
- **Demander au porteur sa disponibilité réelle** (heures par semaine) et sa contrainte de date : en vibe coding, **c'est le temps du porteur qui limite**, pas celui de l'IA (chaque ticket demande validation du plan, test et relecture).
- Jalons : objectif, contenu, **critère de sortie**, nombre de tickets, heures du porteur. Le **plus gros risque technique en premier**.
- **Piste du porteur** en parallèle : démarches hors code qui bloquent des jalons (statut juridique, comptes, contrats, avis extérieurs, matériel de test, recrutement de testeurs), à lancer tôt à cause des délais d'attente.
- Calendrier estimé avec une marge (30 %), attention aux **saisons** (une bêta d'un produit scolaire en plein été ne mesure rien).
- Réévaluation à la fin de chaque jalon.

### 10.2 Stratégie de recette et de lancement
Environnements, matériel de test, recette complète et ciblée (listes à cocher), **gravité des anomalies**, bêta (participants, information et accord écrit, mesures **sans outil de suivi tiers** si le produit l'exclut), **critères d'entrée en bêta** et **critères de publication**, lancement progressif, surveillance, retour arrière.

### 10.3 Backlog
- Un fichier par ticket, au format de la charte : contexte, **actions du porteur avant de commencer**, documents à lire, travail attendu et hors périmètre, fichiers concernés, **critères d'acceptation vérifiables**, tests attendus, dépendances, zone sensible oui/non.
- **Seuls les tickets des deux premiers jalons sont rédigés en détail.** Les autres sont une ligne titrée, détaillée au début du jalon précédent, pour profiter de ce qui a été appris.

### 10.4 Journal de progression (`PROGRESS.md`)
En haut, l'**état présent** (jalon, ticket en cours, prochaine action, actions du porteur à faire, points d'attention ouverts) ; en bas, le **journal des sessions**, jamais réécrit, avec ce qui n'a **pas** été fait ou vérifié.

---

## 11. Hors étapes : préparer l'après

### 11.1 Boîte à idées
Les idées pour les versions suivantes, numérotées (`IDEE-xx`), chacune avec intérêt, **découpage en lots**, **effort** (échelle XS à XL définie), **coût récurrent**, dépendances et risques, statut. Ce ne sont **pas** des exigences : l'IA ne les code jamais directement.

### 11.2 De l'idée à la version
Le chemin obligatoire d'une nouveauté : idée → **étude avec grille d'impact** (quels documents sont touchés : données personnelles, sécurité, IA, coûts, écrans, vocabulaire, dépendances, architecture, schéma de base…) → décision du porteur → mini-préparation des documents touchés, validés → tickets → développement → recette → bilan. Parcours **court** si aucun risque, **complet** sinon. Règles d'intégration sûre dans un produit déjà en service : interrupteur sans nouvelle version, aucune donnée perdue, versions qui cohabitent, valeurs par défaut prudentes, retour arrière.

---

## 12. Pièges rencontrés et parades

| Piège | Parade |
|---|---|
| Un terme change en cours de route et reste dans des documents validés | Glossaire tôt ; écart noté plutôt que réécriture silencieuse |
| Un « fait » évident est faux (limite d'un abonnement gratuit, compatibilité de versions) | L'IA vérifie à la source et cite la source avant de recommander |
| Une version d'outil plus récente que celle prévue par l'architecture | Règle « dernière version stable au démarrage », vérifiée au premier ticket |
| Des pourcentages d'avancement au jugé | Valeurs fixes par état, 100 % seulement sur validation |
| Des documents qui se recopient et divergent | Une source par sujet, renvois |
| Des retours de relecture dispersés | Panneau de notes dans les maquettes, export groupé |
| Des données réelles qui fuient dans le dépôt ou les tests | `.gitignore` dès le début, données de test fictives, contrôle avant commit |
| Une IA qui avance seule sur une idée séduisante | Boîte à idées séparée du PRD, chemin d'une idée obligatoire |
| Des tickets trop gros ou rédigés trop tôt | Moins de 10 fichiers, détail seulement pour les deux jalons suivants |
| Un calendrier qui ignore la disponibilité réelle ou les saisons | Demander les heures par semaine ; regarder quand tombe la bêta |
| Des dépenses lancées sans accord | Accord du porteur à chaque lancement payant, jamais par la CI |

---

## 13. Adapter la démarche

- **Petit projet** (outil personnel, quelques écrans) : fusionner note de cadrage et PRD, réduire sécurité et conformité à une page chacune, sauter l'étude de marché et le modèle économique. Garder **toujours** : charte, glossaire, conventions, DoD, outillage, `CLAUDE.md`, backlog, journal.
- **Sans partie risquée** : l'étape 3 se réduit au modèle économique, ou disparaît.
- **Sans données personnelles** : la conformité se limite aux licences et au droit de la consommation.
- **Projet web ou service** : remplacer « stores » par l'hébergement et la mise en production, et les appareils de test par les navigateurs.

La seule partie **non négociable** : rien n'est codé avant que l'IA dispose de règles écrites, validées et vérifiables par une machine.

---

## 14. Liste de contrôle

**Démarrage**
- [ ] Dépôt Git, `.gitignore` (secrets, données privées)
- [ ] Plan de préparation adapté, suivi à 0 %

**Cadrage**
- [ ] Note de cadrage (objectifs V1, dans / hors / exclu)
- [ ] Charte de vibe coding (rôles, cycle d'un ticket, zones sensibles, arrêts, interdits)
- [ ] Glossaire (un mot par notion, terme du code)
- [ ] Étude de marché rapide

**Produit**
- [ ] PRD (exigences numérotées, critères vérifiables, périmètre, questions ouvertes)

**Risques**
- [ ] POC mesuré sur la partie la plus incertaine
- [ ] Spécification de cette partie (schémas, contrôles, jeu de test, seuils)
- [ ] Modèle économique sur coûts mesurés

**Conception**
- [ ] UX/UI et maquettes générées, relues sur le vrai support
- [ ] Architecture et ADR
- [ ] Sécurité (menaces, `SEC-xx`, tests de sécurité)
- [ ] Conformité (actions avant lancement)
- [ ] Charte graphique (jetons, contrastes vérifiés)

**Préparation du code**
- [ ] Conventions (dont numéros de version)
- [ ] Definition of Done et stratégie de tests
- [ ] Outillage vérifié, permissions de l'IA
- [ ] `CLAUDE.md`

**Planification**
- [ ] Plan de projet (disponibilité réelle, jalons, piste du porteur)
- [ ] Recette et lancement (critères d'entrée en bêta et de publication)
- [ ] Backlog (deux premiers jalons détaillés)
- [ ] Journal de progression

**Après**
- [ ] Boîte à idées
- [ ] Chemin d'une idée jusqu'à une version
