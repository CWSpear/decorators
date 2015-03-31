# Usecase: Mixins

It should be possible to coppy mixin methods from other class and bring all
annotations with them.

## Example


`test.ts`:
```
class AuthMixin {
  @Inject()
  onLogin(a:A, b:B) {}

  @Inject()
  onLogout(b:B, c:C) {}
}

@Component(...)
@Template(...)
class MyApp {
}

Mixin(MyApp, AuthMixin);// copies methods from AuthMixin to MyApp
```

The resulting `MyApp` class should be equivalent to:

```
@Component(...)
@Template(...)
class MyApp {
  @Inject()
  onLogin(a:A, b:B) {}

  @Inject()
  onLogout(b:B, c:C) {}
}

```