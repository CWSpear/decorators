# Metadata Reflection API

## Syntax

```JavaScript
// define metadata on an object or property
Reflect.defineMetadata(metadataKey, metadataValue, target);
Reflect.defineMetadata(metadataKey, metadataValue, target, propertyKey);

// check for presence of a metadata key on the prototype chain of an object or property
let result = Reflect.hasMetadata(metadataKey, target);
let result = Reflect.hasMetadata(metadataKey, target, propertyKey);

// check for presence of an own metadata key of an object or property
let result = Reflect.hasOwnMetadata(metadataKey, target);
let result = Reflect.hasOwnMetadata(metadataKey, target, propertyKey);

// get metadata value of a metadata key on the prototype chain of an object or property
let result = Reflect.getMetadata(metadataKey, target);
let result = Reflect.getMetadata(metadataKey, target, propertyKey);

// get metadata value of an own metadata key of an object or property
let result = Reflect.getOwnMetadata(metadataKey, target);
let result = Reflect.getOwnMetadata(metadataKey, target, propertyKey);

// get all metadata keys on the prototype chain of an object or property
let result = Reflect.getMetadataKeys(target);
let result = Reflect.getMetadataKeys(target, propertyKey);

// get all own metadata keys of an object or property
let result = Reflect.getOwnMetadataKeys(target);
let result = Reflect.getOwnMetadataKeys(target, propertyKey);

// delete metadata from an object or property
let result = Reflect.deleteMetadata(metadataKey, target);
let result = Reflect.deleteMetadata(metadataKey, target, propertyKey);
```

# Abstract Operations

## Operations on Objects

### GetOrCreateMetadataMap ( O, P, Create )

When the abstract operation GetOrCreateMetadataMap is called with Object <var>O</var>, property key <var>P</var>, and Boolean <var>Create</var> the following steps are taken:

  1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
  2. Let <var>metadataMap</var> be **undefined**.
  3. Let <var>succeeded</var> be **true**.
  4. Let <var>targetMetadata</var> be the value of <var>O</var>'s \[\[Metadata\]\] internal slot.
  5. If <var>targetMetadata</var> is **undefined**, then
    1. If <var>Create</var> is **false**, return **undefined**.
    2. Set <var>targetMetadata</var> to be a newly created **Map** object.
    3. Set the \[\[Metadata\]\] internal slot of <var>O</var> to <var>targetMetadata</var>.
  6. Let <var>metadataMap</var> be [Invoke][](<var>targetMetadata</var>, "get", <var>P</var>).
  7. [ReturnIfAbrupt][](<var>metadataMap</var>).
  8. If <var>metadataMap</var> is **undefined**, then
    1. If <var>Create</var> is **false**, return **undefined**.
    2. Set <var>metadataMap</var> to be a newly created **Map** object.
    3. Let <var>setStatus</var> be [Invoke][](<var>targetMetadata</var>, "set", <var>P</var>, <var>metadataMap</var>).
    4. [ReturnIfAbrupt][](<var>setStatus</var>).
  9. Return <var>metadataMap</var>.

### \[\[HasMetadata\]\] ( MetadataKey, P )

When the \[\[HasMetadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var> and property key <var>P</var>, the following steps are taken:

  1. Return OrdinaryHasMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).

### OrdinaryHasMetadata ( MetadataKey, O, P )

When the abstract operation OrdinaryHasMetadata is called with ECMAScript language value <var>MetadataKey</var>, Object <var>O</var>, and property key <var>P</var>, the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>hasOwn</var> be OrdinaryHasOwnMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).
3. If <var>hasOwn</var> is **true**, return **true**.
4. Let <var>parent</var> be <var>O</var>.\[\[GetPrototypeOf\]\]().
5. [ReturnIfAbrupt][](<var>parent</var>).
6. If <var>parent</var> is not **null**, then
  1. return <var>parent</var>.\[\[HasMetadata\]\](<var>MetadataKey</var>, <var>P</var>).
7. Return **false**.

### \[\[HasOwnMetadata\]\] ( MetadataKey, P )

When the \[\[HasOwnMetadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var> and property key <var>P</var>, the following steps are taken:

1. Return OrdinaryHasOwnMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).

### OrdinaryHasOwnMetadata ( MetadataKey, O, P )

When the abstract operation OrdinaryHasOwnMetadata is called with ECMAScript language value <var>MetadataKey</var>, Object <var>O</var>, and property key <var>P</var>, the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>metadataMap</var> be GetOrCreateMetadataMap(<var>O</var>, <var>P</var>, **false**).
3. [ReturnIfAbrupt][](<var>metadataMap</var>).
4. If <var>metadataMap</var> is **undefined**, return **false**.
5. Return [Invoke][](<var>metadataMap</var>, "has", <var>MetadataKey</var>).


### \[\[GetMetadata\]\] ( MetadataKey, P )

When the \[\[GetMatadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var> and property key <var>P</var>, the following steps are taken:

1. Return [OrdinaryGetMetadata](#ordinarygetmetadata--metadatakey-o-p-)(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).

### OrdinaryGetMetadata ( MetadataKey, O, P )

When the abstract operation OrdinaryGetMetadata is called with ECMAScript language value <var>MetadataKey</var>, Object <var>O</var>, and property key <var>P</var>, the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>hasOwn</var> be OrdinaryHasOwnMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).
3. If <var>hasOwn</var> is **true**, then
  1. return OrdinaryGetOwnMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).
4. Let <var>parent</var> be <var>O</var>.\[\[GetPrototypeOf\]\]().
5. [ReturnIfAbrupt][](<var>parent</var>).
6. If <var>parent</var> is not **null**, then
  1. return <var>parent</var>.\[\[GetMetadata\]\](<var>MetadataKey</var>, <var>P</var>).
7. Return **undefined**.

### \[\[GetOwnMetadata\]\] ( MetadataKey, P, ParamIndex )

When the \[\[GetOwnMetadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var> and property key <var>P</var>, the following steps are taken:

1. Return OrdinaryGetOwnMetadata(<var>MetadataKey</var>, <var>O</var>, <var>P</var>).

### OrdinaryGetOwnMetadata ( MetadataKey, O, P )

When the abstract operation OrdinaryGetOwnMetadata is called with ECMAScript language value <var>MetadataKey</var>, Object <var>O</var>, and property key <var>P</var>, the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>metadataMap</var> be GetOrCreateMetadataMap(<var>O</var>, <var>P</var>, **false**).
3. [ReturnIfAbrupt][](<var>metadataMap</var>).
4. If <var>metadataMap</var> is **undefined**, return **undefined**.
5. Return [Invoke][](<var>metadataMap</var>, "get", <var>MetadataKey</var>).

### \[\[DefineOwnMetadata\]\] ( MetadataKey, MetadataValue, P )

When the \[\[DefineOwnMetadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var>, ECMAScript language value <var>MetadataValue</var>, and property key <var>P</var>, the following steps are taken:

1. Return OrdinaryDefineOwnMetadata(<var>MetadataKey</var>, <var>MetadataValue</var>, <var>O</var>, <var>P</var>)

### OrdinaryDefineOwnMetadata ( MetadataKey, MetadataValue, O, P )

When the abstract operation OrdinaryDefineOwnProperty is called with ECMAScript language value <var>MetadataKey</var>, ECMAScript language value <var>MetadataValue</var>, Object <var>O</var>, and property key <var>P</var>, the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>metadataMap</var> be GetOrCreateMetadataMap(<var>O</var>, <var>P</var>, **true**).
3. [ReturnIfAbrupt][](<var>metadataMap</var>).
4. Return [Invoke][](<var>metadataMap</var>, "set", <var>MetadataKey</var>, <var>MetadataValue</var>).

### \[\[MetadataKeys\]\] ( P )

When the \[\[MetadataKeys\]\] internal method of <var>O</var> is called with property key <var>P</var> the following steps are taken:

1. Return OrdinaryMetadataKeys(<var>O</var>, <var>P</var>).

### OrdinaryMetadataKeys ( O, P )

When the abstract operation OrdinaryMetadataKeys is called with Object <var>O</var> and property key <var>P</var> the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>ownKeys</var> be OrdinaryOwnMetadataKeys(<var>O</var>, <var>P</var>).
3. Let <var>parent</var> = <var>O</var>.\[\[GetPrototypeOf\]\]().
4. [ReturnIfAbrupt][](<var>parent</var>).
5. If <var>parent</var> is **null**, then return <var>ownKeys</var>.
6. Let <var>parentKeys</var> be <var>O</var>.\[\[OrdinaryMetadataKeys\]\](<var>P</var>).
7. [ReturnIfAbrupt][](<var>parentKeys</var>).
8. Let <var>ownKeysLen</var> = [Get][](<var>ownKeys</var>, "length").
9. [ReturnIfAbrupt][](<var>ownKeysLen</var>).
10. If <var>ownKeysLen</var> is 0, return <var>parentKeys</var>.
11. Let <var>parentKeysLen</var> = [Get][](<var>parentKeys</var>, "length").
12. [ReturnIfAbrupt][](<var>parentKeysLen</var>).
13. If <var>parentKeysLen</var> is 0, return <var>ownKeys</var>.
14. Let <var>set</var> be a newly created **Set** object.
15. Let <var>keys</var> be [ArrayCreate][](0).
16. Let <var>k</var> be 0.
17. For each element <var>key</var> of <var>ownKeys</var>
  1. Let <var>hasKey</var> be [Invoke][](<var>set</var>, "has", <var>key</var>).
  2. [ReturnIfAbrupt][](<var>hasKey</var>).
  3. If <var>hasKey</var> is **false**, then
    1. Let <var>Pk</var> be [ToString][](<var>k</var>).
    2. Let <var>addStatus</var> be [Invoke][](<var>set</var>, "add", <var>key</var>).
    3. [ReturnIfAbrupt][](<var>addStatus</var>).
    4. Let <var>defineStatus</var> be [CreateDataPropertyOrThrow][](<var>keys</var>, <var>Pk</var>, <var>key</var>).
    5. [ReturnIfAbrupt][](<var>defineStatus</var>).
    6. Increase <var>k</var> by 1.
18. For each element <var>key</var> of <var>parentKeys</var>
  1. Let <var>hasKey</var> be [Invoke][](<var>set</var>, "has", <var>key</var>).
  2. [ReturnIfAbrupt][](<var>hasKey</var>).
  3. If <var>hasKey</var> is **false**, then
    1. Let <var>Pk</var> be [ToString][](<var>k</var>).
    2. Let <var>addStatus</var> be [Invoke][](<var>set</var>, "add", <var>key</var>).
    3. [ReturnIfAbrupt][](<var>addStatus</var>).
    4. Let <var>defineStatus</var> be [CreateDataPropertyOrThrow][](<var>keys</var>, <var>Pk</var>, <var>key</var>).
    5. [ReturnIfAbrupt][](<var>defineStatus</var>).
    6. Increase <var>k</var> by 1.
19. Let <var>setStatus</var> be [Set][](<var>keys</var>, "length", <var>k</var>).
20. [ReturnIfAbrupt][](<var>setStatus</var>).
21. return <var>keys</var>.

### \[\[OwnMetadataKeys\]\] ( P )

When the \[\[OwnMetadataKeys\]\] internal method of <var>O</var> is called with property key <var>P</var> the following steps are taken:

1. Return OrdinaryOwnMetadataKeys(<var>O</var>, <var>P</var>).

### OrdinaryOwnMetadataKeys ( O, P )

When the abstract operation OrdinaryOwnMetadataKeys is called with Object <var>O</var> and property key <var>P</var> the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>keys</var> be [ArrayCreate][](0).
3. Let <var>metadataMap</var> be GetOrCreateMetadataMap(<var>O</var>, <var>P</var>, **false**).
4. [ReturnIfAbrupt][](<var>metadataMap</var>).
5. If <var>metadataMap</var> is **undefined**, return <var>keys</var>.
6. Let <var>keysObj</var> be [Invoke][](<var>metadataMap</var>, "keys").
7. [ReturnIfAbrupt][](<var>keysObj</var>).
8. Let <var>iterator</var> be [GetIterator][](<var>keysObj</var>).
9. [ReturnIfAbrupt][](<var>iterator</var>).
10. Let <var>k</var> be 0.
11. Repeat
  1. Let <var>Pk</var> be [ToString][](k).
  2. Let <var>next</var> be [IteratorStep][](<var>iterator</var>).
  3. [ReturnIfAbrupt][](<var>next</var>).
  4. If <var>next</var> is **false**, then
    1. Let <var>setStatus</var> be [Set][](<var>keys</var>, "length", <var>k</var>, <var>true</var>).
    2. [ReturnIfAbrupt][](<var>setStatus</var>).
    3. Return <var>keys</var>.
  5. Let <var>nextValue</var> be [IteratorValue][](<var>next</var>).
  6. [ReturnIfAbrupt][](<var>nextValue</var>).
  7. Let <var>defineStatus</var> be [CreateDataPropertyOrThrow][](<var>keys</var>, <var>Pk</var>, <var>nextValue</var>).
  8. If <var>defineStatus</var> is an abrupt completion, return [IteratorClose][](<var>iterator</var>, <var>defineStatus</var>).
  9. Increase <var>k</var> by 1.

### \[\[DeleteMetadata\]\]( MetadataKey, P )

When the \[\[DeleteMetadata\]\] internal method of <var>O</var> is called with ECMAScript language value <var>MetadataKey</var> and property key <var>P</var> the following steps are taken:

1. [Assert][]: <var>P</var> is **undefined** or [IsPropertyKey][](<var>P</var>) is **true**.
2. Let <var>metadataMap</var> be GetOrCreateMetadataMap(<var>O</var>, <var>P</var>, **false**).
3. [ReturnIfAbrupt][](<var>metadataMap</var>).
4. If <var>metadataMap</var> is **undefined**, return **false**.
5. Return [Invoke][](<var>metadataMap</var>, "delete", <var>MetadataKey</var>).

[ArrayCreate]:                https://people.mozilla.org/~jorendorff/es6-draft.html#sec-arraycreate                       "ArrayCreate"
[Assert]:                     https://people.mozilla.org/~jorendorff/es6-draft.html#sec-algorithm-conventions             "Assert"
[CreateDataPropertyOrThrow]:  https://people.mozilla.org/~jorendorff/es6-draft.html#sec-createdatapropertyorthrow         "CreateDataPropertyOrThrow"
[Get]:                        https://people.mozilla.org/~jorendorff/es6-draft.html#sec-get-o-p                           "Get"
[Set]:                        https://people.mozilla.org/~jorendorff/es6-draft.html#sec-set-o-p-v-throw                   "Set"
[GetIterator]:                https://people.mozilla.org/~jorendorff/es6-draft.html#sec-getiterator                       "GetIterator"
[Invoke]:                     https://people.mozilla.org/~jorendorff/es6-draft.html#sec-invoke                            "Invoke"
[IsPropertyKey]:              https://people.mozilla.org/~jorendorff/es6-draft.html#sec-ispropertykey                     "IsPropertyKey"
[IteratorStep]:               https://people.mozilla.org/~jorendorff/es6-draft.html#sec-iteratorstep                      "IteratorStep"
[IteratorClose]:              https://people.mozilla.org/~jorendorff/es6-draft.html#sec-iteratorclose                     "IteratorClose"
[IteratorValue]:              https://people.mozilla.org/~jorendorff/es6-draft.html#sec-iteratorvalue                     "IteratorValue"
[ReturnIfAbrupt]:             https://people.mozilla.org/~jorendorff/es6-draft.html#sec-returnifabrupt                    "ReturnIfAbrupt"
[ToString]:                   https://people.mozilla.org/~jorendorff/es6-draft.html#sec-tostring                          "ToString"
[Type]:                       https://people.mozilla.org/~jorendorff/es6-draft.html#sec-ecmascript-data-types-and-values  "Type"
