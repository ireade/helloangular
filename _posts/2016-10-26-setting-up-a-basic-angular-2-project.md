---
layout: post
title:  "Setting up a Basic Angular 2 Project"
author: ire
---

In this tutorial, I'm going to go over how to setup a new Angular 2 project. We will be using the following -

- Typescript
- Angular (version `2.0.0`)
- [Angular CLI](https://github.com/angular/angular-cli) (version `1.0.0-beta.15`)



## Creating a New Project

If you don’t already have the CLI installed, you can do so with the following command - 

```command-line
npm install -g angular-cli@latest
```

This gives us access to a new command, `ng`. To create a new project, we simply write the following - 

```command-line
ng new PROJECT_NAME_HERE
```

This will create a directory, titled after the project, with all the files needed to get started. To start the server, we simply use the `ng serve` command while in the project directory. 

```command-line
cd PROJECT_NAME_HERE
ng serve
```




## Project Files

When the new project is created with the CLI, we get a bunch of files created for us. This is what the project directory looks like by default -

![Angular 2 Project Directory](/content/images/2016/09/Screen-Shot-2016-09-19-at-20.39.38.png)

There's a lot going on here, so I'll just go over the main files you need to know. 

### /angular-cli.json

This file is where all our settings for using the CLI are stored. The CLI allows us to do a lot more than just create a new project. We can generate our components, create builds, and even deploy the app to GitHubPages with a single command. 

For example, we can specify the CSS pre-processor we want to use under "defaults" -

```language-json
{
  // More config stuff here
  "defaults": {
    "styleExt": "scss"
  }
}
```



### /src/index.html

This is the index file for the project. By default, this is what it looks like - 

```language-html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>HelloAngular</title>
  <base href="/">

  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
  
</head>
<body>
  <app-root>Loading...</app-root>
</body>
</html>
```

There are a few things to note here - 

1. The `<base href="/">` meta tag allows us to specify what the base URL is for the application. If you have used Angular 1.x, you will notice that some applications have a `#` in the URL. Specifying the `base` allows us to avoid having to do this.  

1. The `<app-root>` element is what the AppComponent is called by default (the AppComponent is covered below). So whatever we have specified as the template for the AppComponent will show within these tags. 

1. You will notice that there are no JavaScript files included here. When we build our application, the files we be created and included. 



### /src/main.ts

This is the main script for the application. All our other files are loaded into it as [ES6 Modules](). 

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

The main thing to notice here is that this is where we bootstrap our AppModule. I will cover bootstrappping in more detail in a later article, but what you need to know for now is that this is how we launch our application. 



### /src/app/app.module.ts

This is the main module for the application. By convention, it is called `AppModule`. Here, we declare everything that is being used in our application, from the native Angular modules to the components we create ourselves. 

A module is created using `NgModule`. Using `NgModule`, we can specify a number of things about our module, but I will go over the four main ones -

1. `declarations` - The list of directives/pipes that belong to the module, e.g. the `AppComponent`
2. `imports` - The list of modules whose exported directives/pipes should be available to templates in this module. For example, we put Angular's `HttpModule` here. Third-party modules will also be specified here. 
3. `providers` - The list of injectable objects used within the module, e.g. Services
4. `bootstrap` - The list of components that should be bootstrapped when this module is bootstrapped, in this case our `AppComponent` 

And here is the default module generated -

```language-javascript
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
```




### /src/app/app.component.ts

This is the main component for the application. By convention, it is called `AppComponent`, and is created using the `@Component` notation.  


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

Notice here that the selector for our main app component is called `app-root`, and matches the element in our main index.html file. 






## Running the Application

To run the application for development, we use the following CLI command.

```command-line
ng serve
```

This will spin up a server with live reload. 


## Building for Production

Once we have created our application and we want to deploy, we can use the CLI to build.

```command-line
ng build --prod
```

This will create a `dist` directory with all the generated files, ready to be pushed to the production environment.


&nbsp;

That's it! Over the next few articles, I will go over some more basics of Angular 2 before diving into the more complex things. If you have any suggestions for topics you will like to have covered, send me a message on twitter at [@IreAderinokun](https://twitter.com/IreAderinokun).