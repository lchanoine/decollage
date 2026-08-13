# Décollage

Outil personnel de sortie de procrastination, conçu pour un profil TDAH précis.
Page statique unique, sans dépendance ni étape de build.

## Ce que l'outil fait

L'objectif n'est pas de gérer des tâches — c'est de casser un cycle :
accumulation silencieuse → panique → poussée d'action → soulagement → dérive.

Les mécaniques implémentées viennent d'une revue de la littérature
(`docs/RECHERCHE-COMPORTEMENTALE.md`, hors dépôt) :

| Mécanique | Ce que ça donne à l'écran | Appui |
|---|---|---|
| Contrat si-alors | chaque tâche du lendemain porte un « quand » et un premier geste | Gollwitzer & Sheeran 2006, d = 0.65 |
| Geste de 2 minutes | le bouton lance 2 min ; **s'arrêter là compte comme une victoire complète** | Woolley & Fishbach 2018 |
| Plafond de 3 | trois tâches maximum pour demain, imposé | Dalton & Spiller 2012 |
| Altitude, pas série | un score qui redescend doucement et ne tombe jamais à zéro | Sharif & Shu 2017 ; Polivy & Herman |
| Journées blanches | 3 jetons par mois, appliqués automatiquement, sans rien demander | Lally et al. 2010 |
| Avance offerte | un projet démarre à 15 % | Nunes & Drèze 2006 |
| Preuve de capacité | écran de faits, dont le nombre de reprises après un jour vide | TMT-TDAH 2023 |
| Minuterie de suffisance | à la fin du budget : « c'est assez bon » | Sirois, Molnar & Hirsch 2017 |
| Témoin bienveillant | lien de partage : accès complet + un écran de suivi qui n'affiche **que** ce qui avance | Ryan & Deci ; Wohl et al. 2010 |
| Graphique de montée | Y = travail accompli, X = les jours. La courbe ne redescend jamais ; ajouter des tâches déplace la cible sans effacer le fait | burn-up chart ; Koo & Fishbach 2012 |

Trois choses que l'outil ne fait jamais, volontairement :
pas de compteur « il te reste N tâches » en page d'accueil, pas d'écran d'échec,
pas de points ni de badges.

Un seul pourcentage existe dans l'application : la part du travail total qui est
réglée, pondérée par les minutes. Les projets de l'écran « pourquoi » n'ont
délibérément aucune barre de progression — un rêve n'est pas une tâche.

Trois sorties possibles pour une tâche, et elles ne se valent pas : **faite**
(elle compte), **annulée** (elle disparaît sans aucun crédit), **restante**.
Un **chantier** ne se coche jamais : il se découpe, et se ferme tout seul quand
sa dernière étape tombe.

## Comment les chiffres sont calculés

Rien n'est journalisé. Le graphique, le pourcentage et l'altitude sont
**entièrement recalculés** à partir des dates portées par les tâches
(`ct` création, `fait`, `annule`) et des gestes. Il n'existe donc aucun compteur
qui puisse dériver de la réalité : si une donnée change, tous les chiffres
suivent. C'est la raison pour laquelle il n'y a pas d'historique à maintenir.

## Architecture

Page unique (`index.html`), vanilla JS, aucune bibliothèque.

Les données vivent dans une base Firebase Realtime Database sous `rooms/<id>`.
La page parle à la base en REST pur et écoute le flux `text/event-stream` natif
de Firebase — donc rien à charger, et la synchronisation est poussée par le
serveur plutôt que sondée. Même approche que le dépôt `feuille-de-route`.

L'identifiant de salon est un aléatoire de 128 bits qui vit uniquement dans
l'URL (`#r=…`). Les règles de la base interdisent de lister `rooms` : connaître
l'adresse de la base ne donne accès à rien sans l'identifiant exact.

`#r=<id>` ouvre l'outil · `#r=<id>&m=1` ouvre la vue du témoin (lecture).

## Données personnelles

**Ce dépôt est public et ne doit contenir aucune donnée personnelle.**
La liste de tâches et les projets sont poussés directement dans la base, jamais
commités (voir `.gitignore`). Les fichiers `taches.js` et `reves.js` restent en
local sur le portable comme sauvegarde et comme moyen de tout réimporter.

Sauvegarde : réglages ⋯ → « Sauvegarder mes données » télécharge un JSON complet.

## Modifier la page

```powershell
git -C $HOME\decollage add -A
git -C $HOME\decollage commit -m "message"
git -C $HOME\decollage push
```

GitHub Pages redéploie tout seul en une minute environ.
