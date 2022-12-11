# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

## General Overview

- MongoDB (Document database) – Stores and allows retrieval of data.

- Express (Back-end application framework) – Makes requests to Database for Reads and Writes.

- Angular (Front-end application framework) – Handles Client and Server Requests

- Node.js (JavaScript runtime environment) – Accepts requests and displays results to end user


## Prerequisites

## STEP 1: INSTALL NODEJS

`sudo apt update && sudo apt upgrade`

**Add Certificates**

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`
 
`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`

Then run:

`sudo apt install -y nodejs`

**ALTERNATIVELY, WE COULD USE;**

`sudo apt update && sudo apt install nodejs npm`

Once done, verify the installation by running:

`nodejs -v`


## STEP 2: INSTALL MONGODB

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

**Install MongoDB**  
`sudo apt install -y mongodb`

**Start The server**  
`sudo service mongodb start`

Verify that the service is up and running  
`sudo systemctl status mongodb`

**Install npm – Node package manager**  
`sudo apt install -y npm`

**Install body-parser package:**‘body-parser’ package to help us process JSON files passed in requests to the server.  
`sudo npm install body-parser`


Create a folder named ‘Books’  
`mkdir Books && cd Books`

In the Books directory, Initialize npm project  
`npm init`
 
Add a file to it named server.js  
`nano server.js`

Copy and paste the web server code below into the server.js file.

```var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

## STEP 3: INSTALL EXPRESS AND SET UP ROUTES

*** Mongoose package which provides a straightforward, schema-based solution to model your application data will be used.***

`sudo npm install express mongoose`

In ‘Books’ folder, create a folder named apps  
`mkdir apps && cd apps`

Create a file named routes.js  
`nano routes.js`
 
 
Copy and paste the code below into routes.js

```var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
```

In the ‘apps’ folder, create a folder named models  
`mkdir models && cd models`

Create a file named book.js
`nano book.js`

Copy and paste the code below into ‘book.js’
```var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);
```
Step 4 – Access the routes with AngularJS


