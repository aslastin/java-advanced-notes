# Lambdas / Streams

`@FunctionalInterface` - правило хорошего тона, при помощи аннотации позволяет контролировать добавление лишних методов.

```java
filter((String::isEmpty).negate()) // не скомпилируется (не выведет типы)

// vs

filter(Predicate.not(String::isEmpty)) // скомпилируется
```

`.forEach()`

```java
...forEach(e -> {
    if (...) {
        //нельзя выйти из forEach
    }
})
```
* Даже если заключить `forEach` в функцию и вызвать `return`, он все равно отработает до конца
