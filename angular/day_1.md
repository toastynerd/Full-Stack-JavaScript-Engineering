Angular Day1
=============================================
Angular, its got the magics. Angular represents a shift in the way that 
developers create web applications. Unlike other frameworks Angular uses the DOM
and html documents as a springboard for its content. If you love semantic HTML
that lints, you're not probably going to like Angular. It uses html itself as
part of the application, instead of creating html from template. While this
will make your ui/ux friends cringe it does make an extremely flexible and
expressive framework.
Build Tools Setup
------------------------------------------------
For the Angular setup and build process we'll be using a combination of Bower 
and Browserify. Bower will make sure that we always have the latest version of
Angular and its dependencies. Browserify will not only let us make modular code
but will also allow us to reuse our Angular bits and pieces. To install the
angular base use `bower install --save angular`. Next install the grunt pieces
`npm install --save-dev grunt grunt-contrib-copy grunt-contrib-clean grunt-browserify debowerify`
Now, make sure that your Gruntfile looks something like this:
```javascript
module.exports = function(grunt) {
  grunt.loadNpmTasks('grunt-contrib-clean');
  grunt.loadNpmTasks('grunt-contrib-copy');
  grunt.loadNpmTasks('grunt-browserify');

  grunt.initConfig({
    clean: {
      dev: {
        src: ['build/']
      }
    },

    copy: {
      dev: {
        expand: true,
        cwd: 'app/',
        src: ['*.html', '*.css', 'views/**/*.html'],
        dest: 'build/',
        filter: 'isFile'
      }
    },
    browserify: {
      dev: {
        options: {
          transform: ['debowerify'],
          debug: true
        },
        src: ['app/js/**/*.js'],
        dest: 'build/bundle.js'
      }
    }
  });
  grrunt.reggisterTask('build:dev', 'clean:dev', 'browserify:dev', 'copy:dev'); 
}
```
For the front end of the application there are two pieces, our source and our
build. The build lives in the build directory and should never be touched with
an editor. The source of the app lives in the app directory and is where changes
should be made the code. Everytime a new version off the app needs to be built
the build folder has all off its contents removed, then the browserify task
takes all of the javascript and places it into a single file `build/bundle.js`
and then copies over all of the static assets such as html, css and images.
The important piece of browserify is the debowerify transform. The debowerify
package knows how to extract the javascript libraries that are needing for
our application from the bower package, hence the name 'debowerify', de bowers 
the code we want.

App Setup
--------------------
All of the angular code is going to be contained in app/js minus our html views.
So fist, we have to create that folder `mkdir -p app/js`. Inside of that folder
create an app.js file with the following code:
```javascript
require('angular/angular');

var notesApp = angular.module('notesApp', []);
```
This is the beginning of our Angular app. The require statment looks a little
different in that it doesn't save the library to var. Angular expects to be
a global variable with this js file so saving it will actually lead to issues
with the angular library. The next statement creates a new angular app using
the module method. The fist argument is the name of the app we want to create
or get an instance of. The second argument is an array of dependencies which
we'll see more of later.

Now we have to get this Angular app into the our html and onto the page.
Create an app/index.html file and add the following code:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Notes App</title>
</head>
<body data-ng-app="notesApp">

  <script src="bundle.js"></script>
</body>
</html>
```
For the most part this looks like a standard html file, except for two lines.
The first is the body tag that contains a data-ng-app attribute. This tells
angular that when it goes over the code that this is the tag that contains the
entire angular app of "notesApp". When the bundle gets run the body tag will
have elements replaced based upon what we add to the notesApp variable in
app.js. The script tag just links in our bundle.js file which contains angular.
When it gets included,  angular will be run and will change evaluate our 
angular code. Right now, this code won't actually do anything but it will load
angular and our notesApp module.

Making it actually do something
---------------------------------------
Alright, the next step is to actually make angular do something. To actually
get angular working let's add a controller. This is the basic building block
that interacts with our views (the html) and our models (plain old javascript objects).
First, lets add the controller to our index.html file so that it looks like this:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
</head>
<body data-ng-app="notesApp">
  <div data-ng-controller="notesController"></div>

  <script src="bundle.js"></script>
</body>
</html>
```
This sets up a div that will contain the area where we can bind data between
our view and our controller. Next, we have to setup the javascript angular 
controller. I like to organize my angular pieces based on resources so, we're
going to make a notes folder in app/js with a controller diretory insidde of it.
`mkdir app/js/notes/controllers` Now create a notes-controller.js fileinside of 
that directory  with the following code:
```javascript
'use strict'
module.exports = function(app) {
  app.controller('notesController', function($scope) {
    $scope.greeting = 'hello world!';
  });
};
```
This file expects to be passed an app when it's required in. We assume that this
is an angular and has access to methods such as controller. The controller method
takes two arguments, a string name  of the controller and a function that 
determines which dependencies are injected into the controller. Any variable
within this function that starts with a '$' is an angular defined variable.
In our case, we're just including the $scope variable which is a plain old
javascript object that we can access both in the view and in the controller.
We then add a greeting property to that object and set it to the tradional
'hello world' string. Now we need to include this controller in our app. Modify
the app/app.js file to look like this:
```javascript
require('angular/angular');


var notesApp = angular.module('notesApp', []);
require('./notes/controllers/notes-controller.js')(notesApp);
```
We only addded one line to this that requires in our controller and passes
the notesApp object to that file, which will modify ourr app by adding the
notesController to that app.

Now we want to be able to display that grreeting that we set in our controller
in our view. Modify the index.html file to look like this:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
</head>
<body data-ng-app="notesApp">
  <div data-ng-controller="notesController">
    <h1>{{greeting}}</h1>
  </div>
  <script src="bundle.js"></script>
</body>
</html>
```
Inside of our controller div tag we've added an h1 tag that contrains what is
called an angular expression. Anything wrapped in two curly brackets {{}} is
going to be evaluated as javascript, usually it will try to read the whatever
is  in the brackets as a parameter of the local $scope object that we added
to our controller. In our case the h1 tag is going to contain the text that was
set to the $scope.greeting in our controller.

Two Way Data Binding
------------------------------------------------
Angular uses what are called directives to maniupate the dom. Both the 
data-ng-app and data-ng-controller are examples of this. Angular comes with
a series of built in directives for accomplishing common tasks. The first one of
these that we'll look at is the data-ng-model directive, which allows us to
use an input tag to manipulate data on the $scope. With this we can do what is
called a two way data binding which lets an input tag directly change a value
on the dom as we update it. To see this action change the index.html to contain
the following:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title></title>
</head>
<body data-ng-app="notesApp">
  <div data-ng-controller="notesController">
    <h1>{{greeting}}</h1>
    <input type="text" data-ng-model="greeting" />
  </div>
  <script src="bundle.js"></script>
</body>
</html>
```
This will create a text input that directly maps to the greeting in our h1 tag.

Creating a router
----------------------------------------
Now
