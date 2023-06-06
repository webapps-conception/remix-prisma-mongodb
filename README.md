# remix-prisma-mongodb

Demo application using Remix, MongoDB, Prisma and TailwindCSS that you can build by following along in the series: <tbd>

- [Remix Docs](https://remix.run/docs)

## Installation
* https://www.prisma.io/blog/fullstack-remix-prisma-mongodb-1-7D0BfTXBmB6r
* https://cloud.mongodb.com

### Initialisation du projet
```sh
npx create-remix@latest remix-prisma-mongodb
Need to install the following packages:
  create-remix@1.16.1
Ok to proceed? (y) y
npm WARN deprecated @npmcli/move-file@1.1.2: This functionality has been moved to @npmcli/fs
? What type of app do you want to create? Just the basics
? Where do you want to deploy? Choose Remix App Server if you're unsure; it's easy to change deployment targets. Vercel
? TypeScript or JavaScript? TypeScript
? Do you want me to run `npm install`? Yes
npm WARN deprecated @npmcli/move-file@1.1.2: This functionality has been moved to @npmcli/fs

added 1028 packages, and audited 1029 packages in 2m

260 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
💿 That's it! `cd` into "/mnt/dev/react/remix-prisma-mongodb" and check the README for development and deploy instructions!
```

### Set up TailwindCSS
```sh
npm install -D tailwindcss postcss autoprefixer concurrently
```

Initialize Tailwind in the project:
```sh
npx tailwindcss init -p

Created Tailwind CSS config file: tailwind.config.js
Created PostCSS config file: postcss.config.js
```

tailwind.config.js:
```js
// tailwind.config.js
module.exports = {
    content: [
      "./app/**/*.{js,ts,jsx,tsx}",
    ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

package.json:
```json
{
  "scripts": {
    "build": "npm run build:css && remix build",
    "build:css": "tailwindcss -m -i ./styles/app.css -o app/styles/app.css",
    "buildRemix": "remix build",
    "dev": "concurrently \"npm run dev:css\" \"remix dev\"",
    "dev:css": "tailwindcss -w -i ./styles/app.css -o app/styles/app.css",
    "devRemix": "remix dev",
    "typecheck": "tsc"
  },
}
```

Installation des styles:
```sh
mkdir styles app/styles
touch styles/app.css

yarn build:css
yarn run v1.22.19
$ tailwindcss -m -i ./styles/app.css -o app/styles/app.css

Rebuilding...

warn - No utility classes were detected in your source files. If this is unexpected, double-check the `content` option in your Tailwind CSS configuration.
warn - https://tailwindcss.com/docs/content-configuration

Done in 419ms.
Done in 1.65s.
```

Modifier le script app/root.tsx:
```tsx
// 1
import type { MetaFunction, LinksFunction } from "@remix-run/node";

// 2
import styles from './styles/app.css';

// ...

// 3
export const links: LinksFunction = () => {
  return [{ rel: 'stylesheet', href: styles }]
}

// ...
```

Modifier le script app/routes/index.tsx:
```tsx
export default function Index() {
  return (
    <div className="h-screen bg-slate-700 flex justify-center items-center">
      <h2 className="text-blue-600 font-extrabold text-5xl">TailwindCSS Is Working!</h2>
    </div>
  )
}
```

### Set up Prisma
#### Initialize and configure Prisma
```sh
npm i -D prisma
npx prisma init --datasource-provider mongodb
```

#### Set your environment variable
To find your connection string on the Atlas dashboard hit the Connect button.

.env:
```env
DATABASE_URL="mongodb+srv://dbuser:<password>@cluster0.prsynlz.mongodb.net/kudos?retryWrites=true&w=majority"
```

#### Model the data

prisma/prisma.schema:
```schema
model User {
  id        String   @id @default(auto()) @map("_id") @db.ObjectId
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  email     String   @unique
  password  String
}
```

#### Push schema changes
```sh
npx prisma db push
Environment variables loaded from .env
Prisma schema loaded from prisma/schema.prisma
Datasource "db": MongoDB database "kudos" at "cluster0.prsynlz.mongodb.net"
Applying the following changes:

[+] Collection `User`
[+] Unique index `User_email_key` on ({"email":1})


🚀  Your database indexes are now in sync with your Prisma schema. Done in 1.66s

Running generate... (Use --skip-generate to skip the generators)

added 2 packages, and audited 1075 packages in 23s

270 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

✔ Generated Prisma Client (4.15.0 | library) to ./node_modules/@prisma/client in 226ms
```

## Deployment

After having run the `create-remix` command and selected "Vercel" as a deployment target, you only need to [import your Git repository](https://vercel.com/new) into Vercel, and it will be deployed.

If you'd like to avoid using a Git repository, you can also deploy the directory by running [Vercel CLI](https://vercel.com/cli):

```sh
npm i -g vercel
vercel
```

It is generally recommended to use a Git repository, because future commits will then automatically be deployed by Vercel, through its [Git Integration](https://vercel.com/docs/concepts/git).

## Development

To run your Remix app locally, make sure your project's local dependencies are installed:

```sh
npm install
```

Afterwards, start the Remix development server like so:

```sh
npm run dev
```

Open up [http://localhost:3000](http://localhost:3000) and you should be ready to go!

If you're used to using the `vercel dev` command provided by [Vercel CLI](https://vercel.com/cli) instead, you can also use that, but it's not needed.
