# MEAN Cheat Sheet
## Created by Michael Arnold @xmbarnold
---
### Express Server Setup
---
- [ ] Create the root project folder with the name of your project
- [ ] Inside the root folder, create a new folder called **server**
- [ ] Create a new file called **server.js**
- [ ] Inside the server folder create three new folders:
    * **config**
    * **controllers**
    * **models**
- [ ] Inside **config** create two new files:
    * **mongoose.js**
    * **routes.js**
- [ ] Inside **models** create a new file:
    * **{model}.js** // replace model with name of your model
- [ ] Inside **controllers** create a new file:
    * **{models}.js** //replace models with plural name of your model
- [ ] Open a terminal and run the following commands:
```cli
npm init
npm install body-parser express path mongoose mongoose-unique-validator --save
```

- [ ] Open the **server.js** file and add the following code:
```javascript
var express = require('express');
var app = express();

var bodyParser = require('body-parser');
app.use(bodyParser.json());
app.use(express.static(__dirname + '/public/dist/public'));

var server = app.listen(2000, function() { // replace 2000 with port of choice
    console.log('SERVER RUNNING @ localhost:2000');
})

require('./server/config/mongoose');
require('./server/config/routes')(app);
```

- [ ] Open the **mongoose.js** file in **config** and add the following code:
```javascript
var mongoose = require('mongoose');
var path = require('path');
var fs = require('fs');

mongoose.connect('mongodb://localhost/database_name', {}, (err) => {
    if(err){
        console.log('========================');
        console.log('Mongoose Connection Error:', err);
        console.log('========================');
    }
    else{
        console.log('Successfully connected to MongoDB @ localhost');
    }
})
var models_path = path.join(__dirname, './../models');

fs.readdirSync(models_path).forEach((file) => {
    if(file.indexOf('.js') >= 0){
        require(models_path + '/' + file);
    }
})
```

- [ ] Open the **routes.js** file in **config** and add the following code
```javascript
var models = require('../controllers/models'); // replace models with name of your controller file

module.exports = (app) => {
    // API routes here
    // example:
    app.get('/models', (req, res) => models.index(req, res));
    // CRUD routing examples
    // Create
    app.post('/models', (req, res) => models.create(req, res));
    // Read
    app.get('/models/:id', (req, res) => models.read(req, res));
    // Update
    app.put('/models/:id', (req, res) => models.update(req, res));
    // Destroy
    app.delete('/models/:id', (req, res) => models.destroy(req, res));
}
```

- [ ] Open the **model.js** file in **models** and add the following code
```javascript
var mongoose = require('mongoose');
var uniqueValidator = require('mongoose-unique-validator');

var ModelSchema = new mongoose.Schema({ // change Model with name of your Model
    // field: { type: Type, validation: [valid, 'Error Message'],}
    // common validations include:
    // required: true
    // unique: true
    // min/max(length): value
}, { timestamps: true });
// unique validation
// again, replace Model with name of your Model, replace message string
ModelSchema.plugin(uniqueValidator, {message: 'Unique validation message here'});

module.exports = mongoose.model('Model', ModelSchema); // replace Model with Model name
```

- [ ] Open the **models.js** file in **controllers** and add the following code
```javascript
var mongoose = require('mongoose');
require('../models/model'); // replace model with your model.js file name
const Model = mongoose.model('Model') // replace Model with model name

module.exports = {
    // route functions here
    // example
    index: (req, res) => {
        Model.find({}, (err, data) => { // replace Model with model name above
            if(err){
                res.json({message: 'Error', error: err});
            }
            else{
                res.json({message: 'Success', result: data});
            }
        })
    }
    // CRUD function examples
    // create: POST - /models
    create: (req, res) => {
        Model.create(req.body, (err, data) => {
            if(err){
                res.json({message: 'Error', error: err});
            }
            else{
                res.json({message: 'Success', result: data});
            }
        })
    }
    // read: GET - /models/:id
    read: (req, res) => {
        Model.findByID(req.params.id, (err, data) => {
            if(err){
                res.json({message: 'Error', error: err});
            }
            else{
                res.json({message: 'Success', result: data});
            }
        })
    }
    // update: PUT - /models/:id
    update: (req, res) => {
        Model.findByIdAndUpdate(
            req.params.id, 
            req.body, 
            {runValidators: true, context: 'query'}, 
            
            (err, data) => {
            if(err){
                res.json({message: 'Error', error: err});
            }
            else{
                res.json({message: 'Success', result: data});
            }
        })
    }
    // destroy: DELETE - /models/:id
    destroy: (req, res) => {
        Model.findByIdAndDelete(req.params.id, (err, data) => {
            if(err){
                res.json({message: 'Error', error: err});
            }
            else{
                res.json({message: 'Success', result: data});
            }
        })
    }
}
```
---
### Angular Setup
---
- [ ] Inside the root folder for the project, run the following command in your terminal
```cli
ng new public
```

- [ ] Move inside the public folder and run the following command
```cli
ng g s http
```

- [ ] If you have additional components to create, create them now using the following command
```cli
ng g c component-name
```
> replace component name with desired name


- [ ] Finally create a 404 Page Not Found component
```cli
ng g c page-not-found
```

- [ ] Navigate to *your_project/public/src/app/*
- [ ] Open **app.module.ts**
- [ ] Add the following import statements at the top
```typescript
import { HttpClientModule } from '@angular/common/http';
import { HttpService } from './http.service';
import { FormsModule } from '@angular/forms';
```
- [ ] Ensure that all of the components you created are also imported
- [ ] In *@NgModule* locate the array **declarations** and ensure all the components imported are included in the array
- [ ] Now locate the array **imports** and add HttpClientModule and FormsModule to it
```typescript
{
    imports: [
        BrowserModule,
        AppRoutingModule,
        HttpClientModule, // added this line
        FormsModule, // added this line
    ]
}
```
- [ ] Finally locate the array **providers** and add HttpService to it
```typescript
{
    providers: [
        HttpService, // added this line
    ]
}
```
- [ ] Open **http.service.ts**
- [ ] Import the HttpClient at the top of the page
```typescript
import { HttpClient } from '@angular/common/http';
```

- [ ] Use the HttpClient in the constructor
```typescript
export class HttpService {

    constructor(private _http: HttpClient) { }
}
```

##### Refer to your **routes.js** file from the Express server
- [ ] Inside the HttpService class, add methods that return the observable for each API route you previously set up
```typescript
getAllModels(){
    return this._http.get('/models');
}
// Create the rest of the CRUD commands using the proper Http method
```

- [ ] Open **app.component.ts**
- [ ] Change the *Component* import statement to include OnInit and import HttpService
```typescript
import { Component, OnInit } from '@angular/core'; // changed this line
import { HttpService } from './http.service';
```

- [ ] Change the *AppComponent* class to implement OnInit
```typescript
export class AppComponent implements OnInit {

}
```
- [ ] Within *AppComponent* class add a constructor that takes in the HttpService and create the ngOnInit method
```typescript
export class AppComponent implements OnInit {
    
    constructor(private _httpService: HttpService) { } // added this line

    ngOnInit(){ } // added this line, may not be used depending on your app structure
}
```

- [ ] Open **app-routing.module.ts**
- [ ] Ensure *Routes* and *RouterModule* are imported at the top
```typescript
import { Routes, RouteModule } from '@angular/router';
```

- [ ] Ensure that all the components you created are also imported
- [ ] Locate the *routes* object
- [ ] Within *Routes = [ ]* create new routes for each component you added
```typescript
const routes: Routes = [
    //example
    {path: '', component: HomeComponent},
    {path: 'new', component: NewComponent},
    {path: 'update/:id', component: UpdateComponent},
    {path: '**', component: PageNotFoundComponent},
] 
```
#### IMPORTANT!!
It is very important that the component routes ARE NOT the same as your Express API routes

- [ ] Finally ensure that *@NgModule* contains the following
```typescript
@NgModule({
    imports: [RouterModule.forRoot(routes)],
    exports: [RouterModule]
})
```

#### At this point, the rest of the code will be up to you

---
### Git Repo
---
- [ ] Open a git or linux terminal (gitbash, linux terminal, mac terminal)
- [ ] Navigate into the public folder of your project
- [ ] Run the following command
```cli
rm -rf .git*
```
// This will remove the auto-generated git repo Angular initializes
- [ ] Now open the project folder and create a new file called **.gitignore**
- [ ] Add the following code inside the file
```
# Created by https://www.gitignore.io/api/mean
# Edit at https://www.gitignore.io/?templates=mean

### MEAN ###
# MEAN Stack Base

### MEAN.Anglar Stack ###
## Angular ##
# compiled output
/public/dist
/public/tmp
/public/src/app/**/*.js
/public/src/app/**/*.js.map

# dependencies
/public/node_modules
/node_modules
/public/bower_components

# IDEs and editors
/public/.idea

# misc
/public/.sass-cache
/public/connect.lock
/public/coverage/*
/public/libpeerconnection.log
/public/npm-debug.log
/public/testem.log
/public//typings

# e2e
/public/e2e/*.js
/public/e2e/*.map

#System Files
.DS_Store

### MEAN.Node Stack ###
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*

# Diagnostic reports (https://nodejs.org/api/report.html)
report.[0-9]*.[0-9]*.[0-9]*.[0-9]*.json

# Runtime data
pids
*.pid
*.seed
*.pid.lock

# Directory for instrumented libs generated by jscoverage/JSCover
lib-cov

# Coverage directory used by tools like istanbul
coverage

# nyc test coverage
.nyc_output

# Grunt intermediate storage (https://gruntjs.com/creating-plugins#storing-task-files)
.grunt

# Bower dependency directory (https://bower.io/)
bower_components

# node-waf configuration
.lock-wscript

# Compiled binary addons (https://nodejs.org/api/addons.html)
build/Release

# Dependency directories
node_modules/
jspm_packages/

# TypeScript v1 declaration files
typings/

# Optional npm cache directory
.npm

# Optional eslint cache
.eslintcache

# Optional REPL history
.node_repl_history

# Output of 'npm pack'
*.tgz

# Yarn Integrity file
.yarn-integrity

# dotenv environment variables file
.env
.env.test

# parcel-bundler cache (https://parceljs.org/)
.cache

# next.js build output
.next

# nuxt.js build output
.nuxt

# vuepress build output
.vuepress/dist

# Serverless directories
.serverless/

# FuseBox cache
.fusebox/

# DynamoDB Local files
.dynamodb/

# End of https://www.gitignore.io/api/mean
```