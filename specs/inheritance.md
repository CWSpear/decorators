# Usecase: Annotation Inheritance

In general it is upto the framework to decide whether or not annotations are
inheritable. 

## Example

In this example a common thing to is to have common behavior component which 
is subclassed for specific platforms or even a testing mock. It is upto
the framework to walk the class inhertance and decide how the annotations
should be merged across sub-classes. 

`test.ts`:
```
@Component('tab');
@Template({url: '...';})
@Template({locale: 'us', url: '...'}
class Tab {}


@Component('ios-tab');
@Template({platform: 'ios', url: '...';})
class IOSTab extends Tab {}


class MockTab extends Tab {}
```
* It is upto the framework to decide if an annotation inherits to children or 
  not. (Use case specific)
* `MockTab` has no `@Component` and so it can not be used in HTML. 
  (It would be wrong to assume that `<tab>` is `MockTab`.)
* `IOSTab` has different set of `@Templates` than Tab. It would be wrong to 
  merge them.
