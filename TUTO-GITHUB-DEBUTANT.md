# Tuto GitHub pour Michael 👋

Bienvenue dans PROJECT-M&M ! Ce doc te fait passer de **"je sais pas ce que c'est"** à **"je peux bosser avec Manu sur un projet"** en 30 minutes.

Lis dans l'ordre. Prends ton temps. Copie-colle les commandes.

---

## 🗺️ Sommaire

1. [Les mots qui font peur (et ce qu'ils veulent dire)](#1-les-mots-qui-font-peur)
2. [Installer les outils](#2-installer-les-outils)
3. [Créer et connecter ta clé SSH](#3-créer-et-connecter-ta-clé-ssh)
4. [Récupérer un projet sur ton ordi (clone)](#4-récupérer-un-projet-clone)
5. [Le workflow quotidien](#5-le-workflow-quotidien)
6. [Ouvrir une Pull Request](#6-ouvrir-une-pull-request-pr)
7. [Les pièges du débutant](#7-les-pièges-classiques)
8. [Cheatsheet : les commandes que tu utiliseras tous les jours](#8-cheatsheet)

---

## 1. Les mots qui font peur

Imagine que **Git** c'est comme Google Docs, mais **pour du code**, et **en mieux**.

| Mot | C'est quoi | Analogie |
|---|---|---|
| **Repo** (repository) | Un projet. Un dossier qui contient tout le code + l'historique de tous les changements. | Un Google Doc |
| **Commit** | Une sauvegarde d'un changement, avec un message qui décrit ce que tu as changé. | Ctrl+S, mais avec un petit mot explicatif |
| **Branche** (branch) | Une "copie parallèle" du projet où tu peux bosser sans toucher au vrai projet. | Mode brouillon |
| **`main`** | La branche principale, celle qui est "le vrai projet officiel". | La version publique du Google Doc |
| **Push** | Envoyer tes commits de ton ordi vers GitHub. | Upload |
| **Pull** | Télécharger les commits que les autres ont faits. | Download |
| **Clone** | La toute première fois : copier un repo depuis GitHub sur ton ordi. | git download + setup |
| **Pull Request (PR)** | "Hé les gars, je propose que ma branche soit intégrée dans `main`. Validez ?" | Mode suggestion sur Google Docs |
| **Merge** | Intégrer une branche dans une autre. "Ok ta PR est validée, on la met dans `main`". | Accepter la suggestion |
| **Conflict** | Deux personnes ont modifié la même ligne → git ne sait pas laquelle garder. Tu dois choisir. | Quand tu édites le même paragraphe qu'un collègue en même temps |

### Pourquoi on utilise des branches ?

**Règle d'or** : on ne bosse **jamais direct** sur `main`. Pourquoi ?

- Si tu casses un truc, tu casses le projet de tout le monde
- Si tu veux tester une idée bizarre, tu peux sans risque
- On peut bosser à plusieurs en parallèle sans se marcher dessus

Le schéma mental :

```
Manu crée une branche "feat/login" ────────●──────●──────╮
                                                          │ merge
main ──────●────────────────────────●────────────●────────●─────►
                                    │                     │
Michael crée une branche "fix/bug" ─●──────●──────────────╯ merge
```

Tout le monde bosse sur sa branche, et quand c'est fini et reviewé, on merge dans `main`.

---

## 2. Installer les outils

### Sur Mac

```bash
# Vérifie si git est déjà là
git --version
# Si rien → installe les outils de ligne de commande d'Apple
xcode-select --install
```

### Sur Windows

Télécharge [Git for Windows](https://git-scm.com/download/win) et installe-le (options par défaut, ça roule).

### Sur Linux

```bash
sudo apt install git    # Debian/Ubuntu
sudo dnf install git    # Fedora
```

### Configurer ton identité (une seule fois)

Git a besoin de savoir qui tu es pour signer tes commits :

```bash
git config --global user.name "Michael"
git config --global user.email "l-email-de-ton-compte-github@exemple.com"
```

**Important** : utilise le **même email que celui de ton compte GitHub**, sinon tes commits ne seront pas attribués à ton profil.

---

## 3. Créer et connecter ta clé SSH

SSH = une clé numérique qui prouve à GitHub que "c'est bien toi qui push". Plus sécurisé qu'un mot de passe et tu le tapes jamais.

### Étape 1 — Vérifie si t'en as déjà une

```bash
ls -la ~/.ssh/
```

Si tu vois `id_ed25519.pub` ou `id_rsa.pub` dans la liste → t'as déjà une clé, passe à l'étape 3. Sinon :

### Étape 2 — Créer la clé

```bash
ssh-keygen -t ed25519 -C "ton-email-github@exemple.com"
```

Ça te pose 3 questions, appuie juste **Entrée** à chaque fois (ou mets une passphrase si tu veux, mais c'est optionnel).

### Étape 3 — Copier la clé publique

```bash
# Mac / Linux :
cat ~/.ssh/id_ed25519.pub

# Windows (PowerShell) :
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Tu verras un truc qui commence par `ssh-ed25519 AAAAC3Nza...` et finit par ton email.

**Sélectionne et copie-colle TOUT** (le `ssh-ed25519` inclus).

### Étape 4 — Ajouter la clé à GitHub

1. Va sur https://github.com/settings/keys
2. Clique **New SSH key**
3. Title : "Mon laptop" (ou ce que tu veux)
4. Key type : laisse **Authentication Key**
5. Colle ta clé dans **Key**
6. Clique **Add SSH key**

### Étape 5 — Tester

```bash
ssh -T git@github.com
```

Si ça te répond :
```
Hi michael92350! You've successfully authenticated...
```

🎉 C'est bon !

---

## 4. Récupérer un projet (clone)

Disons qu'il y a un repo `mon-app` dans l'org PROJECT-M-M et tu veux bosser dessus.

```bash
# Va dans le dossier où tu stockes tes projets
cd ~/Documents   # ou ~/Dev ou ce que tu veux

# Clone le repo
git clone git@github.com:PROJECT-M-M/mon-app.git

# Entre dedans
cd mon-app

# Regarde ce qu'il y a
ls -la
```

Tu as maintenant une **copie locale** du projet. Toutes les commandes `git` que tu fais dans ce dossier agissent sur ce repo.

### Où trouver l'URL de clonage ?

Sur github.com, va sur le repo → bouton vert **"Code"** → onglet **SSH** → copie l'URL `git@github.com:...`.

---

## 5. Le workflow quotidien

C'est **toujours la même boucle**. Apprends-la une fois, tu la referas à vie.

### 5.1 — Récupérer les derniers changements AVANT de bosser

```bash
cd ~/Documents/mon-app    # va dans le repo
git checkout main          # assure-toi d'être sur main
git pull                   # télécharge les derniers changements de Manu
```

### 5.2 — Créer une branche pour ton travail

**Ne bosse jamais directement sur `main`.** Crée une branche :

```bash
git checkout -b feat/ma-nouvelle-feature
```

`-b` = "create branch", et `checkout` = "switch to it". Donc ici : "crée et bascule sur".

**Convention de naming** (voir CONTRIBUTING.md) :
- `feat/description` pour une nouvelle feature
- `fix/description` pour corriger un bug
- `docs/description` pour de la doc
- `chore/description` pour du ménage (deps, config)

Exemple concret : `feat/page-login`, `fix/timeout-api`, `docs/readme-install`.

### 5.3 — Bosse normalement

Modifie les fichiers, teste ton code, tout ce que tu fais d'habitude. Git observe dans ton dos.

Pour voir ce que tu as changé :

```bash
git status        # liste des fichiers modifiés
git diff          # voir les changements précis ligne par ligne
```

### 5.4 — Sauvegarder tes changements (commit)

Tu commit = tu crées une "photo" de ton travail avec un message.

```bash
# Ajouter les fichiers modifiés à la "zone de commit"
git add .                    # . = tous les fichiers modifiés
# OU
git add fichier1.js          # un fichier spécifique

# Créer le commit avec un message explicite
git commit -m "feat: ajoute la page de login"
```

### Convention de message de commit

Format conseillé :
```
<type>: <description courte en français ou anglais, au présent>
```

Types : `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `style`.

Exemples :
- ✅ `feat: ajoute la page de login`
- ✅ `fix: corrige le timeout sur /api/users`
- ✅ `docs: met à jour le README`
- ❌ `update` (trop vague)
- ❌ `bla bla` (pas explicite)
- ❌ `ça marche enfin putain` (on garde pour soi 😄)

### 5.5 — Envoyer ta branche sur GitHub (push)

```bash
git push -u origin feat/ma-nouvelle-feature
```

Le `-u origin feat/ma-nouvelle-feature` tu le mets **seulement la première fois**. Après, un simple `git push` suffira sur cette branche.

`origin` = nom par défaut du remote GitHub. Pas besoin de changer.

---

## 6. Ouvrir une Pull Request (PR)

Ta branche est sur GitHub mais pas encore dans `main`. Pour proposer le merge :

### Option A — Depuis le terminal (avec `gh` CLI)

Si t'as installé [GitHub CLI](https://cli.github.com) :

```bash
gh pr create --title "feat: ajoute la page login" --body "Description de ce que fait la PR"
```

### Option B — Depuis github.com

1. Va sur le repo sur github.com
2. GitHub affiche un bandeau jaune "**feat/ma-branche had recent pushes**" → **Compare & pull request**
3. Remplis le template (il s'affiche tout seul grâce aux templates de l'org) :
   - Contexte
   - Changements
   - Comment tester
4. Clique **Create pull request**

### 6.1 — Review

Manu reçoit une notif. Il lit ton code, commente si besoin, approuve ou demande des modifs.

- Si tu dois corriger un truc : modifie en local → commit → push. La PR se met à jour automatiquement.
- Quand Manu approuve : tu peux merger.

### 6.2 — Merger

Sur la PR, bouton **Squash and merge** (on recommande Squash pour garder l'historique propre). Confirme.

### 6.3 — Nettoyer

```bash
git checkout main            # retour sur main
git pull                     # récupère le merge
git branch -d feat/ma-branche # supprime la branche locale (elle sert plus)
```

Si GitHub est configuré pour auto-delete les branches mergées (recommandé), la branche distante sera supprimée toute seule.

---

## 7. Les pièges classiques

### 🔥 "J'ai committé sur `main` par erreur"

Calme-toi. Si t'as pas encore push :

```bash
# Crée une branche à partir de là où tu es, avec ton commit dedans
git branch feat/ma-branche

# Annule le commit sur main (sans perdre ton travail, il est sur l'autre branche)
git reset --hard HEAD~1

# Bascule sur ta nouvelle branche
git checkout feat/ma-branche
```

Si t'as déjà push sur main : demande à Manu, ça se répare mais c'est plus chiant.

### 🔥 "Y'a un conflit quand je merge"

Un conflit = git voit que la même ligne a été modifiée différemment dans deux branches. Tu dois choisir.

```bash
git pull origin main    # ou git merge main

# Git dira "CONFLICT in fichier.js"
# Ouvre le fichier, tu verras des trucs genre :

<<<<<<< HEAD
ta version
=======
la version de main
>>>>>>> main

# Édite le fichier pour ne garder que ce qui va bien, supprime les marqueurs <<< === >>>

git add fichier.js
git commit -m "fix: resolve merge conflict"
git push
```

### 🔥 "git dit 'please tell me who you are'"

T'as oublié la config de ton identité (étape 2 de ce doc).

### 🔥 "Permission denied (publickey)"

Ta clé SSH n'est pas chargée ou pas ajoutée à GitHub. Revoir section 3.

### 🔥 "J'ai push un fichier avec un mot de passe dedans, merde"

**Dis-le à Manu TOUT DE SUITE.** Ne te contente pas de le supprimer dans un nouveau commit (l'historique garde tout). Il faut rotate le secret ET nettoyer l'historique. Vaut mieux agir vite.

Pour éviter ça : mets **toujours** tes secrets dans un fichier `.env` (qui est dans `.gitignore`, donc jamais commité).

### 🔥 "J'ai oublié de créer une branche, j'ai bossé directement sur main"

Même solution que le premier piège : on sauve ton travail dans une branche avant de reset main.

---

## 8. Cheatsheet

À imprimer et coller à côté de ton écran.

```bash
# ===== Setup une fois =====
git config --global user.name "Michael"
git config --global user.email "ton@email.com"
ssh-keygen -t ed25519 -C "ton@email.com"   # puis ajouter ~/.ssh/id_ed25519.pub à github.com/settings/keys

# ===== Récupérer un repo =====
git clone git@github.com:PROJECT-M-M/mon-app.git
cd mon-app

# ===== Workflow quotidien =====
git checkout main               # va sur main
git pull                        # récupère les derniers changements
git checkout -b feat/ma-feat    # crée et va sur ta branche

# ... tu bosses ...

git status                      # voir où t'en es
git diff                        # voir ce que t'as changé
git add .                       # stage tes changements
git commit -m "feat: ..."       # commit
git push -u origin feat/ma-feat # push (première fois, après juste `git push`)

# ===== Puis ouvrir une PR sur github.com =====

# ===== Après le merge =====
git checkout main
git pull
git branch -d feat/ma-feat

# ===== Utils =====
git log --oneline              # voir l'historique
git remote -v                  # voir où ça push
git branch                     # liste des branches locales
git stash                      # mettre de côté temporairement
git stash pop                  # récupérer ce qu'on a stash
```

---

## 9. Ressources pour aller plus loin

- [**Learn Git Branching**](https://learngitbranching.js.org/?locale=fr) — jeu interactif pour apprendre git, **ultra-recommandé**
- [Documentation officielle GitHub (en français)](https://docs.github.com/fr)
- [Oh Shit, Git!?!](https://ohshitgit.com/fr) — comment se sortir des galères classiques

---

## Au moindre doute

**Demande à Manu plutôt que de tenter un truc random.** Git peut être impressionnant au début, mais la règle de base :

> Tant que tu n'as pas push un truc chelou sur `main` ou force-push, **rien n'est jamais perdu**. Git garde tout.

Welcome à bord 🚀
