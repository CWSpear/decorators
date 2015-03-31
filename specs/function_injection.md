# Usecase: `function` annotation

It is desirable to be able to annotate function expressions. 
(Possibly function declarations, but that creates TDZ issues. 
Maybe a decoratr should convert a function declaration into function expression)



## Example

In unit tests it is often desirable to annotate function with `@inject` 
decorator, which than allows TypeScript to create type annotations.

`test.ts`:
```
describe('some test', () => {
  it('should do something', @inject (a:DepA) => {
    ...
  });
});
```

TypeScript would then transpile the output into ES6 like so:

`test.es6`:
```
describe('some test', () => {
  it('should do something', @inject @Parameters([DepA]) (a) => {
    ...
  });
});
```


The assumption is that TypeScript will generate the `@Parameters(...)` 
decorator whenever it sees any decorator (in this case `@inject`).


## Suporting code

```
function GenerateTypeAnnotation(target) {
  // marker annotation to let TypeScript know that it should generate
  // annotation @Paramaters implicitly.
}

@GenerateTypeAnnotation
function inject(target) {
  // no need to do anything here as it only acts like a marker
}

function Paramaters(types) {
  return function(target) {
    Reflect.setMetadata(target, 'paramaters', types);
  }
}
```