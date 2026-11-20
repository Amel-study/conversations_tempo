# Compte rendu ListeCommune : plan, consignes et infos

Préparé par Claude 1 (assistant d'Amel) pour Claude 2 (assistant de Coura).

## 1. Les consignes du prof (Yves Benabou)

- **Même plan que la soutenance, sans la démo physique, mais en zoomant** : on détaille chaque partie.
- **Un lien vers le dépôt GitHub du projet** : https://github.com/couraba-web/MyApplication
  - Le dépôt est **privé** : **Coura (propriétaire) doit inviter le prof**, `yvesbenabou`, dans *Settings → Collaborators → Add people*. Sinon il ne pourra pas lire le code.
  - Le prof va **analyser le code** sur GitHub.
- **À rendre au plus tard une semaine après la soutenance** (soutenance les 23 et 24 novembre, donc le 30 novembre au plus tard).
- **Le style ne doit pas faire « IA »** : le prof veut le style des étudiants, moins formel, pas le « style Claude ». Il a cité le mot « **métier** », qu'il n'aime pas : ne jamais l'utiliser.
- Les 6 parties de la soutenance, telles que le prof les a données :
  1. pourquoi ce projet (motivation, besoin) ;
  2. **l'architecture** : vue d'ensemble indépendante du code, schéma-bloc avec des responsabilités qui ont des conséquences sur d'autres. Ce n'est pas le storyboard. **Très important**, beaucoup de questions ;
  3. **la distribution des responsabilités** : pourquoi chacun a pris ce qu'il a pris, comment le projet a été piloté. **Très important** ;
  4. le journal de bord : les problèmes techniques ou d'organisation qui ont pris le plus de temps, et la solution ou le contournement trouvé ;
  5. la démo (pas dans le rapport) ;
  6. et la suite : oui ou non, mais argumenté.
- Le prof parle aussi de **l'arbre des écrans** (mail 3) : tous les écrans forment un arbre, et **aucune feuille n'est tolérée**, c'est-à-dire aucun écran sans retour possible.

## 2. Comment Claude 2 doit aider Coura

**Coura écrit le rapport avec ses propres mots.** Claude 2 ne rédige pas les paragraphes à sa place.

Pour chaque partie, dans l'ordre :
1. Claude 2 explique à Coura **ce que la partie doit contenir** : les idées, les faits, les chiffres, et **quelles images mettre** (le nom du fichier dans `compte_rendu/images/`).
2. **Coura écrit** la partie elle-même.
3. Claude 2 **relit** : il vérifie que les faits sont justes, signale ce qui manque ou ce qui n'est pas clair, et pose des questions. Il **ne réécrit pas** ses phrases et ne rend pas son style plus formel. Il peut signaler une faute d'orthographe, mais sans transformer le texte.
4. Ensuite seulement, on passe à la partie suivante.

Claude 2 doit aussi vérifier à chaque partie que **Coura comprend ce qu'elle écrit** (surtout l'architecture et la partie technique du journal de bord), car le prof peut poser des questions.

**Le style à éviter** : le mot « métier », les tirets longs, les listes de trois adjectifs, les formules du type « il est important de noter que », « en conclusion, ce projet nous a permis de… », les titres trop travaillés. Mieux vaut des phrases simples, au « on ».

**Amel** relira les parties 2 et 4 (techniques) et écrira sa partie de la conclusion personnelle. Si Claude 2 a besoin d'une info, d'une capture ou d'une relecture, il l'écrit dans `conversation.md`.

## 3. Le plan proposé

Environ 12 à 15 pages, images comprises.

### Page de garde
Liste Commune, « la liste de courses partagée en temps réel », IHM Android 2026/2027, BA Coura & AGA Amel, E4FE, M. Benabou, ESIEE Paris, **lien GitHub**.

### Introduction (une demi-page)
L'appli en deux phrases, le cadre (projet de groupe en Java sous Android Studio), et l'annonce du plan.

### 1. Pourquoi ce projet ?
- **Le besoin** : Coura en colocation (chacun fait ses courses de son côté, produits achetés en double, « qui a payé quoi ? »). Amel fait les courses pour sa famille (la liste passe par SMS ou sur papier, oublis au magasin, on ne sait pas ce qui est déjà acheté).
- **L'idée** : une seule liste partagée, à jour en direct sur le téléphone de chacun.
- **Prévu dans le synopsis** : comptes (inscription/connexion), plusieurs listes par personne, articles avec quantité et case à cocher, partage d'une liste par un code, mise à jour en temps réel.
- **Ajouté en cours de route** : discussion dans chaque liste, membres et rôles (créateur, éditeur, lecteur), renommer/supprimer une liste, budget partagé (qui doit combien), répartition (qui s'occupe de quel article), mot de passe oublié, œil pour afficher le mot de passe, majuscule automatique au clavier.
- Images : `mes_listes.png`, `detail_liste.png`.

### 2. Architecture (la partie la plus importante)
**2.1 Vue d'ensemble** (`diapo5_vue_ensemble.png`)
- Au centre, Firebase (service en ligne de Google) avec deux blocs : **les comptes** (Firebase Authentication, qui est qui) et **les données partagées** (Cloud Firestore : listes, articles, messages).
- Sur chaque téléphone : **les écrans** et une **copie locale** des données, gérée par Firestore, qui permet de marcher hors ligne. Les changements faits sans réseau partent quand le réseau revient.
- Les téléphones ne se parlent jamais directement : tout passe par Firebase.

**2.2 Les responsabilités en chaîne** (`schema_chaine.png`, `ecran_lecteur.png`, `detail_liste.png`)
- 1. Le compte donne une identité unique → sans compte, aucune liste n'est accessible.
- 2. Être membre d'une liste décide des listes visibles → quand on quitte une liste, elle disparaît de « Mes listes ».
- 3. Le rôle dans la liste (créateur, éditeur ou lecteur) → seul le créateur modifie la liste (renommer, supprimer) et les rôles.
- 4. Les actions à l'écran (ajouter, cocher, partager) dépendent du rôle → passé lecteur, l'écran se verrouille **en direct** (plus de champ d'ajout, cases non cochables).
- C'est exactement ce que demande le prof : une responsabilité qui a des conséquences sur les suivantes.

**2.3 Les données et le temps réel** (`schema_donnees.png`)
- `users` : pseudo, email.
- `listes` : nom, code (6 caractères), créateur, membres, rôles. Un membre sans rôle enregistré est éditeur par défaut.
  - sous-collection `articles` : nom, quantité, coché, prix, payé par, responsable (« pour qui »).
  - sous-collection `messages` : texte, auteur, pseudo, date.
- Le temps réel, en 4 étapes : Coura coche « Lait » → la base enregistre → elle prévient tous les téléphones qui écoutent la liste → l'écran d'Amel se met à jour tout seul.
- Dans le code : un écouteur (`addSnapshotListener`) posé dans `onStart` de l'écran.

**2.4 L'arbre des écrans** (images d'écrans, voir la section 5)
```
Accueil
├── Connexion ──► Mot de passe oublié
├── Inscription
└──► Mes listes
      ├── Créer une liste
      ├── Rejoindre une liste (fenêtre avec le code)
      ├── Paramètres ──► déconnexion, retour à l'Accueil
      └── Détail d'une liste
            ├── Discussion
            ├── Budget
            └── menu : voir le code, membres, renommer, supprimer, quitter, mes articles
```
Chaque écran a un retour possible (pas de feuille, comme le demande le prof). On peut le comparer au storyboard de départ (les maquettes de Coura) et dire ce qui a changé.

**2.5 Deux fonctionnalités en détail**
- **Le budget** : on additionne le prix des articles cochés → on divise par le nombre de membres pour avoir la part → pour chacun, solde = payé − part → ceux qui ont un solde négatif remboursent ceux qui ont un solde positif. Exemple réel de la liste « Courses de la semaine » : total 12 €, part 6 €, Amel a payé 7 € (tomates 3,50, pain 1,20, lait 2,30), Coura 5 € (riz 1,90, yaourts 3,10), donc **Coura doit 1 € à Amel**. Limite : le calcul est fait à l'ouverture de l'écran, il n'est pas en temps réel.
- **La répartition** : un clic sur un article → « Qui s'en occupe ? » → « Pour : Coura » s'affiche sous l'article, et le filtre « Mes articles » n'affiche que les siens.
- Images : `menu_liste.png`, `voir_membres.png`, `changer_role.png`.

### 3. Responsabilités
**3.1 Qui a fait quoi**
- **Coura** : recherche d'idées et choix du sujet, storyboard et maquettes (~80 %), organisation (relecture et fusion des pull requests), écrans Mes listes, mot de passe oublié, œil, clavier en majuscules, présentation et rapport (~80 %).
- **Amel** : Firebase (comptes et base de données), les fonctionnalités (listes, partage, discussion, rôles, budget, répartition), les tests sur son téléphone, un coup de main sur le storyboard, la présentation et le rapport.

**3.2 Pourquoi ce partage**
- Coura est plus à l'aise pour organiser et structurer les idées, et le besoin de départ vient de sa colocation : elle porte le projet et l'interface.
- Amel avait déjà utilisé un outil proche de Firebase dans un projet perso. La base de données était le bloc le plus risqué techniquement (comptes, temps réel), donc on l'a confiée à celui qui le connaissait déjà.
- **Le pivot** : en cours de route, on a ajusté selon les disponibilités. Amel a repris des écrans prévus pour Coura (par exemple « Créer une liste »), et Coura a pris en charge la présentation et le rapport. Le prof parle justement de ces « pivots de responsabilités » dans son mail : c'est bien de l'assumer.

**3.3 La méthode** (`schema_branches.png`)
- GitHub, une branche par fonctionnalité, une pull request relue par l'autre avant la fusion dans `master`.
- 19 pull requests, d'octobre au 18 novembre (voir la liste en annexe).
- Le graphique : Amel en rose, Coura en bleu. La couleur du point sur master montre qui a relu. Quand Coura était moins disponible, Amel a fusionné lui-même certaines de ses PR, avec son accord et après les avoir testées (#6 à #9, puis #15 à #19).
- Un fichier de suivi pour savoir où en était chaque branche.

### 4. Journal de bord
Pour chaque problème : ce qui s'est passé, combien de temps ça a bloqué, comment on a trouvé, la solution. Images : `problemes_techniques.png`, `problemes_organisation.png`.

**Techniques**
1. **Versions d'Android Studio** : Amel était sur une version Canary (de test), et Coura sur la version stable. Le projet ne s'ouvrait pas chez l'autre (version du plugin Gradle). Solution : on est passés tous les deux sur Android Studio Quail 4 (2026.1.4 Patch 1, stable), et on a remis le projet en AGP 9.4.1 (PR #1 `agp-9.4.1`).
2. **Pas de téléphone, émulateur trop lent** : impossible de tester correctement. Solution : on a testé sur un vrai téléphone Android branché au PC, comme le prof le conseillait.
3. **Téléphone sans Internet** : « Inscription impossible » et bouton Valider bloqué. On a trouvé la cause dans les messages d'erreur d'Android Studio (Logcat). En plus, l'appli attendait la réponse du serveur avant de continuer : on l'a changée pour qu'elle n'attende plus.
4. **Listes figées** : en revenant sur un écran, les données ne se mettaient plus à jour. L'écoute de Firebase n'était lancée qu'une fois, à la création de l'écran. Solution : la lancer dans `onStart`, appelée à chaque retour sur l'écran.

**Organisation**
1. **Disponibilités différentes** : la branche « créer une liste » de Coura n'avançait pas. On a redistribué : Amel l'a refaite (`creer-liste`, PR #5), Coura a pris la présentation et le rapport.
2. **Conflits Git** : on modifiait les mêmes fichiers (`strings.xml`, `AndroidManifest.xml`). Exemple : les PR #10 (mot de passe oublié) et #11 (discussion) ajoutaient des lignes au même endroit. Solution : garder les deux côtés, et fusionner une PR à la fois.
3. **Branches dépendantes** : les dernières branches étaient construites les unes sur les autres (membres → rôles → paramètres de la liste → budget → répartition). Solution : un ordre de fusion fixé à l'avance.
4. **Commit sur la mauvaise branche** : du travail s'est retrouvé sur la branche d'une autre fonctionnalité. Solution : toujours vérifier la branche en bas à droite d'Android Studio avant de commiter.

### 5. L'appli en images (remplace la démo)
Le parcours de la démo, avec une capture par étape (`etapes_demo.png` donne l'ordre) : connexion → Mes listes → créer une liste et son code → ajouter et cocher un article avec un prix → le budget → membres et rôles (éditeur / lecteur) → la discussion.

### 6. Et la suite ?
(`et_la_suite.png`)
- **Oui, mais pour nous et nos proches** : utile au quotidien (famille, coloc, soirées), c'est la nôtre (pas de pub, on peut l'adapter à ce que nos proches demandent), et savoir qui a payé quoi évite les oublis.
- **Mais pas pour la vendre** : face aux grosses applis, il faudrait une équipe, du temps et de la pub. Firebase est gratuit pour un petit groupe (plan Spark) mais devient payant avec beaucoup d'utilisateurs. Et gérer les données d'inconnus implique le RGPD, la sécurité et le support.
- **À améliorer pour nos proches** : sécuriser la base (aujourd'hui, les rôles ne sont vérifiés que dans l'appli, pas dans les règles Firestore), les notifications, un budget en temps réel, et une installation simple (partager directement le fichier de l'appli, sans Play Store).

### Conclusion personnelle
Quelques lignes chacun, avec ses propres mots : ce qu'on a appris, ce qui a été dur, ce qu'on referait autrement. Coura écrit la sienne, Amel écrit la sienne.

### Annexes
- Le lien GitHub.
- La liste des 19 pull requests (ci-dessous).
- La structure Firestore (section 2.3).

## 4. Les 19 pull requests

| # | Branche | Auteur | Relu et fusionné par | Période |
|---|---|---|---|---|
| 1 | agp-9.4.1 | Amel | Coura | 3 nov. |
| 2 | mes-listes | Coura | Coura | 3 nov. |
| 3 | firebase-auth | Amel | Coura | 3 nov. |
| 4 | database | Amel | Coura | 3 nov. |
| 5 | creer-liste | Amel | Coura | 3 nov. |
| 6 | afficher-listes | Amel | Amel | 13 nov. |
| 7 | rejoindre-liste | Amel | Amel | 13 nov. |
| 8 | parametres | Amel | Amel | 13 nov. |
| 9 | detail-liste | Amel | Amel | 13 nov. |
| 10 | mot-de-passe-oublie | Coura | Amel | 13 nov. |
| 11 | chat-liste | Amel | Coura | 16-18 nov. |
| 12 | oeil-mot-de-passe | Coura | Amel | 16-18 nov. |
| 13 | clavier-majuscules | Coura | Amel | 16-18 nov. |
| 14 | membres-listes | Amel | Coura | 16-18 nov. |
| 15 | roles-liste | Amel | Amel (approuvée par Coura) | 16-18 nov. |
| 16 | parametres-liste | Amel | Amel | 16-18 nov. |
| 17 | budget | Amel | Amel | 16-18 nov. |
| 18 | repartition-articles | Amel | Amel | 16-18 nov. |
| 19 | nettoyage | Amel | Amel | 16-18 nov. |

Avant les PR (octobre) : Coura a créé le projet, puis Amel a poussé les premiers écrans (accueil, connexion, inscription) directement sur `master`.

## 5. Les fichiers dans `compte_rendu/`

- `support_soutenance.pdf` : les 15 diapos de la soutenance. **C'est la base du rapport.**
- `texte_soutenance.docx` : ce que chacun dit à l'oral, diapo par diapo, avec les questions possibles du jury. Il contient beaucoup de détails réutilisables (à reformuler par Coura, pas à copier).
- `images/` :

| Fichier | Contenu | Partie |
|---|---|---|
| `mes_listes.png` | écran Mes listes (cartes) | 1, 5 |
| `detail_liste.png` | « Courses de la semaine » vue par un éditeur | 1, 2.2, 5 |
| `diapo5_vue_ensemble.png` | schéma-bloc de l'architecture (diapo 5) | 2.1 |
| `schema_chaine.png` | les responsabilités en chaîne | 2.2 |
| `ecran_lecteur.png` | la même liste vue par un lecteur (verrouillée) | 2.2 |
| `schema_donnees.png` | structure des données + temps réel en 4 étapes | 2.3 |
| `menu_liste.png` | le menu ≡ d'une liste | 2.5 |
| `voir_membres.png` | la liste des membres et leur rôle | 2.5 |
| `changer_role.png` | le créateur change le rôle d'un membre | 2.5 |
| `schema_branches.png` | graphique des 19 branches (Amel rose, Coura bleu) | 3.3 |
| `problemes_techniques.png` | les 4 problèmes techniques | 4 |
| `problemes_organisation.png` | les 4 problèmes d'organisation | 4 |
| `etapes_demo.png` | les 6 étapes du parcours | 5 |
| `et_la_suite.png` | oui / pas pour la vendre / à améliorer | 6 |

**Captures qui manquent** (Coura n'a pas accès au téléphone) : connexion, inscription, mot de passe oublié, créer une liste, rejoindre une liste, discussion, budget, « Qui s'en occupe ? », Mes articles, paramètres. **Claude 1 les ajoutera dans `images/`** dès qu'Amel branche son téléphone. Si Claude 2 a besoin d'une autre capture, il la demande dans `conversation.md`.

Les maquettes du storyboard : Coura les a déjà.
