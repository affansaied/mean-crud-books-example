  #                                           Angular 13 + MongoDB Example with Node.js Express
                      This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 13.2.3.

Angular 13 node js mongodb crud example. In this tutorial, we will learn how to create ( MEAN Stack) crud apps using angular 13 + node js express rest API and MongoDB.

## Use the following steps to create angular 13 + node js mongodb crud application; as follows:
Step 1 – Create New Angular App<br>
Step 2 – Create Components in Angular<br>
Step 3 – Import Modules in app.module.ts<br>
Step 4 – Create CRUD Routes<br>
Step 5 – Build RESTful API using Node + Express js + MongoDB<br>
Step 6 – Create Angular Service for REST API Consumption<br>
Step 7 – Add code In app.component.html<br>
Step 8 – Create Operation<br>

## Step 1 – Create New Angular App
First of all, open your terminal and execute the following command on it to install angular app:<br>
'ng new my-new-app'

Then execute the following command on terminal to install angular material:<br>
`ng add @angular/material`

## Step 2 – Create Components in Angular
In this step, you need to execute the following commands on terminal to generate components in our angular application. So, open y cour terminal and execute the following command:<br>
`ng g c components/add-book`<br>
`ng g c components/book-detail`<br>
`ng g c components/books-list`<br>
<br>

# Step 3 – Import Modules in app.module.ts
In this step, visit src/app directory and open app.module.ts file. Then add the following code into it:<br>

```typescript
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
import { HttpClientModule } from '@angular/common/http';
 <br>
@NgModule({
  declarations: [],
  imports: [
    FormsModule,
    HttpClientModule,
    ReactiveFormsModule
  ],
  providers: [],
  bootstrap: []
})
 
export class AppModule { }
```

## Step 4 – Create CRUD Routes
In this step, create routes; with the help of Angular routes. So, you will make the consensus with components to enable the navigation in the CRUD application so add the below code in the app-routing.module.ts file.<br>

```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';

import { BooksListComponent } from './components/books-list/books-list.component';
import { AddBookComponent } from './components/add-book/add-book.component';
import { BookDetailComponent } from './components/book-detail/book-detail.component';

const routes: Routes = [
  { path: '', pathMatch: 'full', redirectTo: 'add-book' },
  { path: 'books-list', component: BooksListComponent },
  { path: 'add-book', component: AddBookComponent },
  { path: 'edit-book/:id', component: BookDetailComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
 
export class AppRoutingModule { }
```

 
## Step 5 – Build RESTful API using Node + Express js + MongoDB
In this step, you will learn how to create RESTful API with Node and Express.js, not just that to handle the data we will learn to use mongoDB.
So, execute the below command to invoke the REST API development with Node and Express.js:
<br>
`mkdir node-rest-api && cd node-rest-api`
<br>
Then execute:<br>
`npm init -y`
<br>

After that, execute the following commands to install imperative npm packages which will help us to create REST APIs for our Angular CRUD system:<br>
`npm install express cors body-parser mongoose`<br>
`npm install nodemon --save-dev`<br>

To store the data flawlessly, you need a reliable database of what else could be a better choice than mongoDB. So, create a node-backend/database directory, also generate the db.js file where all the logic will be piled up for invoking the mongoDB connection.<br>
`mkdir database && cd database && copy con db.js`<br>

Add the below code in node-backend/database/db.js file.<br>
```javascript
module.exports = {
    db: 'mongodb://localhost:27017/db'
};
```

Now, you need to create the Book model or schema, create node-backend/model folder. Also create a Book.js file and add the below code into it:

```javascript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
 
let Book = new Schema({
  name: {
    type: String
  },
  price: {
    type: String
  },
  description: {
    type: String
  }
}, {
  collection: 'books'
})
 
module.exports = mongoose.model('Book', Book)
```

Then, You need to define the REST API routes using Express js in a node project. Create node-backend/routes folder, also create book.routes.js file, Add the below code into it:
<br>
<br>
<br>
```javascript
const express = require('express');
const app = express();
 
const bookRoute = express.Router();
let Book = require('../model/Book');
 
// Add Book
bookRoute.route('/add-book').post((req, res, next) => {
    Book.create(req.body, (error, data) => {
    if (error) {
      return next(error)
    } else {
      res.json(data)
    }
  })
});
 
// Get all Book
bookRoute.route('/').get((req, res) => {
    Book.find((error, data) => {
    if (error) {
      return next(error)
    } else {
      res.json(data)
    }
  })
})
 
// Get Book
bookRoute.route('/read-book/:id').get((req, res) => {
    Book.findById(req.params.id, (error, data) => {
    if (error) {
      return next(error)
    } else {
      res.json(data)
    }
  })
})
 
 
// Update Book
bookRoute.route('/update-book/:id').put((req, res, next) => {
    Book.findByIdAndUpdate(req.params.id, {
    $set: req.body
  }, (error, data) => {
    if (error) {
      return next(error);
      console.log(error)
    } else {
      res.json(data)
      console.log('Book updated successfully!')
    }
  })
})
 
// Delete Book
bookRoute.route('/delete-book/:id').delete((req, res, next) => {
    Book.findByIdAndRemove(req.params.id, (error, data) => {
    if (error) {
      return next(error);
    } else {
      res.status(200).json({
        msg: data
      })
    }
  })
})
 
module.exports = bookRoute;```

<br>
Now, you need to sum up all the code and conjugate at one place so that you can run our backend and propel the CRUD app development forward.
Create and add the below code in index.js file:<br>
<br>

```javascript
let express = require('express'),
path = require('path'),
mongoose = require('mongoose'),
cors = require('cors'),
bodyParser = require('body-parser'),
mongoDb = require('./database/db');
 
mongoose.Promise = global.Promise;
mongoose.connect(mongoDb.db, {
  useNewUrlParser: true,
  //useFindAndModify: false,
  useUnifiedTopology: true
}).then(() => {
    console.log('Database sucessfully connected ')
  },
  error => {
    console.log('Database error: ' + error)
  }
)
 
const bookRoute = require('./routes/book.routes')
 
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({
  extended: false
}));

app.use(cors());
 
// Static directory path
app.use(express.static(path.join(__dirname, 'dist/mean-crud')));
 
 
// API root
app.use('/api', bookRoute)
 
// PORT
const port = process.env.PORT || 8000;
 
app.listen(port, () => {
  console.log('Listening on port ' + port)
})
 
// 404 Handler
app.use((req, res, next) => {
  next(createError(404));
});
 
// Base Route
app.get('/', (req, res) => {
  res.send('invaild endpoint');
});
 
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, 'dist/mean-crud/index.html'));
});
 
// error handler
app.use(function (err, req, res, next) {
  console.error(err.message);
  if (!err.statusCode) err.statusCode = 500;
  res.status(err.statusCode).send(err.message);
});
```

<br>
<br>
<br>
Next, execute the command while staying in the server folder (node-backend):<br>
` nodemon `

Here is your bash URL for REST API built with Node and Express http://localhost:8000/api<br>
The endpoints we created and you can use these to handle the CRUD operations with Angular application:<br>
Methods		Endpoints
GET			/api
POST			/add-book
GET			/read-book/id
PUT			/update-book/id
DELETE		/delete-book/id

## Step 6 – Create Angular Service for REST API Consumption
In this step, visit app/service directory in Angular project and create Book.ts class within.
Then, add the below code in app/service/Book.ts file:

```typescript
export class Book {
    _id!: String;
    name!: String;
    price!: String;
    description!: String;
}
```

Then, execute the command to create crud service file:<br>
` ng g s service/crud `

<br>
Then, add the below code in app/service/crud.service.ts file:
<br>

```typescript
import { Injectable } from '@angular/core';
import { Book } from './Book';
import { catchError, map } from 'rxjs/operators';
import { Observable, throwError } from 'rxjs';
import { HttpClient, HttpHeaders, HttpErrorResponse } from '@angular/common/http';
 
@Injectable({
  providedIn: 'root'
})
 
export class CrudService {
 
  // Node/Express API
  REST_API: string = 'http://localhost:8000/api';
 
  // Http Header
  httpHeaders = new HttpHeaders().set('Content-Type', 'application/json');
 
  constructor(private httpClient: HttpClient) { }
 
  // Add
  AddBook(data: Book): Observable<any> {
    let API_URL = `${this.REST_API}/add-book`;
    return this.httpClient.post(API_URL, data)
      .pipe(
        catchError(this.handleError)
      )
  }
 
  // Get all objects
  GetBooks() {
    return this.httpClient.get(`${this.REST_API}`);
  }
 
  // Get single object
  GetBook(id:any): Observable<any> {
    let API_URL = `${this.REST_API}/read-book/${id}`;
    return this.httpClient.get(API_URL, { headers: this.httpHeaders })
      .pipe(map((res: any) => {
          return res || {}
        }),
        catchError(this.handleError)
      )
  }
 
  // Update
  updateBook(id:any, data:any): Observable<any> {
    let API_URL = `${this.REST_API}/update-book/${id}`;
    return this.httpClient.put(API_URL, data, { headers: this.httpHeaders })
      .pipe(
        catchError(this.handleError)
      )
  }
 
  // Delete
  deleteBook(id:any): Observable<any> {
    let API_URL = `${this.REST_API}/delete-book/${id}`;
    return this.httpClient.delete(API_URL, { headers: this.httpHeaders}).pipe(
        catchError(this.handleError)
      )
  }
  
  // Error 
  handleError(error: HttpErrorResponse) {
    let errorMessage = '';
    if (error.error instanceof ErrorEvent) {
      // Handle client error
      errorMessage = error.error.message;
    } else {
      // Handle server error
      errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
    }
    console.log(errorMessage);
    return throwError(errorMessage);
  }
 
} ```

<br>
<br>

## Step 7 – Add code In app.component.html
In this step, create html and for crud app in angular . So, visit src/app/app.component.html and update the following code into it:<br>
<br>

``` html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand">Angular 13 CRUD Operations Demo</a>
 
  <div id="navbarNav" class="collapse navbar-collapse">
    <ul class="navbar-nav ml-auto ">
      <li class="nav-item">
        <a class="nav-link" routerLinkActive="active" routerLink="/books-list">Show Books</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" routerLinkActive="active" routerLink="/add-book">Add Books</a>
      </li>
    </ul>
  </div>

</nav>
 
<router-outlet></router-outlet>
```
<br>

## Step 8 – Create Operation
After that, Add the code in add-book.component.ts file:
<br>
```typescript
import { Component, OnInit, NgZone } from '@angular/core';
import { Router } from '@angular/router';
import { CrudService } from './../../service/crud.service';
import { FormGroup, FormBuilder } from "@angular/forms";
 
@Component({
  selector: 'app-add-book',
  templateUrl: './add-book.component.html',
  styleUrls: ['./add-book.component.scss']
})
 
export class AddBookComponent implements OnInit {
 
  bookForm: FormGroup;
   
  constructor(
    public formBuilder: FormBuilder,
    private router: Router,
    private ngZone: NgZone,
    private crudService: CrudService
  ) { 
    this.bookForm = this.formBuilder.group({
      name: [''],
      price: [''],
      description: ['']
    })
  }
 
  ngOnInit() { }
 
  onSubmit(): any {
    this.crudService.AddBook(this.bookForm.value)
    .subscribe(() => {
        console.log('Data added successfully!')
        this.ngZone.run(() => this.router.navigateByUrl('/books-list'))
      }, (err) => {
        console.log(err);
    });
  }
 
} ```

<br>
<br>
<br>
Then, Add the code in add-book.component.html file:
<br>


```html
  <div class="row justify-content-center mt-5">
    <div class="col-md-4">
        <form [formGroup]="bookForm" (ngSubmit)="onSubmit()">
          <div class="form-group">
            <label>Name</label>
            <input class="form-control" type="text" formControlName="name" required>
          </div>
   
          <div class="form-group">
            <label>Price</label>
            <input class="form-control" type="text" formControlName="price" required>
          </div>
   
          <div class="form-group">
            <label>Description</label>
            <input class="form-control" type="text" formControlName="description" required>
          </div>
   
          <div class="form-group">
            <button class="btn btn-primary btn-block" type="submit">Add Book</button>
          </div>
        </form>
    </div>
  </div>
```
<br>
    
Add the code in books-list.component.ts file:<br>
<br>
```typescript
import { Component, OnInit } from '@angular/core';
import { CrudService } from './../../service/crud.service';
 
@Component({
  selector: 'app-books-list',
  templateUrl: './books-list.component.html',
  styleUrls: ['./books-list.component.scss']
})
 
export class BooksListComponent implements OnInit {
   
  Books:any = [];
 
  constructor(private crudService: CrudService) { }
 
  ngOnInit(): void {
    this.crudService.GetBooks().subscribe(res => {
      console.log(res)
      this.Books =res;
    });    
  }
 
  delete(id:any, i:any) {
    console.log(id);
    if(window.confirm('Do you want to go ahead?')) {
      this.crudService.deleteBook(id).subscribe((res) => {
        this.Books.splice(i, 1);
      })
    }
  } 
}
```
<br>
<br>

Add the code in books-list.component.html file:
<br>

```html
 <div class="container">
  <div class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
    <h2 class="h2">Books List</h2>
  </div>
 
  <div class="table-responsive">
    <table class="table table-bordered">
      <thead>
        <tr>
          <th scope="col">Id</th>
          <th scope="col">Name</th>
          <th scope="col">Price</th>
          <th scope="col">Description</th>
          <th class="text-center" scope="col">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let book of Books; let i = index">
          <th scope="row">{{book._id}}</th>
          <td>{{book.name}}</td>
          <td>{{book.price}}</td>
          <td>{{book.description}}</td>
          <td class="text-center">
            <button class="btn btn-sm btn-primary" routerLink="/edit-book/{{book._id}}">Edit</button>
            <button class="btn btn-sm btn-danger" (click)="delete(book._id, i)">Delete</button>
        </tr>
      </tbody>
    </table>
  </div>
</div>
```
<br>
<br>

Add the code in book-detail.component.ts file:<br>
<br>
```typescript
import { Component, OnInit, NgZone } from '@angular/core';
import { Router, ActivatedRoute } from '@angular/router';
import { CrudService } from './../../service/crud.service';
import { FormGroup, FormBuilder } from "@angular/forms";
 
@Component({
  selector: 'app-book-detail',
  templateUrl: './book-detail.component.html',
  styleUrls: ['./book-detail.component.scss']
})
 
export class BookDetailComponent implements OnInit {
 
  getId: any;
  updateForm: FormGroup;
   
  constructor(
    public formBuilder: FormBuilder,
    private router: Router,
    private ngZone: NgZone,
    private activatedRoute: ActivatedRoute,
    private crudService: CrudService
  ) {
    this.getId = this.activatedRoute.snapshot.paramMap.get('id');
 
    this.crudService.GetBook(this.getId).subscribe(res => {
      this.updateForm.setValue({
        name: res['name'],
        price: res['price'],
        description: res['description']
      });
    });
 
    this.updateForm = this.formBuilder.group({
      name: [''],
      price: [''],
      description: ['']
    })
  }
 
  ngOnInit() { }
 
  onUpdate(): any {
    this.crudService.updateBook(this.getId, this.updateForm.value)
    .subscribe(() => {
        console.log('Data updated successfully!')
        this.ngZone.run(() => this.router.navigateByUrl('/books-list'))
      }, (err) => {
        console.log(err);
    });
  }
 
}
```
<br>
<br>

 Add the code in book-detail.component.html file:<br>
 <br>
```html
<div class="row justify-content-center mt-5">
  <div class="col-md-4">
    <form [formGroup]="updateForm" (ngSubmit)="onUpdate()">
      <div class="form-group">
        <label>Name</label>
        <input class="form-control" type="text" formControlName="name" required>
      </div>
 
      <div class="form-group">
        <label>Price</label>
        <input class="form-control" type="text" formControlName="price" required>
      </div>
 
      <div class="form-group">
        <label>Description</label>
        <input class="form-control" type="text" formControlName="description" required>
      </div>
 
      <div class="form-group">
        <button class="btn btn-primary btn-block" type="submit">Update</button>
      </div>
    </form>
  </div>
</div>
```
<br>
<br>
<br>
    
## Conclusion
Angular 13 CRUD application using Node.js, Express.js, and MongoDB. In this tutorial, we have learned how to create crud app in angular 13 using nodejs and mongo db with built REST API.
<br><br><br>

