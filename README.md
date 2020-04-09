# Introduction to Angular

Welcome to this introductory course about Angular where you will learn about the basics of 
building applications in Angular framework. You'll build a basic blog application where you 
can create, remove, delete and update blog posts that persist in your browser though 
the `indexedDB` API. We recommend you to use `@angular/material` UI library that has a 
lot of pre-built components.

## Generating the project

1. Make sure that you have `node` and `npm` installed on your local machine. If you do not, 
then follow this [guide](https://nodejs.org/en/download/package-manager/)
2. Install `@angular/cli` globally

    `npm install -g @angular/cli`
    
3. Run `ng new blog --style=scss --routing`
* NB! You can replace `blog` with any name you want to use for this project.

* NB! If you do not want to install a global dependency then use the following command to 
generate the project `npx -p @angular/cli ng new blog --style=scss --routing`.

## Understanding the generated project structure

* `angular.json`
* `package.json`
* `src/`
* `e2e/`
* `tsconfig.json`
* `node_modules`
* Finally, where to put my own stuff?

## Running, testing and building your project

* `ng serve` - this starts your Angular application in development mode. Default port is 4200.
* `ng test` - this runs all the unit tests described in your Angular application project
* `ng build` - this builds Angular application
* How to set up sourcemaps and debug in Chrome DevTools?

## Module

* What is a module?
* core, shared and feature module
* Short intro into decorators
* declarations, imports, providers

## Component

* ES6 class syntax
* component metadata
* short intro into semantic HTML
* template interpolation, property binding, event binding
* component interaction
* pipe
* component lifecycle

## Directive

* component vs directive
* why do we need directives?
* structural directives
* reacting to host element's events

## Styling an Angular Application

* style encapsulation vs global nature of CSS (short overview of ShadowDOM?)
* positioning in CSS
* CSS selectors
* flexbox and grid layouts
* `ngClass` and `ngStyle`
* benefits of style preprocessor

## Provider

* Dependency injection in Angular
    
    >Dependencies are services or objects that a class needs to perform its function. 
    DI is a coding pattern in which a class asks for dependencies from external sources rather than creating them itself.
    
    ```typescript
    import { Component }   from '@angular/core';
    import { BlogService } from './services/blog.service';
    
    @Component({
      selector: 'app-my-component',
      // ...
    })
    export class MyComponent {
      // ...
    
      // Dependency Injection
      constructor(private blogService: BlogService) {
        
      }
    
      // ...
    }
    ```
* `OnInit` vs `constructor`
    
    `constructor` is a TypeScript feature to do something specific at the generation of a new instance. When using 
    Angular framework, we should use constructor for dependency injection - to inject required services or context 
    providers, for example, to the component.
    
    `OnInit` is an Angular's lifecycle hook that is run when Angular has finished the creation of the component 
    (after all injections have been done etc). This is the part where we can do our business logic specific 
    initialization, for example.
    
    We can make use of the `OnInit` method by implementing `OnInit` interface in our component as follows:
    ```typescript
        import { Component, OnInit } from '@angular/core';
        import { BlogService } from './services/blog';
        
        @Component({...})
        class MyComponent implements OnInit {
  
          constructor(
            // we inject the blog service to our component
            private blogService: BlogService
          ) {}
          
          // implement the OnInit functionality    
          ngOnInit(): void {
            // here we can use the blog service for example to fetch all blog posts after component is created
            this.blogService.fetchBlogs().subscribe(...);
          }
        }
    ```
* Purpose of a service
    >Components shouldn't fetch or save data directly and they certainly shouldn't knowingly present fake data. 
    They should focus on presenting data and delegate data access to a service.
    
    We are using dependency injection to inject and use service in our components. We can generate service using Angular CLI: `ng generate service blog`.
    *Note, you can use any other name instead of `blog` depending on your service.*
        
    This results in generating `blog.service.ts` file. *For now, let's ignore the generated `*.spec.ts` file.*
    ```typescript
    import { Injectable } from '@angular/core';
    
    @Injectable({
      providedIn: 'root' // this ensures the service is registered as a singleton provider for the whole module
    })
    export class BlogService {
    
      constructor() { }
    }
    ```
* What is a singleton?
    
    The generated service is a `singleton` service. It means that there exists only one instance of the same kind for the whole application.
    Through the dependency injection, the reference to the singleton instance is given to the component using the constructor.

## Routing in Angular

* Setting up routes

    As we have created our Angular application using CLI and `--routing` flag, it already generated required code to begin with basic routing. 
    The `app-routing.module.ts` is the generated module that we can use to define our routes. The module is already imported in the `app.module.ts`.
    In the `app.component.html` file, `<router-outlet></router-outlet>` tag is added.
    
    Routes can be configured in the `app-routing.module.ts` as follows:
    ```typescript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { FirstComponent } from "./first.component";
    import { SecondComponent } from "./second.component";  
    
    // lets describe our routes and components
    const routes: Routes = [
      {
        path: 'first', // the URL to match, from web-page root, this would match for example: http://localhost:4200/first
        component: FirstComponent // the component which is shown in the router-outlet,
        children: [...] // allows to define sub-routes of the current route (is not covered in this course)
      },
      {
        path: 'second', // http://localhost:4200/second
        component: SecondComponent
      }
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)], // configure the described routes for root
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```
* `router-outlet`

    >The RouterOutlet is a directive from the router library that is used like a component. 
    It acts as a placeholder that marks the spot in the template where the router should display the components for that outlet.
    
    Given the configuration `<router-outlet></router-outlet>`, when the URL /first is visited, the router matches that URL 
    to the route path /first and displays the content of the matching component to the RouterOutlet that you've placed in the host component's template.
* Route guards

    Angular routes can be provided different types of guards: 
    * `canActivate` is used when we want to run some checks before navigating to the specified route. This is useful 
    for checking if user has sufficient privileges to view the contents of the specific page.
    * `canActivateChild` is similar to the previous `canActivate` guard, but this can be used to prevent/allow access 
    to child routes of a specific route.
    * `canDeactivate` is used when we want to prevent user from navigating away from the current route. This is useful 
    when we want to check that user has saved the modified data before navigation, for example.
    * `canLoad` as the `canActivate` actually allows loading a module but not navigated to, we can use `canLoad` guard 
    to prevent the route activation.
    * `resolve` is used when we want to pre-load specific data before navigating to specific route. For example, if we 
    navigate to specific item details page, we want to make sure the item data is loaded before navigating there.
    
    As this is an introductory course to Angular, we will not go into details of implementing all of these.
* `resolve` guard

    `resolve` guard is a guard which takes a map of resolvable objects. An example in our `app-routing.module.ts` would be:
    ```typescript
    ...
   
    const routes: Routes = [
      {
        path: 'first',
        component: FirstComponent,
        resolve: {
          // here we set a resolve guard to resolve all posts, before visiting the '/first/ URL
          posts: BlogPostsResolver 
        }
      }
    ];
    ...
    ```
  
    where the `BlogPostsResolver` (`blog-posts.resolver.ts`) is a singleton service which implements `Resolve<T>` interface, as follows:
    ```typescript
    import { Injectable } from '@angular/core';
    import { ActivatedRouteSnapshot, Resolve, RouterStateSnapshot } from '@angular/router';
    import { BlogPost } from './blog-post';
    import { Observable } from 'rxjs';
    import { BlogService } from './blog.service';
    
    @Injectable({
      providedIn: 'root'
    })
    export class BlogPostsResolver implements Resolve<BlogPost[]> {
       
      // Here we are using the DI to inject BlogService into our resolver
      constructor(private blogService: BlogService) {
      }
    
      // this is the implementation of the resolver
      // it uses BlogService to fetch all posts
      resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<BlogPost[]> {
        return this.blogService.fetchPosts();
      }
    }
    ```
  
    The `BlogPost` (`blog-post.ts`) is an interface which describes blog post object. For simplicity, in our example the interface would be:
    ```typescript
    export interface BlogPost {
      title: string;
    }
    ```
  
    Now we would like to use the resolved data in our component. As for this example, we had URL pointing to `/first`
    and the `FirstComponent` is the component responsible handling this. We are implementing our component as follows:
    ```typescript
    import { Component, OnInit } from '@angular/core';
    import { ActivatedRoute } from '@angular/router';
    import { BlogPost } from './blog-post';
    
    @Component({
      selector: 'app-first',
      templateUrl: './first.component.html'
    })
    export class FirstComponent implements OnInit {
    
      // we inject ActivatedRoute context to our component
      constructor(private activatedRoute: ActivatedRoute) {
      }
    
      // we implement the OnInit interface method
      ngOnInit(): void {
        this.activatedRoute.data.subscribe(data => {
          // the resolved data can be accessed by subscribing to the activatedRoute data
          const posts: BlogPost[] = data.posts;
          // for debugging, you can check browser console for results
          console.log(posts);
          
          // now we can use the resolved data in any way we would like to
          ...
        });
      }
    }

    ```
* What is lazy loading?

    Lazy loading is a mechanism used to load only the required parts of the application at the time visited. 
    This means that if an application is big enough, it is sensible to split different parts of the application 
    into different modules (usually this is separated by features and/or sub-features). This means, that specific 
    module JavaScript files are loaded only if the feature-specific route is visited.
    
    As this is an introductory course and the application we are developing is rather small, we will not cover lazy loading this time.

## Basics of RxJS

From the examples above, we have already seen something like `.subscribe(..)` and `Observable`. 
These are part of so-called `reactive programming` and the library providing such features is called `RxJS`.
This is a library which makes it easy to perform asynchronous requests and handle callbacks. 

To help you better understand the basics and features of RxJS, we recommend you to watch
and try out the examples provided in the video below:

[![Top 10 RxJs Operators in Angular](http://img.youtube.com/vi/5TnWFaI49aw/0.jpg)](https://www.youtube.com/watch?v=5TnWFaI49aw "Top 10 RxJs Operators in Angular")

The video will cover topics such as:
* Observer pattern
* `Observable`, `Subject` and `BehaviorSubject`
* Subscribing in the component and template
* Piping operators
* Combining multiple streams

## Reactive vs template-driven forms in Angular

* why prefer reactive form to template-driven one?
* setting up a reactive form
* form validation for each field and for entire form
* submitting a form

## Tasks

### Application shell

### Creating a blog post and saving it into IndexedDB

### Creating a view for showing the blog posts saved in IndexedDB

### Edit a blog post

### Delete a blog post

## [selle põhjal saame igale taskile requirementsid sõnastada, mis on tudengile kohased]

* When the user runs our application and navigates to `localhost:4200` she should be able to see a header with 
application title, a list of blog posts (if the user has added any) and a `+` button to create a new button.
* when user navigates to `/new`
  * she can add a new blog post that has a title and a list of paragraphs
  * title and at least one paragraph are required to create a new blog post
  * if the user has made changes to any of the fields, she will be prompted before navigating away that if she continues, 
  the work will be lost. She can decide to cancel the navigation and return to the page to finish creating the blog post
  * every created blog post has a unique id and timestamp with date of creation
  * created blog posts have to persist across browser sessions
* when user navigates to `/` she should see a list of blog posts
  * by default the list is sorted from newest to oldest, but user can order from oldest to newest
  * if the blog post contains multiple paragraphs, only the first one is shown alongside with a button to view the full 
  blog post. If the first paragraph is longer than `600` characters, the rest gets truncated and `...` is added to the 
  end. When the user clicks a button to view the full post, she is redirected to `/:id`
  * user can delete a single blog post
  * user can edit a blog post, which redirects her to `/edit/:id`
* when user navigates to `/:id` she sees the blog post in full. The blog post can be deep linked by `id`, meaning that 
the correct blog post will be shown when user accesses it directly, without going through the blog post list view. For 
example refreshing the page.
  * when user navigates to /edit/:id
  * she can edit title and paragraphs and save the blog post
  * title and at least one paragraph are required to save the edited blog post
  * if the user has made changes to any of the fields, she will be prompted before navigating away that if she continues, 
  the work will be lost. She can  decide to cancel the navigation and return to the page to finish editing the blog post
  * this view has to support deep linking
