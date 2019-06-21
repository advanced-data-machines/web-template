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

<img width="500" src="https://github.com/adm-devs/web-template/blob/master/images/image8.png" alt="vscode extensions" />

Requests will come in on _http://localhost:5000_
The aspnetcore project will process it the target is a MVC or API controller it will be handled
Otherwise, the request will be proxied to _http://localhost:8080_ for node to handle running the Vue project

This will allow you to utilize the hot-module-replacement and debug the client side project correctly.

For __Production__, npm will process the application in the 'clientapp' folder, compiling it to static js, css, etc files putting them in the wwwroot directory where kestrel will serve them as required.


## Editing the Client-Side Project with VSCode

### 1. Add Required VSCode Extensions

Find the 'Extensions Panel'.

<img width="250" src="https://github.com/adm-devs/web-template/blob/master/images/image1.png" alt="vscode extensions" />

Search for 'Vetur' in the search box and install.

<img width="600" src="https://github.com/adm-devs/web-template/blob/master/images/image3.png" alt="vetur" />

This will add support for _.vue_ files in VSCode.

Then search for the 'ESLint' plugin and install.

<img width="600" src="https://github.com/adm-devs/web-template/blob/master/images/image4.png" alt="eslint" />

This will add support for providing visual feedback on linting and auto-fixing problems on file save.

### 2. Update VSCode settings

Make the following changes to your VSCode settings to allow ESLint to fix formatting issues.

* Locate the settings section in File > Preferences > Settings Or type Ctrl+
* Click the curly brackets button in the top right of VS Code window

<img width="600" src="https://github.com/adm-devs/web-template/blob/master/images/image6.png" alt="VSCode Settings" />

* Modify existing settings to appear as follows

```JSON
{
    "javascript.updateImportsOnFileMove.enabled": "always",
    "editor.tabSize": 4,
    "editor.insertSpaces": false,
    "eslint.validate": [
        {
            "language": "vue",
            "autoFix": true
        },
        {
            "language": "javascript",
            "autoFix": true
        },
        {
            "language": "javascriptreact",
            "autoFix": true
        }
    ],
    "eslint.autoFixOnSave": true,
    "editor.formatOnSave": false,
    "vetur.validation.template": false
}
```

The formatting settings specified in 'eslintrd.js' will now be enforced

### 3. Setup .eslintrc.js File

* Locate the file located in the 'clientapp' directory
* Modify the file to appear as follows:

```Javascript
module.exports = {
    root: true,
    env: {
        node: true
    },
    extends: [
        'eslint:recommended',
        'plugin:vue/strongly-recommended',
        '@vue/prettier'
    ],
    rules: {
        'prettier/prettier': [
            'error',
            {
                singleQuote: true,
                useTabs: true,
                tabsWidth: 4
            }
        ],
        'vue/component-name-in-template-casing': ['error', 'PascalCase'],
        'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
        'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
        'vue/max_attributes-per-line': 'off'
    },
    parserOptions: {
        parser: 'babel-eslint'
    }
};
```


## Debugging the Client-Side Project with VSCode

### Edit vue.config.js File

* Locate the file located in the 'clientapp' directory
* Modify the file to appear as follows:

```Javascript
module.exports = {
    // output production files to the aspnetcore project directory 'wwwroot'
    outputDir: '../wwwroot',
    configureWebpack: {
        // Using source-map allows VS Code to correctly debug inside vue files but not during production
        devtool: process.env.NODE_ENV === 'production' ? false : 'source-map',
        // Breakpoints in VS and VSCode won’t work unless you adjust where it can find the source files
        output: {
            devtoolModuleFilenameTemplate: info => {
                const resourcePath = info.resourcePath.replace(
                    './src',
                    './ClientApp/src'
                );
                return `webpack:///${resourcePath}?${info.loaders}`;
            }
        }
    }
};
```

### Add required VSCode extension

Search for and install 'Debugger for Chrome'

<img width="600" src="https://github.com/adm-devs/web-template/blob/master/images/image2.png" alt="Chrome debugger" />

This will allow you to set a specific launch target for Chrome in the VSCode debugger

### Create a Launch Target

Click on the debug icon on the left toolbar and then click on the configure icon

<img src="https://github.com/adm-devs/web-template/blob/master/images/image9.png" alt="Debug config" />

Edit launch.json to look like the following

```JSON
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome (vue)",
            "url": "http://localhost:8080",
            "webRoot": "${workspaceFolder}/src",
            "breakOnLoad": true
        }
    ]
}
```

> The 'webroot' property depend on wich folder is the root folder in VSCode
> if VSCode is opened in the <ProjectName>/clientapp directory then:
>   "webRoot": "${workspaceFolder}/src"
> else if VSCode is opened in the <ProjectName> directory then:
>   "webRoot": "${workspaceFolder}/clientapp/src"

### Debugging the project

1.  Run the project in Visual Studio
2.  Wait until the project comes up and the webpage appears
3.  In VSCode click 'Run' in the debug panel

<img src="https://github.com/adm-devs/web-template/blob/master/images/image5.png" alt="VSCode debug" />

4.  You can now set breakpoints in your vue files

<img src="https://github.com/adm-devs/web-template/blob/master/images/image7.png" alt="VSCode breakpoint" />
