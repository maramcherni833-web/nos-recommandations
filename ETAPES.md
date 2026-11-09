# Guide pas à pas : apprendre Git et GitHub à deux

Dans ce guide, les deux personnes sont **Personne A** (celle qui crée le dépôt) et **Personne B**.
Remplacez `NOM-UTILISATEUR` et `NOM-DU-DEPOT` par vos vraies valeurs.

---

## Étape 0 : Préparer son poste (A et B)

1. Installer Git : https://git-scm.com/downloads
2. Créer un compte GitHub : https://github.com
3. Se présenter à Git (une seule fois) :

```bash
git config --global user.name "Ton Prénom"
git config --global user.email "ton-email@exemple.com"
git config --global init.defaultBranch main
```

4. Vérifier :

```bash
git --version
git config --list
```

5. Choisir comment s'authentifier à GitHub : HTTPS (avec un token ou GitHub Desktop / Git Credential Manager) ou SSH. Le plus simple pour débuter est HTTPS avec le gestionnaire d'identifiants installé avec Git.

---

## Étape 1 : Créer le dépôt (Personne A)

1. Sur GitHub : **New repository** → nom `nos-recommandations`, **Public**, cocher **Add a README**.
2. Aller dans **Settings → Collaborators → Add people** et inviter Personne B.
3. **Personne B** accepte l'invitation (email ou notification GitHub).

## Étape 2 : Cloner le dépôt (A et B)

```bash
git clone https://github.com/NOM-UTILISATEUR/nos-recommandations.git
cd nos-recommandations
git status
git log --oneline
```

## Étape 3 : Le premier commit sur `main` (Personne A seulement)

C'est la seule fois où l'on pousse directement sur `main` : pour poser les fichiers de départ.

1. Copier dans le dossier les fichiers `index.html`, `style.css`, `README.md`, `ETAPES.md` (le README du projet remplace celui créé par GitHub).
2. Puis :

```bash
git status
git add .
git commit -m "Ajoute les fichiers de départ du site"
git push origin main
```

3. **Personne B** récupère le travail :

```bash
git pull origin main
```

## Étape 4 : Protéger `main` (Personne A)

Sur GitHub : **Settings → Branches → Add branch ruleset** (ou *Add rule*) sur `main` :
- Cocher **Require a pull request before merging**
- Mettre **1 approval** requis

À partir de maintenant, plus personne ne peut pousser directement sur `main`.

> Si l'option est limitée sur un dépôt privé en compte gratuit, gardez le dépôt public ou appliquez la règle « pas de push sur main » par discipline.

---

## Étape 5 : Sa première branche et sa première carte (A et B, chacun de son côté)

```bash
git switch main
git pull
git switch -c ajout-carte-prenom
```

1. Ouvrir `index.html` et **copier le modèle de carte** (en commentaire) dans votre zone (« CARTES DE LA PERSONNE A » ou « B »). Remplir titre, avis, auteur.
2. Vérifier dans le navigateur.
3. Commiter :

```bash
git status
git diff
git add index.html
git commit -m "Ajoute la carte du film X"
git push -u origin ajout-carte-prenom
```

`-u` ne sert que la première fois : il relie votre branche locale à la branche distante.

## Étape 6 : La Pull Request (A et B)

1. Sur GitHub, cliquer sur **Compare & pull request** (bandeau jaune).
2. Titre clair, description courte de ce qui change.
3. Dans **Reviewers**, choisir l'autre personne.
4. **L'autre personne relit** : onglet *Files changed*, laisser au moins un commentaire sur une ligne (clic sur le « + » à gauche de la ligne), puis **Review changes → Approve**.
5. Cliquer sur **Merge pull request**, puis **Delete branch**.
6. En local, se remettre à jour :

```bash
git switch main
git pull
git branch -d ajout-carte-prenom
```

Faites cette boucle **deux fois chacun** avec de nouvelles cartes avant de passer à la suite.

---

## Étape 7 : Provoquer un conflit volontaire (le plus formateur)

Objectif : modifier **la même ligne** chacun dans sa branche, puis résoudre le conflit.

**A et B** partent tous les deux de `main` à jour :

```bash
git switch main
git pull
```

**Personne A** :

```bash
git switch -c titre-version-a
```
Dans `index.html`, changer le `<h1>` en `Nos coups de cœur`.
```bash
git add index.html
git commit -m "Change le titre en Nos coups de coeur"
git push -u origin titre-version-a
```
Ouvrir la PR et la **merger** (B l'approuve).

**Personne B** (sans avoir fait `git pull` entre-temps) :

```bash
git switch -c titre-version-b
```
Dans `index.html`, changer le même `<h1>` en `Notre sélection`.
```bash
git add index.html
git commit -m "Change le titre en Notre selection"
git push -u origin titre-version-b
```
Ouvrir la PR : GitHub indique **This branch has conflicts**.

**Résolution (Personne B), en local** :

```bash
git switch main
git pull
git switch titre-version-b
git merge main
```

Git affiche `CONFLICT (content): Merge conflict in index.html`. Ouvrir le fichier :

```html
<<<<<<< HEAD
<h1>Notre sélection</h1>
=======
<h1>Nos coups de cœur</h1>
>>>>>>> main
```

1. Choisir la version finale (ou en écrire une troisième, ex. `Nos coups de cœur à deux`).
2. **Supprimer les lignes** `<<<<<<<`, `=======`, `>>>>>>>`.
3. Terminer :

```bash
git add index.html
git commit -m "Résout le conflit sur le titre"
git push
```

La PR est maintenant mergeable : l'autre relit et merge.

> Astuce : `git status` vous dit toujours où vous en êtes pendant un conflit. Pour annuler et recommencer : `git merge --abort`.

---

## Étape 8 : Les issues et le tableau de projet (A et B)

1. Sur GitHub, onglet **Issues → New issue**. Créez ensemble au moins ces issues :
   - `Ajouter un mode sombre`
   - `Ajouter un filtre par catégorie (JavaScript)`
   - `Ajouter un champ note sur 5 étoiles`
   - `Rendre le site plus beau sur mobile`
   - `Ajouter une page « À propos »`
2. **Assignez** chaque issue à l'un de vous deux, ajoutez des **labels** (`enhancement`, `bug`, `good first issue`).
3. Optionnel : onglet **Projects** → créer un tableau Kanban (To do / In progress / Done).
4. Travailler une issue = une branche :

```bash
git switch main
git pull
git switch -c feature-mode-sombre
```

5. Dans le message de commit ou la description de PR, écrire **`Fixes #3`** (numéro de l'issue) : l'issue se fermera toute seule au merge.

```bash
git commit -m "Ajoute le mode sombre, Fixes #3"
```

---

## Étape 9 : Mettre le site en ligne avec GitHub Pages (A)

1. Sur GitHub : **Settings → Pages**.
2. **Source : Deploy from a branch**, branche `main`, dossier `/ (root)`, **Save**.
3. Après 1 à 2 minutes, le site est disponible à :

`https://NOM-UTILISATEUR.github.io/nos-recommandations/`

4. Mettre le lien dans le README (par PR, bien sûr).

Désormais, chaque merge sur `main` met le site à jour automatiquement.

---

## Étape 10 : Les commandes utiles du quotidien

### Voir l'historique

```bash
git log --oneline --graph --all
git log --oneline -5
git show <id-du-commit>
git blame index.html
```

### Mettre de côté un travail en cours (`stash`)

Vous avez des modifications non commitées et devez changer de branche :

```bash
git stash
git switch autre-branche
# ... on fait autre chose ...
git switch branche-initiale
git stash pop
```

### Annuler proprement

```bash
git restore index.html            # annule les modifs non ajoutées d'un fichier
git restore --staged index.html   # retire un fichier du prochain commit
git commit --amend -m "Nouveau message"   # corrige le DERNIER commit (pas encore poussé)
git revert <id-du-commit>         # crée un commit qui annule un ancien commit (sans réécrire l'historique)
```

### Récupérer les changements des autres sans les fusionner tout de suite

```bash
git fetch
git status
git pull
```

### Bonus : rebase (pour un historique propre)

Au lieu de `git merge main` dans votre branche :

```bash
git switch ma-branche
git fetch origin
git rebase origin/main
git push --force-with-lease
```

À ne faire **que sur vos propres branches**, jamais sur `main`.

### Bonus : étiquette de version

```bash
git tag -a v1.0 -m "Première version en ligne"
git push origin v1.0
```

---

## Étape 11 : Défis finaux (à deux)

- [ ] 10 cartes au total sur le site
- [ ] Un conflit résolu sans aide
- [ ] Une PR avec au moins 3 commentaires de relecture
- [ ] Une issue fermée automatiquement avec `Fixes #N`
- [ ] Un `git revert` effectué sur un vrai commit
- [ ] Un `git stash` utilisé en conditions réelles
- [ ] Site en ligne sur GitHub Pages
- [ ] Un filtre JavaScript par catégorie ajouté (issue + branche + PR)
- [ ] Un tag `v1.0` publié

---

## Mémo : le cycle de travail à retenir

```
git switch main  →  git pull  →  git switch -c ma-branche
→ modifier  →  git add  →  git commit  →  git push -u origin ma-branche
→ Pull Request  →  relecture par l'autre  →  merge  →  git switch main  →  git pull
```

## Si vous êtes bloqués

- `git status` : toujours la première commande à lancer.
- Ne paniquez pas : presque tout se rattrape avec `git reflog`, qui liste tout ce que vous avez fait.
- Ne faites jamais `git push --force` sur `main`.
