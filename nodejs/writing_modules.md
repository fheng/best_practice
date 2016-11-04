There are many patterns for writing modules within node. Here are some that we believe to have 
good value and reflect how we believe code should look and be written.

## The module pattern. 

The module pattern describes a way to construct your code to allow encapsulation of private variables
and exposing a simple public interface. 

Here is an example of what this looks like in practice, remembering we are working within node.js and not the browser.

```javascript
    function myBusinessLogic(){
        const myPrivateValue = "this is mine";
        
        return{
            "doSomething": function (cb){
                return cb(undefined,myPrivateValue);
            },
            "doSomethingElse":function (val,cb){
                if (val !== myPrivateValue){
                    return cb(New Error("..."));
                }
                return cb();
            }
        };
    }
```

This pattern allows for simple enhancement, and adding additional dependencies as needed. In many ways it is similar to how 
you might think about a class in another language. The function is a constructor and the returned object is the instance.

## Externalise Dependencies 

By dependencies we are not talking about ``` require('async') ``` but rather things that your code depends on that are external to the code.
What do I mean? 
- A database connection
- A module that interacts with the database such as a mongoose model 
- A logger 
- configuration 
- http client 
Other potential dependencies include another module in your code that also depends on something external to your code. 
Here is the same piece of code but now using some of these dependencies.  

```javascript
    function myBusinessLogic(dbModel,config, httpClient){
        const myPrivateValue = "this is mine";
        
        return{
            "doSomething": function (cb){
                if (!config.myBusinessLogic.enabled){
                    return cb(new Error("myBusinessLogic is not enabled"))
                }
                return cb(undefined,myPrivateValue);
            },
            "doSomethingElse":function (val,cb){
                if (val !== myPrivateValue){
                    return cb(New Error("..."));
                }
                return cb();
            }
        };
    }
```

Why do this? By externalising our dependencies, we allow for simpler and more maintainable unit testing. We also communicate clearly to the clients of our api, that this module is
going to do something with the database or with the network (as an example).

### How does this help with testing?

Now we can mock these external dependencies and control their behaviour in a very simple way either at test file level or on a test by test basis.
We significatly reduce the chances that we are effecting global test state by requiring and modifying a global config (as an example).

```javascript
function TestMyBusinessLogic(done){
    var dbModel = { // you could of course use stubs etc here.
        "fetchAll": function (cb){
            return cb(undefined,[{"Name":"test"}])
        }
    }
    var config = {
        "myBusinessLogic":{"enabled":"true"} // you can easily switch on or off values without effecting the config for the whole test suite 
    }
    var httpClient = {
        "get":function (url,cb){
             //.... not filled out for brevity 
        }
    }
    underTest = require('../myBusinessLogic.js')(dbModel,config,httpClient);
    underTest.doSomething(function (err, val){
        //asserts
        done();
    });
}

``` 

### Our code is clear and communicates well its intentions

By externalising our dependencies in this way it becomes clearer what our module depends on, but notice that in this case, we have not used
```require('../someModel.js')``` for example, this means our module is not tightly coupled to the someModel file, but instead only requires 
an object that implements the correct interface. Finally returning our clean api from our function allows us to clearly undersand what this module does 
and distinguish its business logic (public api), from the dependencies it requires in order to achieve this. 

