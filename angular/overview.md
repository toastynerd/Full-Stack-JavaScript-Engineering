Angular Overview
==============================
Angular is a client side javascript webframework that is both flexible and
powerful. This overview will contain brief explanations for the moving pieces
of Angular as well as how our Angular front end to the notes app is going to be
structured.

Moving Pieces
----------------------------------------
Angular is an MVC framework, meaning it is composed of three major moving 
pieces.
  * The view determines what actually gets displayed to the browser. The view
    should only contain code that is directly related to the display of your app.
  * The model is wwhat contains the data that the app manipulates. In Angular
    the models are just plain old javascript files.
  * The controller is the piece that determines how the models and views 
    communicate. It is in charge it determining what data needs to be fetched
    and how the app fetches.

Each of these elements is actually made up of some other moving pieces of
Angular.
  * Direcctives - encapsulate view code
  * Services - encapsulate controller code
  * Filters - Modify data in a view
  * 

How our app is going to be different
--------------------------------------
Our app is going to be created using browserify to build our app into a single
file that can be included in an html file. This simplifies our html file and
lets us use the common.js method of modularization. Our libraries will also
be managed by a combination of browserify and bower. Which once again simplifies
dependencies and requires less code to be stored in our repo.

The way in which we're going to use the common.js is by setting our notesApp
Angular module to a variable in our app.js file and passing that variable
to functions  that will modify that app by adding things such as controllers,
filters, etc.

Here is an exaple app.js
```javscript
/*
 * app/js/app.js
 * angular is pulled in from bower and set to global within this file
 * this angular library expects to be a global which is why we don't
 * set it to a variable
 */
require('angular/angular');

/*
 * The notes app is created by using the module method of angular
 * if the notesApp module was already defined elsewhere it would pull that
 * in and set it to a variable
 */
var notesApp = angular('noteApp', []);

/*
 * Here a controller is add to the app by requiring in the controller file
 * and passing it our notesApp. This lets us add that controller to any app
 * which could be useful for something like a users controller/resource
 */
require('./notes/controllers/notes-controller')(notesApp);
```javascript
/*
 * app/js/notes/controllers/notes-controller.js
 * first we module.exports a function that takes expects an Angular app to 
 * passed to it as an argument
 */
module.exports = function(app) {
  //we then call the controller method of the app to create a new controller
  app.controller('notesApp', function($scope) {

  });
}
And here is what that controller looks like:
This is the basic method that we will be using to create our application. Each
time we need to add a new piece to our angular app we will send over the app
to a function that will call the appropriate methods of the Angular app.
