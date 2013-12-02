# angular-common

Some common Angular JS directives and services that I reuse across my various projects.

## Flash

Flash has been extracted to https://github.com/wmluke/angular-flash.

## startInterval

The `startInterval` service provides an Angular aware interval.  

NOTE: Angular 1.2 now has a built-in [interval wrapper](http://docs.angularjs.org/api/ng.$interval).

### Usage

```javascript

var FooController = function ($scope, startInterval, SomeFeed, $timeout) {

    $scope.items = [];
    $scope.count = 0;
    $scope.elapsed = 0;

    /**
     * Create and start an interval to load feed items every 2 seconds.
     */
    var _interval = startInterval(function (count, elapsed) {
        SomeFeed.findAll()
            .success(function (items) {
                // Angular ready: No need for $apply
                $scope.items = items;
                $scope.count = count;
                $scope.elapsed = elapsed;
            })
            .error(function () {
                // Stop the interval and restart it in 5 seconds.
                _interval.run = false;
                $timeout(function () {
                    _interval.run = true;
                }, 5000);
            });
    }, 2000);

    /**
     * Be sure to stop the interval when the current scope is destroyed.
     */
    $scope.$on('$destroy', function () {
        if (_interval && _interval.run) {
            _interval.run = false;
        }
    });
};

FooController.$inject = ['$scope', 'startInterval', 'SomeFeed', '$timeout'];

```

### Installation

Load the `angular-common.interval-service` module in your app.

```javascript
angular.module('app', ['angular-common.interval-service']);
```
