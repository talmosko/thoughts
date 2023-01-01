# Deploy to Railway.app

Now that Heroku no longer offers a free version, it's time to explore alternatives. One such alternative is [Railway.app](https://railway.app), which provides Node.js hosting, database hosting, and a great developer experience.

Railway.app has a great free plan, which includes 5$ monthly credits and a 500-hour limit that disappears as soon as you enter your credit card details. With these credits, you can run approximately two full sites with a database and still have a few cents left over 😋.

To demonstrate the deployment process we'll use the following GitHub repository that I've created in my last [BudapestJS](https://www.meetup.com/budapest-js/) meetup [Todo Demo](https://github.com/noam-honig/budapestjs-meetup), using [Remult](https://remult.dev).

Once you've cloned the project it is actually ready for deployment, but let's review some of the key settings I've set for it before the deployment, in `src/backend/index.ts` file:


## Preparing the app for deployment
1. **Postgres connection string** - 
To [set a database](https://remult.dev/docs/databases.html#postgres) connection for Remult, we need to set the `dataProvider` property, and here we've done that with `createPostgresConnection`.
   
   On [line 44](https://github.com/noam-honig/budapestjs-meetup/blob/master/src/backend/index.ts#L44) in `src/backend/index.ts` file:
   ```ts
   connectionString:process.env['DATABASE_URL']||  'postgres://postgres:MASTERKEY@localhost/postgres'
   ```
   We've configured the `postgres` database connection string to the connection string received as an environment variable called `DATABASE_URL`. 
   
   That environment variable will be automatically set by `railway.app` to the correct connection string for the associated project.

2. **Port** - On [line 58](https://github.com/noam-honig/budapestjs-meetup/blob/master/src/backend/index.ts#L58) in `src/backend/index.ts` file:
   ```ts
   app.listen(process.env['PORT'])
   ```
   We've configured `express` to listen on the port that it receives as an environment variable - that port will be set by the `railway.app` environment according to their required configuration.

3. **Session Secret**- On [line 13](https://github.com/noam-honig/budapestjs-meetup/blob/master/src/backend/index.ts#L13)  in `src/backend/index.ts` file:
   ```ts
   app.use('/api', session({ secret: process.env['SESSION_SECRET'] || 'my secret' }))
   ```
   We've configured the sessions's connection string to also be determined by an environment variable - that variable will be set by us on the project settings on railway.app.

## Setup a railway.app account
1. Visit [railway.app](https://railway.app) and create an account.
2. Install the `railway.app` `cli` 
   ```sh
   npm i -g @railway/cli
   ``` 
3. Sign in to railway:
   ```sh
   railway login
   ```
   And hit Enter, to login by the browser:
   ```sh
   Press Enter to open the browser (^C to quit)
   ```

## Deploy your app to railway
1. In your project folder, type:
   ```sh
   railway init
   ```
   Answer a few questions and as soon as you're done - railway will create the project and open it in the browser.
2. In the project console in the browser, select `Add a Service` and choose `Database`, then `Add PostgreSQL`. 

   ![Add a Service in Railway.app](add-a-service-railway.jpg)

   At this stage, railway will create the `Postgres` database for this project.
3. Once the database is setup, go back to the terminal in your project folder and type:
   ```sh
   railway up
   ```
   This will upload your source code to Railway. Wait until the deployment is complete, without errors:
   ```sh
   ======= Build Completed ======

   Waiting for deploy to finish
   ☁️ Deployment logs available at https://railway.app/project/......
   OR run `railway logs` to tail them here

   ☁️ Deployment is live
   ```
   Once the source code is uploaded, Railway will run the following commands:
   1. `npm ci`: installs all the necessary npm packages.
   2. `npm run build`: builds the project
   3. `npm run start`: runs the project.

   Make sure that the `build` and `start` scripts in your `package.json` do what you want them to do :)
4. Once `railway.app` is building the project, we'll need to assign it a url that works. On your browser:
   1. Find your new deployed project in the Railway project dashboard, near the `Postgress` service. 

      You might need to refresh the page to see it.
   2. Click on the deployed project.
   3. Click on the `Deployments` tab. You can read the deployment logs for making sure there were no errors.
   4. Click on the `Settings` tab.
   5. Under `Environment`, Scroll to the `Domain` title and click the `Generate Domain` button. 
   
      It will generate a domain that you can use to open your app.

      ![Domain setting in Railway.app](adding-domain-railway.jpg)

   5. Click on the generated url - and be a little patient - it will take it a few minutes to be up and running.


Here's a video of our BudapestJS meetup where we used used this method as part of our demo where we showed how to turn a front-end React app into a full-stack app using Node.js, Postgres, and Remult. The video's timeline is positioned at the deployment stage.
<iframe width="560" height="315" src="https://www.youtube.com/embed/CnCaMQCu3Kc" title="Noam Honig - Writing full-stack code without repeating yourself" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>