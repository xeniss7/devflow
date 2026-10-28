# devflow

Plugin Claude Code : une chaîne agentique de développement réutilisable sur n'importe quel projet.

Principe : **l'agent définit une méthode, le projet fournit le contexte** (son `CLAUDE.md` et ses
règles).

## Le cycle

```
analyst → [validation] → architect → [validation] → qa → [validation] → developer → reviewer → mentor → [validation] → merge
```

Chaque étape produit un livrable écrit, que l'étape suivante lit. Les validations sont humaines.

Le cycle suit le **TDD** : le QA écrit les tests unitaires, d'intégration et E2E avant le code, et
vérifie qu'ils échouent. Une fois validés, ces tests sont la spécification exécutable : le
développeur les fait passer sans jamais les modifier, et le reviewer contrôle qu'ils sont intacts.

## Agents

| Agent | Rôle | Écrit dans | Peut modifier le code |
| --- | --- | --- | --- |
| `devflow:analyst` | Besoin → spécification | `docs/specs/` | Non |
| `devflow:architect` | Spec → conception, signatures, stratégie de test, ADR | `docs/design/`, `docs/adr/` | Non |
| `devflow:qa` | Tests des 3 niveaux, en échec (phase rouge) | tests, `docs/reviews/` | Tests et squelettes seulement |
| `devflow:developer` | Tests au vert, puis refactoring | code source | Oui, sauf les tests existants |
| `devflow:reviewer` | Revue de code priorisée | `docs/reviews/` | Non |
| `devflow:mentor` | Explication + quiz | `docs/mentor/` | Non |

Un même numéro `NNNN` relie tous les livrables d'une fonctionnalité. Les ADR ont leur propre
numérotation.

## Statuts

- Spec : `Brouillon` → `Validée`
- Conception : `Proposée` → `Validée`
- ADR : `Proposé` → `Accepté` (ou `Rejeté`, `Remplacé`)

L'architecte refuse une spec non validée ; le QA refuse une conception non validée ; le développeur
refuse de commencer sans tests en échec documentés.

## Utilisation en développement

```bash
claude plugin validate ./devflow
cd mon-projet
claude --plugin-dir ../devflow
```

Puis, dans la session : `@agent-devflow:analyst <besoin>`.

## Prérequis côté projet

Un `CLAUDE.md` décrivant le contexte, la stack, les règles métier, les conventions et, si besoin,
les emplacements de documentation (valeurs par défaut : `docs/specs/`, `docs/design/`, `docs/adr/`,
`docs/reviews/`, `docs/mentor/`).
