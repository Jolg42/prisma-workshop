# Une introduction pratique à Prisma.

Ce dépôt git contient le projet de démarrage pour l'atelier **Une introduction pratique à Prisma** de [Joël Galeran](https://twitter.com/Jolg42).

## Setup

### 1. Cloner ce dépôt git

Vous pouvez cloner ce dépôt avec la commande suivante :

```
git clone https://github.com/prisma/prisma-workshop-2021-fr.git
```

> Vous pouvez également télécharger le projet via l'interface utilisateur de GitHub. Cliquez sur le bouton vert **Code** dans le coin supérieur droit, puis cliquez sur **Download ZIP**.

### 2. Installer les dépendances

Naviguez dans le répertoire du projet et installez les dépendances npm avec la commande suivante :

```
cd prisma-workshop-2021-fr
npm install
```

## Atelier

Vous pouvez trouver toutes les informations pour suivre l'atelier [ici](https://pris.ly/2021-intro-fr).

# Une introduction pratique a Prisma (2021)

# Bonjour!

👋 Bienvenue à l'atelier **Une introduction pratique à Prisma**.

🇬🇧 *English version here:*

[A Practical Introduction to Prisma (2021)](https://github.com/nikolasburk/prisma-workshop)

# Ressources

**Raccourci pour cette page Notion** 

[`https://pris.ly/2021-intro-fr`](https://pris.ly/2021-intro-fr)

**GitHub starter** 

[`https://github.com/prisma/prisma-workshop-2021-fr`](https://github.com/prisma/prisma-workshop-2021-fr)

# Prérequis

Afin d'accomplir avec succès les tâches de l'atelier, vous devez avoir :

- [Node.js](https://nodejs.org/en/) installé sur votre machine (12.2.X / 14.X).
- Il est recommandé (mais pas obligatoire) d'utiliser [VS Code](https://code.visualstudio.com/) pour les travaux pratiques.

C'est tout 🙌 (*aucune connaissance préalable de SQL ou de Prisma n'est requise*).

# Ce que vous allez faire

Dans cet atelier, vous apprendrez les différents flux de travail qu'il est utile de connaître quand on utilise Prisma.

Vous commencerez par configurer Prisma avec une base de données SQLite, vous apprendrez à modéliser des données avec Prisma et à effectuer des migrations de bases de données (*leçon 1*).

Ensuite, vous découvrirez Prisma Client, un *query builder* *type-safe* qui peut être utilisé pour interroger votre base de données. Vous allez explorer différentes requêtes, du simple CRUD aux requêtes de relation, en passant par les filtres et la pagination (*leçon 2*).

Ensuite, vous apprendrez comment utiliser Prisma Client pour implémenter les routes d'une API REST (*leçon 3*).

Enfin, nous verrons comment utiliser Prisma Client pour implémenter les résolveurs d'une API GraphQL (*leçon 4*).

# Agenda

*(Heure de Paris - CEST)*

**14:00:** Coup d'envoi

**14:10:** Configuration de Prisma, modélisation de données et migrations (*leçon 1*)

**14:30:** Explorez Prisma Client pour interroger votre base de données (*leçon 2*)

**15:10:** Implémentez les routes REST API avec Express (*leçon 3*)

**15:40:** Implémentez des résolveurs GraphQL avec Apollo Server (*leçon 4*)

# Leçons

[1. Configuration de Prisma](configuration-de-prisma.md)

[2. Exploration du Prisma Client](exploration-du-prisma-client.md)

[3. REST API](rest-api.md)

[4. GraphQL API](graphql-api.md)

# À quoi ressemble une leçon ?

Une *leçon* est structurée en deux parties :

1. *Au début de chaque *leçon*, votre hôte vous guidera à travers les différentes *tâches* que vous rencontrerez dans cette leçon. Soyez *attentif* pendant ce temps et suivez les explications de l'animateur pour être sûr de pouvoir accomplir les tâches vous-même lorsque vous travaillerez dessus plus tard. **Ne codez pas encore et ne travaillez pas sur les tâches vous-même ! Vous pouvez plutôt penser à des questions ou soulever tout ce que vous ne comprenez pas (par exemple, dans la section **Q & A** de Zoom).
2. **Faites-le vous-même:** Une fois que l'animateur a fini de montrer et d'expliquer les différentes tâches, vous disposez d'un temps dédié pour travailler sur ces tâches vous-même !