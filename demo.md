
---
marp: true

---
# Setup

```sh
npm create vite
```
* name - `todo-demo`
* react
* typescript
```sh
cd todo-demo
```
install stuff
```sh
npm i express remult
```
```sh
npm i -D @types/express ts-node-dev
```
---

# configuration

1. `package.json` remove `type`:`module` -  ***For our node js server we'll use common js, so we need to remove type-module from the package json***

2. Create `tsconfig.server.json`
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true
  }
}
```

3. Edit `tsconfig.json`, under `compilerOptions` remove `"experimentalDecorators": true`

***If you don't like decorators or prefer javascript, see our docs on how to do so.***

---

# Create the nodejs server

1. create server express `src/server/index.ts`
   ```ts
   import express from "express";
   const app = express();
   app.listen(3002, () => console.log("Started"));
   ```
2. start the node server
   ```sh
   npx ts-node-dev -P tsconfig.server.json src/server
   ```
3. create `api.ts` file
4. register it on the server

---

* add `shared/Task.ts`
  * create the class with the members
  * Add the decorators
  * Indicate that we support also uuid
* Register to api
* Demo get
* Demo post:
  * Task a
  * Task b, completed true
  * Task c, completed false
* Explain that data is saved by default to a json db, and we fully support all databases including ....

---

# vite
1. Open a new terminal and enter directory
2. Run `npm run dev`
   ***In our docs we explain how to use `concurrently` to run both vite and node js in one terminal***
3. Configure proxy `vite.config.ts`
   ```ts
   server: { proxy: { '/api': 'http://localhost:3002' } }
   ```
4. Demo proxy

---
# App.tsx

```tsx
function App() {
  const [tasks, setTasks] = useState<Task[]>([]);
  return <main>
    <h2>Todo </h2>
    <ul>
      {tasks.map(task =>
        <li key={task.id}>
          {task.title}
        </li>)}
    </ul>
  </main>
}
```
---
# use remult to get the data from the server
```tsx
const taskRepo = remult.repo(Task);
```
***returns a `Repository` of type Task that we'll use to interact with the backend. That same repository can be used in the front end to interact with the backend via REST API, and on the backend to interact with the Database***
```tsx
useEffect(()=>{
  fetchTasks().then(setTasks)
},[])
```
Add Checkbox to indicate completed
```html
<input type="checkbox" checked={task.completed}/>
```

---
# Demo 
1. Show limit and page
   1. Show network tab
2. Show order by
3. Show Where
## Style
```css
main {
  text-align: left;
}
ul {
  padding: 0;
  list-style-type: none;
}
```
---
# Add new Task
```ts
const [newTaskTitle,setNewTaskTitle] = useState("");
```
```html
<input value={newTaskTitle}
  onChange={e => setNewTaskTitle(e.target.value)} />
```
```ts
const addTask = async () => {
  if (newTaskTitle) {
    setTasks([...tasks, await taskRepo.insert({
      title: newTaskTitle
    })]);
    setNewTaskTitle('');
  }
};
```
* Add `onBlur` to `input`


---

# Delete a task
* use trick with `{` and then `(`
```ts
const deleteTask = async () => {
  await taskRepo.delete(task);
  setTasks(tasks.filter(t => t !== task));
}
```
```tsx
<button onClick={deleteTask} >x</button>
```

---

# styling
```css
li>button {
  color: red;
  font-weight: bolder;
  padding: 4px 8px;
  visibility: hidden;
}
li:hover>button,
li:focus-within>button {
  visibility: visible;
}
```

---
# Set Completed
```ts
const setCompleted = async (completed: boolean) => {
  const updatedTask = await taskRepo.save({ ...task, completed });
  setTasks(tasks.map(t => t === task ? updatedTask : t));
}

```
```tsx
onChange={e => setCompleted(e.target.checked)}
```
---

# Edit Task

```ts
const setTitle = (title: string) => {
  const updatedTask = { ...task, title };
  // refactor replace task
  replaceTask(updatedTask);
}
```

Refactor
```ts
const replaceTask = (updatedTask: Task) => {
  setTasks(tasks.map(t => t === task ? updatedTask : t));
}
```
```html
<input
  value={task.title}
  onChange={e => setTitle(e.target.value)} 
  />
```

---
# Save Task Edit
```ts
const saveTask = async () => {
  replaceTask(await taskRepo.save(task));
}
```

```ts
onBlur={saveTask}
```


---
# Validation - `Task.ts`
```ts
@Fields.string({
    validate: Validators.required
})
title = '';
```
```ts
const saveTask = async () => {
  try {
    replaceTask(await taskRepo.save(task));
  } catch (err: any) {
    alert(err.message);
  }
}
```
* Demo
* Demo post call
* Refactor to Arrow Method
---

# Backend methods
```ts
const setAll = async (completed: boolean) => {
  for (const task of await taskRepo.find()) {
    await taskRepo.save({ ...task, completed });
  }
  setTasks(await taskRepo.find());
};
```
```html
<button onClick={() => setAll(!tasks.find(t => t.completed))}>Toggle Completed</button>
```
```css
main>button {
  background: seagreen;
  color: #fff;
  font-size: small;
}
```
---


