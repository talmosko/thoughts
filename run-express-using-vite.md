# Running node.js express using `vite`

Vite is a fast and lightweight build tool for modern JavaScript applications. It is designed to be simple to use and easy to configure, with a focus on providing a smooth developer experience.

Vite uses native ES module imports to build your applications and supports hot module replacement (HMR) for fast development. It also has built-in support for JSX and TypeScript, and allows you to use modern syntax such as the optional chaining operator and nullish coalescing operator without needing to set up a complicated build configuration.

Overall, Vite aims to make it easy for developers to create high-performance, modern applications with minimal setup and maintenance overhead.

In our `full-stack` app we'll need a `node.js` server that runs `express`, but we want to enjoy `vite`'s great hot reloading features and flexibility.
Here's how we can do that.

We'll start with a simple vite project:

## Creating a simple vite project 
```sh
npm create -y vite@latest my-project-name -- --template react-ts
```

## Add the express server code
Next we'll install  `express` by running:
```sh
cd my-project-nome
npm i express
npm i -D @types/express 
```

After that we'll create the server's index.ts file in the `src/server` folder
```ts
import express from 'express';
export const app = express()
app.get('/api/test', (_, res) => res.json({ greeting: "Hello" }))
```
Note 1, that we didn't add the `app.listen` call - we'll add that later.
Note 2, we've exported the `app` express application - we'll use that later for vite to run.


## Create the `vite` plugin:
In the project root create a file called `express-plugin.ts` with the following code
```ts
export default function express(path: string) {
  return {
    name: "vite3-plugin-express",
    configureServer: async (server: any) => {
      server.middlewares.use(async (req: any, res: any, next: any) => {
        process.env["VITE"] = "true";
        try {
          const { app } = await server.ssrLoadModule(path);
          app(req, res, next);
        } catch (err) {
          console.error(err);
        }
      });
    },
  };
}
```

This code instructs vite to load on it's server the path that we send it as a parameter. It also sets the `VITE` process environment variable to true - we can use that differentiate when the express server is called using this plugin or not.


## Use the `vite` plugin
Edit the `tsconfig.node.json` file to include the `express-plugin` file
```json
{
  "compilerOptions": {
    "composite": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts","express.plugin.ts"] // Adjust this
}

```
Edit the `vite.config.ts` file to import the plugin and use it.
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import express from './express-plugin'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), express('src/server')],
})
```

## Test that it works
Run the `vite` app using `npm run dev` and navigate to the api url we've defined at http://127.0.0.1:5173/ to see the greeting.


## Using an npm package
To make this easier to use, we've created an `npm` package with this plugin called `vite3-express-plugin` 

Here's a video of our BudapestJS meetup where we used used this method as part of our demo where we showed how to turn a front-end React app into a full-stack app using Node.js, Postgres, and Remult. The video's timeline is positioned at the deployment stage.
<iframe width="560" height="315" src="https://www.youtube.com/embed/CnCaMQCu3Kc?start=448" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>