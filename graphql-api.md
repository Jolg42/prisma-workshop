# 4. GraphQL API

## Objectif

Le but de cette leçon est d'utiliser vos connaissances fraîchement acquises avec le Prisma Client et de les utiliser pour implémenter quelques résolveurs d'une API GraphQL en utilisant  Apollo Server.

## Configuration

Vous pouvez continuer à travailler dans le même projet `prisma-workshop` que vous avez mis en place dans la leçon 1. Cependant, le starter de cette leçon est situé dans la branche `graphql-api` du repo que vous avez cloné.

Avant de passer à cette branche, vous devez commiter l'état actuel de votre projet. Pour simplifier, vous pouvez utiliser la commande `stash` :

```tsx
git stash
```

Après avoir exécuté cette commande, vous pouvez passer à la branche `graphql-api` et supprimer le répertoire `migrations` et le fichier `dev.db` :

```tsx
git checkout graphql-api
rm -rf prisma/migrations
rm prisma/dev.db
```

Ensuite, effacez vos dépendances npm et réinstallez-les pour tenir compte des nouvelles dépendances dans `package.json` :

```graphql
rm -rf node_modules
npm install
```

Le modèle de données que vous allez utiliser ici est similaire à celui de la leçon précédente sur l'API REST :

```graphql
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int      @id @default(autoincrement())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  title     String
  content   String?
  published Boolean  @default(false)
  viewCount Int      @default(0)
  author    User?    @relation(fields: [authorId], references: [id])
  authorId  Int?
}
```

Puisque vous recommencez votre installation de Prisma, vous devez recréer la base de données et ses tables. Exécutez la commande suivante :

```tsx
npx prisma migrate dev --name init
```

Enfin, vous pouvez amorcer la base de données avec un échantillon de données qui est spécifié dans le fichier `prisma/seed.ts`. Vous pouvez exécuter ce script d'amorçage avec cette commande :

```tsx
npx prisma db seed --preview-feature
```

Voilà, vous êtes prêt pour vos tâches maintenant !

## Tâches

Vous pouvez trouver les tâches pour cette leçon à l'intérieur du fichier `src/index.ts` marqué avec `TODO`. Votre objectif est d'insérer les bonnes requêtes Prisma Client pour chaque résolveur GraphQL.

Vous pouvez tester vos implémentations en démarrant le serveur et en ouvrant le GraphQL Playground à `http://localhost:4000`.

### `Query.allUsers: [User!]!`

Récupère tous les utilisateurs.

- Solution
    
    ```tsx
    allUsers: (_parent, _args, context: Context) => {
      return context.prisma.user.findMany()
    },
    ```
    
- Exemple de requête
    
    ```graphql
    {
      allUsers {
        id
        name
        email
        posts {
          id
          title
        }
      }
    }
    ```
    

### `Query.postById(id: Int!): Post`

Récupère un post par son ID.

- Solution
    
    ```tsx
    postById: (_parent, args: { id: number }, context: Context) => {
      return context.prisma.post.findUnique({
        where: { id: args.id }
      })
    },
    ```
    
- Exemple de requête
    
    ```graphql
    {
      postById(id: 1) {
        id
        title
        content
        published
        viewCount
        author {
          id
          name
          email
        }
      }
    }
    ```
    

### `Query.feed(searchString: String, skip: Int, take: Int): [Post!]!`

Récupère tous les post publiés et, éventuellement, les pagine et/ou les filtre en vérifiant si la chaîne de recherche apparaît dans le titre ou le contenu.

- Solution
    
    ```tsx
    feed: (
      _parent,
      args: {
        searchString: string | undefined;
        skip: number | undefined;
        take: number | undefined;
      },
      context: Context
    ) => {
      const or = args.searchString
        ? {
            OR: [
              { title: { contains: args.searchString as string } },
              { content: { contains: args.searchString as string } },
            ],
          }
        : {};
    
      return context.prisma.post.findMany({
        where: {
          published: true,
          ...or,
        },
        skip: Number(args.skip) || undefined,
        take: Number(args.take) || undefined,
      });
    },
    ```
    
- Exemple de requête
    
    ```graphql
    {
      feed {
        id
        title
        content
        published
        viewCount
        author {
          id
          name
          email
        }
      }
    }
    ```
    

### `Query.draftsByUser(id: Int!): [Post]`

Récupère les post non publiés d'un utilisateur spécifique.

- Solution
    
    ```tsx
    draftsByUser: (_parent, args: { id: number }, context: Context) => {
      return context.prisma.user.findUnique({
        where: { id: args.id }
      }).posts({
        where: {
          published: false
        }
      })
    },
    ```
    
- Exemple de requête
    
    ```graphql
    {
      draftsByUser(id: 3) {
        id
        title
        content
        published
        viewCount
        author {
          id
          name
          email
        }
      }
    }
    ```
    

### `Mutation.signupUser(name: String, email: String!): User!`

Crée un nouvel utilisateur.

- Solution
    
    ```tsx
    signupUser: (
      _parent,
      args: { name: string | undefined; email: string },
      context: Context
    ) => {
      return context.prisma.user.create({
        data: {
          name: args.name,
          email: args.email
        }
      })
    },
    ```
    
- Exemple de mutation
    
    ```graphql
    mutation {
      signupUser(
        name: "Nikolas"
        email: "burk@prisma.io"
      ) {
        id
        posts {
          id
        }
      }
    }
    ```
    

### `Mutation.createDraft(title: String!, content: String, authorEmail: String): Post`

Crée un nouveau post.

- Solution
    
    ```tsx
    createDraft: (
      _parent,
      args: { title: string; content: string | undefined; authorEmail: string },
      context: Context
    ) => {
      return context.prisma.post.create({
        data: {
          title: args.title,
          content: args.content,
          author: {
            connect: {
              email: args.authorEmail
            }
          }
        }
      })
    },
    ```
    
- Exemple de mutation
    
    ```graphql
    mutation {
      createDraft(
        title: "Hello World"
        authorEmail: "burk@prisma.io"
      ) {
        id
        published
        viewCount
        author {
          id
          email
          name
        }
      }
    }
    ```
    

### `Mutation.incrementPostViewCount(id: Int!): Post`

Augmente les vues d'un post par 1.

- Solution
    
    ```tsx
    incrementPostViewCount: (
      _parent,
      args: { id: number },
      context: Context
    ) => {
      return context.prisma.post.update({
        where: { id: args.id },
        data: {
          viewCount: {
            increment: 1
          }
        }
      })
    },
    ```
    
- Exemple de mutation
    
    ```graphql
    mutation {
      incrementPostViewCount(id: 1) {
        id
        viewCount
      }
    }
    ```
    

### `Mutation.deletePost(id: Int!): Post`

Supprime un post.

- Solution
    
    ```tsx
    deletePost: (_parent, args: { id: number }, context: Context) => {
      return context.prisma.post.delete({
        where: { id: args.id }
      })
    },
    ```
    
- Exemple de mutation
    
    ```graphql
    mutation {
      deletePost(id: 1) {
        id
      }
    }
    ```
    

### `User.posts: [Post!]!`

Retourne les post d'un utilisateur donné.

- Solution
    
    ```tsx
    User: {
      posts: (parent, _args, context: Context) => {
        return context.prisma.user.findUnique({
          where: { id: parent.id }
        }).posts()
      },
    },
    ```
    

### `Post.author: User`

Retourne l'auteur d'un post donné.

- Solution
    
    ```tsx
    Post: {
      author: (parent, _args, context: Context) => {
        return context.prisma.post.findUnique({
          where: { id: parent.id }
        }).author()
      },
    },
    ```
    

```tsx
git checkout graphql-api
rm -rf prisma/migrations
rm prisma/dev.db
```