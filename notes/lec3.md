# Lambdas / Streams

`@FunctionalInterface` - правило хорошего тона, при помощи аннотации позволяет контролировать добавление лишних методов.

```java
filter((String::isEmpty).negate()) // не скомпилируется (не выведет типы)

// vs

filter(Predicate.not(String::isEmpty)) // скомпилируется
```
