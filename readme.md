# Angular Notes

## The Big Picture

-   Every angular app has an "App Module"
	-   Modules are "buckets" to organize components and services.
	-   Some apps organize features into different modules.
	-   Angular also provides other modules to provide more functionality.

-   Routes allow changes in the url to rerender components.
-   Components are made of HTML templates and code.
	-   They should not have the logic for complex things like getting data or making requests, these should be delegated to services.
	-   Rendering can also be accomplished through directives or child components.


## Components and Modules

-   Building blocks of Angular for building out the UI.
-   Made up of code and an HTML template.
-   Has a "selector" which is technically a tag name
	-   Ex: `<app-customers> </app-customers>`

-   Code
	-   Imports
	-   Decorators
		-   Meta data about components.
		-   Starts with the "@" symbol
-   Class
	-   Actual code for getting data in and out of the html template.

-   Modules organize components and services into groups for reusable functionality.
-   In a module, if the bootstraped/root module imports something, the rest of its modules can also use it. For example, importing browserModule shouldn't be done more than once. If you want that functionality, child modules import "CommonModule", which inherits the imports of the parent.

## More Components and Modules Basics
- When you create an Angular app with the Angular CLI, it will automatically create an app.component.ts file and an app.module.ts file.
- A **decorator** is a function that intercepts the creation of a service (like components, modules, etc.) allowing it to override or modify the behavior of the service.

### Example of an app.component
import { Component, OnInit } from '@angular/core';
    
    @Component({
    
    selector:  'app-root',
    template: `
    <h1>Hello World</h1>
    `
    })
    
    export  class AppComponent implements OnInit {
  
	    constructor() { }
	    
	    ngOnInit() {
	    
	    }
    }

- @Component decorator has been given a selector of 'app-root'. This means the tag name for the element, and it's how you address it in the index.html (or whatever html file)
- It will render the 'template' which above is inline, but in normal projects will reference another html file.
- The third part, the component class,  implements an intereface called "OnInit", which forces the component to have an ngOnInit function, which is useful for data binding.


### Example of an app.module

    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    
    import { AppComponent } from './app.component';
    
    @NgModule({
	    imports: [ BrowserModule ],
	    declarations: [ AppComponent ],
	    bootstrap: [ AppComponent ]
    })
    export  class AppModule { }
- Angular CLI also automatically adds a module for us, which is like a bucket that declares the different components we use and groups it together.
- NgModule import is a decorator for creating the module.
- BrowserModule introduces more functionality, it's a built in module we're importing from to use directives from later on.
- A **directive** is used to add behavior to our existing DOM elements or existing components. 



****What is the difference between a directive and a component?****

*Write a component when you want to create a reusable set of DOM elements of UI with custom behaviour. Write a directive when you want to write reusable behaviour to supplement existing DOM elements.*

 
 
- AppComponent is the component exported from the app.component file.
- Under @NgModule, we pass in an object with the following properties so the module can properly communicate with other components and modules:
	- imports are other exported declarations from other modules we want to make available in the current module (we want BrowswerModule to be available to components in our AppModule)
	- declarations are to make directives (including components and pipes) from the current module available to other directives also in the current module. Whenever we create another component in the same module, and we want them to talk to each other, they both have to be declared here.
	- bootstrap is a special property, it tells Angular what file to "bootstrap" or which file to start with first. It is the first thing that loads.

## Creating Components
You can create components with the Angular CLI command:

`ng generate component componentName` or `ng g c componentName`

It will automatically create a folder with 4 files, a unit test file, an html template, a css file, and the component code.

You can also add on the -d flag for "dryrun" for any Angular CLI command, which will tell you what the command will do without actually altering your file structure. ex: `ng g c customerTable -d`

When naming files, you normally do nameOfThing.typeOfThing.extension or like `customers.component.ts`

### Another example of a component

    import { Component, OnInit } from '@angular/core';
    
    @Component({
	    selector:  'app-customers',
	    templateUrl:  './customers.component.html'
    })
    
    export class CustomersComponent implements OnInit {
	    title: string;
	    people: any[];
    
	    constructor() {}
    
	    ngOnInit() {
		    this.title =  'Customers';
		    this.people = [];
	    }
    }
- Notice how in the @Component decorator, instead of "template" we use "templateUrl", which will reference another html file.
- Also it is important that this is exported so that a module can import it.


### Another example of a module

    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';

    import { CustomersComponent } from './customers.component';

    @NgModule({
	    imports: [ CommonModule ],
	    declarations: [ CustomersComponent ],
	    exports: [ CustomersComponent ]
    })
    
    export class CustomersModule { }
- This is a CustomersModule, which is another module we created seperate from the AppModule, and shows an example of other modules/components working with each other.
- Notice we import the CustomerComponent we exported from the last example.
- Instead of importing BrowserModule, we import CommonModule. Importing browserModule shouldn't be done more than once. If you want that functionality, child modules import "CommonModule", which inherits the imports of the parent.
- NgModule explanation:
	- We want this shared functionality to be available to this module, so under imports we import [ CommonModule ].
	- We want other components in the CustomersModule to be able to use the CustomersComponent, so we declare [ CustomersComponent ].
	- We want other modules to be able to also use the CustomersComponent in this module, so we export [ CustomersComponent ].

Meanwhile in the original app.module, we import the CustomersModule so that the actual app can use it:

    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    
    import { CustomersModule } from './customers/customers.module';
    import { AppComponent } from './app.component';
    
    @NgModule({
	    imports: [ BrowserModule, CustomersModule ],
	    declarations: [ AppComponent ],
	    bootstrap: [ AppComponent ]
    })
    export  class AppModule { }
