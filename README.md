# APPAO

Plateforme de réponse aux appels d'offres publics assistée par IA, pour FrenchFlair.

> **État du projet : cadrage.** Le dépôt ne contient pour l'instant que le PRD ([`APPAO_PRD.docx`](APPAO_PRD.docx), V1 et V2, statut « à valider », 18 novembre 2026). Aucun code n'existe encore ; la pile technique et l'hébergement ne sont pas choisis (voir [Questions ouvertes](#questions-ouvertes)). Ce README résume le PRD et documente l'API du BOAMP telle qu'elle a été testée. En cas d'écart, le PRD fait foi.

## Sommaire

- [Pourquoi APPAO](#pourquoi-appao)
- [Règles de fiabilité](#règles-de-fiabilité)
- [Profils et droits](#profils-et-droits)
- [Parcours de réponse](#parcours-de-réponse)
- [Base de connaissances](#base-de-connaissances)
- [Feuille de route](#feuille-de-route)
- [Intégration BOAMP](#intégration-boamp)
- [Exigences non fonctionnelles](#exigences-non-fonctionnelles)
- [Hors périmètre](#hors-périmètre)
- [Questions ouvertes](#questions-ouvertes)

## Pourquoi APPAO

FrenchFlair intervient sur la diversité et l'inclusion, le handicap en entreprise, la RSE et la QVCT. Des marchés publics correspondent à ces expertises, mais FrenchFlair n'y répond pas : une réponse coûte environ 1,5 jour-homme d'un profil senior, rare et sollicité ailleurs.

**Objectif :** transférer la production d'une réponse du senior vers un préparateur moins expert, le senior ne conservant que deux points de validation (GO/NO-GO, puis dossier final). Le savoir-faire doit résider dans la base de connaissances et dans les règles de l'outil, pas dans la mémoire d'une personne : tout dossier doit pouvoir être repris par quelqu'un d'autre à n'importe quelle étape.

Particularité des marchés visés : l'acheteur impose son propre cadre de réponse (cadre de réponse technique, BPU ou DPGF). APPAO **remplit les documents de l'acheteur** ; il ne produit pas de dossier au format FrenchFlair.

| Indicateur | Aujourd'hui | Cible |
|---|---|---|
| Temps senior par réponse | 1,5 jour-homme | À fixer (proposition : 0,5 j-h max) |
| Réponses déposées par an | 1 | À fixer |
| Exigences du DCE couvertes à la validation finale | Non mesuré | 100 % |
| Contenus non sourcés dans le dossier déposé | Non mesuré | 0 |
| Coût complet par réponse (temps humain + API) | Non mesuré | À fixer |

## Règles de fiabilité

Non négociables, elles priment sur la vitesse et le confort. Toute contribution au code doit les respecter.

1. **Seul le corpus validé parle au nom de FrenchFlair** (couche A de la base de connaissances).
2. **Aucun contenu sans source** : tout texte proposé par l'IA cite ses sources, visibles en regard.
3. **Pas de source, pas d'invention** : un manque produit un marqueur `[À COMPLÉTER]`, jamais un texte plausible.
4. **Aucun montant généré par l'IA** : les prix sont calculés de façon déterministe depuis la grille tarifaire, ou saisis par un humain.
5. Aucune référence client, aucun chiffre, aucun nom d'intervenant hors corpus validé.
6. Toute information extraite du DCE porte sa localisation (document, page).
7. **La décision appartient à un humain** : ni score de succès, ni estimation de concurrence, ni estimation de prix.
8. Les données publiques complètent les données internes sans s'y substituer : des montants d'attribution publics peuvent être affichés à titre indicatif, sourcés et datés ; le prix reste fixé par un humain, avec motivation tracée.
9. Aucune source n'est citable sans validation humaine, y compris les textes issus de la veille réglementaire.
10. Un dossier comportant un `[À COMPLÉTER]` ou une exigence non couverte ne peut pas être validé.

## Profils et droits

Une seule entreprise, des comptes nominatifs, cinq profils fixes (non configurables), cumulables. Tous les droits sont contrôlés côté serveur.

| Profil | Rôle |
|---|---|
| **Administrateur** | Comptes et profils, clé d'API Anthropic, tarifs de l'API, coûts horaires, budget ; seul à voir les temps et coûts de tous |
| **Valideur** (senior) | GO/NO-GO, validation des blocs et des textes réglementaires, validation du dossier final |
| **Préparateur** | Crée le dossier, vérifie l'extraction, complète les réponses, remplit les pièces, propose des blocs |
| **Contributeur expert** | Propose et relit les blocs de ses domaines ; n'accède ni aux dossiers ni aux prix |
| **Lecteur** | Consultation seule (hors offre financière et coûts) |

Un dossier ne peut pas être validé par la personne qui l'a préparé, sauf dérogation motivée et tracée. La matrice complète des droits figure en section 3.2 du PRD.

## Parcours de réponse

| # | Étape | Acteur | Epic |
|---|---|---|---|
| 1 | Création du dossier et dépôt du DCE | Préparateur | E2 |
| 2 | Extraction et matrice de conformité | IA, puis préparateur | E3 |
| 3 | Pré-qualification | IA, puis préparateur | E4, E12 |
| 4 | **Décision GO/NO-GO — point de validation 1** | Valideur | E4 |
| 5 | Réponse technique (cadre de l'acheteur, sources visibles) | IA, puis préparateur | E6 |
| 6 | Offre financière (BPU/DPGF, calcul déterministe) | Préparateur | E7 |
| 7 | Pièces administratives | Préparateur | E8 |
| 8 | Contrôle final automatique | Outil | E9 |
| 9 | **Validation finale — point de validation 2** | Valideur | E9 |
| 10 | Export aux formats de l'acheteur, dépôt manuel | Préparateur | E9 |
| 11 | Clôture : résultat, motifs, capitalisation | Préparateur | E10, E12 |

## Base de connaissances

Quatre couches, distinguées par l'usage autorisé de leur contenu :

| Couche | Contenu | Usage autorisé | Version |
|---|---|---|---|
| **A. Corpus validé** | Blocs validés : présentation, références, intervenants, méthodologies, moyens, pièces administratives, grille tarifaire | Seule source des textes qui parlent au nom de FrenchFlair | V1 |
| **B. Archives** | Plaquettes, rapports, propositions, réponse déjà produite | Recherche uniquement ; rien n'en sort sans devenir un bloc validé de la couche A | V1 |
| **C. Réglementation** | Commande publique (V1), réglementation métier (V2) | Citable comme réglementation, avec source, version et dates ; jamais comme fait sur FrenchFlair | V1, V2 |
| **D. Historique** | Dossiers FrenchFlair clos (V1), données d'attribution publiques (V2) | Rappel des dossiers similaires et des motifs communiqués ; montants indicatifs (règle 8) | V1, V2 |

Le corpus est vide au départ et se constitue au fil des dossiers : chaque `[À COMPLÉTER]` renseigné puis validé devient un bloc réutilisable. Un bloc a un statut (brouillon, validé, expiré), une preuve (obligatoire pour une référence ou un chiffre), un valideur et une date de validité. La recherche plein texte suffit tant que la base est petite ; la recherche sémantique n'est pas un prérequis.

## Feuille de route

**V1 — Répondre de bout en bout.** Tout le parcours, les deux points de validation, la base de connaissances interne et la réglementation de la commande publique.

**V2 — Répondre plus souvent et mieux.** Proposition à arbitrer après usage réel de la V1 (critères de passage en section 8.2 du PRD, seuils à fixer).

| Epic | Intitulé | Version |
|---|---|---|
| E1 | Utilisateurs, profils et traçabilité | V1 |
| E2 | Dossiers et dépôt du DCE | V1 |
| E3 | Extraction du DCE et matrice de conformité | V1 |
| E4 | Pré-qualification et décision GO/NO-GO | V1 |
| E5 | Corpus validé | V1 |
| E6 | Rédaction assistée de la réponse technique | V1 |
| E7 | Offre financière | V1 |
| E8 | Pièces administratives | V1 |
| E9 | Contrôle final, validation et export | V1 |
| E10 | Clôture et capitalisation | V1 |
| E11 | Timesheet et coûts (temps humain, appels à l'API) | V1 |
| E12 | Base de connaissances : archives, réglementation commande publique, historique | V1 |
| E13 | Veille et import des annonces **(BOAMP)** | V2 |
| E14 | Échéances et notifications | V2 |
| E15 | Modifications du DCE et questions à l'acheteur | V2 |
| E16 | Pilotage | V2 |
| E17 | Capitalisation avancée du corpus | V2 |
| E18 | Groupement et sous-traitance | V2 |
| E19 | Base de connaissances étendue : réglementation métier et données d'attribution **(BOAMP)** | V2 |

## Intégration BOAMP

Le BOAMP (Bulletin officiel des annonces des marchés publics, édité par la DILA) alimente deux epics de la V2 : la veille des annonces (E13) et les données d'attribution (E19). Le PRD classe l'hypothèse « les données du BOAMP sont exploitables automatiquement » comme non vérifiée.

**Ce qui suit a été vérifié par des appels réels à l'API le 18 novembre 2026.** La page officielle [Données ouvertes et API](https://www.boamp.fr/pages/donnees-ouvertes-et-api/) charge son contenu en JavaScript et n'a pas pu être lue automatiquement : les conditions d'usage ci-dessous viennent des réponses de l'API et de la mention de licence du site, et restent à relire sur cette page avant mise en service.

### Fonctionnement

Les données sont exposées par un portail Opendatasoft, via l'API **Explore v2.1** :

```
https://boamp-datadila.opendatasoft.com/api/explore/v2.1/catalog/datasets/boamp/records
```

| Point | Constat |
|---|---|
| Authentification | Aucune : accès anonyme en lecture, pas de clé |
| Format | JSON (`{"total_count": n, "results": [...]}`) |
| Fraîcheur | Les avis parus le jour même sont présents |
| Volume | ~1,73 million d'avis dans le jeu `boamp` |
| Quota | En-têtes `x-ratelimit-limit: 2000000` par jour, `x-ratelimit-remaining`, `x-ratelimit-reset` (minuit UTC) |
| Cache | `cache-control: max-age=60` |
| Pagination | `limit` ≤ 100 par appel ; `offset + limit` ≤ 10 000 |
| Gros volumes | `/exports/{csv,json,jsonl,parquet}` avec les mêmes filtres, sans plafond de pagination |
| Licence | Licence Ouverte Etalab 2.0 (mention du site) : réutilisation libre, avec mention de la source et de la date de mise à jour |

Les requêtes s'écrivent en ODSQL, par les paramètres `select`, `where`, `order_by`, `limit`, `offset` :

- recherche plein texte : `search(objet, "handicap")`, ou `search("80500000")` sur tous les champs ;
- égalité : `nature="APPEL_OFFRE"`, `type_marche="SERVICES"`, `code_departement="75"` ;
- dates : `dateparution>=date'2026-11-01'`, `datelimitereponse>=now()` ;
- combinaisons avec `and`, `or` et parenthèses.

Le catalogue contient aussi des jeux de test ou de recette (`*_qualification`, `*_preprod`, `*_recette`…) à ignorer, et deux jeux utiles : `liste-mots-descripteurs-boamp` (les 375 descripteurs) et `joue` (correspondance avec les références TED).

### Champs utiles

| Champ | Contenu | Usage dans APPAO |
|---|---|---|
| `idweb` | Identifiant de l'avis (ex. `26-110554`) | Clé de dédoublonnage |
| `objet` | Intitulé du marché | Affichage, recherche par mots-clés |
| `nomacheteur` | Acheteur | Affichage, préremplissage du dossier |
| `dateparution`, `datelimitereponse`, `datefindiffusion` | Dates | Tri, filtre des avis encore ouverts, date limite du dossier |
| `nature` | `APPEL_OFFRE`, `ATTRIBUTION`, `RECTIFICATIF`, `ANNULATION`, `PRE-INFORMATION`… | Distingue annonces (E13), attributions (E19) et rectificatifs (E15) |
| `famille` | `JOUE` (européen), `FNS` (90 k€ à seuils européens), `MAPA`, `DSP`, `DIVERS` | Indique la tranche de montant et le format de `donnees` |
| `type_marche` | `SERVICES`, `TRAVAUX`, `FOURNITURES` | Filtre : FrenchFlair ne vise que les services |
| `code_departement` | Départements de publication (liste) | Critère de zone |
| `descripteur_code`, `descripteur_libelle` | Mots-clés BOAMP (liste) | Critère de veille |
| `titulaire` | Attributaire(s), sur les avis d'attribution | E19 |
| `annonce_lie`, `annonces_anterieures` | `idweb` des avis liés | Relie un rectificatif ou une attribution à l'avis initial |
| `url_avis` | Lien vers l'avis sur boamp.fr | « Lien vers l'annonce » du dossier |
| `donnees` | **Chaîne JSON** contenant l'avis complet | CPV, lots, critères, montants, lien vers le DCE |

Le champ `donnees` doit être désérialisé, et sa structure change selon la famille et la version de schéma (`source_schema`) : formulaire national simplifié (`FNSimple`) pour les avis nationaux, **eForms** (arborescence UBL, `ContractNotice` ou `ContractAwardNotice`) pour les avis européens. Un même nœud est tantôt un objet, tantôt une liste, selon qu'il y a un ou plusieurs lots. Il faut un extracteur par format, tolérant aux champs absents.

### Exemple : annonces ouvertes (E13)

```bash
curl -G "https://boamp-datadila.opendatasoft.com/api/explore/v2.1/catalog/datasets/boamp/records" \
  --data-urlencode 'select=idweb,objet,nomacheteur,dateparution,datelimitereponse,code_departement,descripteur_libelle,url_avis' \
  --data-urlencode 'where=(search(objet,"handicap") or search(objet,"QVCT") or search(objet,"diversité")) and nature="APPEL_OFFRE" and type_marche="SERVICES" and datelimitereponse>=now()' \
  --data-urlencode 'order_by=dateparution desc' \
  --data-urlencode 'limit=100'
```

### Exemple : attributions comparables (E19)

```bash
curl -G "https://boamp-datadila.opendatasoft.com/api/explore/v2.1/catalog/datasets/boamp/records" \
  --data-urlencode 'select=idweb,objet,nomacheteur,titulaire,dateparution,annonce_lie,famille,donnees' \
  --data-urlencode 'where=nature="ATTRIBUTION" and search(objet,"handicap")' \
  --data-urlencode 'order_by=dateparution desc' \
  --data-urlencode 'limit=100'
```

Cette requête renvoyait 1 293 avis d'attribution le jour du test.

### Conséquences pour le produit

**E13 — Veille et import des annonces**

- **Faisable sans obstacle technique** : pas de clé, quota très large, données du jour. Une synchronisation quotidienne par profil de veille, filtrée sur `dateparution` depuis le dernier passage et dédoublonnée sur `idweb`, suffit.
- **Mots-clés, zones (US-13.1)** : couverts par `search()`, `code_departement` et les descripteurs.
- **Codes CPV (US-13.1)** : il n'existe pas de champ CPV filtrable. Le code se trouve dans `donnees` ; `search("80500000")` le retrouve en plein texte (52 avis depuis le 1er octobre 2026 lors du test), mais sans gérer la hiérarchie des codes. Un filtrage CPV exact doit se faire après récupération, côté APPAO.
- **Montants (US-13.1)** : pas de champ de montant estimé au premier niveau, et il est souvent absent de l'avis. `famille` donne seulement une tranche. Le critère de montant ne peut être qu'approximatif.
- **Bruit** : les mots-clés seuls ramènent des faux positifs. Sur le test, « handicap » renvoie aussi une assurance construction pour des travaux d'accessibilité et du transport d'usagers. Le filtre `type_marche="SERVICES"` et le tri humain (US-13.4, écarter une annonce) restent nécessaires.
- **Préremplissage du dossier (US-13.3)** : intitulé, acheteur, lien et date limite sont directement disponibles, ce qui couvre les champs minimaux de l'US-2.1.
- **Le DCE n'est pas dans l'API.** L'avis fournit au mieux un lien vers le profil acheteur (`urlDocConsul` dans `donnees` pour les avis nationaux), où le retrait demande souvent une identification. Le dépôt du DCE reste manuel (E2), y compris en V2.
- **Couverture** : le BOAMP ne contient pas tous les marchés. Les petits marchés à procédure adaptée peuvent n'être publiés que sur le profil de l'acheteur. Le PRD prévoit « autres sources à étudier » ; ce point pèsera si FrenchFlair vise surtout des marchés de faible montant.

**E15 — Modifications du DCE**

- Les avis rectificatifs et d'annulation sont publiés (`nature="RECTIFICATIF"`, `"ANNULATION"`) et reliés à l'avis initial par `annonce_lie`. La veille peut donc signaler qu'un dossier en cours a fait l'objet d'un rectificatif, ce que le PRD ne prévoit pas explicitement.

**E19 — Données d'attribution**

- **Acheteur, objet, attributaire, date, source (US-19.2)** : disponibles directement (`nomacheteur`, `objet`, `titulaire`, `dateparution`, `url_avis`).
- **Montant** : uniquement dans `donnees`, à extraire selon le format. Attention à sa nature : sur les accords-cadres, très fréquents dans les prestations visées, le montant publié est un **maximum de l'accord-cadre**, pas un prix payé ni un prix unitaire. L'afficher comme indication pour une ligne de BPU (US-19.4, règle 8) serait trompeur sans préciser ce qu'il représente. Le libellé du montant doit accompagner chaque valeur affichée.
- **Nombre d'offres reçues** : les avis d'attribution eForms le publient. C'est un fait publié, pas une estimation ; l'afficher ou non relève d'un arbitrage au regard de la règle 7 (pas d'estimation de concurrence).
- **Comparabilité** : mêmes limites que pour la veille (CPV non filtrable nativement, mots-clés bruités).

**Règles communes**

- Les données BOAMP sont des données publiques de couche D : elles ne parlent jamais au nom de FrenchFlair et sont toujours affichées avec leur source (`url_avis`) et leur date.
- La licence impose de mentionner la source et la date de mise à jour.
- Les appels au BOAMP se font côté serveur, comme toute opération (section 11.1 du PRD).

## Exigences non fonctionnelles

Obligatoires dès la V1 (section 11 du PRD) :

- **Sécurité** : aucun secret dans le code ni dans le dépôt ; aucun fichier en accès public (liens temporaires pour utilisateurs authentifiés) ; contrôle des droits côté serveur pour toute opération, traitements IA et recherches compris ; clé d'API Anthropic dans un coffre de secrets, utilisée côté serveur uniquement, jamais réaffichée ; revue de sécurité avant mise en service.
- **Traçabilité** : historique non modifiable par dossier ; sources et valideur de chaque texte ; décision GO/NO-GO motivée ; journal de chaque appel à l'API (dossier, étape, utilisateur, modèle, tokens, coût) ; journal des actions d'administration.
- **Maintenabilité** : un seul fournisseur de modèle d'IA (Anthropic), un seul chemin de traitement par fonction ; schéma de base reconstructible depuis le dépôt ; documentation suffisante pour une reprise par un autre développeur.

## Hors périmètre

Multi-entreprise ; profils configurables ; dépôt automatique sur les profils acheteurs ; génération au format FrenchFlair quand l'acheteur impose un cadre ; toute génération de prix, de score de succès ou d'estimation de concurrence par l'IA ; blocage des appels à l'API en cas de dépassement de budget ; suivi du temps par chronomètre ; calcul de facteurs de succès ou d'échec.

## Questions ouvertes

Reprises du PRD (section 14), elles conditionnent le démarrage du développement :

- Pile technique et hébergement, au regard des exigences de sécurité
- Qui développe et qui maintient la plateforme
- Premier préparateur et temps disponible ; détenteur du profil administrateur
- Critères de la grille GO/NO-GO ; marchés à cibler en priorité ; volume annuel visé
- Seuils de passage à la V2
- Sources réglementaires à surveiller ; archives à verser au démarrage
- Coûts horaires par profil ; budget mensuel de l'API et seuils d'alerte

Ajoutées par l'analyse du BOAMP :

- Relire les conditions d'usage sur la page officielle du BOAMP (non lisible automatiquement)
- Quelles sources compléter pour les marchés absents du BOAMP ?
- Afficher ou non le nombre d'offres reçues publié dans les avis d'attribution ?
- Comment libeller les montants d'accords-cadres pour qu'ils ne soient pas lus comme des prix ?
