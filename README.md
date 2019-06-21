# web-template
Guide for creating a aspnet core web project integrated with vue-cli

## Creating the Project

### Prerequisites

* Visual Studio 2017+
* .NET Core SDK 2.2.x
* Microsoft.AspNetCore 2.2.x
* Vue cli 3.x

### Create the AspNetCore Project

_The following instructions will work both in Windows or a linux desktop but using the windows remove directory command where you would have to replace with the linux command_

Open a command prompt in the target directory and run the following:

```
$ dotnet new react --name <ProjectName>
$ cd <ProjectName>
$ rd ClientApp /S /Q
$ dotnet add package VueCliMiddleware
$ vue ui
```

> where <ProjectName> is the name of the directory to put the project in

In the vue ui, do the following:

1.  If you are not already there, goto the ‘Vue Project Manager’ page
2.  Click ‘Create’
3.  Navigate to your project folder and click ‘Create new project here’
4.  Make the folder name ‘clientapp’, untick ‘Initialize git repo’, then click ‘Next’
5.  Select ‘Manual’ preset
6.  Tick, Babel, Router, Vuex, CSS Pre-processors, Linter / Formatter and Use config files. Then click ‘Next’
7.  Untick history mode, Select ‘node-sass’ and ‘ESLint + Prettier’
8.  Tick ‘Lint on save’, then click ‘Create Project’
9.  Save your settings to ‘Default Project’
10. When the project is created and the page switches to the Project Dashboard
11. Navigate to ‘Configuration / Vue CLI’ and set
    a)  Output directory = '../wwwroot'
    b)  Untick ‘Enable Production Source Maps’
12. Click 'Save Changes'
13. Navigate to ‘Configuration / ESLint configuration’ and set
    a)  Untick ‘List on save’
    b)  Set ‘Select config’ to ‘Strongly recommended’
14. Click ‘Save Changes’

Now open the project <ProjectName>.csproj in Visual Studio (or VSCode if you prefer)

Open 'Startup.cs' and make the following edits:

1.  Around line 28 in the 'ConfigureServices' method change the line setting the RootPath to:
```
configuration.RootPath = "wwwroot";
```
2.  Around line 52 in the 'Configure' method change the line with 'UseReactDevelopmentServer' to
```
spa.UseVliCli("serve", 8080);
```
3.  Make sure you add the import:
```
using VueCliMiddleware
```
4.  On the top toolbar in visual studio where the debugging context is set to 'IIS Express', click the dropdown and select <ProjectName> instead. This will run the project under kestrel as it will run in production rather than under IIS
5.  In the solution explorer, right-click on the project and click 'Add / New Folder' and name it 'wwwroot'
6.  Click 'Save All' and save the solution file

### Results

Your __Development__ environment will look as follows:

<img src="https://github.com/adm-devs/web-template/blob/master/images/image1.png" alt="dev environment" />

Requests will come in on _http://localhost:5000_
The aspnetcore project will process it the target is a MVC or API controller it will be handled
Otherwise, the request will be proxied to _http://localhost:8080_ for node to handle running the Vue project

This will allow you to utilize the hot-module-replacement and debug the client side project correctly.

For __Production__, npm will process the application in the 'clientapp' folder, compiling it to static js, css, etc files putting them in the wwwroot directory where kestrel will serve them as required.


<img src="https://github.com/adm-devs/web-template/blob/master/images/image8.png" alt="vscode extensions" />
