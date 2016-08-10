# angularjs1-5-route-resolve
AngularJS 1.5 Route Resolve

Based on 'Route Resolve' at https://www.youtube.com/watch?v=7BVo-72tgG0&list=PL6n9fhu94yhWKHkcL7RJmmXyxkuFB3KSl&index=41

#Route Resolve

The benefit of route resolve is that we won't transition to the new route until all promises have been resolved.

Using the route ```resolve``` property on the '/students' route as follows

Note: here we are imitating a network latency of 2000 milliseconds in the students controller

```javascript
    [...]
    .when("/students", {
        templateUrl: "templates/students.html",
        controller: "studentsCtrl",
        controllerAs: "ctrl",
        resolve: {
            // A function that returns a promise
            studentsList: function($http) {
                // return $http.get("StudentsService/GetAllStudents")
                //      .then(function(response) {
                //            return response.data;
                //       }, function(reason) {
                //            return reason.data;
                //       });
                /**
                 * Delay for a number of milliseconds
                 */
                function sleep(delay) {
                    var start = new Date().getTime();
                    while (new Date().getTime() < start + delay);
                }
                // Imitate a network latency by 2000 milliseconds
                sleep(2000);
                vm.students = [{ id: 1, name: "Ben", gender: "Male", city: "London" }, { id: 2, name: "Matt", gender: "Male", city: "New York" }, { id: 3, name: "Pam", gender: "Female", city: "Chennai" }];
            }
        }
    })
```

Then inject the newly created resolve function 'studentsList' into the students controller and assign the model vm.students to it.
 
```javascript
    .controller("studentsCtrl", function(studentsList, $route, $location) {

        var vm = this;

        vm.searchStudent = function() {
            if(vm.name) {
                $location.url("/studentsSearch/" + vm.name);
            }
            else {
                $location.url("/studentsSearch");
            }
        }

        vm.reloadData = function() {
            $route.reload();
        }

        vm.students = studentsList;
``` 

In summary:

1) The resolve property contains one or more promises that must resolve successfully before transitioning to the new route.

2) The property names used in the resolve property can then be injected in the controller. This means the controller does not have to fetch the data.

See script.js, index.html and styles.css how to implement this.
