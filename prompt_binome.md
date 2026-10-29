Salut Claude. Je suis étudiant(e) en 2e année d'école d'ingénieur (ESIEE Paris, systèmes embarqués). Je travaille en binôme sur un projet Android noté pour la matière IHM Android. Mon binôme avance déjà sur le projet avec Claude Code, et je reprends le travail de mon côté. Lis tout ce message avant de faire quoi que ce soit.

## Le projet

- **ListeCommune** : une liste de courses partagée en temps réel entre plusieurs personnes (colocation, famille, soirée).
- **Fonctionnalités prévues** :
  - comptes (Firebase Authentication)
  - plusieurs listes par personne
  - articles avec quantité et case à cocher
  - partage d'une liste par un code
  - synchronisation en temps réel (Cloud Firestore)
- **Langage** : Java obligatoire, pas de Kotlin ni de Jetpack Compose. Interface en XML.
- **Outils** : Android Studio Quail 4 (2026.1.4). Tests sur un vrai téléphone Android, le prof déconseille l'émulateur.
- **Dépôt GitHub (privé)** : https://github.com/couraba-web/MyApplication. Je suis le/la propriétaire (`couraba-web`), mon binôme est `Amel-study`.
- **À la fin** : une soutenance et un rapport. Je dois pouvoir expliquer chaque ligne du code.

## Ce qui existe déjà sur `master`

- **`MainActivity` + `activity_main.xml`** : l'écran d'accueil, avec la roue des paramètres, le titre « Liste Commune. », une icône profil et les boutons Connexion / Inscription. Chaque bouton ouvre son écran avec un Intent explicite.
- **`ConnexionActivity` + `activity_connexion.xml`** :
  - champs Identifiant et Mot de passe
  - `setError` si un champ est vide
  - sinon un Toast « Pas encore fait » (il y a un `// TODO : verifier avec Firebase`)
- **`InscriptionActivity` + `activity_inscription.xml`** :
  - champs Pseudo, Mail, Mot de passe et Confirmation
  - vérifie les champs vides, la longueur minimale de 6 caractères et que les deux mots de passe sont identiques
  - puis un Toast (`// TODO : creer le compte avec Firebase`)
- **`res/values/colors.xml`** : `fond` #E1E7DE, `bouton` #B8C0B5, `texte` #4B5240, `icone` #6A736A.
- **`res/values/strings.xml`** : tous les textes de l'appli, y compris `pas_encore_fait`.
- **`res/drawable`** :
  - `ic_settings.xml` et `ic_compte.xml` (icônes Material)
  - `fond_champ.xml` (rectangle arrondi pour les champs)
- **Package** : `com.example.myapplication`. On ne le renomme pas pour l'instant.
- **Versions** : AGP `9.4.0-alpha07`, dans `gradle/libs.versions.toml`. C'est la version maximale supportée par Android Studio Quail 4. **Ne la change pas**, même si Android Studio propose une mise à jour. S'il refuse de synchroniser, signale-le-moi au lieu de modifier la version.

Les maquettes (9 écrans) suivent toutes le même style : fond vert-gris clair, la roue en haut à gauche et le titre « Liste Commune. » en gras, blocs gris-vert arrondis, texte vert foncé.

## Ma tâche : la branche `mes-listes` (premier commit simple)

Je crée l'écran **« Mes listes » vide**. C'est l'écran qu'on voit après la connexion quand on n'a encore aucune liste :
1. **Créer `MesListesActivity.java` et `activity_mes_listes.xml`** :
   - la même barre du haut que les autres écrans (roue + titre), à recopier depuis `activity_connexion.xml` ;
   - un texte centré du genre « Aucune liste pour l'instant » (dans `strings.xml`) ;
   - un **bouton rond « + »** en bas au centre (`FloatingActionButton` de Material, déjà disponible via la dépendance `material`), couleur `@color/icone` avec le + blanc ;
   - au clic sur le +, un Toast avec `@string/pas_encore_fait`. La fenêtre « Créer une liste » viendra dans une autre branche.
2. **Déclarer l'Activity** dans `AndroidManifest.xml`, comme `ConnexionActivity`.
3. **Pour pouvoir tester** : dans `ConnexionActivity`, remplacer **uniquement** la ligne du Toast (celle sous le `// TODO`) par un Intent qui ouvre `MesListesActivity`. Garder le `// TODO` : mon binôme branchera Firebase à cet endroit plus tard. Ne rien changer d'autre dans ce fichier.

Ne fais rien de plus (pas de RecyclerView, pas de Firebase, pas de base de données). Ce sera pour les prochaines branches.

## Règles très importantes

### 1. Le code doit avoir l'air écrit par un étudiant de 2e année

- **Code simple**, avec uniquement les notions vues en cours : Activity, Intent explicite, `findViewById`, `setOnClickListener` avec une classe anonyme `new View.OnClickListener() { ... }`, Toast, `LinearLayout`.
- **Même style que les fichiers existants.** Lis `ConnexionActivity.java` et `activity_connexion.xml` avant d'écrire quoi que ce soit, et recopie leur façon de faire : mêmes noms de variables en français, même structure, même bloc `EdgeToEdge` / `setOnApplyWindowInsetsListener`.
- **Commentaires rares et courts**, en français, comme `<!-- barre du haut -->` ou `// TODO : ...`. Pas de Javadoc, pas de longs commentaires explicatifs.
- **Pas d'architecture compliquée** : pas de MVVM, de ViewModel, de LiveData, de fragments, de lambdas inutiles ni de classes utilitaires.
- **Tous les textes dans `strings.xml`**, toutes les couleurs dans `colors.xml`. Ajoute `android:contentDescription="@null"` sur les images décoratives.

### 2. Tu ne fais JAMAIS de commit, de push ni de pull toi-même

- **Tu modifies les fichiers, c'est moi qui fais toutes les opérations Git** dans Android Studio. Explique-moi chaque étape, sans lancer de commande Git toi-même.
- **Aucune trace d'IA dans le dépôt** : pas de `Co-Authored-By`, pas de fichier `CLAUDE.md`, pas de dossier `.claude/` dans le projet, aucune mention de Claude ou d'IA dans le code ou les messages de commit.
- **Ne jamais commiter les fichiers `.idea/`** : rappelle-moi de les laisser décochés.

### 3. Je dois tout comprendre

À la fin, explique-moi simplement chaque fichier modifié ou créé et ce que fait chaque partie du code, pour que je puisse le réexpliquer à l'oral.

## Le déroulé que j'attends de toi

1. **Vérifie l'état de mon projet** : le dossier du projet, la branche actuelle, et si je dois récupérer les commits de mon binôme. Les derniers commits sur GitHub sont « Ecran d'accueil… », « Ecran de connexion… » et « Ecran d'inscription ». Dis-moi aussi si mon nom Git est mal configuré : mes anciens commits apparaissent sous le nom « MyApllication », je dois mettre mon vrai nom (Settings → Version Control → Git, ou `git config --global user.name`).
2. **Explique-moi comment récupérer `master` à jour** dans Android Studio : Ctrl+T, ou Git → Pull.
3. **Explique-moi comment créer la branche `mes-listes`** depuis `master` : clic sur `master` en haut de la fenêtre, puis New Branch.
4. **Code l'écran** en respectant les règles ci-dessus.
5. **Dis-moi comment le tester** sur mon téléphone : mode développeur, débogage USB, puis ▶ Run.
6. **Explique-moi le code.**
7. **Guide-moi pour le commit et le push** :
   - Ctrl+K, cocher uniquement les fichiers du projet (pas `.idea`), message court en français, par exemple « Ecran mes listes (vide) avec bouton + » ;
   - Commit and Push. Vérifie que le push part bien sur `origin/mes-listes` et pas sur `master`.
8. **Explique-moi comment ouvrir la Pull Request** `mes-listes` → `master` sur GitHub, pour que mon binôme la relise avant le merge.

On a décidé ensemble de ces règles d'équipe :
- **une branche par thème** (`firebase-auth`, `mes-listes`, `creation-liste`, `detail-liste`, `database`, `partage`, `parametres`) ;
- **une Pull Request** pour fusionner dans `master` ;
- **jamais de push direct sur `master`** ;
- **Ctrl+T avant de commencer.**
