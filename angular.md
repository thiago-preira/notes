# Angular Notes

## Angular Seed
 Used to bootstrap new AngularJS projects. The seed project is pre-configured to install the AngularJS framework (via bower into the app/bower_components/ directory) and tools for developing and testing a typical web application (via npm).
 
## Ng-show Ng-Hide Ng-if
Just adds the style display none. Ng-if does not render de html element

## Ng-Switch
Acts like Ng-If. Does not render de html element if does not match with the condition. Have a default condition if needed.

## Directives
```javascript
function f(){
    return {
        scope:{}, //isolate the scope
        restrict: 'A', //A === atributo E === element
        link: function ($scope,$element,$attrs){
            //to do custom DOM manipulation
        }
        //template: if define as a string
        //templateUrl: if use a template as file
        //controller: 'Controller as ctrl'
    }
}
```
To make stateful directives:
```javascript
function f(){
    return {
        scope: {},
        bindToController:{
            name: '@' //@ read only
            count: '=' //= toway databinding
            submit: '&' //& delegate function
        }, 
        restrict: 'E', //A === atributo E === element
        link: function ($scope,$element,$attrs){
            //to do custom DOM manipulation
        }
        //template: if define as a string
        //templateUrl: if use a template as file
        //controller: 'Controller as ctrl'
    }
}
```

## Angular Ui Router
Most Powerful Ui router [UI-Router](https://github.com/angular-ui/ui-router)
```javascript
angular
	.module('app')
	.config(function ($stateProvider, $urlRouterProvider) {
		$stateProvider
			.state('home', {
				url: '/',
				controller: 'HomeController as ctrl',
				template: `
					<p>{{ ctrl.viewName }}</p>
				`
			})
			.state('about', {
				url: '/about',
				controller: 'AboutController as ctrl',
				template: `
					<p>{{ ctrl.viewName }}</p>
				`
			});
			$urlRouterProvider.otherwise('/');
	});
```

## Scope watch and scope watch collection
`$scope.$watch` watches changes in a particular property.
```javascript
function Controller($scope){
    this.conter=0;

    $scope.$watch(angular.bind(this,function(){
        return this.count
    }),function (newValue,oldValue){
        if (newValue === oldValue) {
            return
        }
        console.log(newValue, oldValue)
    })
}
```

`$scope.$watchCollection` watches changes in a collection of objetcs property.
```javascript
function Controller($scope){
    this.objList=[];

    $scope.$watchCollection(angular.bind(this,function(){
        return this.objList
    }),function (newValue,oldValue){
        if (newValue === oldValue) {
            return
        }
        console.log(newValue, oldValue)
    })
}
```

## Link Function
Similar to Watch Function, gives some options to watch changes directives properties but it is more performant because it does not set a watcher
```javascript
link: function ($scope, $element, $attrs) {
			$attrs.$observe('name', function (value) {
				if (value === 'Food counter') {
					$attrs.$updateClass('counter--food', 'counter--drink');
				} else if (value === 'Drink counter') {
					$attrs.$updateClass('counter--drink', 'counter--food');
				}
			});
		}
```
## Directives Controllers
It is recomended to use Controller in directives as standalone files
```javascript
function counter() {
	return {
		scope: {},
		template: `
			<div class="counter">
				<h3>Counter: {{ counter.count }}</h3>
				<a class="counter__increment" href="" ng-click="counter.increment()">Increment Counter</a> -
				<a class="counter__decrement" href="" ng-click="counter.decrement()">Decrement Counter</a>
			</div>
		`,
		controller: 'CounterController as counter'
	}
}
```
## Isolated and Inheritance scope
With `scope: false` the directive share the scope with the html element that the directive is on.
With  `scope: true` the directive creats a new child scope, inherited from parent. With  `scope: {}` the scope is isolated, broken the inheritance chain. 
```javascript
function counter() {
	return {
		scope: false, // {},false,true
		template: `
			<div class="counter">
				<h3>Counter: {{ counter.count }}</h3>
				<a class="counter__increment" href="" ng-click="counter.increment()">Increment Counter</a> -
				<a class="counter__decrement" href="" ng-click="counter.decrement()">Decrement Counter</a>
			</div>
		`,
		controller: 'CounterController as counter'
	}
}
```

## Bind To Controller
`bindToController` binds properties from other scopes to the current scope. 
```javascript
function counter() {
	return {
		scope: {},
		bindToController:{
			count: '='
		},
		template: `
			<div class="counter">
				<h3>Counter: {{ counter.count }}</h3>
				<a class="counter__increment" href="" ng-click="counter.increment()">Increment Counter</a> -
				<a class="counter__decrement" href="" ng-click="counter.decrement()">Decrement Counter</a>
			</div>
		`,
		controller: 'CounterController as counter'
	}
}
```
## Compile and Link function
`compile` function acts before the element is compiled.
`link` function acts after the DOM elements are compiled.
```javascript
function ErrorMessage() {
	return {
		restrict: 'A',
		compile: function ($element, $attrs) {
			// raw template
			$element.addClass('error');
			console.log(1);
			// return {
			// 	pre: function ($scope, $element, $attrs) {
			// 		// access to child elements that are NOT linked
			// 	},
			// 	post: function ($scope, $element, $attrs) {
			// 		// access to child elements that are linked
			// 		$element.addClass('error--' + $attrs.type);
			// 	}
			// };
			return function postLink($scope, $element, $attrs) {
				$element.addClass('error--' + $attrs.type);
				console.log(2);
			};
		}
	}
}
```

## Transclusion
Transclusion allow to pass content between two tags. There are 3 tipes
### SingleSlot transcusion
```javascript
function ContactCard() {
	return {
		transclude: true,
		template: `
			<div>
				<h4>Contact</h4>
				<div ng-transclude></div>
			</div>
		`
	}
}
```
### Manual Transclusion
For include transclusion manually, using link function.
```javascript
function ContactCard() {
	return {
		transclude: true,
		template: `
			<div>
				<span></span>
				<div></div>
			</div>
		`,
		link: function ($scope, $element, $attrs, $ctrl, $transclude) {
			var div = $element.find('div');
			var span = $element.find('span');
			var cloned = $transclude(function (elements) {
				elements[0].textContent = elements[0].textContent.toUpperCase();
			});
			console.log(cloned);
			span.append(cloned[0]);
			div.append(cloned[1]);
		}
	}
}
```

### Multi Transclusion
Insert content using named properties. Use `?` if the property is optional
```javascript
function ContactCard() {
	return {
		transclude: {
			name: 'h1',
			desc: '?p'
		},
		template: `
			<div>
				<span ng-transclude="name"></span>
				<div ng-transclude="desc">No description</div>
			</div>
		`
	}
}
```
## Require property for directives
Use for inherit controllers from parent directives
```javascript
function tab() {
	return {
		scope: {},
		transclude: true,
		template: `
			<div ng-if="tab.selected">
				<div ng-transclude></div>
			</div>
		`,
		require: '^^tabs',// use ^ for current directive and then at parent, ^^ for parent directive
		link: function ($scope, $element, $attrs, $ctrl) {
			$scope.tab = {
				label: $attrs.label,
				selected: false
			};
			$ctrl.addTab($scope.tab);
		}
	}
}
```
## Component
They should be use in order to directives. Directives should be used only if a custom DOM behaviour is desired.
```javascript
/**
 * Below (commented out) are other supported
 * properties of the .component() method
 *
 * Remember: bindings: {} is a wrapper
 * for bindToController and scope: {}
 * so you never access $scope directly.
 * "$ctrl" is the default controllerAs name.
 */
var counter = {
	// templateUrl
	// controllerAs
	// transclude: true,
	// require: {
	//	 parent: '^^parent'
	// },
	bindings: {
		count: '='
	},
	controller: function () {
		this.increment = function () {
			this.count++;
		};
		this.decrement = function () {
			this.count--;
		};
	},
	template: `
		<div class="todo">
			<button type="button" ng-click="$ctrl.decrement();">-</button>
			<input type="text" ng-model="$ctrl.count">
			<button type="button" ng-click="$ctrl.increment();">+</button>
		</div>
	`
};

angular
	.module('app')
	.component('counter', counter);

``` 
Components have lifecicle hooks and require property.

```javascript
var tabs = {
	transclude: true,
	controller: function () {
		this.tabs = [];
		this.addTab = function addTab(tab) {
			this.tabs.push(tab);
		};
		this.selectTab = function selectTab(index) {
			for (var i = 0; i < this.tabs.length; i++) {
				this.tabs[i].selected = false;
			}
			this.tabs[index].selected = true;
		};
		this.$postLink = function () {
			this.selectTab(0);
		};
		this.$onDestroy = function () {

		};
	},
	template: `
		<div class="tabs">
			<ul class="tabs__list">
				<li ng-repeat="tab in $ctrl.tabs">
					<a href=""
						ng-bind="tab.label"
						ng-click="$ctrl.selectTab($index);"></a>
				</li>
			</ul>
			<div class="tabs__content" ng-transclude></div>
		</div>
	`
};

angular
	.module('app')
	.component('tabs', tabs);


var tab = {
	bindings: {
		label: '@'
	},
	require: {
		tabs: '^^tabs'
	},
	transclude: true,
	template: `
		<div class="tabs__content" ng-if="$ctrl.tab.selected">
			<div ng-transclude></div>
		</div>
	`,
	controller: function () {
		this.tab = {
			label: this.label,
			selected: false
		};
		this.$onInit = function () {
			this.tabs.addTab(this.tab);
		};
	}
};

angular
	.module('app')
	.component('tab', tab);
```

`$onInit` when the element is created. `$postLink` when the elements are linked, `$onDestroy` qhen the element i destroyed.

## Oneway Databinding
Stateless components should pass data via events. Parent components pass data to the child component, and are updated via events.

```javascript
var statefulComponent = {
	template: `
		<div>
			<pre>{{ $ctrl.user | json }}</pre>
			<stateless-component
				user="$ctrl.user"
				on-update="$ctrl.updateUser($event);">
			</stateless-component>
		</div>
	`,
	controller: function () {
		this.user = {
			name: 'Todd Motto',
			location: 'England, UK'
		};
		this.updateUser = function (event) {
			this.user = event.user;
		};
	}
};

angular
	.module('app')
	.component('statefulComponent', statefulComponent);

var statelessComponent = {
	bindings: {
		user: '<', //oneway databinding
		onUpdate: '&'
	},
	controller: function () {
		this.$onChanges = function (changes) {
			if (changes.user) {
				this.user = angular.copy(this.user);
			}
		};
		this.updateUser = function () {
			this.onUpdate({
				$event: {
					user: this.user
				}
			});
		};
	},
	template: `
		<div>
			<input type="text" ng-model="$ctrl.user.name">
			<input type="text" ng-model="$ctrl.user.location">
			<a href="" ng-click="$ctrl.updateUser();">Update</a>
		</div>
	`
};

angular
	.module('app')
	.component('statelessComponent', statelessComponent);

```
## Event emit and broadcast
Events could be sent via emit to a parent component, and the data could be sent back via broadcast.
```javascript
var userManager = {
	bindings: {},
	controller: function ($scope) {
		var ctrl = this;
		ctrl.user = {
			name: '',
			email: ''
		};
		ctrl.loginUser = function () {
			$scope.$emit('login', ctrl.user);
		};
		$scope.$on('logout', function (event, data) {
			console.log(data);
		});
	},
	template: `
		<form ng-submit="$ctrl.loginUser();" class="child">
			<div>
				Name: <input type="text" ng-model="$ctrl.user.name">
			</div>
			<div>
				Email: <input type="text" ng-model="$ctrl.user.email">
			</div>
			<button type="submit">
				Log in
			</button>
		</form>
	`
};

angular
	.module('app')
	.component('userManager', userManager);


function ContactController($scope) {
	var ctrl = this;
	ctrl.contacts = [];
	ctrl.logoutUser = function () {
		$scope.$broadcast('logout', ctrl.contacts);
	};
	$scope.$on('login', function (event, data) {
		ctrl.contacts.push(data);
	});
}

angular
	.module('app')
	.controller('ContactController', ContactController);

```
Events could also be emited by rootScope. But if use rootScope you should unbind to avoid memory leaks.
```javascript
var outerComponent = {
	bindings: {},
	controller: function ($rootScope, $scope) {
		var unbind = $rootScope.$on('logout', function (event, data) {
			console.log(data);
		});
		$scope.$on('$destroy', unbind);
	},
	template: `
		<div class="outer">
			I am the outer $scope component!
		</div>
	`
};

angular
	.module('app')
	.component('outerComponent', outerComponent);

```

## Parsers and Formatters
With formatters we can manipulate the value in the ng-model and the return to the view
Parsers change the way that view values are stored in the model.
```javascript
function couponFormat() {
	return {
		require: 'ngModel',
		link: function ($scope, $elem, $attrs, $ctrl) {

			var ngModelCtrl = $ctrl;

			ngModelCtrl.$formatters.unshift(function (value) {
				// coupon: 'summer-50' -> 'SUMMER_50'
				return value.replace(/-/g, '_').toUpperCase();
			});

			ngModelCtrl.$parsers.unshift(function (value) {
				// coupon: 'SUMMER_50' -> 'summer-50'
				return value.replace(/_/g, '-').toLowerCase();
			});

		}
	}
}

angular
	.module('app')
	.directive('couponFormat', couponFormat);

```
## Validators pipeline
With validators pipeline we could create custom validators for the view value and the modl value.
```javascript
function couponFormat() {
	return {
		require: 'ngModel',
		link: function ($scope, $elem, $attrs, $ctrl) {

			var ngModelCtrl = $ctrl;

			ngModelCtrl.$formatters.unshift(function (value) {
				// coupon: 'summer-50' -> 'SUMMER_50'
				return value.replace(/-/g, '_').toUpperCase();
			});

			ngModelCtrl.$parsers.unshift(function (value) {
				// coupon: 'SUMMER_50' -> 'summer-50'
				return value.replace(/_/g, '-').toLowerCase();
			});

			ngModelCtrl.$validators.coupon = function (modelValue, viewValue) {
				var COUPON_REGEX = /[A-Z]+\_\d{2}/;
				return COUPON_REGEX.test(viewValue);
			};

		}
	}
}

angular
	.module('app')
	.directive('couponFormat', couponFormat);
```

## Css Validations
We could do css validations. Every input in a form has classes attached to then. We could add styles to show the current status
```css
<style>
		form .ng-dirty.ng-invalid {
			border: 1px solid red;
		}
		form .ng-valid {
			border: 1px solid green;
		}
		</style>
```

## Ng-Messages
A angular module to display error and validation messages easier
```html
<div
	ng-if="register.username.$touched"
	ng-messages="register.username.$error">
	<div ng-message="required">
		This is field required!
	</div>
	<div ng-message="minlength">
		Minimum of 2 characters
	</div>
</div>
```
Also we could display multiple error messages at one time.
```html
<div
	ng-if="register.password.$touched"
	ng-messages="register.password.$error"
	ng-messages-multiple>
	<div ng-message="required">
		This is field required!
	</div>
	<div ng-message="pattern">
		Password should contain at least one digit,
		at least one lower case, at least one upper case
	</div>
	<div ng-message="minlength">
		Minimum of 8 characters
	</div>
	<div ng-message="maxlength">
		Maximum of 20 characters
	</div>
</div>
```
And also could use generic messages.
```html

<script type="text/ng-template" id="generic-messages.html">
	<div ng-message="required">
		This field is required, please fill it in
	</div>
	<div ng-message="minlength">
		This field is too short
	</div>
</script>
<div
	ng-if="register.username.$touched"
	ng-messages="register.username.$error">
	<!-- <div ng-message="required">
		This field is required!
	</div> -->
	<div ng-message="minlength">
		Minimum of 2 characters
	</div>
	<div ng-messages-include="generic-messages.html"></div>
</div>
```
Dynamic messages could be displayed either.
```html
<div
	ng-if="register.password.$touched"
	ng-messages="register.password.$error"
	ng-messages-multiple>
	<div ng-message-exp="['minlength', 'maxlength']">
		Minimum of 8 characters, maximum of 20 characters
	</div>
	<div ng-messages-include="generic-messages.html"></div>
	<div ng-repeat="error in checkout.passwordErrors">
		<div ng-message-exp="error.type">
			{{ error.text }}
		</div>
	</div>
</div>
```
## Custom filters
This allows to apply functions to a set of data.
```javascript
function ordinal() {
	return function(value) {
		var suffix = '';
		var last = value % 10;
		var specialLast = value % 100;
		if (!value || value < 1) {
			return value;
		}
		if (last === 1 && specialLast !== 11) {
			suffix = 'st';
		} else if (last === 2 && specialLast !== 12) {
			suffix = 'nd';
		} else if (last === 3 && specialLast !== 13) {
			suffix = 'rd';
		} else {
			suffix = 'th';
		}
		return value + suffix;
	};
}

angular
	.module('app')
	.filter('ordinal', ordinal);
```
## Component Routing
New Ui Router [UI-Router](https://github.com/angular-ui/ui-router) version allows us to build routed components.
```javascript
var home = {
	template: `
		<div class="home">Home Page</div>
	`
};

angular
	.module('home')
	.component('home', home)
	.config(function ($stateProvider, $urlRouterProvider) {
		$stateProvider
			.state('home', {
				url: '/',
				component: 'home'
				// template: '<home></home>'
			});
		$urlRouterProvider.otherwise('/');
	});
```
All components should be declared in their on modules.
```javascript
angular
	.module('home', [
		'ui.router'
	]);
```
And the app.js should have all component modules.
```javascript
angular
	.module('app', [
		'home',
		'about',
		'contact'
	]);
```
We could also  include nested views to components. Just insert a ui-view property to 
a div, inside a template component, and create a child component.
```javascript
var contactEmail = {
	template: `
		<div>Contact via email: todd@toddmotto.com</div>
	`
};
angular
	.module('contact')
	.component('contactEmail', contactEmail)
	.config(function ($stateProvider) {
		$stateProvider
			.state('contact.email', {
				url: '/email',
				component: 'contactEmail'
			});
	});
```
It is possible to customize the url of the component using the property `url: '^/url',` so the
parent url will not be inhitered. And it is possible to set a default state using the property
`redirectTo: 'component.child',`

### Resolve property
The resolve property allow us to load data to our components and oly render when the data is 
fully loaded. It uses the `binding` property from components to attatch the data to our defined property
```javascript
var repos = {
	bindings: {
		list: '<'
	},
	template: `
		<div class="repos"
			My Repos:
			<ul>
				<li ng-repeat="repo in $ctrl.list">
					<a href="{{ repo.html_url }}">
						{{ repo.name }}
					</a>
					({{ repo.stargazers_count }} stars)
				</li>
			</ul>
		</div>
	`
};

angular
	.module('repos')
	.component('repos', repos)
	.config(function ($stateProvider) {
		$stateProvider
			.state('repos', {
				url: '/repos',
				component: 'repos',
				resolve: {
					list: function (ReposService) {
						return ReposService.getRepos();
					}
				}
			});
	});
```
### Named Views
It is possible to compose components with different views.
```javascript
var contact = {
	template: `
		<div class="contact">
			<div class="contact-methods">
				<h3>How would you like to contact me?</h3>
				<div ui-view="methods"></div>
			</div>
			<div class="contact-details" ui-view="details"></div>
		</div>
	`
};

angular
	.module('contact')
	.component('contact', contact)
	.config(function ($stateProvider) {
		$stateProvider
			.state('contact', {
				url: '/contact',
				views: {
					// default view
					'@': {
						component: 'contact'
					},
					'methods@contact': {
						component: 'contactMethods'
					}
				}
			});
	});

```
To declare views we should use views object. We declared two views in the component, the first as a default declared as `@`, and the second will be a named view `methods` that inherit from `contact`.

### Ui Router Directives
We can use `ui-sref` to link to states. `ui-sref-active` to add a class when the state and child states are the current one, and `ui-sref-active-eq` is the exact current state.

### Ui Router Hooks
It is possible to fire hooks using the Ui Router to start hooks in transitions states.
```javascript
var contact = {
	template: `
		<div class="contact">
			<div class="contact-methods">
				<h3>How would you like to contact me?</h3>
				<div ui-view="methods"></div>
			</div>
			<div class="contact-details" ui-view="details"></div>
		</div>
	`,
	controller: function ($transitions) {
		this.uiCanExit = function () {
			console.log('Exiting...');
			return window.confirm('Are you sure you want to leave?');
		};
		var myTransition = $transitions.onStart({
			to: 'contact.*'
		}, function ($transition$) {
			// $transition$
		});
		this.$onDestroy = function () {
			myTransition();
		};
	}
};

angular
	.module('contact')
	.component('contact', contact)
	.config(function ($stateProvider) {
		$stateProvider
			.state('contact', {
				url: '/contact',
				data: {
					requiredAuth: true
				},
				onEnter: function () {
					console.log('onEnter');
				},
				onExit: function () {
					console.log('onExit');
				},
				onRetain: function () {
					console.log('onRetain');
				},
				views: {
					'@': {
						component: 'contact'
					},
					'methods@contact': {
						component: 'contactMethods'
					}
				}
			});
	});

```
### Dynamic Route Params
We could provide query and path params to dynamic change states.
```javascript
var repos = {
	bindings: {
		list: '<'
	},
	template: `
		<div class="repos">
			My Repos:
			<div class="repo-search">
				<form ng-submit="$ctrl.submitForm()">
					<input ng-model="$ctrl.name">
					<button type="submit">View repos</button>
				</form>
			</div>
			<ul>
				<li ng-hide="$ctrl.list.length">
					Nothing here yet, type and search!
				</li>
				<li ng-repeat="repo in $ctrl.list">
					<a href="{{ repo.html_url }}">
						{{ repo.name }}
					</a>
					({{ repo.stargazers_count }} stars)
				</li>
			</ul>
		</div>
	`,
	controller: function ($state) {
		this.submitForm = function () {
			$state.go('repos', {
				username: this.name
			});
		};
	}
};

angular
	.module('repos')
	.component('repos', repos)
	.config(function ($stateProvider) {
		$stateProvider
			.state('repos', {
				url: '/repos?username',
				component: 'repos',
				params: {
					username: null
				},
				resolve: {
					list: function (ReposService, $transition$) {
						var params = $transition$.params().username;
						if (!params) return;
						return ReposService.getRepos(params);
					}
				}
			});
	});

```
## Constans
It is possible to create constants to reuse in the app
```javascript
angular
	.module('app', [])
	.constant('FIREBASE_CONFIG', {
		apiKey: 'b3oc_09KcKkk3WijJsbc',
		authDomain: 'tm-contacts-manager.firebaseapp.com',
		databaseURL: 'https://tm-contacts-manager.firebaseio.com',
		storageBucket: '',
	})
	.value('EventEmitter', function (event) {
		$event: event
	});
```
## $Resource
`$resource` makes http requests in easy way.
```javascript
function TodoService($resource) {
	return $resource('http://jsonplaceholder.typicode.com/todos/:id', {
		id: '@id'
	}, {
		update: {
			method: 'PUT'
		}
	});

}

angular
	.module('app')
	.service('TodoService', TodoService);

...
ctrl.list = TodoService.query();

	ctrl.singleTodo = TodoService.get({
		id: 1
	});

	ctrl.addTodo = function () {
		var newTodo = new TodoService();
		newTodo.title = ctrl.title;
		newTodo.completed = false;
		newTodo.userId = 999;
		newTodo.$save(function () {
			ctrl.list.unshift(newTodo);
		});
	};

	ctrl.deleteTodo = function (todo) {
		todo.$delete(function () {
			ctrl.list = ctrl.list.filter(function (item) {
				return item.id !== todo.id;
			});
		});
	};

	ctrl.updateTodo = function (todo) {
		todo.$update(function () {
			console.log('Updated!', todo);
		});
	};
...
```
## Promises
We could use promises to resolve async calls.
Inject `$q` to use the promise functionality.
```javascript
this.getTodos = function () {
		var defer = $q.defer();
		var xhr = new XMLHttpRequest();
		xhr.onreadystatechange = function () {
			if (xhr.readyState === 4) {
				if (xhr.status === 200) {
					defer.resolve(JSON.parse(xhr.responseText));
				} else {
					defer.reject(JSON.parse(xhr.responseText));
				}
			}
		};
		xhr.open('GET', todos, true);
		xhr.send();
		return defer.promise;
	};

	this.getComments = function () {
		return $q(function (resolve, reject) {
			var xhr = new XMLHttpRequest();
			xhr.onreadystatechange = function () {
				if (xhr.readyState === 4) {
					if (xhr.status === 200) {
						resolve(JSON.parse(xhr.responseText));
					} else {
						reject(JSON.parse(xhr.responseText));
					}
				}
			};
			xhr.open('GET', comments, true);
			xhr.send();
		});
	};

	this.getAll = function () {
		var promiseOne = $http.get(todos);
		var promiseTwo = $http.get(comments);
		return $q.all([promiseOne, promiseTwo]);
	};

	this.getFirstResolved = function () {
		var promiseOne = $http.get(todos);
		var promiseTwo = $http.get(comments);
		return $q.race([promiseOne, promiseTwo]);
	};

```
## Http Interceptors
Allow us to intercet http requests
```javascript
function MyHttpInterceptor(TODO_API, $q) {
	return {
		request: function (config) {
			if (config.url === TODO_API) {
				config.headers['x-csrf-token'] = 'toddmotto';
			}
			console.log(config);
			return config;
		},
		requestError: function (config) {
			console.log(config);
			return $q.reject(config);
		},
		response: function (response) {
			console.log(response);
			return response;
		},
		responseError: function (response) {
			console.log(response);
			return $q.reject(response);
		}
	};
}

angular
	.module('app')
	.factory('MyHttpInterceptor', MyHttpInterceptor);



angular
	.module('app', [])
	.constant('TODO_API', 'http://jsonplaceholder.typicode.com/todos')
	.config(function ($httpProvider) {
		$httpProvider.interceptors.push('MyHttpInterceptor');
	});

```
## Unit Tests
To run unit tests we could use `karma` and `jasmine`.
To configure we need to declare the dependencies in the package.json file.
```javascript
{
  "name": "karma-jasmine",
  "version": "1.0.0",
  "devDependencies": {
    "jasmine-core": "^2.4.1",
    "karma": "^0.13.19",
    "karma-chrome-launcher": "^0.2.2",
    "karma-jasmine": "^0.3.6",
    "karma-spec-reporter": "0.0.23"
  }
}
```
After, run the command `npm install`to install all dependencies. Then run `karma init`, so 
a wizard will configure karma. We should define the files that karma should watch to run the tests.
Also we need to define the plugins that we installed. Change the reporter to spec. Set the `singleRun` to only one time. For run the tests use `karma start`.
All tests should have the spec suffix.
```javascript
describe('Example Test', function () {
	it('should pass this test', function () {
		expect(true).toBe(true);
	});
});
```
### Test Controllers
To mock controllers that use services, we need the lib `angular-mocks.js` and `angular.js` defined at karma.conf.js. We use `module` function from `angular-mocks` to mock a module. The `$injector` allows us to inject dependencies from the application. To use the controller as syntax we should use the `$controller` method, passing the arguments needed.

```javascript
describe('TodoController', function () {
	var $controller, TodoController;

	//runs berfore each test
	beforeEach(module('app'));

	//runs berfore each test
	beforeEach(inject(function ($injector) {
		$controller = $injector.get('$controller');

		var $scope = {};

		// mock the todo service
		var TodoService = function () {};

		TodoService.prototype.$save = function () {};

		TodoService.query = function () {
			return {
				$promise: { // mock the promise object $resource gives us
					then: function (callback) {
						callback([
							{
								id: 1,
								title: 'Example Todo',
								userId: 1
							}
						]) // callback with fake data
					}
				}
			}
		};

		TodoService.delete = function () {
			return true;
		};

		TodoController = $controller('TodoController as todo', {
			$scope: $scope,
			TodoService: TodoService
		});
	}));

	it('should have get items from the service', function () {
		var resp = TodoController.getTodos();

		expect(TodoController.list[0]).toEqual({
			id: 1,
			title: 'Example Todo',
			userId: 1
		});
	});

	it('should delete the item from the service', function () {
		TodoController.deleteTodo(1);

		expect(TodoController.list.length).toEqual(0);
	});

	it('should be create a new item', function () {
		TodoController.title = 'Example Title';

		TodoController.newTodo();

		expect(TodoController.list.length).toEqual(1);
	});
});

``` 

### Test Components
`$componentController` provided from angular-mocks provide a mock of components. The second argument is the dependencies. The third argument is the binding argument. To call life cicle hooks, you need to do explict.
```javascript
describe('Counter', function () {
	var $componentController, controller, initialCount = 200;

	beforeEach(module('app'));

	beforeEach(inject(function ($injector) {
		$componentController = $injector.get('$componentController');
		controller = $componentController('counter',
			{ $scope: {}},
			{ initialCount: initialCount }
		);
	}));

	it('should have an initial count of 0', function () {
		expect(controller.count).toEqual(0);
	});

	it('should initialize to the correct count', function() {
		expect(controller.count).toEqual(0);
		controller.$onInit();
		expect(controller.count).toEqual(initialCount);
	});

	it('should increment the counter correctly', function () {
		controller.increment();

		expect(controller.count).toEqual(1);
	});

	it('should decrement the counter correctly', function () {
		controller.decrement();

		expect(controller.count).toEqual(-1);
	});
});
```
### Test Directives
To test directives we need to compile the directive first, and then get the element to have access to 
the controller
```javascript
describe('Counter', function () {
	var $compile, $scope, $controller, element;

	beforeEach(module('app'));

	beforeEach(inject(function ($injector) {
		$compile = $injector.get('$compile');

		var $rootScope = $injector.get('$rootScope');

		element = angular.element('<counter></counter>');
		$compile(element)($rootScope.$new());

		$controller = element.controller('counter');
		$scope = element.isolateScope() || element.scope();
	}));

	it('should have an initial count of 0', function () {
		expect($controller.count).toEqual(0);
	});

	it('should increment the counter correctly', function () {
		$controller.increment();

		expect($controller.count).toEqual(1);
	});

	it('should decrement the counter correctly', function () {
		$controller.decrement();

		expect($controller.count).toEqual(-1);
	});
});

```
### Test Filters
WE could test filter. Just use the `$filter` object
```javascript
describe('GenderFilter', function () {
	var $filter;

	beforeEach(module('app'));

	beforeEach(inject(function ($injector) {
		$filter = $injector.get('$filter');
	}));

	it('should filter out based on gender', function () {
		var list = [{
			name: 'john doe',
			gender: 'male'
		}, {
			name: 'sarah smith',
			gender: 'female'
		}];

		var result = $filter('gender')(list, 'male');
		expect(result.length).toBe(1);
		expect(result[0].name).toEqual('john doe');
	});
});

```
### Testing services
To test Services we use `$httpBackend`. We should pass a function `done` to tell angular 
when the test has finished. Also we need to tell `$httpBackend` that the request has finished.
```javascript
describe('TodoService', function () {
	var TodoService, $httpBackend;

	beforeEach(module('app'));

	beforeEach(inject(function ($injector) {
		TodoService = $injector.get('TodoService');
		$httpBackend = $injector.get('$httpBackend');

		$httpBackend
			.when('GET', 'http://jsonplaceholder.typicode.com/todos')
			.respond(
				[
					{
						id: 1,
						title: 'Fake Title',
						userId: 1
					}
				]
			);

		$httpBackend
			.when('DELETE', 'http://jsonplaceholder.typicode.com/todos/1')
			.respond({})
	}));

	it('should get a list of todos from the server', function (done) {
		$httpBackend.expectGET('http://jsonplaceholder.typicode.com/todos');

		TodoService
			.query()
			.$promise
			.then(function (res) {
				if (res[0].title === 'Fake Title') {
					done();
				}
			});

		$httpBackend.flush();
	});

	it('should delete todo items from the server', function () {
		$httpBackend.expectDELETE('http://jsonplaceholder.typicode.com/todos/1');

		TodoService
			.delete({
				id: 1
			});

		$httpBackend.flush();
	});
});

```
### Test Routes
To test routes we need to verify if the route use templateUrl. This will make a request to get
the template, so we need to mock also.
```javascript
describe('Routes', function () {
	var $state, $http, $httpBackend;

	beforeEach(module('app'));

	beforeEach(inject(function ($injector) {
		$state = $injector.get('$state');
		$http = $injector.get('$http');
		$httpBackend = $injector.get('$httpBackend');

		$httpBackend
			.when('GET', 'views/home.html')
			.respond('');

		$httpBackend
			.when('GET', '/users/example')
			.respond(
				{
					name: 'Example User'
				}
			);

	}));

	describe('Home Page', function () {
		var state;

		it('should have the correct URL', function () {
			state = $state.get('home');

			expect(state.url).toEqual('/');
		});

		it('should use the correct template', function () {
			expect(state.templateUrl).toEqual('views/home.html');
		});
	});

	describe('User Page', function () {
		var state;

		it('should have the correct URL', function () {
			state = $state.get('user');

			expect(state.url).toEqual('/user/:name');
		});

		it('should use the correct template', function () {
			expect(state.templateUrl).toEqual('views/user.html');
		});

		it('should use the correct controller', function () {
			expect(state.controller).toEqual('UserController as user');
		});

		it('should resolve data correctly', function (done) {
			$httpBackend.expectGET('views/home.html');
			$httpBackend.expectGET('/users/example');

			state
				.resolve
				.user($http, {name: 'example'})
				.then(function (res) {
					if (res.data.name === 'Example User') {
						done();
					}
				});

			$httpBackend.flush();
		});
	});

	describe('User Notes Page', function () {
		var state;

		it('should have the correct URL', function () {
			state = $state.get('user.notes');

			expect(state.url).toEqual('/notes');
		});

		it('should use the correct template', function () {
			expect(state.templateUrl).toEqual('views/user/notes.html');
		});

		it('should use the correct controller', function () {
			expect(state.controller).toEqual('UserNotesController as user');
		});
	});

	describe('User Settings Page', function () {
		var state;

		it('should have the correct URL', function () {
			state = $state.get('user.settings');

			expect(state.url).toEqual('/settings');
		});

		it('should use the correct template', function () {
			expect(state.templateUrl).toEqual('views/user/settings.html');
		});

		it('should use the correct controller', function () {
			expect(state.controller).toEqual('UserSettingsController as user');
		});
	});
});

```
