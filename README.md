# Méthode de vibe coding

Plugin Claude Code de la série [« Une app par mois »](https://github.com/une-app-par-mois). Il contient le skill `methode-vibe-coding` : la méthode pour démarrer ou outiller une app, ou tout projet dont l'IA écrit le code, les tests et la documentation, pendant que l'humain décide, valide, teste et relit.

Il s'appuie sur le modèle [`une-app-par-mois/app-template`](https://github.com/une-app-par-mois/app-template) et sur le bilan d'un projet réel (app mobile avec serveur, 17 jours, ~400 PR).

## Installer

Dans Claude Code :

```text
/plugin marketplace add une-app-par-mois/methode-vibe-coding
/plugin install methode-vibe-coding@methode-vibe-coding
```

Ou depuis un terminal :

```bash
claude plugin marketplace add une-app-par-mois/methode-vibe-coding
claude plugin install methode-vibe-coding@methode-vibe-coding
```

Lisez le contenu avant d'installer, comme pour tout plugin : celui-ci ne contient que du texte (aucun hook, aucun serveur MCP, aucun script).

## Utiliser

Le skill se déclenche quand vous parlez de lancer une nouvelle app, de démarrer un projet codé par l'IA, d'appliquer la méthode à un dépôt existant ou de préparer une version. On peut aussi l'appeler directement :

```text
/methode-vibe-coding:methode-vibe-coding
```

Il couvre :

1. **Le choix du niveau** : léger (app locale, le modèle suffit) ou renforcé (serveur, paiement, mineurs, IA en production, stores).
2. **La mise en route** d'une nouvelle app depuis le modèle : cadrage par questions, mode de CI, charte et maquettes, stack, hooks, jalons et tickets.
3. **Les modes de CI** : par jalon (par défaut), par groupe ou par ticket ; lieu de la CI et de la release (GitHub Actions ou local).
4. **Le cycle d'un ticket** : plan validé, test d'abord, revue, PR avec Definition of Done et « non vérifié », fusion selon le mode.
5. **La préparation d'une version** : checklist, audit adverse par un sous-agent, test hors ligne ou sur appareil, tag et publication vérifiable.

## Contenu

| Fichier | Rôle |
| --- | --- |
| `skills/methode-vibe-coding/SKILL.md` | La méthode, lue par Claude Code |
| `skills/methode-vibe-coding/references/preparation-renforcee.md` | Préparation complète du niveau renforcé |
| `skills/methode-vibe-coding/references/lecons.md` | Pièges vécus et parades |
| `skills/methode-vibe-coding/references/glossaire.md` | Mots et abréviations de la méthode |

## Mettre à jour

```bash
claude plugin marketplace update methode-vibe-coding
claude plugin update methode-vibe-coding@methode-vibe-coding
```

Puis `/reload-plugins` dans une session ouverte. Les mises à jour automatiques peuvent être activées pour cette marketplace dans `/plugin`, onglet Marketplaces.

Chaque version change le numéro de `.claude-plugin/plugin.json` et ajoute une entrée à `CHANGELOG.md` : sans changement de numéro, les installations gardent l'ancienne copie.

## Licence

[MIT](LICENSE)
