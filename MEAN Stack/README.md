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

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20nodejs.png)

**ALTERNATIVELY, WE COULD USE;**

`sudo apt update && sudo apt install nodejs npm`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20npm.png)

Once done, verify the installation by running:

`nodejs -v`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/node%20-v.png)

Run the following command as a user with sudo privileges to download and execute the NodeSource installation script:

`curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -`

The script will add the NodeSource signing key to your system, create an apt repository file, install all necessary packages, and refresh the apt cache.  
Once the NodeSource repository is enabled, install Node.js and npm:

sudo apt install nodejs

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20nodejs.png)

The nodejs package contains both the node and npm binaries.

Verify that the Node.js and npm were successfully installed by printing their versions:

node --version
Copy
v14.2.0Copy
npm --version
Copy
6.14.4Copy
To be able to compile native addons from npm you’ll need to install the development tools:

sudo apt install build-essential

## STEP 2: INSTALL MONGODB

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`

**Install MongoDB**  
`sudo apt install -y mongodb`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20mongodb.png)

**Start The server**  
`sudo service mongodb start`

Verify that the service is up and running  
`sudo systemctl status mongodb`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/start%20mongodb.png)

**Install npm – Node package manager**  
`sudo apt install -y npm`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20npm%20-y.png)

**Install body-parser package:**‘body-parser’ package to help us process JSON files passed in requests to the server.  
`sudo npm install body-parser`

![f](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/insttall%20body%20parser.png)

Create a folder named ‘Books’  
`mkdir Books && cd Books`

In the Books directory, Initialize npm project  
`npm init`
 
![alt](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/mkdir%20Books.png)

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
![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/nano%20server%20js.png)

## STEP 3: INSTALL EXPRESS AND SET UP ROUTES

*** Mongoose package which provides a straightforward, schema-based solution to model your application data will be used.***

`sudo npm install express mongoose`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/install%20express%20mongoose.png)

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

![a](https://github.com/IwunzeGE/DevOps-Project/blob/7a8cc8b999c10030428af8c85f033a69515e1731/MEAN%20Stack/images/routes%20js.png)

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

![a](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/mkdir%20models.png)

## STEP 4 – ACCESS THE ROUTES WITH ANGULARJS

Change the directory back to ‘Books’  
`cd ../..`

Create a folder named public  
`mkdir public && cd public`

Add a file named script.js  
`nano script.js`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/mkdir%20public.png)

Copy and paste the Code below (controller configuration defined) into the script.js file.


```var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});
```

![a](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/scrpit%20js.png)

In the public folder, create a file named index.html;  
`nano index.html`

Copy and paste the code below into index.html file.

```<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>
 
        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>
 
          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

![a](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/index%20html.png)

Change the directory back up to 'Books'  
`cd ..`

Start the server by running this command:  
`node server.js`

![a](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/node%20server%20js.png)

The server is now up and running, we can connect it via port 3300.

Go to http://PublicIP-or-PublicDNS:3300 on your browser.

***A quick reminder on how to get your server’s Public IP and public DNS name:***

- You can find it in your AWS web console in EC2 details.

- Run curl -s http://169.254.169.254/latest/meta-data/public-ipv4 for Public IP address.

- curl -s http://169.254.169.254/latest/meta-data/public-hostname for Public DNS name.

This is how your WebBook Register Application will look in the browser:

![alt](https://github.com/IwunzeGE/DevOps-Project/blob/d5982bcd8c54f971cd0d305fd39a2dc8b0c379cb/MEAN%20Stack/images/default.png)
