---
layout: post
title:  "How to Bootstrap an Angular Application"
author: ire
---

In order to launch an Angular application, we need to bootstrap it. Bootstrapping is a way for us to tell Angular where to start running our application. 


## Bootstrapping in Angular 1.x

In Angular 1.x, we could bootstrap our application in one of two ways.

### 1. Automatic Bootstrapping

The more common way of bootstrapping an Angular 1.x application is the automatic method. Using this method, all we need to do is use the `ng-app` directive in our HTML, and reference the application name when creating the `angular.module`.


```language-html
<html>
    <body ng-app="myApp">
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.5/angular.min.js"></script>
        <script>
            var app = angular.module('myApp', []);
        </script>
    </body>
</html>
```


### 2. Manual Bootstrapping

We can also trigger the bootstrapping process manually  using `angular.bootstrap`. This becomes useful if we need to bootstrap the application asynchronously.

```language-html
<html>
    <body>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.5/angular.min.js"></script>
        <script>
            var app = angular.module('myApp', []);
            angular.element(document).ready(function () { angular.bootstrap(document, ['myApp']); });
        </script>
    </body>
</html>
```


## Bootstrapping in Angular 2

Bootstrapping an application in Angular 2 works in a completely different way. There are two stages to the process of bootstrapping. First, we need to define, in our main application module, what the root application component is. Second, we need to bootstrap our main application module using the `bootstrapModule` method of the platform being used. 

So, there are three main files we need to be concerned with; `app.component.ts`, `app.module.ts`, and `main.ts` 


### Defining the App Component

Every Angular application needs a root component, conventionally called the `AppComponent`. If you use the Angular CLI to generate your application, this is located in `/src/app/app.component.ts`.

```language-javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'app works!';
}
```

In order to bootstrap our application, we need to make sure this component exists. 


### Defining the App Module

In our application module, we need to specify which component should be bootstrapped as the root component. If you use the Angular CLI to generate your application, this is located in `/src/app/app.module.ts`.

As I covered in my article on [Setting up an Angular 2 Project](), all components are defined under `declarations` in `NgModule`. However, `NgModule` also accepts an array of components that need to be bootstrapped, under `bootstrap`. This is where, in addition to the `declarations`, we include our AppComponent. 

```language-javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Bootstrapping the Module

Finally, we need to call the `bootstrapModule` method to perform the actual bootstrapping action. In order to keep functionality separate, we do this in `main.ts`, which is located in `/src/main.ts`.

The bootstrap method belongs to the platform reference, which can be different depending on the platform being used. For example, for a typical Angular application running in the browser, the `bootstrapModule` method will be available as a method of `platformBorwserDynamic()`, which is imported from `@angular/platform-browser-dynamic`. However, if you are using Angular Universal, you would use the `bootstrapModule` method that is a sub property of `platformUniversalDynamic`, which is imported from `angular2-universal`.


```language-javascript
import './polyfills.ts';

import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { enableProdMode } from '@angular/core';
import { environment } from './environments/environment';
import { AppModule } from './app/';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic().bootstrapModule(AppModule);
```


&nbsp;


That's it! Once all this is done, our application will be ready to be launched. 


