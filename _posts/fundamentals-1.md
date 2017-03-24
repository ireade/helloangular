Routing is not a hard thing if your website serves different pages for each URL, let say using PHP to serve pages from an Apache server or doing it the good old  way by linking static HTML pages together. Come to think of it, what if you have a single page application i.e apps that has only one view and rotates other views within this single/main view, how can you attach a corresponding extension to your base view for each view displayed in the container(e.g /about), or get parameters from a URL etc?.

Don’t think too much, angular has got you. Angular router is an optional service provided by Angular 2 framework to help implement routing in single page Angular 2 app without much fuss.

In this article i will be taking you through the process of creating an Angular 2 app, implementing routing logic in the app, getting parameters from your route and using the title functionality of the platform-browser class. Let’s go there.

##GETTING STARTED

The first thing to do is to create a new project using Angular Cli.
````
ng new YOUR-PROJECT-NAME
```
######Note:
This project is available on github. You can clone it here [here](https://github.com/Enirate/ng-router).

Next thing to do is to create the needed components. We need four: about component, tech-leaders component , techpreneurs component and error component.

But before we create any component we need to create a sub-folder named 'components' inside our app folder in order to keep things neat.

Angular Cli can also help with scaffolding, so go to you editor terminal( I use Vs Code and the terminal can be accessed by
```
Ctrl + `
```).

Run these commands in the terminal of your editor
```
ng g component components/about
```
```
ng g component components/tech-leaders
```
```
ng g component components/techpreneurs
```
and 

```
ng g component components/error
```
users will be redirected to the error component if the URL entered doesn't match our predefined routes.

Up next is our service, i twill be named 'general' because it will feed data to techpreneur and tech-leaders components.

we'll create it using Angular Cli like so:

```
ng g service general
```

before we call it a day with file creation we need to set-up our data source, to do that, we shall create another sub-folder in our src/app folder named 'data' and inside it we shall create two files

i. 'tech-leaders.ts' which will look like this
```javascript
interface TechLeaders{
    id:number;
    name:string;
    bio:string;
};


export const techLeaders:TechLeaders[] = [
    {
        id:1,
        name:"Otemuyiwa Prosper",
        bio:"Google Developer Expert, Mentor, extremly      friendly and extremly intelligent guy. He's a E-dragon, he  breathe fire"
    },
    {
        id:2,
        name:"Christian Nwamba",
        bio:`CODE BEAST! Tame him if you can.`
    },
    {
        id:3,
        name:"Ire Aderinokun",
        bio:`Google Developer Expert, an Inspiration. She code live at a developer meet-up, How about that? `
    }
];
```

ii. 'techprenuer.ts' which will look like this:
```javascript
interface Techprenuers {
    id:number;
    name:string;
    bio:string;
}

export const techprenuers:Techprenuers[] = [
    {
        id:1,
        name:"Mark Essien",
        bio:`A prominent tech entrepreneur from ikot ikpene. Mark has a background in software development and he's keen on building the future of africa. He's the CEO of hotels ng. `
    },
    {
        id:2,
        name:"Dimgba Kalu",
        bio:`The CEO of Projaro NG. Dimgba is passionate  about building people and product in order to create a desirable future for Nigeria and africa as a whole`
    },
    {
        id:3,
        name:"Iyin Aboyeji",
        bio:`AKA E, Iyin is a legendary strategist and brillant entrepreneur. He's the Elon Musk of africa, bringing the future to us and implementing ideas many will dread to think of.`
    }
];
```
Up next is the logic for our service i.e general service. Open the `general.service.ts` file and update it in this manner
```javascript
import { Injectable } from '@angular/core';

import { techLeaders } from './data/tech-leaders'; //import techLeaders array 
import { techprenuers } from './data/techpreneurs'; //import techpreneurs array

@Injectable()
export class GeneralService {

  constructor() { }

  getTechLeaders(){
    return techLeaders; //return techLeaders array of objects
  }

  getTechpreneurs(){
    return techprenuers; //return techpreneurs array of objects.
  }



}

```
The code has a lot of comment to tell you what it's all about, so pay attention to the comment.

Next on the agenda is putting our services to use in the components so go inside your component and make necessary changes like so:

```javascript
// src/app/components/tech-leaders/tech-leaders.components.ts

import { Component, OnInit } from '@angular/core';
import { GeneralService } from '../../general.service'; //import GeneralService

@Component({
  selector: 'app-tech-leaders',
  templateUrl: './tech-leaders.component.html',
  styleUrls: ['./tech-leaders.component.css']
})
export class TechLeadersComponent implements OnInit {

techLeadersList:any[];
  constructor(private generalService:GeneralService) { } //dependency injection i.e we inject our service as a dependency

getTechLeaders() {
  this.techLeadersList = this.generalService.getTechLeaders(); // we fetch tech leaders list from our service
}

  ngOnInit() {
    this.getTechLeaders() //we run getTechLeaders method on init(ialization of this component).
  }

}

```

we are going to use the same service in our techpreneur component, so go to your techpreneur component and add the needed code like so:

```javascript
// src/app/components/techpreneurs/techpreneurs.components.ts

import { Component, OnInit } from '@angular/core';
import { GeneralService } from '../../general.service';

@Component({
  selector: 'app-techpreneurs',
  templateUrl: './techpreneurs.component.html',
  styleUrls: ['./techpreneurs.component.css']
})
export class TechpreneursComponent implements OnInit {

  techpreneursList:any[];
  constructor(private generalService:GeneralService) { }

  getTechpreneurs() {
    this.techpreneursList = this.generalService.getTechpreneurs();
  }

  ngOnInit() {
    this.getTechpreneurs();
  }

}

```
Our service is will not work until is declared as a provider in our root module. let's head over to `app.module.ts` file and add general service as a provider like this:

```javascript
import { GeneralService } from './general.service';//import service

@NgModule({
  ...
  providers: [GeneralService], //declare as provider
  ...
})
 ....
```
Before we start routing, we need to finish off our components by working on the views of our components.

let's start with the About component folder and make changes to the about.component.html like so:

```html
<div>
  <p class="text-justify">
    This is a sample app that teaches about routing in angular 2 
    while celebrating tech legends in Nigeria and africa as a whole
  </p>
</div>
```
###Note: I use bootstrap css framework to style my mark up, to do download bootstrap zip file from bootstrap official site, extract it, copy the `bootstrap.css` file and paste it in your project's assets folder. Finally, reference it in your project `index.html` file like so
```html
<!-- src/app/components/about/about.component.html -->
...
<!--bootstrap css-->
  <link rel="stylesheet" type="text/css" href="assets/bootstrap.css">
...
```
Next in line is the error component, we need something to show those who miss their way. so we make changes in the `error.component.ts` file like so:

```html
<!-- src/app/components/error/error.component.html -->
<h1 class="text-center text-danger">Oga, Nothing dey here oh! 404 things</h1>
```

Next up is the view for our techleaders component, which will be updated like so:

```html
<!-- src/app/components/tech-leaders/tech-leaders.component.html -->
<ul>
<li *ngFor="let person of techLeadersList" >{{person.name}}</li>
</ul>
<!-- here we bind our list to the techLeaderList array in our component and we display the name
property of each object in the array-->
```

And our techprenuer component will be update like so:
```html
<!-- src/app/components/techpreneurs/techpreneurs.html -->
<ul>
<li *ngFor="let person of techpreneursList" class="text-success" >{{person.name}}</li>
</ul>

```
Then we will set up our main layout i.e. `app.component.html` file. This will be the view in which the view of other components will be displayed. We shall add a navigation bar and links to other components. To do all these we shall upload the code in this manner:

```html
<!-- src/app/app.component.html -->
  <div class="container-fluid">
    <div class="navbar-header">
      <a class="navbar-brand" href="#">Nigeria Tech Community</a>
    </div>
    <ul class="nav navbar-nav">
      <li><a routerLink="/tech-leaders">Tech leaders</a></li>
      <li><a routerLink="/about">About</a></li>
      <li><a routerLink="/techpreneurs">Techpreneurs</a></li>
    </ul>
  </div>
</nav>
<div class="container">
  <router-outlet></router-outlet>
</div>
``` 

Here we go. You might be wondering that the `href` attribute of the anchor element has been replaced by `routerLink`, Yeah, `routerLink` is an angular directive in which pre-defined routes are passed as a value. It help us to navigate around our app and it can also be use to effortlessly manage dynamic routes. And `<router-outlet></router-outlet>`? it's an Angular directive that specifies the spot where views will be displayed as we navigate through the app.

##Declaring the routes

The next thing on our agenda is to tell angular to keep an eye on the address bar and display corresponding components for each URL pattern discovered in relation to our app URL. That is, let's say the URL of our app is http://localhost:4200, we will tell angular to display the tech-leaders component for this route and display the about component for this URL pattern http://localhost:4200/about e.t.c

#####Here's how to do it 

i.) We will create a new file inside the src/app directory and name it `app.routing.ts` it's a module that will contain our route configuration. so the first thing to do after creating the file is to import the module decorator from `@angular/core` library like so:

```javascript
// src/app/app.routing.ts

import { NgModule } from '@angular/core';
```

ii.) We will import the RouterModule from `@angular/router` service. This module is that guy that will match our URL to the corresponding component. But it is yet to be configured. We import it like so.

```javascript
// src/app/app.routing.ts
...
import { Routes, RouterModule } from '@angular/router';
```

iii.) The next thing to is to import all our components, because they will be referenced when during the configuration of our routes.

```javascript
// src/app/app.routing.ts
...
import { AboutComponent } from './components/about/about.component';
import { TechLeadersComponent } from './components/tech-leaders/tech-leaders.component'; 
import { TechpreneursComponent } from './components/techpreneurs/techpreneurs.component'; 
import { ErrorComponent } from './components/error/error.component';
```

iv.) Defining our routes and corresponding components is the next thing to do. To do this, we will create an array that will hold objects. one of the most common property of this type of objects is `path:` which defines a URL pattern in relation to the base URL, then we have the `component:` property which contains components that will be displayed for the URL pattern. An important note about the arrangement of the route object is that it has to follow an arrangement, well defined URL will come first then the Wildcard route object (i.e the routing configuration that will help handle invalid URL through redirection to valid route or by displaying the error component) will come after. We will define the routes like so:

```javascript
// src/app/app.routing.ts

...
const routes:Routes = [ 
    
    //we create an array of type Routes holding objects which in turn holds routing logics as props
    {
        path: 'about', //first props of routes object is path of type string holding our base url extension.
        component: AboutComponent //component loaded at this url. The second props. (props = property.)
    },
    {
        path: 'techpreneurs',
        component: TechpreneursComponent,
        children:[

        ]
    },
    {
        path: 'tech-leaders',
        component: TechLeadersComponent,
        children:[

        ]
    },
    {
        path:'', //base url will show this component.
        pathMatch:'full',
        redirectTo:'tech-leaders' //redirect to this component path. Notice: component path(string) not component class name
    },
    {
        path:'**', //if url extension doesn't exit users will be redirected to this component.
        component:ErrorComponent
    }
];

```
v.) The next thing to do is to configure the `RouterModule` using the pre-defined routes after which we will export it as a module. Like so:

```javascript
// src/app/app.routing.ts
...
//implement modules
@NgModule({
    imports:[RouterModule.forRoot(routes)], //returns a Router Module configured for our predeclared routes
    exports:[RouterModule] //we export the configured module so we can import it in our root module, thereby implementing
    //the routing logic in the entire app.
})
```
vi.) Finally, we will export an array that contains all our components so as not to import them again in the root module.

```javascript
// src/app/app.routing.ts
...
export class AppRoutingModule {}

//Finally we import our components as one, so we don't import twice.
export const routingComponent = [ErrorComponent, TechLeadersComponent, TechpreneursComponent, AboutComponent];
```
and we'll upadate the `app.module.ts` file like so:

```javascript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';
import { GeneralService } from './general.service';//import service

import { AppRoutingModule, routingComponent } from './app.routing';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent,
    routingComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpModule,
    AppRoutingModule
  ],
  providers: [GeneralService], //declare as provider
  bootstrap: [AppComponent]
})
export class AppModule { }

```
Congrats, you now know the ABC(Basics) of routing in Angular. In the second part, we shall go a bit beyond the basics. We shall access data in our route object and display it as page title and we shall see how to create and use child components and dynamic URL. 
Thank you. 