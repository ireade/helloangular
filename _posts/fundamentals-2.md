###Why we are here
In the first part of this series you were taken through how we can set up basic routing functionality in our Angular (Angular 2) app. Don't forget, you can clone the [repo](https://github.com/Enirate/ng-router) if you need it. In this part we shall be flexing our routing muscles a wee bit by reading data from the route parameter and displaying a corresponding information.

####The Details components
Here is what we want to achieve, if a click is made on a name, the app will redirect us to a new component that holds the bio of that individual. To do this we shall create to new components using angular cli. To do this run the following commands
```bash
ng g c components/tech-leaders-detail
```
```bash
ng g c components/techpreneur-detail
```

The next step is to add these components to the routing system of the app in the `app.routing.ts` file. After which our `app.routing.ts` will look like so:
```javascript
import { NgModule } from '@angular/core'; //we need this because we'll import routing modules and it will serves as decorator.
import { Routes, RouterModule } from '@angular/router'; //routing modules needed

//Our component on which we want to use routes
import { AboutComponent } from './components/about/about.component';
import { TechLeadersComponent } from './components/tech-leaders/tech-leaders.component'; //  'import { TechLeadersComponent }' we import classes 
//so we locate the habitat file
import { TechpreneursComponent } from './components/techpreneurs/techpreneurs.component'; //'./' takes you out of the present file into the app folder
import { ErrorComponent } from './components/error/error.component';
import { TechLeadersDetailComponent } from './components/tech-leaders-detail/tech-leaders-detail.component';
import { TechpreneurDetailComponent } from './components/techpreneur-detail/techpreneur-detail.component';

const routes:Routes = [ 
    
    //we create an array of type Routes holding objects which in turn holds routing logics as props
    {
        path: 'about', //first props of routes object is path of type string holding our base url extension.
        component: AboutComponent //component loaded at this url. The second props. (props = property.)
    },
    {
        path: 'techpreneurs',
        component: TechpreneursComponent,
    },
    {
        path: 'tech-leaders',
        component: TechLeadersComponent,
    },
    {
        path:'techpreneurs/techpreneurs-details/:id',
        component: TechpreneurDetailComponent
    },
    {
        //the ':' prefix indicates a placeholder parameter that will be replaced by a real value. in this case the id of techpreneurs and tech-leaders
        path:'tech-leaders/tech-leaders-details/:id', 
        component: TechLeadersDetailComponent
    },
    {
        path:'', //base url will show this component.
        pathMatch:'full',
        redirectTo:'techpreneurs' //redirect to this component path. Notice: component path(string) not component class name
    },
    {
        path:'**', //if url extension doesn't exit users will be redirected to this component.
        component:ErrorComponent
    }
];

//implement modules
@NgModule({
    imports:[RouterModule.forRoot(routes)], //returns a Router Module configured for our predeclared routes
    exports:[RouterModule] //we export the configured module so we can import it in our root module, thereby implementing
    //the routing logic in the entire app.
})

export class AppRoutingModule {}

//Finally we import our components as one, so we don't import twice.
export const routingComponent = [ErrorComponent, TechLeadersComponent, TechpreneursComponent, AboutComponent, TechpreneurDetailComponent, TechLeadersDetailComponent];




```

###Re-writting the Views
The next thing is to add the anchor element to the list of techpreneurs and tech-leaders this will turn them into active links that will redirect users to the bio of that individual. Here is what `techleaders.component.html`and `techprenuers.component.html` components will look like after the change:
```html
<ul>
<li *ngFor="let person of techLeadersList" ><a [routerLink]="['/tech-leaders/tech-leaders-details', person.id]">{{person.name}}</a></li>
</ul>
<!-- here we bind our list to the techLeaderList array in our component and we display the name
property of each object in the array-->
<!--[routerLink] is an angular directive that helps with navigation, here it's has two elements
 as properties, the first is the detination url the second, the route parameter -->
 <!--person is an object of the techLeaders array, it has 3 properties: id, name and bio-->
```
and 

```html
<ul>
<li *ngFor="let person of techpreneursList" class="text-success" ><a [routerLink]="['/techpreneurs/techpreneurs-details',person.id]">
{{person.name}}</a></li>
</ul>

```

###Getting route parameters
We need route parameters to get the bio and the name of a particular techpreneur/tech-leader so as to display them in the body and title of the detail page, respectively. To do this we head over to the detail components `component.ts` files and add few lines of code. Here is the summary of what will be done:
1.  We use the activated route service of `@angular/router` library to get the value of `id`
2.  We use `id` to get the object of the tech-leader/techpreneur from the array
3.  We use the Title service of the `@angular/platform-browser` to set the title the page to the name of the techpreneur/tech-leader.
at the end our component class will look like so
```javascript
//components/tech-leaders-detail/tech-leaders-detail.component.ts

import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { Title } from '@angular/platform-browser';
import { techLeaders } from '../../data/tech-leaders';

@Component({
  selector: 'app-tech-leaders-detail',
  templateUrl: './tech-leaders-detail.component.html',
  styleUrls: ['./tech-leaders-detail.component.css']
})
export class TechLeadersDetailComponent implements OnInit {
  
  //these class variables will hold the properties of the object the tech-leader under consideration 
  personID:number;
  personBio:string;
  personName:string;

 //Dependency injection. Inject the ActivatedRoute & Title service
  constructor(private activatedRoute:ActivatedRoute, private title:Title) { }

 //Every thing should happen OnInit i.e once the component is displayed in the view
  ngOnInit() {
    //activatedRoute returns observables, here we subscribe to the params method i.e the method that hold the url parameters
    this.activatedRoute.params
    .subscribe(res => {
      //res is an object that contains a property named id, it holds the id of the tech-leader in the url
      this.personID = +res['id']; //the addition operator turns the type to integer and the id is stored in personID variable
      this.personID -=1; //we are dealing with an array. Though our id starts from 1, arrays index from 0, here we minus 1 from the id to get the correct techLeader
      this.personBio = techLeaders[this.personID].bio;//the bio is stored in personBio variable
      this.personName = techLeaders[this.personID].name; //the name is stored in personName variable
      this.title.setTitle(this.personName); //we use the setTitle method of title service to set the page title to the name of the techLeader
    })
  }

}

```
and

```javascript
//components/techpreneur-detail/techpreneur-detail.component.ts

import { Component, OnInit } from '@angular/core';
import { techprenuers } from '../../data/techpreneurs';
import { ActivatedRoute } from '@angular/router'; //to get params from route we need this service. it returns observables
import { Title } from '@angular/platform-browser'; //to set page title we need the title service from the platform-browser library

@Component({
  selector: 'app-techpreneur-detail',
  templateUrl: './techpreneur-detail.component.html',
  styleUrls: ['./techpreneur-detail.component.css']
})
export class TechpreneurDetailComponent implements OnInit {

  personID:number;
  personName:string;
  personBio:string;

  //dependency injection: activatedRoute & title are injected here
  constructor(private activatedRoute: ActivatedRoute, private title: Title) { } 

  ngOnInit() {
    this.activatedRoute.params
    .subscribe(res => {
      this.personID = +res['id']; //the addition sign converts the value to int
      this.personID -=1; //array index starts from 0 but the id starts from one, we need to subtract one to get the correct person
      this.personName = techprenuers[this.personID].name; 
      this.personBio = techprenuers[this.personID].bio;
      this.title.setTitle(this.personName); //we use the setTitle method of the title service to dynamically change our title.
    })
  }

}

```

###Wehdone sir, wehdone ma.
We've come to the end of this series but don't forget this is about Angular routing basics/fundamentals, there is still a lot of stuffs to know about routing in Angular. Are you are an adventurous fellow? please click on this [link](https://angular.io/docs/ts/latest/guide/router.html) to continue your adventure on routing island. Thank you.