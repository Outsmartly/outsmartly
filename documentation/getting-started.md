# Installation

Below are the instructions you will need to get up and running with Outsmartly.

## Part A: Installing Next.js App and Outsmartly Plugin

1. Begin by installing Next.js.

   `yarn create next-app`

2. Install Outsmartly.

   `yarn add --dev @outsmartly/next-plugin-outsmartly`

3. Open `package.json` in the root directory. In the scripts object add `OUTSMARTLY_DEV=true` as a value of `dev`, and enter `outsmartly:deploy` script.

   ```json
   {
     "name": "example",
     "version": "0.1.0",
     "private": true,
     "scripts": {
       "dev": "next build && if [ '$VERCEL_ENV' == 'production' ]; then outsmartly deploy production; fi",
       "build": "next build",
       "start": "next start",
       "outsmartly:deploy": "outsmartly deploy production"
     },
     "dependencies": {
       "next": "9",
       "react": "16",
       "react-dom": "16"
     },
     "devDependencies": {
       "@outsmartly/next-plugin-outsmartly": "^0.0.20"
     }
   }
   ```

## Part B: Setting up Next.js and Outsmartly Config File

1. Create a Next.js config file in the root of your directory.

   `touch next.config.js`

2. Put `@outsmartly/next-plugin-outsmartly` into the plugin array in your newly created `next.config.js` file. The key is `name` and the value is `@outsmartly/next-plugin-outsmartly`.

   ```javascript
   module.exports = (phase, { defaultConfig }) => {
     return {
       experimental: {
         plugins: true,
       },
       plugins: [
         {
           name: "@outsmartly/next-plugin-outsmartly",
         },
       ],
     };
   };
   ```

3. Stay at the root of your project and create a new Outsmartly config file.

   `touch outsmartly.config.js`

4. Enter the following into your Outsmartly config file.

   ```javascript
   export default {
     host: "example.outsmartly.app",
     environments: [
       {
         name: "production",
         origin: "",
       },
     ],
     routes: [],
   };
   ```

   `host:` The Outsmartly domain you have been assigned

   `name:` Env where you are deploying your app

   `origin:` Where the edge will point to and make requests

## Part C: Creating Your First Override

1. From the root of your project cd into the `pages/` directory and create `_document.js`. 

   a. Write a Outsmartly tag before `<NextScript />`.

   ```javascript
   import Document, { Html, Head, Main, NextScript } from "next/document";
   import { OutsmartlyScript } from "@outsmartly/react";
   export default class CustomDocument extends Document {
     render() {
       return (
         <Html>
           <Head />
           <body>
             <Main />
             <OutsmartlyScript />
             <NextScript />
           </body>
         </Html>
       );
     }
   }
   ```

2. Enter the following code to the index file.

   a. Add the `// @outsmartly` comment before the `Home` function
   b. On the bottom of the file write a server-side function and return the prop with the headline value of `Welcome Humans`

   ```jsx
   import { useState } from "react";
   import Head from "next/head";
   import styles from "../styles/Home.module.css";

   // @outsmartly
   export default function Home({ headline }) {
     return (
       <div className={styles.container}>
         <Head>
           <title>Create Next App</title>
           <link rel="icon" href="/favicon.ico" />
         </Head>
         <main className={styles.main}>
           <h1 className={styles.title}>{headline}</h1>
           <p className={styles.description}>
             Get started by editing{" "}
             <code className={styles.code}>pages/index.js</code>
           </p>
           <div className={styles.grid}>
             <a href="https://nextjs.org/docs" className={styles.card}>
               <h3>Documentation &rarr;</h3>
               <p>Find in-depth information about Next.js features and API.</p>
             </a>
             <a href="https://nextjs.org/learn" className={styles.card}>
               <h3>Learn &rarr;</h3>
               <p>Learn about Next.js in an interactive course with quizzes!</p>
             </a>
             <a
               href="https://github.com/vercel/next.js/tree/master/examples"
               className={styles.card}
             >
               <h3>Examples &rarr;</h3>
               <p>Discover and deploy boilerplate example Next.js projects.</p>
             </a>
             <a
               href="https://vercel.com/import?filter=next.js&utm_source=create-next-app&utm_medium=default-template&utm_campaign=create-next-app"
               className={styles.card}
             >
               <h3>Deploy &rarr;</h3>
               <p>
                 Instantly deploy your Next.js site to a public URL with Vercel.
               </p>
             </a>
           </div>
         </main>
         <footer className={styles.footer}>
           <a
             href="https://vercel.com?utm_source=create-next-app&utm_medium=default-template&utm_campaign=create-next-app"
             target="_blank"
             rel="noopener noreferrer"
           >
             Powered by{" "}
             <img src="/vercel.svg" alt="Vercel Logo" className={styles.logo} />
           </a>
         </footer>
       </div>
     );
   }
   export async function getServerSideProps() {
     return {
       props: {
         headline: "Welcome humans!",
       },
     };
   }
   ```

3. Go into your Outsmartly config located at the root of your directory and create your personalization. In this example, you are going to change the headline prop value from `Welcome Humans!` to `I have made another change. 🤯🤖`.

   ```javascript
   export default {
     host: "example.outsmartly.app",
     environments: [
       {
         name: "production",
         origin: "",
       },
     ],
     routes: [
       {
         path: "/",
         overrides: [
           {
             name: "Landing Page Title",
             component: "Home",
             propOverrides: ["headline"],
             async compute() {
               return {
                 props: {
                   headline: "I have made another change. 🤯🤖",
                 },
               };
             },
           },
         ],
       },
     ],
   };
   ```

## Part D: Deploying App with Production Origin

1. [Get on Outsmartly's waiting list](https://www.outsmartly.com/signup), and you will be provided with `host` url. In this example the `host` url is example.outsmartly.app.

1. [Sign up for Vercel](https://vercel.com/#get-started).

2. Create your [app to Vercel](https://vercel.com/#get-started). When you are in the process of deploying your app on Vercel, be sure to enter your Outsmartly token into the Vercel platform.
`name: OUTSMARTLY_TOKEN value: 404e45a6-83fc-4b93-b1bk-437671222hb8`

  Step A Go Settings/Environmental Variables from Vercel's home page
  !['Be sure to choose secret'](https://res.cloudinary.com/blockchain-side-hustle/image/upload/v1608394503/Screen_Shot_2020-12-19_at_5.08.03_PM_vyplhj.png)

   !['Enter your Outsmartly token here'](https://res.cloudinary.com/blockchain-side-hustle/image/upload/v1607886268/env-variables_vxpemr.png)

1. Take the url propagated by Vercel, open `outsmartly.config.js` and enter the url as the value of `origin`. In this example, the url is `https://outsmartly-override-example.vercel.app/`. Outsmartly edge servers goes in front of the origin and executes your overrides.

   ```javascript
   export default {
     host: "example.outsmartly.app",
     environments: [
       {
         name: "production",
         origin: "https://outsmartly-override-example.vercel.app",
       },
     ],
     routes: [
       {
         path: "/",
         overrides: [
           {
             name: "Landing Page Title",
             component: "Home",
             propOverrides: ["headline"],
             async compute() {
               return {
                 props: {
                   headline: "I have made another change. 🤖🤯",
                 },
               };
             },
           },
         ],
       },
     ],
   };
   ```

4. After you have entered the origin url `https://outsmartly-override-example.vercel.app` into your Outsmartly config file. Commit and push your changes to Github, which will set a webhook that will build and deploy your app.

Congratulations. You have made your first override!
<p align="left">
  <img src="https://media.giphy.com/media/6EQIMBLbHXGeY/giphy.gif" />
</p>


## Bonus: Make Your First Override Locally

1. Go to the root of your `Next.js` project and run the development server.

   `yarn dev`

2. [Install cloudflared](https://developers.cloudflare.com/argo-tunnel/quickstart) on your machine. Then open a new terminal and navigate to the root of your Next.js project. Enter the following into the command line:

   `cloudflared tunnel http://localhost:3000`

3. Start the cloudflare tunnel. Take the url in the output and put it in `origin` found in your Outsmartly config file. Note that the url will change every time you restart the tunnel.

   ```javascript
   origin: `https://outsmartly-override-example.vercel.app`;
   ```

4. Open the third terminal and deploy the override onto Outsmartly's edge server.

   `yarn outsmartly:deploy`

5. Navigate to the browser and enter your app url. You should see your override in the browser.

6. Enter your host url `example.outsmartly.app`. You should see your override "I have made another change. 🤖🤯" in the browser.

