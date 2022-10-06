---
marp: true
paginate: true
---
<style>
  pre {
  line-height: 150%;
}

blockquote {
  background-color: black;
  color: white;
  padding:1em;
  font-family: "Arial";
  font-size:48px
}
</style>


# Prepare
- delete cookies
- toggle auto hide
- remove detect indentation
- disable gitlens
- Zen mode
- make sure network tab has only methods and is correctly scaled
- Size the terminal window
- clear terminal window
- delete db, backend and frontend folder
- drop task table postgres


---
> Hi I’m Noam Honig, I'm a full-stack developer and I love TypeScript.

> What I don't like is application logic spread between front-end code and back-end code, when it actually belongs in one place.

> I'm also a lazy developer, so I like my code short and simple. To API routes with boilerplate code I say - no thanks.

> That's why I create Remult - a CRUD framework for full-stack TypeScript, that makes life easier.

> Remult uses TypeScript entities, shared between front-end and back-end code. 
> The entities serve as a single-source-of-truth for: database queries, a REST API with access control, validation rules and type-safe data fetching for front-end code.

> Let me show you how in just a few minutes, I turn a front-end only React todo app into a full-stack app with a Postgres database, including: 
> server-side paging, sorting and filtering, 
> full CRUD operations, 
> validation, 
> server-side logic, 
> authentication and authorization.

---
> Here we have a react todo app that uses vite.
[ Goto task.ts]
> We can see that Task defined Here
---

[ goto app.tsx]
> * We have a state for the task array
> * A state for the new task title
> * And an `addTask` method
> 
> Let's add a few tasks (Sleep, Work, Eat)

---
[ Highlight the setAllCompleted ]
> Using the `setAllCompleted` We can set them all as completed or uncompleted

[ Scroll to set completed ]
> We can tick individual items using the `setCompleted` method

---

> And we can delete items using the `deleteTask` method

---
> Let's turn this app into a `full-stack` app.
> Todo that, we first need to add a backend folder and an index.ts with a standard express server.

---
> Next we'll stop the `vite` dev server, and run the `dev` npm script which will run both the `vite` dev server and the express server we've just written.

---
> Now let's add remult express middleware to our express server.

---
[ Create shared and move task there ]
> Now that we've completed the setup of the server, the first thing I want to do is to share `Task` class between the front end and the backend.
> Let's add a shared folder and move it there.

--- 

> Using Remult, a TypeScript entity class becomes a single-source-of-truth for everything our application needs around that entity.

---
[ highlight entity decorator ]

> The Entity decorator tells Remult that this class will be used as a model for both frontend and backend code, so Remult should create CRUD API endpoints and a database table for this entity. 

---

[ highlight allow api crud ]

> Here I’ve told Remult to allow all CRUD operations for the Task entity. Later, I’ll restrict that.

--- 

[ highlight field decorators ]

> We also have decorators for each field to define its datatype and behavior.

> now let's register it with our api
> We can see here that as soon as we save our code, a new route for tasks is added.
----
Add:
- ```json
  {"title":"work"}
  ```
- ```json
  {"title":"eat"}
  ```
- ```json
  {"title":"sleep", "completed":"true"}
  ```

Demo Get.

---
> Remult provides a json database to be used in development as you can see here.
> Of course you can use remult with a wide selection of databases including postgres, mongo db, mysql and many more.
---
> let's adjust the code to use postgres.
1.  import remult/postgres
2.  adjust middleware 
3. Demo that the data is empty
add:
- ```json
  {"title":"Setup", "completed":"true"}
  ```
- ```json
  {"title":"database", "completed":"true"}
  ```
- ```json
  {"title":"Paging, sorting & filtering", "completed":"false"}
  ```
---
[ goto vite.config.ts (last file)]

> On the frontend I’ve used the proxy feature of vite dev server,
as you can see here, 
>to forward all requests sent to '/api' to the backend server that is listening on port 3002.

---
# use remult to get the data from the server
> In our react app we'll use remult to get the data from the backend
> 
```tsx
const taskRepo = remult.repo(Task);
```
> we ask Remult to provide us with a repository object for the Task entity. We use this taskRepo object to interact with the backend.
---
[ add use effect ]
> We'll use it's `find` method to get all the tasks from the backend.
[ Open developer tools ]
> you can see that everything here is simple REST API requests

---
>Let’s have a closer look at what we can do with this “find” method up here. Right now it just returns all the tasks from the database, but we can easily use it for paging
1. Show limit and page
   1. Show network tab
---
>These definitions go through the REST API request and into the database query on the backend.
2. Show order by
3. Show Where
> you can see that everything here is simple REST API requests
---
> Remember all I had to do was write a simple TypeScript class and register it with Remult, and I get a type-safe API client, with full auto-complete, and an API endpoint that fetches data from a database table, that was also created for me by Remult. Nice!
---

> Now let's adjust the code of the `addTask` method to use `taskRepo.insert`

> Any task that we add, causes a `POST` call to the backend to save the task.

---

> Let's add the tasks we want to complete:

- CRUD, 
- Validation
- Backend logic
- Authentication
- Authorization

---

> Let's adjust adjust `setCompleted` method to save the change.

> Now as I tick the tasks, a `PUT` request gets sent to the backend

---

> Let's fix the `deleteTask` method to delete.

> And let's add a task and delete it. - see,  a `delete` rest api call

---
> And last - let's add a `saveTask` method to save the task on blur.

> We'll also add a try catch here, with an alert.

---
>Did you notice I didn't write any backend code to get the rest of these CRUD operations to work? That's because, with Remult, writing simple full-stack CRUD is, well... simple!

---

> Now let’s see how, with Remult, I can add data validation to the Task entity, and have it affect both the frontend and the backend.

---

[ goto task.ts, validate ]

> So let’s go over to our Task entity and for the title field,  let’s send an “options” object with “validate”
and use a predefined “required” validator.

---

[ save and see error ]

> As soon as I’ve done that, if I’ll try to save an empty todo item, 
I’ll get an error message. 

--- 

[ clear network and retry]

> If I clear the network tab and try again, 
you can see there’s no request being sent to the backend because the validation check is done by javascript running in the browser. 

---

[ post request using new request, remember to choose post] 

> Now let’s try to bypass that by sending a POST request to the “tasks” API route with an empty title

> Doesn’t work and I get the same validation error from the API.

---

> Of course, you don’t have to use a predefined validator, you can use any arrow function to validate the data, 
so I can say for example 
“if task.title.length 
is less than 3 
throw “too short””. 

---

>Now, when I try to save 
I’m going to get the “too short” error both here
and in our API call


--- 

--- 

[ scroll to setAll, and demo click on set all ]

> Let’s look at the “setAllCompleted” function in the App component. It works but it’s not efficient, because it’s sending multiple requests to update all of these tasks. Let’s refactor this code to run on the backend instead.

---
[ add tasks controller ]

> Let’s create a new file in the “shared” folder a call it TasksController.ts. 
In it, I’ll define a TasksController class,

--- 
[ copy set all ]

> and let’s copy the code from the “setAll” function to this class. 

[adjust syntax ]

>Let’s make this a static method 

---

> and decorate it with the BackendMethod decorator from Remult.
> The BackendMethod decorator tells Remult that if I call this method from Frontend code, don’t run this code, but instead send an API request and run it on the backend.

--- 



[ and task repo]

 >All that’s left is to define a taskRepo just like before. 
 
 ---

 > and that’s it - we can use the same CRUD syntax both in Frontend code, to interact with the API, and in Backend code, to interact directly with the database.

---

[ register on index ]

> Let’s register the TasksController here.

[ call from app.tsx ]

> And now, in the App component, instead of this loop I can simply say: await TasksController.setAll(completed). That’s it.

--- 

[ demo click set all ]

> So now when I click “set all as completed” - I only get this one call to the backend. 

--- 
[ change set all parameter to string]


> One more important thing we get from this is type-safety for this API call, so if I try to send a string instead of a boolean here, TypeScript tells me I’ve made a mistake.

---

[ goto task.ts ]

> The last thing I want to show you is how to use Remult to control access to the REST API using simple, declarative code.

> Let’s say I want to restrict the API, so that only authenticated users can create, read and update tasks, 

---

[ allow.authenticate ]

> All I have to do is say here “allowApiCrud”-”Allow-authenticated”.

> As soon as I save this my page will fail because I’m not authenticated. 
> Let’s add a “sign-in” feature so we can make this app usable again.

---

[ goto backend/index.ts ]

>You can choose any way you like to implement an authentication flow. All you have to tell Remult is, how to extract the current user from any incoming request.

---


> For this demo I’m going to use the Express “cookie-session” middleware.

> Since this is an external concern to remult, I'll use a `snippet` to add `cookie-session` authentication.

---
[ highlight valid users]

> We have a list of valid users

[ highlight signIn ]

> We have a signIn endpoint
> and a signOut endpoint. 

---

> Real-life authentication flows are much more complex than this, but for a demo this will do.



[ back to index.ts - get user]

> Next, I need to tell Remult how to extract the current user from a request. For that, I’ll set the getUser option here to a function that returns the “request.session.user”.

---

> Now let’s go back to the frontend and add user sign-in.

[ goto main.tsx ]

> To do that I’ll go to the main.tsx file and wrap the App component with an Auth component I’ve prepared in advance. 

---

[goto Auth.tsx]

> The “Auth” component is very simple, it has a currentUser state, signIn and signOut functions that call the API… really simple.

---

[ sign in as steve]

> Let’s try it out. I’ll sign in as “Steve”... now I can see the todos again, and I can update them.

--- 

>Let’s go back to the Task entity and add another restriction, only users who have an "admin" role can delete tasks.

> Now we Steve can add items, but he can't delete them.

---
[ goto auth.tsx ]
> Let's make Jane an admin
[ add roles["admin"]]

[Sign in as Jane]
> Now I’ll sign out and sign back in as Jane, and now I can delete.

---

> Well, there you have it. 
> Full CRUD operations across the stack  with no boilerplate, 
> end-to-end type-safety, 
> sharing code between Frontend  and Backend , 
> and a single-source-of-truth - our TypeScript entity. 

---

> These are the core features of Remult. 

[ url, remult.dev ]

> If you want to find out more, go to remult.dev. 

---

> Thanks for watching.
