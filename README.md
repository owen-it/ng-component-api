# ng-component-api

Provides an extra layer for register AngularJs components.

# Install

`npm install ng-component-api --save`

# Understanding Components

In AngularJS, a Component is a special kind of [directive](https://docs.angularjs.org/guide/directive) that uses a simpler
configuration which is suitable for a component-based application structure.

This makes it easier to write an app in a way that's similar to using Web Components or using the new Angular's
style of application architecture.

Advantages of Components:
- simpler configuration than plain directives
- promote sane defaults and best practices
- optimized for component-based architecture
- writing component directives will make it easier to upgrade to Angular

When not to use Components:

- for directives that need to perform actions in compile and pre-link functions, because they aren't available
- when you need advanced directive definition options like priority, terminal, multi-element
- when you want a directive that is triggered by an attribute or CSS class, rather than an element

## Creating and configuring a Component

Components can be registered using the `.component()` method of an AngularJS module (returned by {@link module `angular.module()`}). The method takes two arguments:

  * The name of the Component (as string).
  * The Component config object. (Note that, unlike the `.directive()` method, this method does **not** take a factory function.)
  
 Example:
 ```js
   import angular from 'angular';

   const mortgageForm = {
     template: `
       <form>
         {{ $ctrl.applicant | json }}
       </form>
     `,
     controller() {
       this.applicant = {
         name: 'Todd Motto',
         email: 'todd@toddmotto.com'
       };
     }
   };

   angular.module('app').component('mortgageForm', mortgageForm);
 ```

## Creating and configuring a Component with ng-component-api

tab-component.js
```js
    var tab = {
      bindings: {
        label: '@'
      },
      require: {
        tabs: '^^'
      },
      transclude: true,
      template: `
        <div class="tabs__content" ng-if="$ctrl.tab.selected">
          <div ng-transclude></div>
        </div>
      `,
      controller: function () {
        this.$onInit = function () {
          this.tab = {
            label: this.label,
            selected: false
          };
          this.tabs.addTab(this.tab);
        };
      }
    };
    
    export default tab;
```

tabs-component.js
```js
  import tab from 'tab-component.js';
  
  var tabs = {
    components: { tab }, // <- with ng-component-api
    transclude: true,
    controller: function () {
      this.$onInit = function () {
        this.tabs = [];
      };
      this.addTab = function addTab(tab) {
        this.tabs.push(tab);
      };
      this.selectTab = function selectTab(index) {
        for (var i = 0; i < this.tabs.length; i++) {
          this.tabs[i].selected = false;
        }
        this.tabs[index].selected = true;
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
  
  export default tabs;

```

```js
  import angular from 'angular';
  import compApi from 'ng-component-api';
  
  // init
  compApi.install(angular);
  
  import tabs from 'tabs-component.js';
  import other from 'other-component.js';
  
  // Regiter component
  angular.module('someModule', []).components({ tabs, other })
  
```

tabs.html
```html
<tabs selected="0">
  <tab label="Tab 1">...</tab>
  <tab label="Tab 2">...</tab>
  <tab label="Tab 3">...</tab>
</tabs>
```

# License

The [Mit License](LICENSE)
