# SIMPLE TO-DO APPLICATION ON MERN WEB STACK

## MERN Web stack consists of the following components:
- MongoDB: A document-based, No-SQL database used to store application data in a form of documents.
- ExpressJS: A server-side Web Application framework for Node.js.
- ReactJS: A frontend framework developed by Facebook. It is based on JavaScript, used to build User Interface (UI) components.
- Node.js: A JavaScript runtime environment. It is used to run JavaScript on a machine rather than in a browser.

## STEP 1 – BACKEND CONFIGURATION

Update ubuntu  
`sudo apt update`

Upgrade ubuntu  
`sudo apt upgrade`

Let’s get the location of Node.js software from Ubuntu repositories.  
`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0636e30f9680e6c72bf7c2aa139a373a2a4ea7d0/MERN%20Stack/images/Screenshot%20(48).png)

Install Node.js on the server with the command below  
`sudo apt-get install -y nodejs`
![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0636e30f9680e6c72bf7c2aa139a373a2a4ea7d0/MERN%20Stack/images/Screenshot%20(49).png)

**Note: The command above installs both nodejs and npm. NPM is a package manager for Node like apt for Ubuntu, it is used to install Node modules & packages and to manage dependency conflicts.**

Verify the node installation with the command below  
`node -v`

### Application Code Setup

Create a new directory for your To-Do project:  
`mkdir Todo`

Run the command below to verify that the Todo directory is created with ls command `ls`

***TIP: In order to see some more useful information about files and directories, you can use following combination of keys `ls -lih` – it will show you different properties and size in human readable format. You can learn more about different useful keys for ls command with `ls --help`.***

Now change your current directory to the newly created one:  
`cd Todo`

Next, you will use the command `npm` init to initialise your project, so that a new file named package.json will be created. This file will normally contain information about your application and the dependencies that it needs to run. Follow the prompts after running the command. You can press Enter several times to accept default values, then accept to write out the package.json file by typing `yes`.
`npm init`

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0636e30f9680e6c72bf7c2aa139a373a2a4ea7d0/MERN%20Stack/images/Capture.PNG)

**Next, we will Install ExpressJs and create the Routes directory.**

### INSTALL EXPRESS JS

Express is a framework for Node.js, therefore a lot of things developers would have programmed is already taken care of out of the box. Therefore, it simplifies development, and abstracts a lot of low-level details. For example, Express helps to define routes of your application based on HTTP methods and URLs.

To use express, install it using npm:  
`npm install express`

Now create a file index.js with the command below  
`touch index.js`

Run ls to confirm that your index.js file is successfully created

Install the dotenv module  
`npm install dotenv`

Open the index.js file with the command below  
`nano index.js`

Type the code below into it and save. For now, simply paste the code into the file.

```const express = require('express');
require('dotenv').config();
 
const app = express();
 
const port = process.env.PORT || 5000;
 
app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});
 
app.use((req, res, next) => {
res.send('Welcome to Express');
});
 
app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/4189308ea2e90f63fdf04b017430f95b1ae6f4e4/MERN%20Stack/images/install%20express.PNG)

Now it is time to start our server to see if it works. Open your terminal in the same directory as your index.js file and type:  
`node index.js`

If everything goes well, you should see Server running on port 5000 in your terminal.
![alt](https://github.com/IwunzeGE/DevOps-Project/blob/4189308ea2e90f63fdf04b017430f95b1ae6f4e4/MERN%20Stack/images/server%20running.PNG)

Open up your browser and try to access your server’s Public IP or Public DNS name followed by port 5000:
http://PublicIP-or-PublicDNS:5000

You should get **Welcome to Express**

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/93bf59a766b3e7587364d9c7f092a7ae2567a480/MERN%20Stack/images/express.PNG)

## ROUTES
There are three actions that our To-Do application needs to be able to do:
- Create a new task
- Display list of all tasks
- Delete a completed task

Each task will be associated with some particular endpoint and different standard HTTP request methods: POST, GET, DELETE.

For each task, we need to create routes that will define various endpoints that the To-do app will depend on. So let us create a folder routes  
`mkdir routes`

Change directory to routes folder.  
`cd routes`

Now, create a file api.js with the command below  
`touch api.js`

Open the file with the command below  
`nano api.js`
Copy below code in the file.

```const express = require ('express');
const router = express.Router();
 
router.get('/todos', (req, res, next) => {
 
});
 
router.post('/todos', (req, res, next) => {
 
});
 
router.delete('/todos/:id', (req, res, next) => {
 
})
 
module.exports = router;
```

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/93bf59a766b3e7587364d9c7f092a7ae2567a480/MERN%20Stack/images/routes.PNG)

## MODELS
We are going to make use of Mongodb which is a NoSQL database, we need to create a model.

**A model is at the heart of JavaScript-based applications, and it is what makes it interactive.
We will also use models to define the database schema.**

To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with `cd ..` and install Mongoose
`npm install mongoose`

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/93bf59a766b3e7587364d9c7f092a7ae2567a480/MERN%20Stack/images/mongoose.PNG)

Create a new folder models :  
`mkdir models`

Change the directory into the newly created ‘models’ folder with  
`cd models`

Inside the models folder, create a file and name it todo.js  
`touch todo.js`

Open the file created with nano todo.js then paste the code below in the file:

```const mongoose = require('mongoose');
const Schema = mongoose.Schema;
 
//create schema for todo
const TodoSchema = new Schema({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})
 
//create model for todo
const Todo = mongoose.model('todo', TodoSchema);
 
module.exports = Todo;
```

Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model.
In Routes directory, open `api.js` with `nano api.js`, update the code inside with the code below.

```const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');
 
router.get('/todos', (req, res, next) => {
 
//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});
 
router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});
 
router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
})
 
module.exports = router;
```

The next piece of our application will be the MongoDB Database!

## MongoDB DATABASE

We need a database where we will store our data. For this, we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS). [Sign up here](https://www.mongodb.com/atlas-signup-from-mlab) and select AWS as the cloud provider, and choose a region near you.
![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/Screenshot%20(50).png)

- Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mogodb%20IP%20access.PNG)

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mongodb%20ip.PNG)

- Create a MongoDB database and collection inside mLab

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mongodb%20collection.PNG)

In the index.js file, process.env (access environment variables) was specified but haven't been created.

- Create a file in your Todo directory and name it `.env`  
`touch .env` and edit using `nano .env`

Add the connection string to access the database in it, just as below:

```DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'```

***Ensure to update <username>, <password>, <network-address> and <database> according to your setup***

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mongodb%20%20.env.png)

***Heres's how to get your connection string***

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mongodb%20connect%20cluster.png)

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/0ff3f6c282610d3b811fc3a130baa51970a28a84/MERN%20Stack/images/mongodb%20db%20link.png)

- Update the ```index.js``` to reflect the use of ```.env``` so that ```Node.js``` can connect to the database.

edit with `nano index.js`

```const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();
 
const app = express();
 
const port = process.env.PORT || 5000;
 
//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then(() => console.log(`Database connected successfully`))
.catch(err => console.log(err));
 
//since mongoose promise is depreciated, we overide it with node's promise
mongoose.Promise = global.Promise;
 
app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});
 
app.use(bodyParser.json());
 
app.use('/api', routes);
 
app.use((err, req, res, next) => {
console.log(err);
next();
});
 
app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```

 ![alt](https://github.com/IwunzeGE/DevOps-Project/blob/fccf04f89222b3226cf0b4c890e16f32afdbe130/MERN%20Stack/images/index.js%20config.png)
 
***NOTE: Using environment variables to store information is considered more secure and best practice to separate configuration and secret data from the application, instead of writing connection strings directly inside the index.js application file.***

 Start your server using the command:  
 `node index.js`
You should see a message **‘Database connected successfully’**
 
### TESTING THE CODES

We will need a way to test our code using RESTful API.
  
In this project, we will use Postman to test our API.

Click [Install Postman](https://www.getpostman.com/downloads/) to download and install postman on your machine.
 
[Click HERE](https://www.youtube.com/watch?v=FjgYtQK_zLE) to learn how perform [CRUD operartions](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) on Postman.

Open your Postman, create a POST request to the API http://PublicIP-or-PublicDNS:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.  
**Note: make sure your set header key Content-Type as application/json**

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/3e1eabd698ad5e3587e17b3d582ed888048ee5d5/MERN%20Stack/images/postman%20header.png)

Reference the body as shown below;

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/3e1eabd698ad5e3587e17b3d582ed888048ee5d5/MERN%20Stack/images/post%20request.png)
 
Create a GET request to the API http://PublicIP-or-PublicDNS:5000/api/todos.

Reference the body as shown below;

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/3e1eabd698ad5e3587e17b3d582ed888048ee5d5/MERN%20Stack/images/get%20request.png)
 

 
##FRONTEND CREATION

To start out with the frontend of the To-do app, use the create-react-app command to scaffold the app. In the same root directory as your backend code, which is the Todo directory, run:  
`npx create-react-app client`

1.	Install concurrently. It is used to run more than one command simultaneously from the same terminal window.  
`npm install concurrently --save-dev`
 
2.	Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.  
`npm install nodemon --save-dev`
 
3.	In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.  
```"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

This will create a new folder in your Todo directory called client, where all the react code will be added.

Configure Proxy in package.json
- Change directory to ‘client’  
`cd client`

- Open the package.json file  
`nano package.json`

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/cd%20Clients.png)
 
- Add the key value pair in the package.json file "proxy": "http://PublicIP:5000".

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/clients%20proxy%20config.png)
 
***The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url***

Now, ensure you are inside the Todo directory, and simply do:

`npm run dev` 
 
![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/npm%20run%20dev.png)

Your app should open and start running on PublicIP:3000

![a}(https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/react%20app.png)

`cd client`
 
move to the src director  
`cd src`
 
Inside your src folder create another folder called components  
`mkdir components`

Move into the components directory with  
`cd components`

Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.  
`touch Input.js ListTodo.js Todo.js`
 
Open Input.js file  
`nano Input.js`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/js%20input.png)

Copy and paste the following

```import React, { Component } from 'react';
import axios from 'axios';
 
class Input extends Component {
 
state = {
action: ""
}
 
addTodo = () => {
const task = {action: this.state.action}
 
    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }
 
}
 
handleChange = (e) => {
this.setState({
action: e.target.value
})
}
 
render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}
 
export default Input
```

To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to `cd` into your client from your terminal and run  
`yarn add axios` or `npm install axios`.

Move to the src folder  
`cd ..`
 
Move to clients folder  
`cd ..`
 
Install Axios  
`npm install axios`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/install%20axios.png)
 
Go to ‘components’ directory  
`cd src/components`
 
After that open your ListTodo.js  
`nano ListTodo.js` and in the ListTodo.js, copy and paste the following code.
 
```
import React from 'react';
 
const ListTodo = ({ todos, deleteTodo }) => {
 
return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}
 
export default ListTodo
Then in your Todo.js file you write the following code
import React, {Component} from 'react';
import axios from 'axios';
 
import Input from './Input';
import ListTodo from './ListTodo';
 
class Todo extends Component {
 
state = {
todos: []
}
 
componentDidMount(){
this.getTodos();
}
 
getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}
 
deleteTodo = (id) => {
 
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))
 
}
 
render() {
let { todos } = this.state;
 
    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )
 
}
}
 
export default Todo;
```

![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/ListTodo%20clients.png)

We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.

Move to the src folder  
`cd ..`

Make sure that you are in the src folder and run

`nano App.js`

Copy and paste the code below into it
 
```import React from 'react';
 
import Todo from './components/Todo';
import './App.css';
 
const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/App%20js%20clients.png)
 
After pasting, exit the editor.

In the src directory open the App.css

`nano App.css`

Then paste the following code into App.css:

```.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}
 
input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}
 
input:focus {
outline: none;
}
 
button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}
 
button:focus {
outline: none;
}
 
ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}
 
li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}
 
@media only screen and (min-width: 300px) {
.App {
width: 80%;
}
 
input {
width: 100%
}
 
button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}
 
@media only screen and (min-width: 640px) {
.App {
width: 60%;
}
 
input {
width: 50%;
}
 
button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
 ```
![a}(https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/App%20css%20clients.png)
 
In the src directory open the index.css  
`nano index.css`. Copy and paste the code below:

```body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}
 
code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```
![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/index%20css.png)
 
Go to the Todo directory and run `npm run dev`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/Todo%20npm%20run%20dev.png)
 
Assuming no errors when saving all these files, our To-Do app should be ready and fully functional with the functionality discussed earlier: creating a task, deleting a task and viewing all your tasks.

![a](https://github.com/IwunzeGE/DevOps-Project/blob/19ef7b1d1a8d9fec3d95162c69c5372c3837d8c3/MERN%20Stack/images/final%20todo%20app.png)
