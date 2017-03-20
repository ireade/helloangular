---
layout: post
title:  "Observables vs Promises in Angular 2"
author: eniola
---

## In the Land of the Asynchronous

Asynchronous operations are commonplace in Javascript applications. Developers employ asynchronous techniques mostly when dealing with external servers, so as to keep the wheel of their apps moving as they await response from the server. Back in the days you would use callbacks but things quickly get jam-packed when you start making new asynchronous calls with the result of another asynchronous  operation and trying to handle the error for each of the asynchronous calls. 

![Image of Asynchronous operations](http://i.imgur.com/FwXWCfc.jpg)
*Credit: Ire Aderinokun. https://bitsofco.de/javascript-promises-101*

Messy isn’t it?

## Hello, Promises

Promises are a lifesaver. They keep our asynchronous code short and neat. You can get the returned value using the `.then()` operator and handle errors using the `.catch()` operator. Simple but very useful. Feel free to learn more about promises [here](https://www.youtube.com/watch?v=viaF0hM8G94&t=486s), if there is need.

## "I'm Living in the 21st Century, Doing Something Mean To It"

Says observables. One of the major limitations of promises is that they can only deal with one event. Another limitation is that you can’t cancel a promise nor use data tools on it. Observables, on the other hand, is an asynchronous juggernaut that swallows up Promises and transforms it into one of its operators (`.toPromise()`).
Observables are perfect for managing streams (groups of data that arrive over a period of time) using its operators and data processing tools like `.map()`, `.filter()`, `.reduce()`, etc. No single entity should have all that power!

## An Example of Angular 2 Asynchronous Operations

A line of code is worth more than a thousand words. Let’s see how we can implement Promises and Observables in Angular 2 asynchronous operations. You can clone the complete project from [Github](https://github.com/Enirate/promise-and-observables).

We will create two services that will fetch mock data from the same end point using different techniques i.e. Promises and Observables. There is a lot of comment in the code to help explain what’s going on. While there are different files, we are more interested in the service files.

This is what our `app.component.html` looks like:


```html
<div>
  <h2>With Love from Json placeholder via promise</h2>
  <button (click)="getPostPromise()">Get post</button>
  <ul>
    <li *ngFor="let item of postsThruPromise" >{{item.body}}</li>
  </ul>
</div>


<div>
  <h2>With Love from Json placeholder via observables</h2>
  <button (click)="getPostObserve()">Get post</button>
  <ul>
    <li *ngFor="let item of postsThruObservable" >{{item.body}}</li>
  </ul>
</div>

```

We’ll define our services as providers in `app.module.ts` file like so:

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule, JsonpModule } from '@angular/http';
import { PromiseService } from './promise.service'; //import service class
import { ObserveService } from './observe.service'; //import service class

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
  ],
  providers: [
    PromiseService, //we declare promise service as a provider after importing it
    ObserveService  //we declare our observable service as a provider after importing it
    ],
  bootstrap: [AppComponent]
})
export class AppModule { }

```

Here is what our service using Promises technique will look like:

```javascript
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';


@Injectable()
export class PromiseService {

  constructor(private http:Http) { }   //injecting http as a dependency

  getPostsPromise() {
    return this.http.get('http://jsonplaceholder.typicode.com/posts') //hitting an external server with http request.
    .toPromise() //change return type from default observable<response> to promise<response> so that we can mimic a promise
    //asyn operation and implement promise operators
  }

}
```

Note: Angular 2 get request returns an observable by default, so the `.toPromise()` operator will turn the response type to Promises, hence we can use promises operators (i.e. then and catch) on the response.

The second service using observables will look like so:

```javascript
import { Injectable } from '@angular/core';
import {Http} from '@angular/http';


import'rxjs/add/operator/map' //import a single operator, as few operators are added by default
import 'rxjs/Rx' //import all observable operators.

@Injectable()
export class ObserveService {

  constructor(private http:Http) { } 


  getPostObserve() {
    return this.http.get('http://jsonplaceholder.typicode.com/posts')
    .retry(5) //this operator will help fire our http request up to 5 times if initial requests fail
    .debounceTime(600) //not actually useful here, but shows how to chain observables operators. Debounce time will
    //delay the response from the server for the specified time(600 milliseconds in this case) and replace it with a 
    //new value if any is detected before the specified time. Good for autocomplete search forms. 
    .map(res =>res.json()) //Just like the normal map data tool, it turns each item of the observable to a Json string and 
    //roll them up into a json object
  }

}
```

Note: Angular 2 gives access makes few observables operator available out of the box but we can always import the ones we need.

Finally, our `app.component.ts` - 

```javascript
import { Component } from '@angular/core';
import { PromiseService } from './promise.service' //we import our promise class
import { ObserveService } from './observe.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {

postsThruPromise:any; //an empty variable declared, it will hold posts obtained from Promise Service
postsThruObservable:any; //an empty variable declared, it will hold posts obtained from Observe Service

constructor( private promiseService:PromiseService,
 private observeService:ObserveService) {}



 getPostPromise() {
  this.promiseService.getPostsPromise()
  .then(response => this.postsThruPromise = response.json()) //.then() operator handled the returned value, 
  //it assigns it to a variable and converts it to json format
  .catch(err => console.log(err.message)) //.catch operator handles error, here it will log it in the console.
}

getPostObserve() {
  this.observeService.getPostObserve()
  .subscribe(response => this.postsThruObservable = response, err => console.log(err.message)) //the subscribe operator will help 
  //fire the http request and manage the final value (after initial value has been routed 
  //through other operators, if available) or error.
}
}

```

## Finally, Finally..
While promises are an upgrade on callbacks, observables are an upgrade on promises. They leverage on the capabilities of their powerful [operators](http://reactivex.io/documentation/operators.html) to help manage asynchronous operations in a way promises can only dream of. 

Thank you.
