# Usecase: Annotation / Decorator Composability

Annotations and Decorators should be composable. In the sence that adding
a new Decorator should not erase existing Annotations. 

## Example


`test.ts`:
```
class MyApp {

  @log
  @Inject(DepA, DepB)
  @profile
  someMethod(a, b) {
  }

}
```

Both `@log` and `@profile` replace method instance. The annotation should be 
properly preserved.

## Supporting Code

```
function Inject(...types) {
  return function(target) {
    Reflect.setMetadata(target, 'inject', types);
  }
}


function log(target) {
  return function() {
    console.log(target.name);
    return target.apply(this, arguments);
  }
}

function profile(target) {
  return function() {
    var start = performance.now()
    try {
      return target.apply(this, arguments);
    } finally {
      console.log(target.name, performance.now() - start);
    }
  }
}
```