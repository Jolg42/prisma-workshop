# 1. Configuration de Prisma

## Objectif

L'objectif de cette leçon est de configurer Prisma, de vous familiariser avec le langage de modélisation de données de Prisma et d'effectuer votre première migration de base de données.

## Configuration

Tout d'abord, clonez le [projet de démarrage sur GitHub](https://github.com/Jolg42/prisma-workshop) en suivant les instructions du fichier README. Une fois que vous avez le projet sur votre machine, vous pouvez passer à la création des tables de base de données dont vous avez besoin pour cette leçon.

## Tâches

Une fois que vous avez cloné le dépôt git et installé les dépendances npm, vous êtes prêt à commencer les tâches de cette leçon 💪.

### Tâche 1 : Créez votre premier modèle Prisma

Le fichier [Prisma schema](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-schema) (généralement appelé `schema.prisma`) se trouve au cœur de chaque projet qui utilise l'un des outils Prisma. Votre schéma Prisma actuel ressemble à ceci :

```jsx
// prisma/schema.prisma

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}
```

Il spécifie la connexion à un fichier de base de données SQLite via la `datasource` et indique que vous voulez utiliser Prisma Client en spécifiant le `generator`.

En plus de la source de données et des générateurs, le schéma Prisma contient également vos *modèles Prisma* qui sont la représentation par Prisma de vos tables de base de données. Dans ce cas, vous allez créer votre premier modèle Prisma.

Commencez par créer le modèle `User` suivant avec les champs suivants. Choisissez le type de données qui vous semble le plus approprié :

- `id` : un entier auto-incrémenté pour identifier de façon unique chaque utilisateur dans la base de données.
- `name` : le nom de l'utilisateur, ce champ doit être *optionnel* dans la base de données.
- `email` : l'adresse email d'un utilisateur, ce champ doit être *requis* et *unique* dans la base de données.

Une fois que vous êtes prêt, vous pouvez comparer votre résultat avec la solution ci-dessous.

- Solution
    
    ```graphql
    model User {
      id    Int     @id @default(autoincrement())
      name  String?
      email String  @unique
    }
    ```
    

### Tâche 2 : Créez et Exécutez votre première migration

Avec votre premier modèle en place, vous êtes déjà prêt à créer la table de base de données correspondante. Parcourez la [documentation](https://www.prisma.io/docs/concepts/components/prisma-migrate) pour trouver la commande CLI de Prisma qui vous aide à créer et exécuter les migrations locales. Une fois que vous avez trouvé la commande, exécutez-la et donnez à la migration un nom approprié (par exemple `init`).

- Solution
    
    ```bash
    npx prisma migrate dev
    
    # Puis entrer le nom de votre migration, par exemple: init
    ```
    

Si vous avez exécuté cette commande correctement, deux nouvelles choses seront ajoutées à votre projet à l'intérieur du répertoire `prisma` :

- un répertoire `migrations` qui garde la trace des migrations de votre base de données au fil du temps
- un fichier `dev.db` qui est votre fichier de base de données SQLite

### Tâche 3 : Créer des enregistrements de base de données avec Prisma Studio

Félicitations, vous venez de créer une nouvelle base de données avec une table appelée `User` en utilisant Prisma. Avant de passer à la section suivante, utilisez [Prisma Studio](https://www.prisma.io/studio) pour créer quelques enregistrements de base de données. Exécutez la commande suivante pour ouvrir Prisma Studio :

```bash
npx prisma studio
```

Une fois qu'il est ouvert, créez trois enregistrements dans la table `User` et sauvegardez-les dans la base de données.