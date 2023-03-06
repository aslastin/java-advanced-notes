# Collections

`+T` <-> `? extends T`
`-T` <-> `? super T`

__В java бросить исключение - дорого, поймать - весьма дешево (почему???).__

## Collection

В java `iterator` указывает между элементами. `iterator.remove` - удаляет последний элемент, который вернул `iterator.next`.

Коллекции не обязаны бросить `ConcurrentModificationException`.

Расширенный `for` работает и для массивов (проходится по массиву целиком).

В `.toArray` принято передавать либо массив нулевой длины, либо массив, который вместит все элементы. Элементы в результате вызова `.toArray` будут в порядке обхода итератора.

`UnsupportedOperationException` на практике бросается в изменяемых операциях.

`null instanceof T` -> всегда возвращает `false`.

`containsAll(Collection<?> c)` может бросить `ClassCastException`, если тип какого-о элемента `c` не согласован с типом текущей коллекции.

`Collections.reverse(list<?>)` учитывает интерфейс `RandomAccess`.

## Set

`Set.of` кладет элементы в случайном порядке.

Если `Set.copyOf(коллекции, где есть null)` -> кинет `NullPointerExcepiton`.

## List / Queue

`add(index, element)` - вставляет перед указанным индексом.

`List<E> subList(from, to)` - `from` включительно, `to` исключительно.

Вместо `LinkedList` стоит использовать любую другую коллекцию на основе массива (`ArrayList`, `ArrayDeque`), так как он потребляет много памяти.

Очереди не обязаны гарантировать порядок на `peek` с обходом итератора (например, это не эффективно для упорядоченных множеств).

## Map

`keySet / entrySet` работают за O(1).

Под капотом `HashSet` лежит `HashMap`???.

`Map.of` рандомизирует порядок.

Не надо полагаться, что `compareTo` возвращает -1 / 1 - просто какие-то отрицательные / положительные числа.

Передаем `Comparator`, иначе подразумеваем, что реализует интерфейс `Comparable`, иначе валидно бросить `ClassCastException`.

Согласованность компараторов с `equals` нужна при перекладывании из одной коллекции в другую.

## Enumeration

В `Enumeration` есть все то же самое, что и в `Iterator` - сделали из-за сокращения кода (на суффиксе был лишним Element).

## Collection.size()

Если размер коллекции превышает максимальное значение `int`, `size()` вернет `Integer.MAX_VALUE`. 

Почему не `long`? Из-за обратной совместимости. Раньше не особо задумывались, что размер коллекции может превысить максимальный размер `int`, так так такой памятью не располагали.

Однако есть еще одна тонкость касательно размера коллекции, если у нее под капотом скрывается джавовый массив. Пусть мы хотим создать массив: `objs = new Object[n]`. Тогда возможно следующее поведение в зависимости от `n`:

- `n < 0` -> `java.lang.NegativeArraySizeException`
- `n` имеет тип отличный от `int` -> error: incompatible types: possible lossy conversion from `<type>` to `int`
- `n` превышает лимит VM[^1] -> `java.lang.OutOfMemoryError: Requested array size exceeds VM limit`
- Под `n` не хватило памяти -> `java.lang.OutOfMemoryError: Java heap space`

[^1]: Some JVMs reserve a small part of the array for header information, and bail out with an `OutOfMemoryError` for array sizes that are close to the theoretical limit. In the HotSpot VM For example, the [max_array_length](https://github.com/openjdk/jdk14u/blob/84917a040a81af2863fddc6eace3dda3e31bf4b5/src/hotspot/share/oops/arrayOop.hpp#L132) function states the following:

	```java
	// It should be ok to return max_jint here, but parts of the code
	// (CollectedHeap, Klass::oop_oop_iterate(), and more) uses an int for
	// passing around the size (in words) of an object. So, we need to avoid
	// overflowing an int when we add the header. See CRs 4718400 and 7110613.
	return align_down(max_jint - header_size(type), MinObjAlignment);
	```

	This reduces the effective limit to `Integer.MAX_INT - 5`.

	You don't always know what JVM your code will be running on, so to be safe you can follow the approach taken in the core libraries and give a little bit more leeway for the JVM. From the [ArraysSupport](https://github.com/openjdk/jdk14u/blob/84917a040a81af2863fddc6eace3dda3e31bf4b5/src/java.base/share/classes/jdk/internal/util/ArraysSupport.java#L577) class:

	```java
	/**
	 * The maximum length of array to allocate (unless necessary).
	 * Some VMs reserve some header words in an array.
	 * Attempts to allocate larger arrays may result in
	 * {@code OutOfMemoryError: Requested array size exceeds VM limit}
	 */
	public static final int MAX_ARRAY_LENGTH = Integer.MAX_VALUE - 8;
	```
