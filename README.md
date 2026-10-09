# ranel-dist

Canal de distribution de **ranel**, un panel d'hébergement auto-géré.

Ce dépôt ne contient pas de code : il publie les **archives de
distribution** du panel, une par version, et le manifeste qui les liste.
Le code source vit dans un dépôt privé et n'est jamais déployé sur les
serveurs — c'est l'archive qui l'est.

## Le manifeste

[`versions.tsv`](versions.tsv) est le contrat que lisent l'installateur et
la commande de mise à jour. Une ligne par version, séparée par des
tabulations :

```
# <version>	<date>	<sha256>	<url>
1.0.0	2026-10-09	afa2bd5d…	https://github.com/RobinThijsen/ranel-dist/releases/download/v1.0.0/ranel-1.0.0.tar.gz
```

Trois propriétés voulues :

- **La somme de contrôle vit ici, pas dans l'archive.** C'est tout
  l'intérêt de la vérifier : l'installateur compare l'archive téléchargée
  à une valeur venue d'ailleurs, et refuse de la déplier sinon.
- **La dernière version n'est pas déclarée, elle est calculée** : c'est la
  plus grande du fichier. Un champ « latest » pourrait désigner une ligne
  qui n'existe pas.
- **Le format est tabulé, pas du JSON.** L'installateur le lit avant
  d'avoir installé quoi que ce soit sur la machine : à ce moment-là il n'y
  a ni `jq`, ni `php`, ni `python` garantis.

Une ligne mal formée — version non sémantique, somme de contrôle qui
n'est pas 64 caractères hexadécimaux, URL non `https` — fait refuser le
manifeste **en entier**. Ignorer une ligne douteuse reviendrait à
installer silencieusement une version plus ancienne que celle demandée.

## Vérifier une archive à la main

```bash
sha256sum ranel-1.0.0.tar.gz
```

La valeur doit correspondre à celle de la ligne du manifeste et à celle
des notes de la release. Chaque release indique aussi le commit source
dont elle a été construite.

## Installer le panel

L'installation ne se fait pas depuis ce dépôt mais depuis
[ranel-installer](https://github.com/RobinThijsen/ranel-installer), qui
lit ce manifeste. Voir son README.

## Portée de la vérification

Le `sha256` protège d'une archive corrompue en transit ou remplacée côté
stockage. Il ne protège **pas** de quelqu'un qui contrôlerait ce dépôt :
le manifeste et l'archive viennent de la même source. Une signature
détachée, dont la clé publique serait embarquée dans l'installateur, est
l'étape suivante.
