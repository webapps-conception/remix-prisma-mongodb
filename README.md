# remix-prisma-mongodb

Demo application using Remix, MongoDB, Prisma and TailwindCSS that you can build by following along in the series: <tbd>

- [Remix Docs](https://remix.run/docs)

## Create a MongoDB instance
In this project, you will be using Prisma to interact with a MongoDB database. Before you configure Prisma, however, you will need a MongoDB instance to connect to!

You will set up a MongoDB cluster using Mongo's [Atlas cloud](https://www.mongodb.com/atlas) data platform.

🗈 You could, of course, set up a MongoDB instance any way you are comfortable. Atlas, however, provides the easiest and quickest experience. The only requirement by Prisma is that your MongoDB is deployed with a replica set.

Head over to the Atlas home page linked above. If you don't already have an account, you'll want to create one.

Already had an account? Follow these additional steps:

If you will be using an existing account, head to the dashboard. From there you will see a dropdown in the top left corner of the screen. If you pop that open you will see the option New Project.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/new-project.png)

Once you click on that, hit the Build a Database button.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/build-a-database.png)

From there you should be able to follow along with the rest of the steps below.

You should land on a screen with a few options. Choose the Free option for the purposes of this series. Then hit the Create button:

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/mongodb-free-tier.png)

When you select that option, you will be brought to a page that allows you to configure the cluster that will be generated. For your application, you can use the default settings. Just click Create Cluster near the bottom right of the page.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/mongodb-default-settings.png)

This will kick off the provisioning and deployment of your MongoDB cluster! All you need now is a database user and a way to connect to the database. Fortunately, MongoDB will walk you through this setup during their quickstart process.

You will see a few prompts that help you make these configurations. Follow the prompts to create a new user.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/mongodb-user-setup.png)

Then, in the Where would you like to connect from? section, hit Add My Current IP Address to whitelist your development machine's IP address, allowing it to connect to the database.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-1/mongodb-ip-setup.png)

With those steps completed, your database should finish its provisioning process within a few minutes (at most) and be ready for you to play with!

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

## Add an image upload component
### Set up an AWS account

⚠️ Pour accéder au services AWS S3, il faut rentrer un numéro de carte bancaire et payer 1$ pour une vérification d'identité...

Your users now have the ability to update some key information in their profile, however one thing that would be nice to add is the ability to allow a user to set up a profile picture so other users might more easily identify them.

To do this, you will set up an [AWS S3](https://aws.amazon.com/s3/) file storage bucket to hold the uploaded images. If you don't already have an AWS account, you can sign up [here](https://portal.aws.amazon.com/billing/signup#/start/email).

🗈 Amazon offers a [free tier](https://aws.amazon.com/free) that gives you access to S3 for free.

### Create an IAM user
Once you have an account, you will need an Identity [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) (IAM) user set up in AWS so you can generate an access key ID and secret key, which are both needed to interact with S3.

🗈 If you already have an IAM user and its keys, feel free to skip ahead.

Head over to to the AWS console home page. On the top right corner of the page, click on the dropdown labeled with your user name and select Security Credentials.

![Security Credentials](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/security-credentials.png)

Once inside that section, hit the Users option in the left-hand menu under Access Management.

![Your Security Credentials](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-user.png)

On this page, click the Add users button on the top right of the page.

![Add users](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-add-user.png)

This will bring you through a short wizard that allows you to configure your user. Follow through the steps below:

![Set user details](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-details.png)

This first section asks for:

# Username: Provide any user name.
# Select AWS access type: Select the Access key - Programmatic access option, which enables the generation of an access key ID and secret key.

![Access key - Programmatic access](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-permissions.png)

On the second step of the wizard, make the following selections:

# Select the "Attach existing policies directly" option.
# Search for the term "S3".
# Hit the checkmark next to an option labeled AmazonS3FullAccess.
# Hit next at the bottom of the form.

![AmazonS3FullAccess](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-tags.png)

If you would like to add tags to your user help make it easier to manage and organize the users in your account, add those here on the third step of the wizard. Hit Next when you are finished on this page.

![Next](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-overview.png)

If the summary on this page looks good, hit the Create user button at the bottom of the page.

After hitting that button, you will land on a page with your access key ID and secret key. Copy those and store them somewhere you can easily access as you will be using them shortly.

### Set up an S3 bucket

Now that you have a user and access keys, head over to the AWS S3 dashboard where you will set up the file storage bucket.

On the top right of this page, hit the Create bucket button.

![Amazon S3](https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-s3.png)

You will be asked for a name and region for your bucket. Fill those details out and save the values you choose with the access key ID and secret key you previously saved. You will need these later as well.

After filling those out, hit Create bucket at the very bottom of the form.

When the bucket is done being created, you will be sent to the bucket's dashboard page on the Objects tab. Navigate to the Permissions tab.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-bucket-permissions.png)

In this tab, hit the Edit button under the Block public access section. In this form, uncheck the Block all public access box and hit Save changes. This sets your bucket as public, which will allow your application to access the images.

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-bucket-access.png)

Below that section you will see a Bucket policy section. Paste in the following policy, and be sure to replace <bucket-name> with your bucket's name. This policy will allow your images to be publicly read:

```json
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "AllowPublicRead",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<bucket-name>/*"
    }
  ]
}
```

!(https://prismablog.vercel.app/blog/posts/fullstack-mongodb-remix-prisma-4/aws-bucket-policy.png)


You now have your AWS user and S3 bucket set up. Next you need to save the keys and bucket configurations into your .env file so they can be used later on.

.env:
```env
# ...
KUDOS_ACCESS_KEY_ID="<access key ID>"
KUDOS_SECRET_ACCESS_KEY="<secret key>"
KUDOS_BUCKET_NAME="<s3 bucket name>"
KUDOS_BUCKET_REGION="<s3 bucket region>"
```

### Update your Prisma schema
```sh
npx prisma generate
```

### Build the image upload service
```sh
npm i aws-sdk cuid
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
