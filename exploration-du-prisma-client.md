# 2. Exploration du Prisma Client

## Objectif

L'objectif de cette leçon est de vous familiariser avec l'[API du Prisma client](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference) et d'explorer certaines des requêtes de base de données que vous pouvez envoyer avec. Vous découvrirez les requêtes CRUD, les requêtes de relation comme les écritures imbriquées, le filtrage et la pagination. En cours de route, vous effectuerez une autre migration pour introduire un deuxième modèle avec une *relation* avec le modèle `User` que vous avez créé auparavant.

## Installation

Vous pouvez continuer à travailler dans le même projet `prisma-workshop` que vous avez mis en place dans la leçon 1. Le fichier `script.ts` contient une fonction `main` qui est invoquée à chaque fois que le script est exécuté.

## Conseils

- ***Tapez vous-même, ne faites pas de copier-coller***
    
    Pour apprendre et vraiment *comprendre* ce que vous faites pour chaque tâche, assurez-vous de **ne pas copier et coller la solution** mais tapez la solution vous-même (même si vous devez la chercher).
    
- **Autocomplétion**
    
    Le Prisma Client fournit un certain nombre de requêtes que vous pouvez envoyer à votre base de données en utilisant son API. Vous pouvez apprendre à connaître ces requêtes dans la [documentation](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client) ou explorer l'API directement dans votre éditeur en utilisant l'*autocomplétion*.
    
    Pour invoquer l'autocomplétion, vous pouvez ouvrir `src/index.ts` et taper ce qui suit *à l'intérieur* de la fonction `main` (vous pouvez supprimer le commentaire `// ... your Prisma Client queries will go here` qui s'y trouve actuellement) :
    
    ```tsx
    import { PrismaClient } from '@prisma/client'
    
    const prisma = new PrismaClient()
    
    async function main() {
      const result = await **prisma.** // L'autocomplétion apparaîtra si vous tapez ceci
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.disconnect())
    ```
    
    - Ouvrez pour une capture d'écran de l'autocomplétion
        
        ![2%20Exploration%20du%20Prisma%20Client%20d15e11af643f4652b45ee9170e508689/Untitled.png](2%20Exploration%20du%20Prisma%20Client%20d15e11af643f4652b45ee9170e508689/Untitled.png)
        
    
    Une fois que vous avez tapé la ligne `const result = await prisma.` dans votre éditeur, une petite popup s'affiche et vous permet de sélectionner les options pour composer une requête (par exemple, sélectionner un modèle que vous voulez interroger ou utiliser une autre fonction de haut niveau comme `$queryRaw` ou `$transaction`). L'autocomplétion est disponible pour l'ensemble de la requête, y compris les arguments que vous pourriez vouloir fournir !
    
- **Prisma Studio**
    
    Prisma Studio est une interface graphique pour votre base de données que vous pouvez utiliser pour visualiser et modifier les données qu'elle contient. Vous pouvez lancer Prisma Studio en exécutant la commande suivante :
    
    ```tsx
    npx prisma studio
    ```
    

## Tâches

À la fin de chaque tâche, vous pouvez exécuter le script à l'aide de la commande suivante :

```bash
npm run dev
```

### Tâche 1 : Ecrire une requête pour retourner *tous* les enregistrements `User`.

Pour vous échauffer un peu, écrivez une requête pour retourner *tous* les enregistrements `User` de la base de données. Imprimez le résultat sur la console en utilisant `console.log`.

- Solution
    
    ```tsx
    import { PrismaClient } from '@prisma/client'
    
    const prisma = new PrismaClient()
    
    async function main() {
      const result = await prisma.user.findMany()
      console.table(result)
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect())
    ```
    

### Tâche 2 : Écrire une requête pour créer un nouvel **enregistrement d'utilisateur**.

Dans cette tâche, vous allez créer un autre enregistrement `User`. Dans votre requête Prisma Client, fournissez seulement une valeur pour `email` mais *pas* pour `name` :

- `email` : `"alice@prisma.io"`

Pouvez-vous trouver la requête qui vous permet de faire cela ?

- Solution
    
    ```tsx
    import { PrismaClient } from '@prisma/client'
    
    const prisma = new PrismaClient()
    
    async function main() {
      const result = await prisma.user.create({
        data: {
          email: "alice@prisma.io"
        }
      })
      console.log(result)
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect())
    ```
    

### Tâche 3 : Rédigez une requête pour mettre à jour un enregistrement `User` existant.

Dans cette tâche, vous allez mettre à jour l'enregistrement `User` que vous venez de créer et ajouter une valeur pour son champ `name` :

- `name` : `"Alice"`.

Comment mettre à jour un enregistrement de base de données existant avec Prisma Client ?

- Solution
    
    ```tsx
    import { PrismaClient } from '@prisma/client'
    
    const prisma = new PrismaClient()
    
    async function main() {
      const result = await prisma.user.update({
        where: {
          email: "alice@prisma.io"
        },
        data: {
          name: "Alice"
        }
      })
      console.log(result)
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect())
    ```
    

### Tâche 4 : Ajoutez une table `Post` à votre base de données

Pour explorer des requêtes client Prisma plus intéressantes, étendons votre schéma Prisma avec un autre modèle et configurons une [*relation*](https://www.prisma.io/docs/concepts/components/prisma-schema/relations) entre l'existant et le nouveau.

Le nouveau modèle `Post` devrait avoir la forme suivante :

- `id` : un entier auto-incrémenté pour identifier de façon unique chaque message dans la base de données.
- `title` : le titre du message ; ce champ doit être *obligatoire* dans la base de données.
- `content` : le contenu/le corps du message ; ce champ doit être *optionnel* dans la base de données.
- `published` : indique si un message est publié ou non ; ce champ doit être *obligatoire* dans la base de données ; par défaut, tout message créé doit être *non* publié.
- `author` et `authorId` : configure une *relation* entre un message et un utilisateur qui doit être considéré comme l'auteur du message ; la relation doit être *optionnelle* pour qu'un message n'ait pas nécessairement besoin d'un auteur dans la base de données ; notez que *toutes* les relations dans Prisma sont bidirectionnelles, ce qui signifie que vous devrez ajouter le second côté de la relation sur le modèle `User` déjà existant.
- Solution
    
    ```groovy
    model User {
      id    Int     @id @default(autoincrement())
      name  String?
      email String  @unique
      posts Post[]
    }
    
    model Post {
      id        Int     @id @default(autoincrement())
      title     String
      content   String?
      published Boolean @default(false)
      author    User?   @relation(fields: [authorId], references: [id])
      authorId  Int?
    }
    ```
    

Une fois que vous avez ajusté le schéma Prisma et que vos deux modèles sont en place, exécutez une migration pour appliquer les changements à votre base de données :

```graphql
npx prisma migrate dev --name add-post
```

### Tâche 5 : Écrire une requête pour créer un nouvel enregistrement `Post`.

Dans cette tâche, vous allez créer un premier enregistrement `Post` avec le titre `"Hello World"`.

- Solution
    
    ```tsx
    import { PrismaClient } from '@prisma/client'
    
    const prisma = new PrismaClient()
    
    async function main() {
      const result = await prisma.post.create({
        data: {
          title: "Hello World"
        }
      })
      console.log(result)
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect())
    ```
    

### Tâche 6 : écrivez une requête pour connecter les enregistrements `User` et `Post`.

Vous avez maintenant plusieurs enregistrements `User` et exactement un enregistrement `Post` dans la base de données, ceux-ci peuvent être connectés via la colonne de clé étrangère `authorId` dans la base de données.

Lorsque vous utilisez Prisma Client, vous n'avez pas besoin de définir manuellement les clés étrangères mais vous pouvez configurer les relations en utilisant l'API à sécurité de type de Prisma Client. Pouvez-vous trouver comment ***mettre à jour*** l'enregistrement `Post` et le ***connecter*** à un enregistrement `User` existant via le champ `email` ?

Utilisez l'autocomplétion de l'éditeur pour trouver la requête ou lisez la [documentation](https://www.prisma.io/docs/concepts/components/prisma-client/relation-queries#connect-an-existing-record).

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.post.update({
        where: { id: 1 },
        data: {
          author: {
            connect: { email: "alice@prisma.io" },
          },
        },
      });
      console.log(result);
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    

### Tâche 7 : écrivez une requête pour récupérer un seul enregistrement `User` par une valeur unique.

Dans la tâche 1, vous avez appris à extraire une liste d'enregistrements de la base de données. Dans cette tâche, vous devez récupérer un seul enregistrement `User` avec une requête Prisma Client par une valeur *unique*.

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.findUnique({
        where: { email: "alice@prisma.io" }
      })
      console.log(result)
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    
    Note that you can use any *unique* field of a Prisma model to identify a record via the `where` argument, so in this case you could identify a `User` record by its `id` as well.
    

### Tâche 8 : écrivez une requête qui ne sélectionne qu'un sous-ensemble de champs.

Pour cette tâche, vous pouvez réutiliser la même requête `findMany` pour les utilisateurs que vous avez utilisée dans la tâche 1. Cependant, cette fois-ci, votre objectif est de ne sélectionner qu'un sous-ensemble de champs du modèle `User`. Plus précisément, tous les objets retournés ne doivent contenir que les champs `id` et `name` mais *pas* le champ `email`.

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.findMany({
        select: {
          id: true,
          name: true
        }
      })
      console.log(result)
    }
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    

### Task 9: Write a nested query to *include* a relation in the result

You'll now start exploring more [relation queries](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/relation-queries) of Prisma Client! Let's start with a nested read where you *include* a relation, concretely: Take your query from task 7 and include the relation to the `Post` table in the result.

### Tâche 9 : écrivez une requête imbriquée pour *inclure* une relation dans le résultat.

Vous allez maintenant commencer à explorer davantage les [requêtes de relation](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/relation-queries) de Prisma Client ! Commençons par une lecture imbriquée où vous *incluez* une relation, concrètement : Reprenez votre requête de la tâche 7 et incluez la relation avec la table `Post` dans le résultat.

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.findUnique({
        where: { email: "alice@prisma.io" },
        include: { posts: true },
      });
      console.log(result);
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    
    Remarquez que le `result` de votre requête est entièrement typé ! Le type est généré à la volée par Prisma Client, voici à quoi il ressemble :
    
    ```tsx
    const result: (User & { posts: Post[]; }) | null
    
    // ... où les types `Post` et `User` se présentent comme suit :
    
    type Post = {
      id: number
      title: string
      content: string | null
      published: boolean
      authorId: number | null
    }
    
    type User = {
      id: number
      name: string | null
      email: string
    }
    ```
    

### Tâche 10 : écrivez une requête d'écriture imbriquée pour créer un nouvel enregistrement `User` avec un nouvel enregistrement `Post`.

Dans cette tâche, vous allez créer un nouvel enregistrement `User` ainsi qu'un nouvel enregistrement `Post` dans une *simple* requête Prisma Client (écriture imbriquée). Vous pouvez à nouveau utiliser l'autocomplétion pour trouver la bonne requête ou lire la documentation [ici](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/relation-queries#nested-writes).

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.create({
        data: {
          name: "Nikolas",
          email: "burk@prisma.io",
          posts: {
            create: { title: "A practical introduction to Prisma" },
          },
        },
      });
      console.log(result);
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    

### Tâche 11 : écrivez une requête qui filtre les utilisateurs dont le nom commence par "A".

Pour cette tâche, vous pouvez réutiliser la même requête `findMany` pour les utilisateurs que vous avez utilisée dans la tâche 1. Seulement, cette fois, vous ne voulez pas retourner *tous* les enregistrements `User`, mais seulement ceux dont le `name` commence par la lettre `"A"`. Pouvez-vous trouver le bon [opérateur](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference/#filter-conditions-and-operators) qui vous permet d'exprimer cette condition ?

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.findMany({
        where: {
          name: {
            startsWith: "A",
          },
        },
      });
      console.log(result);
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    

### Tâche 12 : écrire une requête de pagination

Prisma Client propose plusieurs façons de paginer sur une liste d'objets. Utilisez la requête `findMany` de tout à l'heure pour retourner uniquement les *trois* et *quatrième* enregistrements `User` de la base de données.

- Solution
    
    ```tsx
    import { PrismaClient } from "@prisma/client";
    
    const prisma = new PrismaClient();
    
    async function main() {
      const result = await prisma.user.findMany({
        skip: 2,
        take: 2,
      });
      console.log(result);
    }
    
    main()
      .catch((e) => console.error(e))
      .finally(async () => await prisma.$disconnect());
    ```
    

### Prochaines étapes

Avec ces tâches, vous n'avez fait qu'effleurer la surface de ce qui est possible avec l'API client Prisma. N'hésitez pas à explorer d'autres requêtes et à essayer certaines des fonctions d'ordonnancement, d'upsert, de plain SQL ou autres.