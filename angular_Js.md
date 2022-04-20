[angularjs 面试（一）](https://www.cnblogs.com/YangqinCao/p/5774637.html)
[angularjs 面试（二）](https://www.cnblogs.com/YangqinCao/p/5785567.html)
[github - angularjs - qa](https://github.com/krosti/angularjs-q-a)
[DI源码实现](http://www.alloyteam.com/2015/09/angularjs-study-of-dependency-injection/)
[AngularJS 中的依赖注入实际应用场景？](https://www.zhihu.com/question/28097646)

# angularJS 的常用指令

- ng-app- 初始化角度应用程序。
- ng-init- 初始化 Angular 应用数据。
- ng-model- 绑定 html 元素
- ng-if
- ng-show / ng-hide
- ng-repeat (相同值： item in array track by $index)

# angularJS 的基础使用流程

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="lib/angular.js"></script>
    <script>
      var app = angular.module("ScopeChain", []);

      app.controller("parentController", function ($scope) {
        $scope.managerName = "Shailendra Chauhan";
        $scope.$parent.companyName = "Dot Net Tricks"; //attached to $rootScope
      });

      app.controller("childController", function ($scope, $controller) {
        $scope.teamLeadName = "Deepak Chauhan";
      });
    </script>
  </head>
  <body ng-app="ScopeChain">
    <div ng-controller="parentController ">
      <table style="border:2px solid #e37112">
        <caption>
          Parent Controller
        </caption>
        <tr>
          <td>Manager Name</td>
          <td>{{managerName}}</td>
        </tr>
        <tr>
          <td>Company Name</td>
          <td>{{companyName}}</td>
        </tr>
        <tr>
          <td>
            <table
              ng-controller="childController"
              style="border:2px solid #428bca;"
            >
              <caption>
                Child Controller
              </caption>
              <tr>
                <td>Team Lead Name</td>
                <td>{{ teamLeadName }}</td>
              </tr>
              <tr>
                <td>Reporting To</td>
                <td>{{managerName}}</td>
              </tr>
              <tr>
                <td>Company Name</td>
                <td>{{companyName}}</td>
              </tr>
            </table>
          </td>
        </tr>
      </table>
    </div>
  </body>
</html>
```

# AngularJS 范围的生命周期

# AngularJS 中的依赖注入是什么？

- 依赖注入的目的是为了减少组件间的耦合
  - aaa.controller("Art", [function(Bar, Car) {}, "Bar", "Car"]);
  - 方便对应类型
- 方便测试

- func.apply(scope || {}, arr);

  - func 控制器 定义函数
  - scope 控制对象
  - arr 依赖注入的内容

- 简单实现

```js
var inject = {
  dependencies: {},
  register: function (key, value) {
    this.dependencies[key] = value;
  },
  resolve: function (deps, func, scope) {
    var arr = [];
    for (var i = 0; i < deps.length; i++) {
      if (this.dependencies.hasOwnProperty(deps[i])) {
        arr.push(this.dependencies[deps[i]]);
      }
    }
    console.log(arr);
    return function () {
      func.apply(scope || {}, arr);
    };
  },
};

// 相当于注册 （factory， service，provider）
// TIPS: 但是这种写法 在进行代码压缩的时候，会导致类型无法对应；
inject.register('$http', {'get':function(){console.log('get')}});
inject.register('$scope', {'test':''});

// 注入
MyController = inject.resolve(['$http','$scope'], MyController);

```

```html
<body ng-app="app">
  <div ng-controller="IndexCtrl as vm">
    <h1>{{vm.greeting}} {{vm.name}}</h1>
    <input type="text" ng-model="vm.name" />
  </div>
</body>

<script>
  (function () {
    angular.module("app", []);

    angular.module("app").controller("IndexCtrl", displayCtrl);

    // 注入：
    displayCtrl.$inject = ["$filter"];

    function displayCtrl($filter) {
      this.greeting = $filter("uppercase")("Hello");
      this.name = "Thomson";
    }
  })();
</script>
```

# factory、service 和 provider 是什么关系？（创建并注册我们自己的 service）

- factory：工厂模式，创建一个 对象 {}，添加属性，添加方法 后 return 出来；

```html
<div ng-controller="IndexCtrl as vm">
  <h4>{{vm.greeting}} {{vm.name}} !!!</h4>
  <hr />

  <input type="text" ng-model="vm.q" /><br />
  <div><b>Total Count : {{vm.count}}</b></div>
  <br />

  <ul>
    <li ng-repeat="person in vm.persons | filter:vm.q">
      {{person.name.first}} {{person.name.last}}
    </li>
  </ul>
</div>
<script>
  (function (angular) {
    "use strict";

    angular.module("app", []);

    angular.module("app").controller("IndexCtrl", indexCtrl);

    // 依赖注入
    indexCtrl.$inject = ["store"];

    function indexCtrl(store) {
      var vm = this;

      vm.name = "Thomson";
      vm.greeting = "Hello";
      vm.persons = store.getPersons();
      vm.count = store.getPersonCount();
    }

    // 工程服务
    angular.module("app").factory("store", function () {
      function getPersons() {
        return persons;
      }

      function getPersonCount() {
        return getPersons().length;
      }

      return {
        getPersons: getPersons,
        getPersonCount: getPersonCount,
      };
    });
  })(angular);
</script>
```

- service：需要通过 new 关键字实例化，（依赖注入中处理），通过 this 添加属性，方法；（构造函数法）

```js
app.controller("myServeCtrl", function ($scope, myServe) {
  $scope.artist = myServe.getArtist();
});

app.service("myServe", function () {
  var _artist = "a";
  this.getArtist = function () {
    return _artist;
  };
});
```

- provider：唯一一种你可以传进 `.config()` 函数的 service
  - 当你想要在 service 对象启用之前，先进行模块范围的配置，那就应该用 provider。
  - 可以把 Provider 想象成由两部分组成。
    - 第一部分的变量和函数是可以在 app.config 函数中访问的,因此 可以在它们被其他地方 `访问到之前` 来修改它们
    - 第二部分 的变量和函数是可以在任何传入了’myProvider‘的 `控制器` 中进行访问的
  - 当你使用 Provider 创建一个 service 时，唯一的可以在你的控制器中访问的属性和方法是通过$get()函数返回内容。
    > 从底层实现上来看，service 调用了 factory，`返回其实例`；
    > factory 调用了 provider，返回其 $get 中定义的内容。factory 和 service 功能类似，只不过 factory 是普通 function，可以返回任何东西（return 的都可以被访问）；
    > service 是`构造器`，可以不返回（绑定到 this 的都可以被访问）；
    > provider 是加强版 factory，返回一个`可配置的 factory`

```js
app.controller("myProviderCtrl", function ($scope, myProvider) {
  $scope.artist = myProvider.getArtist();
  $scope.data.thingFromConfig = myProvider.thingOnConfig;
});

// + 定义 provider
app.provider("myProvider", function () {
  // 可配置内容
  this._artist = "";
  this.thingFromConfig = "";

  // 注入的内容
  this.$get = function () {
    var that = this;

    return {
      getArtist: function () {
        return that._artist;
      },
      thingOnConfig: that.thingFromConfig,
    };
  };
});

// + provide的可配置
app.config(function (myProvider) {
  myProvider.thingFromConfig = "this is set config";
});
```

# 作用域对象（$rootScope） 与 $scope

- $rootScope —— 每个 Angular 应用默认有一个根作用域 $rootScope， 根作用域位于最顶层，从它往下挂着各级作用域。
- $scope ——在DI的情况下，你用$前缀注入作用域对象，也就是$scope。原因是注入的参数必须匹配可注入对象的名称，后面跟着$($)前缀。

# 作用域机制（angularJS 控制器之间如何通信）

- $scope $rootScope
- $scope.$emit("someEvent", {}); $scope.$on(name,listener)
- $scope.$broadcast("someEvent", {});
- fatory('EventBus', function(){})

# 如果你要从 Angular 1.4 迁移到 Angular 1.5，你最需要重构的是什么

- 为了适应新的 Angular 1.5 组件，把.directive 改成.component

# 依赖注入的方式有哪几种

- 简单注入

```js
myModule.controller("myCtrl", myCtrl);
```

- 数组注释法

```js
myModule.controller('myCtrl', ['$scope', 'Preject', function($scope, Project)
```

- 显示调用 function 的$inject

```js
function myCtrl(a, b) {
  //$scope, Project,故意改成a,b模拟压缩后的情形
}
myCtrl.$inject = ["$scope", "Project"];

myModule.controller("PhoneDetailCtrl", myCtrl);
```
