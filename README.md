# Another typescript style guide for Angular 1.x applications

## Introduction

This is a style guide for fictional company "FooBar", i.e. "fb" prefix represents
it where relevant.

Goals:
 - Quick reference on how to implement Angular 1.x elements in TypeScript
 - As little text as possible

Non-goals:
 - Teaching TypeScript or AngularJS
 - Lint ruleset
 - Code review guidance

Tooling workflow we are using:
 - TypeScript 1.5+
 - Browserify, tsify and watchify (because tsc doesn't support bundling yet)
 - Visual Studio Code
 - All third party libs (including angular) dumb-concatenated to lib.js (not exposed to browserify),
   application built with browserify to main.js. "angular" is globally visible.
 - Strict DI used
 - Gulp (but it doesn't matter, as heavy lifting done by browserify)

## Controllers

```typescript

import {MyService} from './MyService';

export class MyController {
	untypedAttribute;
	stringAttribute: string;

	static $inject = ['$http', 'MyService'];
	contructor(private $http: ng.IHttpService, private MyService: MyService) {
	}

	someMethod() {
		this.MyService.someMethodOnService();
	}
}

angular.module('fbSomeFeature').controller('MyController', MyController);

```	

Notable items:
 - MyService 'import'-ed just for type information
 
## Services

```typescript

export class MyService {

	static $inject = ['$http'];
	constructor(private $http: ng.IHttpService) {
	}

	someMethod() {
		// this.$http.get(...)
	}
}

angular.module('fbSomeFeature').service('MyService', MyService);
```

Notable items:
 - Use service() instead of factory
 - class exported to allow importing the type (as in MyController). 

## Directives

```typescript

export function FancyButtonDirective() : ng.IDirective {
	return {
		templateUrl: 'Features/SomeFeature/FancyButton.html',
		controllerAs: 'fancyButton',
		controller: FancyButtonController,
		scope: {
			someData: '=',
		},
		bindToController: true
	}
}

angular.module('fbSomeFeature').directive("fbFancyButton", 
	FancyButtonDirective);

```

Notable items:

 - function, not a class
 - controllerAs and bindToController
 - Relevant file names: FancyButton.ts, FancyButton.html (separate FancyButtonController.ts if you want)

## Angular Modules

```typescript

// this is feature specific "root" module, SomeFeatureModule.ts

// create agular module here
angular.module('fbSomeFeature', ['dep1', 'dep2']);

// add things to the created angular module here
import './MyService'
import './FancyButton'

```

And in root app.ts:

```typescript

import './SomeFeature/SomeFeatureModule';

```

Notable items:

- Angular modules have limited utility compared to standard ES6 
  modules (most useful for reusable third party libraries).
  Don't overdesign them in your app.
