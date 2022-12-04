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
