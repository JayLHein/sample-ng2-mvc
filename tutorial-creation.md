## How to recreate a similar project
This project basic setup was inspired by Mithun Pattankar at http://www.mithunvp.com/using-angular-2-asp-net-mvc-5-visual-studio/
Also see the repo: https://github.com/mithunvp/ng2Mvc5Demo
Mithun's work helped me get a angular2 project started with NPM and Visual Studio.  This sample will build from his work into creating useful Application components.

The C# code structure was influenced by other commercial projects I created using https://github.com/michael-lang/candor-common

The following instructions document the steps taken to create this project.

### Step 1 - Create the project
1. Add New Project - Class library (C#) project.  This will hold our fake (or real) server side application layer.
	* use Framework 4.5.2 or later
2. Add New Project - ASP.NET Web Application (.Net Framework C#).  
	* From the template chooser, pick 'MVC', and check the boxes to include folders for 'MVC' and 'Web API'.
	* use Framework 4.5.2 or later (same version for both projects)
3. Run project and ensure it works.  If not your tooling setup is incorrect.

### Step 2 - Setup project dependencies
1. Add New item (web project), template 'npm Configuration file', named 'package.json'
	* this will create a folder called 'node_modules'in your web project, but it won't be visible in the project unless you press the "show all files" icon in solution explorer.
	If this folder does not get created, then Update 3 is not properly applied to visual studio.
2. Open the package.json and add needed dependencies
	* devDependencies needed for building sass and typescript (build the project to restore these packages)
	* dependencies for angular2 and systemjs (systemjs bootstraps the project into the page)
	* With VS Update 3 installed, you don't need to ever run a command prompt to "npm install" like some workthrus mention, it happens automatically as you add to the package.json file.
3. Add a new 'app' folder at the root of the website project to hold all the Angular2 typescript, sass, and html templates.
4. Configure typescript to transpile the .ts files into javascript (ES5)
	* Add new item under the new 'app' folder, use template 'Typescript JSON Configuration file'.  Accept the default name 'tsconfig.json'
	* Open the tsconfig.json and edit the compile options
	* Create a dist folder to match the 'outDir' setting in tsconfig
	* Create a typings.json file at the root of the webproject to ensure that TypeScript understands all Angular 2 modules in respect to ES5 standard.
	* open a CMD prompt, cd to the web project folder and enter 'typings install'
5. Setup typescript build trigger and sass compilation to css using Gulp
	* Add a new file to the root of the web project, using template 'Gulp Configuration file' and keep the default name gulpfile.js
	* copy in the contents of the sample into gulpfile.js.
	* open window 'Task Runner Explorer' (TRE) and press the refresh button to see all the new tasks.  
		The first comment in the sample gulpfile.js will show some of those tasks linked to built in visual studio build steps,
		so to run the site all you do is press the usual green run button in visual studio and the build process will run the necassary gulp tasks to run your project.
	/// <binding BeforeBuild='dist-libs' AfterBuild='build' Clean='clean-libs' />
6. Build and run your web project (must be the startup project - shown in bold, then press run in the toolbar).
	You will see the clean-libs task run in TRE, then 'dist-libs' before the projects build, and then 'build' after the class library builds.

## Step 3 - Create your back-end model and API
This step is not necassarily part of the angular project work, and may be supplied by another team.  
Creation of C# code is beyondd the scope of explanation here, however a contrived example is included.
Even if another team is working on the back-end, you may need to create an interim front end mock for your anticipated data.

1. Create back-end model classes (or temporary mocks)
2. Create a Web API to interact with the data using typical C# MVC.Net techniques.
3. Create front end typescript classes to match the API layer model.
	* The Typescript front end version of the classes is the responsibility of the front-end team.
	* Structure your model types in folders based on the screen they will be used within.
	* refer to the Angular2 style guide for organizational questions.  https://angular.io/styleguide

## Step 4 - Create the Angular2 barebones app
1. Set the MVC backend to load your app route(s) into a cshtml page using systemjs to load your App.
	* HomeController.cs - add a route with the desired path, return a View.
	* In the view file, use systemjs to load the AppModule shown below.
2. The angular application code needs a way to bootstrap into the page.  Create an AppModule to define the base Application
	* also requires a top level AppComponent defining some markup and possibly application logic
	* this example creates additional 'chrome' named AppChromeModule, containing a header with navigation and a footer.
3. Create a module and component pair for each 'page' you want in the angular app.  (ex.  TripModule and TripComponent)
	* refer to the Angular2 style guide for naming and folder organization guidelines.
	* hold off on adding complexity to any page modules until the barebones outline runs and navigates between 'pages' (modules) properly.  
	This will help you debug getting the project setup.
4.  Add navigation using the angular RouterModule.
	* Define a route for each top level page with a module. (ie. TripRoutingModule)
	* Import your routing module into the page module. (ie. TripModule)
	* Then we gather these routes in a service (AppRouterMenuService) and build a menu structure then used by the AppHeaderComponent.
5. build and run the project and you should see menu items for your routed modules, with one of the pages defined as the default route and initially visible.
	* If you have problems, check versions of all packages to match this project exactly.  Only upgrade dependencies after you have a basic working app.
	* Also check if the typescript is set to build automatically, spot check your most recent changed ts files by looking at the compiled js files for the change.

## Step 5 - Create a Shared module for common components used by multiple modules
1. create a new folder 'app-shared' under the 'app' folder
2. Create a new module called AppSharedModule (file name 'app-shared.module.ts')
3. Add this module to the 'imports' list of the first page module that will use something from the shared module
	* Do not add the shared module as an import to the app module
4. Create a shared component under app-shared.  ie. tabset
5. use the shared component in the markup of the page component
6. build and test.