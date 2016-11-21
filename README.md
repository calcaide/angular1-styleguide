# Angular 1 styleguide

## Table of contents
- [Introduction](#introduction).
- [Reference style guides](#reference-style-guides).
- [Project structure](#project-structure).
- [Naming conventions](#naming-conventions):
	- [Modules](#naming--modules).
	- [Configuration blocks](#naming--configuration-blocks).
	- [Controllers](#naming--controllers).
	- [Data model](#naming--data-model).
	- [Data service, util service and shared model](#naming--data-service-util-service-shared-model).
	- [Directives](#naming--directives).
- [Artifacts recipes](#artifacts-recipes):
	- [Dependency injection](#dependency-injection).

## Introduction

This style guide is deprecated and no longer updated. I use it as a reminder or cheat sheet for maintaining legacy code.

Is important to mention that this guide is strictly based in [ES6](https://en.wikipedia.org/wiki/ECMAScript#6th_Edition_-_ECMAScript_2015) and in [MVC pattern architecture](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). Angular 1.x can ve used in other approaches, but you don't will find it here.

Since Angular 1.5.x and above it is possible to use it with a component based architecture but I never used [Angular](https://angularjs.org/) in this approach, when I decided to move to that architecture I stopped using [Angular](https://angularjs.org/) in favor of [React](https://facebook.github.io/react/).

This style guide is the result of in-depth working with Angular (since 2013), having discussions with co-workers and other developers, reading a lot of articles and other style guides that I will mention in [reference style guides](#reference-style-guides).

**[⬆ back to top](#table-of-contents)**

## Reference style guides

- [Todd Motto - Angular 1.4.x / ES5 styleguide](https://github.com/toddmotto/angular-styleguide/tree/angular-old-es5).
- [Todd Motto - Angular 1.x / ES6 styleguide](https://github.com/toddmotto/angular-styleguide).
- [John Papa - Angular 1 styleguide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md).
- [Minko Gechev - Angular JS in patterns](https://github.com/mgechev/angularjs-in-patterns).
- [Ben Nadel - Blog](https://www.bennadel.com/).
- [Todd Motto - Blog](https://toddmotto.com/).
- [John Papa - Blog](https://johnpapa.net/).

**[⬆ back to top](#table-of-contents)**

## Project structure

There are different ways to structure an Angular project- Here are sme important articles (in my humble opinion) about it:

- [Angular Structure: Refactoring for Growth](https://johnpapa.net/angular-growth-structure/).
- [Angular App Structuring Guidelines](https://johnpapa.net/angular-app-structuring-guidelines/).
- [Cliff Meyers](http://cliffmeyers.com/blog/2013/4/21/code-organization-angularjs-javascript).
- [Todd Motto](https://toddmotto.com/opinionated-angular-js-styleguide-for-teams/#projectfile-structure).
- [Scotch.io](https://scotch.io/tutorials/angularjs-best-practices-directory-structure).

#### Example of app structure:
```bash
some-ui-project
|
|- common --> Common Angular artifacts and modules in the UI context.
|	|
|	|-- models
|	|-- dataServices
|	|-- directives
|	|-- modules
|
|- clients --> Clients section angular artifacts and module definition.
|	|
|	|-- clients.module.js
|	|-- clients.ctrl.js
|	|-- clients.model.js
|	|-- clients.dataSrv.js
|	|-- clients.tmpl.html
|
|- dashboard
|- users
|- root --> Views and controllers that are part of the root state.
|- app.js --> the root module definition.
|- app.config.js --> The app .config blocks definition.
|- app.states.js --> The UI-Router states definition for the entire app.
```

Also I cover huge modules, for example think in clients, is a big module that is made it for other smaller modules.

#### Example of huge module:
```bash
clients
|
|- clients.module.js
|- clients.ctrl.js
|- detail
|	|
|	|-- clientsDetail.ctrl.js
|	|-- clientsDetail.tmpl.html
|	|-- clientsDetail.module.js
|	|-- ...
|
|- list
|	|
|	|-- clientsList.ctrl.js
|	|-- clientsList.tmpl.html
|	|-- clientsList.module.js
|	|-- ...
```

### The importance of modularization

All pieces have to encapsulate in a module (modules that make sense) for the sake of the mental health of the developers. Once all is a module, is very easy to work with it, like a [Lego](https://en.wikipedia.org/wiki/Lego).

In the example above ([example of app structure](#example-of-app-structure)), the dashboard module, do you want to use it in another app? Copy the `/dashboard` folder to your project, register the `dashboard` module in the app module definition (`app.js`) and add a route to it (`app.states.js`), that's all.
This also benefits an agile development and the teamwork.

### [Example of app structure](#example-of-app-structure) explanation

Is important to mention that I use the [UI-Router](https://github.com/angular-ui/ui-router) instead [NG-Router](https://docs.angularjs.org/api/ngRoute). The UI-Router is a state based router that offers [abstract states](https://github.com/angular-ui/ui-router/wiki/Nested-States-and-Nested-Views#abstract-states), [nested states and nested views](https://github.com/angular-ui/ui-router/wiki/Nested-States-and-Nested-Views), basic to develop a structure of a project like this.

- `app.js`: the root module, the app definition.
- `app.config.js`: all the config blocks encapsulated in one module. In this way, you don't pollute the root module with config blocks.
- `app.states.js`: all the app states tree defined in one file. Also, this way, you don't pollute the root module with states.
- `/root`: views and controllers that are part of the root state. I use an abstract state as the first state. This state is called root, and any other state in the application is a nested state of the root. Root states defines the main template that is composed of [multiple named views](https://github.com/angular-ui/ui-router/wiki/Multiple-Named-Views), those named views contains common components that are present in any section of the app like a top bar menu or whatever.
- `/common`: contains common Angular artifacts and modules in the UI context. I use to keep all the module artifacts (controller, service, etc.) together in a folder (like clients module in the example) that typically refers to a section or component of the app, but some artifacts are used for more than one module.
I consider those artifacts are orphans, let's put an example with a directive that is used in dashboard and in clients section, where you will put this directive? in dashboard folder or clients folder? The answer is in `/common` folder.
The same with a common module. That's why is the unique folder that follows the **sort by type** structuring approach, for the variety of artifacts and modules that can contain.
- `/clients`: represents a state/section of the app and wraps all the artifacts that form it. There are simple sections like `clients` in the [example of app structure](#example-of-app-structure) or little bit complex like `clients` in the [example of huge module](#example-of-huge-module).
The huge module example is for a section formed by subsections, following the example, you have a section `/clients`, but inside this section, you will have two subsections: list and detail.
The list of the clients and when you want to go deep in a client of the list, you will have a detail client section.

In the [example of app structure](#example-of-app-structure) I am using a **structuring for modules** approach in all the project except inside the `/common` folder, due to his nature (explained above) has to follow **sort by type**.

**[⬆ back to top](#table-of-contents)**

## Naming conventions

- Directories: there is no secret, just use `lowerCamelCase` as style. For the name itself, use the feature that represents.

- Filename: I follow a pattern that describes the component feature and its type: `feature.type.js`.

	List of types:
	- Root module: `app.js`.
	- Common module: `clients.module.js`.
	- Configuration blocks: `app.config`.
	- Controller: `clients.ctrl.js`.
	- Data model: `clients.model.js`.
	- Data service: `clients.dataSrv.js`.
	- Util or business service: `clients.util.js`.
	- Shared method: `clients.sharedModel.js`.
	- Directives: `scrollTo.drtv.js`.
	- Decorator: `scrollTo.dctr.js`.
	- Component: `clients.comp.js`.

### Angular artifacts:

All the code example below are focused on registrate/create the module or artifact, not in the definition of the artifacts itself. The definitions are cover in the [recipes section](#artifacts-recipes).

<a name="naming--modules"></a>
#### Modules:
- Filename:
	- Root module: `app.js`.
	- Common module: `feature.module.js` -> `clients.module.js`.
	- Style: `lowerCamelCase`.
	- Name: the name is formed by a set of arguments:
		- First: application name, for example: `awCrm` (awesome CRM).
		- Second: module feature, for example: `clients`.
		- Third or more: what is need, like an action that represents the module: `new`, `list`, `detail`, etcetera.
		- Examples:
			- awCrm.clients.list.
			- awCrm.users.new.
			- awCrm.dashboard.

**[⬆ back to top](#table-of-contents)**

<a name="naming--configuration-blocks"></a>
#### Configuration blocks:
- Filename: `app.config.js`.
- Style: `lowerCamelCase`.
- Name: the name is formed by two arguments (some people prefer to use just `config`):
	- First: application name.
	- Second: config.
	- Example: awCrm.config.
- Example in code:
```javascript
const config = angular
	.module('awCrm.config', [])
	.config(['$provide', ($provide) => new LogDecorator($provide)]);
	.name;

export default config;
```

**[⬆ back to top](#table-of-contents)**

<a name="naming--controllers"></a>
#### Controllers
- Filename: `clientsList.ctrl.js`.
- Style: `UpperCamelCase`.
- Name: use the controller feature to name it.
- Example: `ClientsListCtrl`.
- Example of register a controller in code:
```javascript
import ClientsListCtrl from './clientsList.ctrl.js';

const clientsList = angular
	.module('awCrm.clients.list', [])
	.controller('ClientsListCtrl', ClientsListCtrl)
	.name;

export default clientsList;
```

**[⬆ back to top](#table-of-contents)**

<a name="naming--data-model"></a>
#### Data model
- Filename: `clientsList.model.js`.
- Style: `UpperCamelCase`.
- Name: use the data model feature to name it.
- Example: `ClientsListModel`.
- Example of register a data model in code:
```javascript
import ClientsListModel from `./clientsList.model.js`;

const clientsList = angular
	.module('awCrm.clients.list')
	.service('ClientsListModel', ClientsListModel)
	.name;

export default clientsList;
```

**[⬆ back to top](#table-of-contents)**

<a name="naming--data-service-util-service-shared-model"></a>
#### Data service, util service and shared model
*I put these three artifacts together because their are identical in terms of naming conventions.*

- Filename:
	- Data services: `clients.dataSrv.js`.
	- Util or business service: `clients.util.js`.
	- Shared model: `clients.sharedModel.js`.
- Style: `lowerCamelCase`.
- Name: use the feature that their represents to name it.
- Examples:
	- Data service: `clientsDataSrv`.
	- Util or business service: `clientsUtil`.
	- Shared model: `clientsSharedModel`.
- Example of register a data service, util and shared model in code:
```javascript
import clientsDataSrv from './clients.dataSrv.js';
import clientsUtil from './clients.util.js';
import clientsSharedModel from './clients.sharedModel.js';

const clientsList = angular
	.module('awCrm.clients.list', [])
	.service('clientsDataSrv', clientsDataSrv)
	.service('clientsUtil', clientsUtil)
	.service('clientsSharedModel', clientsSharedModel)
	.name;

export default clientsList;
```

**[⬆ back to top](#table-of-contents)**

<a name="naming--directives"></a>
#### Directives
- Filename: `scrollTo.drtv.js`.
- Style: directives are tricky because they have a name to register it in Angular and other to use it in a view (is created automatically).
	- Register: `lowerCamelCase`.
	- Use in a view: `dash-separator`.
- Name: use the feature that represents to name it.
- Example: `scrollTo`.
- Example of register a directive in code:
```javascript
import scrollTo from './scrollTo.drtv.js';

const module = angular
	.module('awCrm.directives.scrollTo', [])
	.directive('scrollTo', scrollTo)
	.name;

export default module;
```

**[⬆ back to top](#table-of-contents)**

## Artifacts Recipes

### Dependency injection

To include the dependency injection in the Angular artifacts implemented as ES6 classes it's recommended to use the next pattern:

```javascript
class SomeCtrl {
	
	static get $inject(){
		return ['DepModule'];
	}

	constructor(DepModule){
		this.DepModule = DepModule;
	}
}
```

Why a static getter? a getter in ES6, adds a pseudo-property in the class prototype that can be read using `classInstance.property`. That is what Angular need when instantiating an artifact.

**[⬆ back to top](#table-of-contents)**