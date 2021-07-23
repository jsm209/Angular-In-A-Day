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
    
    
## Data Binding

 - It's how we get data from the code to the template. 
 - **Property binding** is how we get data from the component code to the template.
 - **Event binding** is how we get data from the template (ex: click events) to
   the component code.
- Data binding syntax:
	- Write values using interpolation: {{ propName }}
	- Bind to DOM  properties using **[property]** or **bind-property**
		- Includes styling properties (ex: `[style.color]="textColor"` and `[class.active]="isActive"`)
	- Bind to DOM  events using **(event)** or **on-event**
- Built-in Directives:
	- Directives are classes that add additional behavior to elements
	- ex: ngFor, ngIf, ngClass, ngStyle, ngModel, etc.

### Basic Data Binding Example:
#### customers.component.html

    <h1 [hidden]="!isVisible">{{ title }}</h1>
    
    <button (click)="changeVisibility()">Show/Hide</button>



#### customers.component.ts

    import { Component } from '@angular/core';
    
    @Component({
	    selector:  'app-customers',
	    templateUrl:  './customers.component.html'
    })
    
    export  class CustomersComponent {
	    title: string;
	    isVisible =  true;
	    
	    changeVisibility() {
		    this.isVisible =  !this.isVisible;
	    }
	}


## Directives and Interpolation
- Structural directives are denoted by a * which means that they will change the structure of the DOM. (ex: *ngFor="")

### Example of directives and interpolation

    <table  class="table table-hover">
	    <thead>
		    <tr>
			    <th>Name</th>
			    <th>City</th>
			    <th>Order Total</th>
		    </tr>
	    </thead>
	    <tr *ngFor="let cust of filteredCustomers">
		    <td>
			    <a>
				    {{ cust.name }}
			    </a>
		    </td>
		    <td>{{ cust.city }}</td>
		    <td>{{ cust.orderTotal }}</td>
	    </tr>
	    <tr *ngIf="filteredCustomers && filteredCustomers.length">
		    <td  colspan="2">&nbsp;</td>
		    <td>
			    {{ customersOrderTotal }}
		    </td>
	    </tr>
	    <tr *ngIf="!filteredCustomers || !filteredCustomers.length">
		    <td  colspan="4">No customers found</td>
	    </tr>
    
    </table>
    Number of Customers: {{ filteredCustomers.length }}
- Loops through filteredCustomers, takes out relevant variables and references them with cust.property to display them in a table.
- There are also ngIf statements for displaying more data or messages depending on if filteredCustomers is empty or not.

## Event Binding
- Detects things like button click, mouse moves, etc.
- Put raw DOM event in parenthesis, followed by the callback function.
- ex: `<th (click)="sort('name')">Name</th>`will call the sort function, passing in the variable string 'name'.


## Input Properties
- A way for child components to accept input from its parent component. This is useful for passing data from the parent to the child.
- You have to create an input property if you want to pass data down.
- You use the @Input() decorator and bind get/set blocks.

### Example of binding input properties
In this example, the child is the `customers-list` component who wants data from its parent, `customers` component.
#### customers-list.component.ts

    private  _customers: ICustomer[] = [];
	    @Input() get customers(): ICustomer[] {
		    return  this._customers;
	    }
	    
	    set customers(value: ICustomer[]) {
		    if (value) {
		    this.filteredCustomers =  this._customers = value;
		    this.calculateOrders();
	    }
    }
- We only need to put the input decorator on one of the blocks and Angular will know that it applies to both.
- We call the get/set blocks get *customers*() and set *customers*(), meaning that the input property's name is "customers".

#### customers.component.html (parent template where child component lives)

    <app-customers-list [customers]="people"></app-customers-list>
- Notice  how `[customers]` is contained in square brackets, denoting a DOM property. It is set to `"people"`, which is a variable (data) found in the parent code (customers.component.ts). In this way data is passed from the parent (customers.component) to the child (customers-list.component).

## Pipes
- Example of a pipe: `{{ cust.name | uppercase }}` 
- Takes data on left, puts it through the pipe, and outputs it.
- Useful for transforming data quickly, like adding a currency symbol, capitalization, etc.
- Another example, but with a pipe that takes in parameters: `{{ cust.orderTotal | currency:'USD':'symbol' }}`

### Example of Building Custom Pipes

    import { Pipe, PipeTransform } from '@angular/core';
    
    @Pipe({ name:  'capitalize' })
    export  class CapitalizePipe implements PipeTransform {
	    transform(value: any) {
		    if (value) {
			    return value.charAt(0).toUpperCase() + value.slice(1);
		    }
		    return value;
	    }
    }

- Remember to register the pipe with at least one module to use it in component tempates.

### Example of Importing Custom Pipes

    import { NgModule } from '@angular/core';
    import { CapitalizePipe } from './capitalize.pipe';
    
    @NgModule({
	    declarations: [ CapitalizePipe ],
	    exports: [ CapitalizePipe ]
    })
    
    export  class SharedModule { }

- In the declaration field of a module, you can put pipes, components, or directives.
- In this specific example, this module is called "shared" module, meaning other modules import it to use shared functionality. If we want other modules to have access to the custom pipe, you also have to put the pipe in the export field of the shared module to make it available.


## Output Properties
- Emitting data from child to parent (input properties are vice versa, parent to child).
- EventEmitter is a way for a child to send data up to a parent.

### Filtering Example for EventEmitter

    import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
    
    @Component({
	    selector:  'filter-textbox',
	    template: `
		    Filter: <input  type="text" [(ngModel)]="filter" />
	    `
    })
    
    export  class FilterTextboxComponent implements OnInit {
    
	    private  _filter: string;
	    @Input() get filter() {
		    return  this._filter;
	    }
    
	    set filter(val: string) {
		    this._filter = val;
		    this.changed.emit(this.filter);  //Raise changed event
	    }
	    
	    @Output() changed: EventEmitter<string> =  new EventEmitter<string>();
    
	    constructor() {}
	    
	    ngOnInit() {
	    }
    }
- Notice how the input property is named "filter". 
- In the set filter function, it emits a value to "this.changed", which is another variable of type EventEmitter.
- "changed" is an output property of a new object of type EventEmitter. It will pass a value of type string up to the parent so that the parent can handle the filtering logic.
- Notice `<input  type="text" [(ngModel)]="filter" />` this is a two way data binding, allowing the filter variable to be consistent with the input element. 
	- It will take the value of filter, and with the square brackets "[]" update the value of the input, and with the parenthesis "()" it will also update the `filter` variable.
	- Remember ngModel needs to be imported. It is in the built in angular module called FormsModule. 

### Using the Emitted Event Object in Parent

    <filter-textbox (changed)="filter($event)"></filter-textbox>
- Notice above how we use the DOM event "changed" so that whenever the input is changed, it will call the function called "filter" and pass in an object called "$event".
- $event stores the filter text we want in a property called "data", but we don't have to do $event.data because Angular automatically extracts it for us.
- We can use the data in a function to filter results like in this example:


	    filter(data: string) {
		    if (data) {
			    this.filteredCustomers =  this.customers.filter((cust: ICustomer) => {
				    return cust.name.toLowerCase().indexOf(data.toLowerCase()) > -1  ||
					    cust.city.toLowerCase().indexOf(data.toLowerCase()) > -1  ||
					    cust.orderTotal.toString().indexOf(data) > -1;
			    });
		    } else {
			    this.filteredCustomers =  this.customers;
		    }
			    this.calculateOrders();
	    }


## Services
- Important for reusing code and sharing it across other components or services.
- Ex: We might want our components to get data from a server but we don't want to tell every component how to do it. Also different components might use the same data in different ways. We can extract this out to a service, or *abstraction*.
- By default, services are **singletons**, or an object created once in memory, and it's shared and reused in different spots in the application.
- In Angular, it's easy to create. It is a simple class with the *Injectable* decorator.
	- Allows the service to be used in the constructor of other service, or dependency injection.

### Example of a Service

    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Observable } from 'rxjs/Observable';
    import { map, catchError } from 'rxjs/operators';
    
    import { ICustomer, IOrder } from '../../app/shared/interfaces';
    
    @Injectable()
    export  class DataService {
    
	    baseUrl: string =  'assets/';
    
	    constructor() { }
    
	    private handleError(error: any) {
		    console.error('server error:', error);
		    if (error.error instanceof Error) {
			    const errMessage = error.error.message;
			    return Observable.throw(errMessage);
			    // Use the following instead if using lite-server
			    // return Observable.throw(err.text() || 'backend server error');
			}
		    return Observable.throw(error ||  'Node.js server error');
	    }
    }
- `import { Injectable } from '@angular/core';` is necessary for creating any service. It is used for the constructor so that we can inject other dependencies, but the best practice is to use the Injectable decorator for all services even if you aren't injecting anything.
- We don't *declare* services. We *provide* services. In other words, in the module the service belongs to, we give the serviceName to a providers field, not the normal declarations field.

### Example of Making Service Available in core.module.ts

    import { NgModule } from '@angular/core';
    import { HttpClientModule } from '@angular/common/http';
    
    import { DataService } from './data.service';
    import { SorterService } from './sorter.service';
    
    @NgModule({
	    imports: [ ],
	    providers: [ DataService, SorterService ]
    })
    export  class CoreModule { }
- Notice that the services are in *providers*, not declarations in the module declaration.

## Making HTTP Calls in Angular

### Observable Objects

- Used for asynchronous requests. It is a part of rxjs (reaactive extensions for javascript)
- `import { Observable } from 'rxjs/Observable';`
- It allows us to start an operation, and allows someone else to subscribe to it so that when the data gets back from the server they'll get it.

### Example HTTP Call

    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Observable } from 'rxjs/Observable';
    import { map, catchError } from 'rxjs/operators';
    
    import { ICustomer, IOrder } from '../../app/shared/interfaces';
    
    @Injectable()
    export  class DataService {
    
	    baseUrl: string =  'assets/';
    
	    constructor(private  http: HttpClient) { }
    
	    getCustomers() : Observable<ICustomer[]> {
		    return  this.http.get<ICustomer[]>(this.baseUrl +  'customers.json')
    
			    .pipe(
				    catchError(this.handleError)
			    );
	    }

	    getCustomer(id: number) : Observable<ICustomer> {
		    return  this.http.get<ICustomer[]>(this.baseUrl +  'customers.json')
			    .pipe(
				    map(customers => {
					    let customer = customers.filter((cust: ICustomer) => cust.id === id);
					    return (customer && customer.length) ? customer[0] :  null;
				    }),
				    catchError(this.handleError)
			    )
	    }
    
	    getOrders(id: number) : Observable<IOrder[]> {
		    return  this.http.get<IOrder[]>(this.baseUrl +  'orders.json')
			    .pipe(
				    map(orders => {
				    let custOrders = orders.filter((order: IOrder) => order.customerId === id);
				    return custOrders;
			    }),
			    catchError(this.handleError)
		    );
	    }
    
	    private handleError(error: any) {
		    console.error('server error:', error);
		    if (error.error instanceof Error) {
			    const errMessage = error.error.message;
			    return Observable.throw(errMessage);
			    // Use the following instead if using lite-server
			    // return Observable.throw(err.text() || 'backend server error');
		    }
			    return Observable.throw(error ||  'Node.js server error');
		}
	}
- We use this.http.get to make a get request, where `<returnType>(URL)` is used. 
- We use a .pipe to handle errors, data flows through the pipe and we attach operators to the pipe (in this example, we use `catchError`) to filter the results.
