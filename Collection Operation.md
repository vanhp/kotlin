# <span style="color:lightblue">Collection Operation</span>

Operation on Collection are define in 2 ways
1. As $\color{orange}{member~functions}$ of the collection interface
2. As $\color{orange}{extension~functions}$
   
## <span style="color:forestgreen">Member functions </span>
Define operations as member functions imply that it's essential for a collection type. The [Collection](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/) interface define various operation e.g. $\color{orange}{isEmpty()}$, $\color{orange}{contains()}$,$\color{orange}{containAll()}$... that are relevant to the collection.

### <span style="color:aquamarine">Collection type</span>
There are 2 types of collections
1. $\color{orange}{Immutable~collection}$
   This [Collection](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/) is $\color{orange}{read~only}$ also $\color{orange}{covariant}$ for item that contain inside it.
2. $\color{orange}{Mutable~collection}$
  [MutableCollection](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-collection/#kotlin.collections.MutableCollection) is  $\color{orange}{read-write}$ and $\color{orange}{invariant}$ is item type that it contain.

## <span style="color:forestgreen">Extension functions</span>

These functions are consider convenient operation e.g. filter,transform,ordering...

### <span style="color:aquamarine">Common operation</span>
These functions are most frequent use so they are defined for both mutable and immutable collections.
- Transformation
- Filtering
- plus and minus
- Grouping
- retrieving single item
- retrieving collection part
- ordering
- Aggregation

These functions do not change the original data since they create a new copy for the result and return it to caller.

#### <span style="color:coral">Caller does not provide storage </span>
These functions will create and return new object to caller since caller does not provide one. They don't have the To in their name e.g. filter(),map()

```kotlin
  val numbers = listOf("one", "two", "three", "four")  
  numbers.filter { it.length > 3 }  // nothing happens with `numbers`, result is lost
  println("numbers are still $numbers")
  val longerThan3 = numbers.filter { it.length > 3 } // result is stored in `longerThan3`
  println("numbers longer than 3 chars are $longerThan3")
```
#### <span style="color:coral">Caller provide storage</span>

an option to specify the destination object. Destination is a mutable collection to which the function appends its resulting items instead of returning them in a new object. These functons have To in their name e.g.
[filterTo](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-to.html), associateTo()... they take destination collection as parameters

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  val filterResults = mutableListOf<String>()  //destination object
  numbers.filterTo(filterResults) { it.length > 3 }
  numbers.filterIndexedTo(filterResults) { index, _ -> index == 0 }
  println(filterResults) // contains results of both operations
```
They can also return the destination object 
```kotlin
  // filter numbers right into a new hash set, 
  // thus eliminating duplicates in the result
  val result = numbers.mapTo(HashSet()) { it.length }
  println("distinct item lengths are $result")
```

#### <span style="color:coral">Mutation operations</span>
Some operations may come in $\color{orange}{pair}$ 
1. one applies the operation in-place and 
2. the other returns the result as a separate collection. 

For example, [sort()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html) sorts a mutable collection in-place, so its state changes; [sorted()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) creates a new collection that contains the same elements in the sorted order.

```kotlin
  val numbers = mutableListOf("one", "two", "three", "four")
  val sortedNumbers = numbers.sorted()
  println(numbers == sortedNumbers)  // false
  numbers.sort()
  println(numbers == sortedNumbers)  // true
```



## <span style="color:forestgreen">Transformation Operations</span>

These operations are provided as extension functions. These functions are $\color{orange}{non~destructive}$ since they leave the original collection alone and they build new collections from existing ones based on the transformation rules provided.

### <span style="color:aquamarine">Map (dictionary)</spam>

$\color{orange}{Map}$ creates a new collection from the results of a function operation on the elements of the original collection. 
[map()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map.html) takes a lambda as argument and applies it to each subsequent element then returns the result list.
The order of results is the same as the original order of elements.

#### <span style="color:coral">Transform by index</span>

Additionally,[mapIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed.html) takes index as a argument.

```kotlin
  val numbers = setOf(1, 2, 3)
  println(numbers.map { it * 3 })
  println(numbers.mapIndexed { idx, value -> value * idx })
```
#### <span style="color:coral">Filter out null value</span>

If the transformation produces null on any element, you can filter out the null from the result collection by calling the [mapNotNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-not-null.html) function or [mapIndexedNotNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-indexed-not-null.html)

```kotlin
  val numbers = setOf(1, 2, 3)
  println(numbers.mapNotNull { if ( it == 2) null else it * 3 })
  println(numbers.mapIndexedNotNull { idx, value -> if (idx == 0) null else value * idx })
```
#### <span style="color:coral">Transform key or value</span>

 Either the keys or value may be transformed leaving the other unchanged and vice versa. To transformation:
 - use [mapKeys()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-keys.html) for transform key or 
 - [mapValues()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/map-values.html) to transforms values. 
 - Both functions take a map entry as an argument, to allow operation on both its key and value.

```kotlin
  val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
  println(numbersMap.mapKeys { it.key.uppercase() })
  println(numbersMap.mapValues { it.value + it.key.length })
```

### <span style="color:aquamarine">Zip</span>
To create a new collection from 2 (array) collections by pairing of items with same position use [zip()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip.html) it return a list of Paired object. 

If the collections have different sizes, the result of the $\color{orange}{zip()}$ is the smaller size; the last elements of the larger collection are not included in the result. it can also be called in the infix form a zip b.

```kotlin
  val colors = listOf("red", "brown", "grey")
  val animals = listOf("fox", "bear", "wolf")
  println(colors zip animals)

  val twoAnimals = listOf("fox", "bear")
  println(colors.zip(twoAnimals))
```
#### <span style="color:coral">With transformation function</span>

A transformation function may be provided that takes 2 parameters
the receiver element and the argument element. The result List contains the return values of the transformation function.

```kotlin
  val colors = listOf("red", "brown", "grey")
  val animals = listOf("fox", "bear", "wolf")

  println(colors.zip(animals) { color, animal -> "The ${animal.replaceFirstChar { it.uppercase() }} is $color"})
```

### <span style="color:aquamarine">Unzip</span>

Allow for reverse of previously zipped transform. It will return 2 lists
- The first list contains the first elements of each Pair in the original list.
- The second list contains the second elements.

To unzip a list of pairs, call [unzip()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/unzip.html).

```kotlin
  val numberPairs = listOf("one" to 1, "two" to 2, "three" to 3, "four" to 4)
  println(numberPairs.unzip())
```

### <span style="color:aquamarine">Association </span>

Allow building maps from the collection elements where the elements can be used as either keys or values in the association map. Inaddition, a lambda may be provide to generate the key-value.

#### <span style="color:coral">Map from Collection (List) as key</span>

The [associateWith()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-with.html) creates a Map by using the elements of the original collection as $\color{orange}{keys}$, the values are produced from them by the given transformation function. If two elements are equal, only the last one remains in the map.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  println(numbers.associateWith { it.length })
```
#### <span style="color:coral">Map from Collection (List) as value</span>

To build map from collection elements as $\color{orange}{values}$, use [associateBy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate-by.html).

It takes a function that returns a key based on an element's value. If two elements are equal, only the last one remains in the map.
$\color{orange}{associateBy()}$ can also be called with a value transformation function.

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  println(numbers.associateBy { it.first().uppercaseChar() })
  println(numbers.associateBy(keySelector = { it.first().uppercaseChar() }, valueTransform = { it.length }))
```
#### <span style="color:coral">With lambda to generate key-value</span>

to build maps in which both keys and values are somehow produced from collection elements is the function [associate()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/associate.html). It takes a lambda function that returns a $\color{orange}{Pair}$: the key and the value of the corresponding map entry.
Note that associate() produces short-live Pair objects which may affect the performance.

```kotlin
  val names = listOf("Alice Adams", "Brian Brown", "Clara Campbell")
  println(names.associate { name -> parseFullName(name).let { it.lastName to it.firstName } }) 
```
Here calling a transform function on an element first, and then build a pair from the properties of that function's result.

### <span style="color:aquamarine">Flatten Nested Collection</span>

These functions allow for transforming a nested collection into a single flatten $\color{orange}{list collection.

1. The [flatten()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flatten.html). Can be called on a collection of collections.

The function returns a single List of all the elements of the nested collections.

```kotlin
  val numberSets = listOf(setOf(1, 2, 3), setOf(4, 5, 6), setOf(1, 2))
  println(numberSets.flatten())
```

2. [flatMap()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/flat-map.html) provides a flexible way to process nested collections. 

It takes a function that maps a collection element to another collection. As a result, $\color{orange}{flatMap()}$ returns a single list of its return values on all the elements. 

So, $\color{orange}{flatMap()}$ behaves as a subsequent call of $\color{orange}{map()}$ (with a collection as a mapping result) and $\color{orange}{flatten()}$.

```kotlin
  val containers = listOf(
      StringContainer(listOf("one", "two", "three")),
      StringContainer(listOf("four", "five", "six")),
      StringContainer(listOf("seven", "eight"))
  )
  println(containers.flatMap { it.values })
```

### <span style="color:aquamarine">Collection content as String</span>

To retrieve the collection content in a readable format, use functions that transform the collections to strings: [joinToString()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to-string.html) and [joinTo()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/join-to.html).

- $\color{orange}{joinToString()}$ builds a single String from the collection elements based on the provided arguments. 
- $\color{orange}{joinTo()}$ does the same but appends the result to the given [Appendable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/-appendable/) object.
- When called with the $\color{orange}{default~arguments}$, the functions return the result similar to calling $\color{orange}{toString()}$ on the collection: a String of elements' string representations separated by commas with spaces.

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  println(numbers)         
  println("as string: ${numbers.joinToString()}")

  val listString = StringBuffer("The list of numbers: ")
  numbers.joinTo(listString)
  println(listString)
  ```
#### <span style="color:coral">Custom string representation</span>

To create custom string specify its parameters in function arguments  $\color{orange}{separator}$,  $\color{orange}{prefix}$, and  $\color{orange}{postfix}$. The resulting string will start with the  $\color{orange}{prefix}$ and end with the  $\color{orange}{postfix}$. The  $\color{orange}{separator}$ will come after each element except the last.

```kotlin
  val numbers = listOf("one", "two", "three", "four")    
  println(numbers.joinToString(separator = " | ", prefix = "start: ", postfix = ": end"))
  ```

#### <span style="color:coral">Limit size</span>

to specify the  $\color{orange}{limit}$ for bigger collection – a number of elements that will be included into result. If the collection size exceeds the  $\color{orange}{limit}$, all the other elements will be replaced with a single value of the truncated argument.

```kotlin
  val numbers = (1..100).toList()
  println(numbers.joinToString(limit = 10, truncated = "<...>"))
```

#### <span style="color:coral">Customize with lambda</span>
to customize the representation of elements themselves, provide the transform function.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  println(numbers.joinToString { "Element: ${it.uppercase()}"})
```

## <span style="color:forestgreen">Filtering</span>
One of the most use task for collection processing. Filtering conditions are defined by $\color{orange}{predicates}$ - lambda functions that take a collection element and return a boolean value: $\color{orange}{true}$ means that the given element matches the predicate, $\color{orange}{false}$ means the opposite.

These functions come as extension functions, they leave the original collection unchanged, so they are available for both $\color{orange}{mutable}$ and $\color{orange}{immutable}$ collections. To operate the filtering result, you should assign it to a $\color{orange}{variable}$ or $\color{orange}{chain}$ the functions after filtering.

### <span style="color:aquamarine">Filter by predicate﻿</span>

The [filter()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html). When called with a $\color{orange}{predicate}$, $\color{orange}{filter()}$ returns the collection elements that match it. 
- For both $\color{orange}{List}$ and $\color{orange}{Set}$, the resulting collection is a $\color{orange}{List}$, 
- for $\color{orange}{Map}$ it's a $\color{orange}{Map}$ as well.
  
```kotlin
  val numbers = listOf("one", "two", "three", "four")  
  val longerThan3 = numbers.filter { it.length > 3 }
  println(longerThan3)

  val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
  val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
  println(filteredMap)
```

#### <span style="color:coral">Filter by value</span>

The predicates in $\color{orange}{filter()}$ can only check the values of the elements.

#### <span style="color:coral">Filter by index</span>
to use element positions in the filter, use [filterIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-indexed.html)

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  val filteredIdx = numbers.filterIndexed { index, s -> (index != 0) && (s.length < 5)  }
  val filteredNot = numbers.filterNot { it.length <= 3 }

  println(filteredIdx)
  println(filteredNot)
```

#### <span style="color:coral">Filter by type</span>

These functions narrow the element type by filtering only elements of a given type and skip other types in the collection:

- [filterIsInstance()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-is-instance.html) returns collection elements of a given type e.g. $\color{orange}{List<Any}$>, $\color{orange}{filterIsInstance<T>()}$ returns a $\color{orange}{List<T>}$ only, thus allowing you to call functions of the $\color{orange}{T}$ type on its items.

```kotlin
  val numbers = listOf(null, 1, "two", 3.0, "four")
  println("return only String elements in upper case:")
  numbers.filterIsInstance<String>().forEach {
      println(it.uppercase())
  }
```
#### <span style="color:coral">Filter by null</span>

[filterNotNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-not-null.html) returns all non-null elements. When called on a $\color{orange}{List<T?>}$, $\color{orange}{filterNotNull()}$ returns a $\color{orange}{List<T: Any>}$, thus allowing you to treat the elements as $\color{orange}{non-null}$ objects.

```kotlin
  val numbers = listOf(null, "one", "two", null)
  numbers.filterNotNull().forEach {
      println(it.length)   // length is unavailable for nullable Strings
  }
```

#### <span style="color:coral">Partition</span>

It filters a collection by a $\color{orange}{predicate}$ and keeps the elements that don't match it in a separate list. It return 2 lists 
- the first list containing elements that $\color{orange}{match}$ the predicate and 
- the second one containing $\color{orange}{everything~else}$ from the original collection.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  val (match, rest) = numbers.partition { it.length > 3 }

  println(match)
  println(rest)
```
#### <span style="color:coral">Test predicates</span>

These functions simply test a $\color{orange}{predicate}$ against collection elements:
- [any()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/any.html) returns $\color{orange}{true}$ if at $\color{orange}{least~one}$ element matches the given predicate.
- [none()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) returns $\color{orange}{true}$ if $\color{orange}{none}$ of the elements match the given predicate.
- [all()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/none.html) returns $\color{orange}{true}$ if $\color{orange}{all}$ elements match the given predicate. Note that all() returns $\color{orange}{true}$ when called with any valid predicate on an $\color{orange}{empty}$ collection. Such behavior is known in logic as vacuous [truth](https://en.wikipedia.org/wiki/Vacuous_truth).

```kotlin
val numbers = listOf("one", "two", "three", "four")

println(numbers.any { it.endsWith("e") })
println(numbers.none { it.endsWith("a") })
println(numbers.all { it.endsWith("e") })

println(emptyList<Int>().all { it > 5 })   // vacuous truth
```
#### <span style="color:coral">Chcck for Empty Collection</span>

$\color{orange}{any()}$ and $\color{orange}{none()}$ can also be used without a $\color{orange}{predicate}$: in this case they just check the collection emptiness. 
- $\color{orange}{any()}$ returns $\color{orange}{true}$ if there are elements and $\color{orange}{false}$ if there aren't; 
- $\color{orange}{none()}$ does the $\color{orange}{opposite}$.

```kotlin
val numbers = listOf("one", "two", "three", "four")
val empty = emptyList<String>()

println(numbers.any())
println(empty.any())

println(numbers.none())
println(empty.none())
```

## <span style="color:forestgreen">Plus and Minus Operators</span>

Collection also work with  [plus (+)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) and [minus (-)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html)operators.  They take a $\color{orange}{collection}$ as the $\color{orange}{first~operand}$; the $\color{orange}{second~operand}$ can be either an $\color{orange}{element~or~collection}$. The return value is a $\color{orange}{new~immutable~ collection}$:

- The result of $\color{orange}{plus}$ contains the elements from the original collection and from the second operand.

- The result of $\color{orange}{minus}$ contains the elements of the original collection except the elements from the second operand. If it's an element, minus removes its first occurrence; if it's a collection, all occurrences of its elements are removed.

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  val plusList = numbers + "five"
  val minusList = numbers - listOf("three", "four")
  println(plusList)
  println(minusList)
```

#### <span style="color:coral">The augmented assignment operators</span>
The plusAssign[(+=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) and the minusAssign[(-=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html)

also defined to work with collections. However, 
- for $\color{orange}{immutable}$ collections, they actually use the plus or minus operators and try to assign the result to the same variable. Thus, they are available only on var read-only collections. 
- For $\color{orange}{mutable}$ collections, they modify the collection if it's a val. For more details see Collection write operations.

## <span style="color:forestgreen">Grouping</span>

 Extension functions for grouping collection elements. The function [groupBy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html)  call with:
 - a $\color{orange}{lambda}$ function and returns a $\color{orange}{Map}$. In this map, each key is the lambda result and the corresponding value is the List of elements on which this result is returned. This function can be used, for example, to group a list of String s by their first letter.

 - with a $\color{orange}{second~lambda}$ argument – a $\color{orange}{value~transformation~unction}$. In the result map of $\color{orange}{groupBy()}$ with two lambdas, the keys produced by $\color{orange}{keySelector~function}$ are mapped to the results of the value transformation function instead of the original elements.

```kotlin
 val numbers = listOf("one", "two", "three", "four", "five")

  println(numbers.groupBy { it.first().uppercase() })
  println(numbers.groupBy(keySelector = { it.first() }, valueTransform = { it.uppercase() }))
```

## <span style="color:forestgreen">Retrieve collection parts</span>

extension functions for retrieving parts of a collection. These functions provide a variety of ways to select elements for the result collection: listing their positions explicitly, specifying the result size, and others.

#### <span style="color:coral">Slice</span>

Work with range to indicate the position to start and end the slicing operation.
The [slice()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/slice.html) returns a list of the collection elements with given indices. The indices may be passed either as a range or as a collection of integer values.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")    
  println(numbers.slice(1..3))
  println(numbers.slice(0..4 step 2))
  println(numbers.slice(setOf(3, 5, 0)))   

   [two, three, four]
[one, three, five]
[four, six, one]
```

#### <span style="color:coral">Take and drop﻿</span>

$\color{yellow}{With~numbers}$
Use number to indicate how many elements to take.

- To get the $\color{orange}{specified~number}$ of elements starting from the first, use the [take()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take.html) function. 
- For getting the last elements, use [takeLast()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last.html). 

When called with a number $\color{orange}{larger}$ than the collection size, both functions return the whole collection.

- To take $\color{orange}{all}$ the elements $\color{orange}{except}$ a given number of first or last elements, call the [drop()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop.html) and [dropLast()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last.html) functions respectively.
  
```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.take(3))
  println(numbers.takeLast(3))
  println(numbers.drop(1))
  println(numbers.dropLast(5))

[one, two, three]
[four, five, six]
[two, three, four, five, six]
[one]
```


$\color{yellow}{With~predicate}$

$\color{orange}{Predicates}$ may be use to define the number of elements to take or drop. There are 4 functions:

1. [takeWhile()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-while.html) is take() with a $\color{orange}{predicate}$: 
   - it takes the elements $\color{orange}{up~to}$ but $\color{orange}{excluding}$ the $\color{orange}{first}$ one not matching the $\color{orange}{predicate}$. 
   - If the $\color{orange}{first}$ collection element $\color{orange}{doesn't~match}$ the $\color{orange}{predicate}$, the result is $\color{orange}{empty}$.

2. [takeLastWhile()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/take-last-while.html) is similar to $\color{orange}{takeLast()}$: it takes the $\color{orange}{range}$ of elements $\color{orange}{matching}$ the $\color{orange}{predicate}$ from the $\color{orange}{end}$ of the collection. 
   - The $\color{orange}{first}$ element of the range is the element next to the $\color{orange}{last}$ element not matching the predicate. 
   - If the $\color{orange}{last}$ collection element $\color{orange}{doesn't ~match}$ the $\color{orange}{predicate}$, the result is $\color{orange}{empty}$;

3. [dropWhile()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-while.html) is the $\color{orange}{opposite}$ to $\color{orange}{takeWhile()}$ with the same $\color{orange}{predicate}$: it returns the elements from the $\color{orange}{first}$ one $\color{orange}{not~matching}$ the $\color{orange}{predicate}$ to the $\color{orange}{end}$.

4. [dropLastWhile()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/drop-last-while.html) is the $\color{orange}{opposite}$ to $\color{orange}{takeLastWhile()}$ with the same $\color{orange}{predicate}$: it returns the elements from the $\color{orange}{beginning}$ to the $\color{orange}{last}$ one not matching the $\color{orange}{predicate}$.
  
```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.takeWhile { !it.startsWith('f') })
  println(numbers.takeLastWhile { it != "three" })
  println(numbers.dropWhile { it.length == 3 })
  println(numbers.dropLastWhile { it.contains('i') })

[one, two, three]
[four, five, six]
[three, four, five, six]
[one, two, three, four]
```

### <span style="color:aquamarine">Chunking</span>

To $\color{orange}{break}$ a collection into parts of a given size, use the [chunked()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/chunked.html) function. $\color{orange}{chunked()}$ takes a single argument: the $\color{orange}{size}$ of the chunk and returns a $\color{orange}{List~of ~List}$ s of the given size. 
- The $\color{orange}{first}$ chunk starts from the $\color{orange}{first}$ element and contains the size elements, 
- the $\color{orange}{second}$ chunk holds the $\color{orange}{next}$ size elements, and so on. 
- The $\color{orange}{last}$ chunk may have a $\color{orange}{smaller}$ size.

```kotlin
  val numbers = (0..13).toList()
  println(numbers.chunked(3))

  [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9, 10, 11], [12, 13]]
```

### <span style="color:aquamarine">Windowed</span>

To retrieve $\color{orange}{all~possible~ranges}$ of the collection elements of a given size. use [windowed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/windowed.html): 
- It take a number to indicate $\color{orange}{size}$ of window (number of items in the group)
- it returns a $\color{orange}{list}$ of element ranges as sliding window of the given size.
- Unlike $\color{orange}{chunked()}$, $\color{orange}{windowed()}$ returns element ranges group as windows starting from each collection element. All the windows are returned as elements of a $\color{orange}{single~List}$.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five")    
  println(numbers.windowed(3))

[[one, two, three], [two, three, four], [three, four, five]]
```

$\color{yellow}{with~optional~parameters}$:
- $\color{orange}{step}$ defines a distance between first elements of two adjacent windows. 
- By default the value is 1, 
  - so the result contains windows starting from all elements. 
  - If you increase the step to 2, you will receive only windows starting from odd elements: first, third, and so on.

- $\color{orange}{partialWindows}$ allow for windows of smaller sizes incase the last windows doesn't have enough items to fill the window.
  

Finally, you can apply a transformation to the returned ranges right away. To do this, provide the transformation as a lambda function when calling windowed().

```kotlin
  val numbers = (1..10).toList()
  println(numbers.windowed(3, step = 2, partialWindows = true))
  println(numbers.windowed(3) { it.sum() })

[[1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9], [9, 10]]
[6, 9, 12, 15, 18, 21, 24, 27]
```
$\color{yellow}{Pair~of~window}$

To build $\color{orange}{two-element}$ windows, use [zipWithNext()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/zip-with-next.html). It creates pairs of adjacent elements of the receiver collection. $\color{orange}{zipWithNext()}$ doesn't break the collection into pairs; it creates a $\color{orange}{new~Pair}$ for each element except the last one,e.g. 

the result of [1, 2, 3, 4] is [[1, 2], [2, 3], [3, 4]], not [[1, 2 ], [3, 4]]. 

$\color{orange}{zipWithNext()}$ can be called with a transformation function as well; it should $\color{orange}{take~two}$ elements of the receiver collection as arguments.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five")    
  println(numbers.zipWithNext())
  println(numbers.zipWithNext() { s1, s2 -> s1.length > s2.length})

[(one, two), (two, three), (three, four), (four, five)]
[false, false, true, false]
```

## <span style="color:forestgreen">Retrieve single elements</span>

Retrieving single elements from collections e.g. $\color{orange}{List}$ or $\color{orange}{Set}$

list is an $\color{orange}{ordered}$ collection. Hence, every element of a list has its $\color{orange}{position}$ that you can use for referring. $\color{orange}{Lists}$ offer a wider set of ways to retrieve and search for elements by indices.

$\color{orange}{Set}$ is not an ordered collection by definition but in Kotlin Set stores elements in certain $\color{orange}{orders}$. 

These can be the order of $\color{orange}{insertion}$ as in $\color{orange}{inkedHashSet}$, $\color{orange}{natural}$ sorting order as in $\color{orange}{SortedSet}$, another order or $\color{orange}{unknown}$ order but the elements are still $\color{orange}{ordered}$ somehow in Kotlin, so the functions that rely on the element positions still return their results. 

But it's $\color{orange}{unpredictable}$ to the caller unless they know the specific implementation of $\color{orange}{Set}$ used.

### <span style="color:aquamarine">Retrieve by position﻿</span>

For retrieving an element at a specific $\color{orange}{position}$, there is the function [elementAt()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at.html). it takes $\color{orange}{integer}$ indicate $\color{orange}{position}$ as an argument, it's return $\color{orange}{element}$ at the given $\color{orange}{position}$. The first element has the $\color{orange}{position~0}$, and the $\color{orange}{last}$ one is (size - 1).

$\color{orange}{elementAt()}$ is useful for collections that $\color{orange}{do~not}$ provide $\color{orange}{indexed}$ access, or are not statically known to provide one. In case of $\color{orange}{List}$, it's more idiomatic to use $\color{orange}{indexed}$ access operator (get() or [] ).

```kotlin
  val numbers = linkedSetOf("one", "two", "three", "four", "five")
  println(numbers.elementAt(3))    

  val numbersSortedSet = sortedSetOf("one", "two", "three", "four")
  println(numbersSortedSet.elementAt(0)) // elements are stored in the ascending order

four
four
```
$\color{yellow}{Optional}$:

For retrieving the first and the last element of the collection: [first()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) and [last()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html).

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five")
  println(numbers.first())    
  println(numbers.last())    

one
five
```

#### <span style="color:coral">Avoiding exceptions </span>

when retrieving element with $\color{orange}{non-existing}$ positions, use safe variations of $\color{orange}{elementAt()}$:

- [elementAtOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-null.html) returns $\color{orange}{null}$ when the specified position is out of the collection bounds.

- [elementAtOrElse()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/element-at-or-else.html) additionally takes a lambda function that maps an $\color{orange}{Int}$ argument to an $\color{orange}{instance~of}$ the collection element type. When called with an $\color{orange}{out-of-bounds position}$, the $\color{orange}{elementAtOrElse()}$ returns the $\color{orange}{result}$ of the lambda on the given value.
  
```kotlin
  val numbers = listOf("one", "two", "three", "four", "five")
  println(numbers.elementAtOrNull(5))
  println(numbers.elementAtOrElse(5) { index -> "The value for index $index is undefined"})

null
The value for index 5 is undefined
```


### <span style="color:aquamarine">Retrieve by condition</span>

Functions [first()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first.html) and [last()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last.html) also let you search a collection for elements matching a given $\color{orange}{predicate}$. When you call $\color{orange}{first()}$ with a $\color{orange}{predicate}$ that tests a collection element, you'll receive the $\color{orange}{first}$ element on which the $\color{orange}{predicate}$ yields $\color{orange}{true}$. 

In turn, $\color{orange}{last()}$ with a $\color{orange}{predicate}$ returns the $\color{orange}{last}$ element matching it.
If $\color{orange}{no~elements}$ match the $\color{orange}{predicate}$, both functions $\color{orange}{throw~exceptions}$.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.first { it.length > 3 })
  println(numbers.last { it.startsWith("f") })

three
five
```

#### <span style="color:coral">Avoiding exceptions </span>

use [firstOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-or-null.html) and [lastOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-or-null.html) instead: they return null if no matching elements are found.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.firstOrNull { it.length > 6 })

null
```
Use the $\color{orange}{aliases}$ if their names suit your situation better:

- [find()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find.html) instead of $\color{orange}{firstOrNull()}$
- [findLast()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/find-last.html) instead of $\color{orange}{lastOrNull()}$

```kotlin
  val numbers = listOf(1, 2, 3, 4)
  println(numbers.find { it % 2 == 0 })
  println(numbers.findLast { it % 2 == 0 })

2
4
```

### <span style="color:aquamarine">Retrieve with selector</span>

If you need to $\color{orange}{map}$ the collection before retrieving the element,use function [firstNotNullOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of.html). It combines 2 actions:

- $\color{orange}{Maps}$ the collection with the selector function
- Returns the $\color{orange}{first non-null}$ value in the result
- Throw $\color{orange}{NoSuchElementException}$ if the resulting collection doesn't have a non-null element.
  
```kotlin
  val list = listOf<Any>(0, "true", false)
  // Converts each element to string and returns the first one that has required length
  val longEnough = list.firstNotNullOf { item -> item.toString().takeIf { it.length >= 4 } }
  println(longEnough)

true
```
- Use the counterpart [firstNotNullOfOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/first-not-null-of-or-null.html) to return $\color{orange}{null}$ in this case.


### <span style="color:aquamarine">Random element</span>

To retrieve an $\color{orange}{arbitrary}$ element of a collection, call the [random()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random.html) function. You can call it $\color{orange}{without}$ arguments or with a Random object as a source of the randomness.

```kotlin
  val numbers = listOf(1, 2, 3, 4)
  println(numbers.random())

1
```

On $\color{orange}{empty}$ collections, $\color{orange}{random()~throws~ exception}$. To receive $\color{orange}{null}$ instead, use [randomOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/random-or-null.html)


### <span style="color:aquamarine">Check element existence</span>

To check the presence of an element in a collection, use the [contains()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains.html) function. It returns $\color{orange}{true}$ if there is a element that $\color{orange}{equals()}$ the argument. Can be called $\color{orange}{contains()}$ in the operator form with the $\color{orange}{in}$ keyword.

To check the presence of $\color{orange}{multiple}$ instances together at once, call [containsAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/contains-all.html) with a $\color{orange}{collection}$ of these instances as an argument.

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.contains("four"))
  println("zero" in numbers)

  println(numbers.containsAll(listOf("four", "two")))
  println(numbers.containsAll(listOf("one", "zero")))

true
false
true
false
```
check if the collection contains $\color{orange}{any}$ elements by calling [isEmpty()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-empty.html) or [isNotEmpty()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/is-not-empty.html).

```kotlin
  val numbers = listOf("one", "two", "three", "four", "five", "six")
  println(numbers.isEmpty())
  println(numbers.isNotEmpty())

  val empty = emptyList<String>()
  println(empty.isEmpty())
  println(empty.isNotEmpty())

false
true
true
false
```



## <span style="color:forestgreen">Ordering</span>

$\color{orange}{Order}$ of elements is an important aspect of certain collection types. For example, two lists of the same elements are not equal if their elements are ordered differently. 

Kotlin provides functions for sorting collections in natural, custom, and even random orders.sorting functions that apply to read-only collections. They return their result as a new collection containing the elements of the original collection in the requested order


In Kotlin, the orders of objects can be defined in several ways.

### <span style="color:aquamarine">Natural order</span>
  
Defined by implementing the [Comparable](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparable/) $\color{orange}{interface}$. it is used for sorting elements when $\color{orange}{no~order}$ is specified.

#### <span style="color:coral">Build-in type:
Most $\color{orange}{built-in}$ types are comparable:
  - $\color{orange}{Numeric}$ types use the traditional $\color{orange}{numerical~order}$: 
    - e.g. 1 is greater than 0; -3.4f is greater than -5f, and so on.
  - $\color{orange}{Char,String}$ use the [lexicographical order](https://en.wikipedia.org/wiki/Lexicographic_order): 
    - e.g. b is greater than a; world is greater than hello.


#### <span style="color:coral">User type:</span>

To define a $\color{orange}{Natural~order}$ for a user-defined type, implementing the $\color{orange}{compareTo()}$ function of the $\color{orange}{Comparable~interface}$. $\color{orange}{compareTo()}$ must take object of the $\color{orange}{same~type}$ as an argument and return an $\color{orange}{integer}$ value indicate the camparison result:

- $\color{orange}{Positive}$ values indicate the object is $\color{orange}{ \gt}$ its argument.
- $\color{orange}{Negative}$ values indicate the object $\color{orange}{\lt}$ its argument.
- $\color{orange}{Zero}$ indicate the objects are $\color{orange}{=}$ to its argument.

Below is a class for ordering versions that consist of the major and the minor part.

```kotlin
  class Version(val major: Int, val minor: Int): Comparable<Version> {
      override fun compareTo(other: Version): Int {
          if (this.major != other.major) {
              return this.major - other.major
          } else if (this.minor != other.minor) {
              return this.minor - other.minor
          } else return 0
      }
  }

  fun main() {    
      println(Version(1, 2) > Version(1, 3))
      println(Version(2, 0) > Version(1, 5))
  }

false
true
```

#### <span style="color:coral">Sorting style</span>

These functions apply to collections of $\color{orange}{Comparable}$ elements.

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  println("Sorted ascending: ${numbers.sorted()}")
  println("Sorted descending: ${numbers.sortedDescending()}")

Sorted ascending: [four, one, three, two]
Sorted descending: [two, three, one, four]
```

$\color{yellow}{Ascending}$

The [sorted()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted.html) 
return elements of a sorted collection as $\color{orange}{acending}$ of their $\color{orange}{natural~order}$. 

$\color{yellow}{Descending}$

 [sortedDescending()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-descending.html) return elements of a sorted collection as descending sequence according to their $\color{orange}{natural~order}$. 


### <span style="color:aquamarine"> Custom ordering</span>
   
$\color{orange}{Custom~orders}$ is used to sort instances of any type in any way e.g. define an order for $\color{orange}{non-comparable}$ objects or define an order of $\color{orange}{non-natural}$ for a comparable type. 

To define a custom order for a type, 
- create the [Comparator](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/) object 


```kotlin
  val lengthComparator = Comparator { str1: String, str2: String -> str1.length - str2.length }
  println(listOf("aaa", "bb", "c").sortedWith(lengthComparator))

[c, bb, aaa]
```

Having the lengthComparator, you are able to arrange strings by their length instead of the default lexicographical order.


- or implement the [compare()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-comparator/compare.html) function of the $\color{orange}{Comparator~interface}$ for it. it takes $\color{orange}{two}$ instances of the class to be compared and returns the result of camparison as $\color{orange}{integer}$ similar to The $\color{orange}{compareTo()}$.


- or use the [compareBy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/compare-by.html). It takes a lambda function that produces a Comparable value from an instance and defines the custom order as the natural order of the produced values.

```kotlin
  println(listOf("aaa", "bb", "c").sortedWith(compareBy { it.length }))

[c, bb, aaa]
```

For sorting in custom orders or sorting non-comparable objects use  [sortedBy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by.html) and [sortedByDescending()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-by-descending.html). They take a selector function that maps collection elements to Comparable values and sort the collection in $\color{orange}{natural~order}$ of that values.

```kotlin
  val numbers = listOf("one", "two", "three", "four")

  val sortedNumbers = numbers.sortedBy { it.length }
  println("Sorted by length ascending: $sortedNumbers")
  val sortedByLast = numbers.sortedByDescending { it.last() }
  println("Sorted by the last letter descending: $sortedByLast")

Sorted by length ascending: [one, two, four, three]
Sorted by the last letter descending: [four, two, one, three]
```

 Or provide your own $\color{orange}{Comparator}$. To do this, call the [sortedWith()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sorted-with.html) function passing in your $\color{orange}{Comparator}$. With this function, sorting strings by their length looks like this:

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  println("Sorted by length ascending: ${numbers.sortedWith(compareBy { it.length })}")

Sorted by length ascending: [one, two, four, three]
```

### <span style="color:aquamarine"> Reverse order﻿</span>

To retrieve the collection in the reversed order using the [reversed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reversed.html) function. it  returns a new collection with the copies of the elements.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  println(numbers.reversed())

[four, three, two, one]
```
- Or use the [asReversed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html)
It returns a reversed view of the same collection instance, so it may be more lightweight and preferable than reversed() if the original list is not going to change.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  val reversedNumbers = numbers.asReversed()
  println(reversedNumbers)

[four, three, two, one]
```
If the original list is mutable, all its changes reflect in its reversed views and vice versa.

```kotlin
  val numbers = mutableListOf("one", "two", "three", "four")
  val reversedNumbers = numbers.asReversed()
  println(reversedNumbers)
  numbers.add("five")
  println(reversedNumbers)

[four, three, two, one]
[five, four, three, two, one]
```
However, if the mutability of the list is unknown or the source is not a list at all, reversed() is more preferable since its result is a copy that won't change in the future.


### <span style="color:aquamarine"> Random order</span>

A function that returns a new List containing the collection elements in a random order - [shuffled()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffled.html). You can call it without arguments or with a [Random](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.random/-random/) object.

```kotlin
  val numbers = listOf("one", "two", "three", "four")
  println(numbers.shuffled())

[one, two, three, four]
```


## <span style="color:forestgreen">Aggregate operations</span>

The operations that return a single value based on the collection content

### <span style="color:aquamarine"> Retrieve a single value of Collection</span>

- [minOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-or-null.html) and [maxOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-or-null.html) return the smallest and the largest element respectively. On empty collections, they return null.

- [average()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/average.html) returns the average value of elements in the collection of numbers.

- [sum()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum.html) returns the sum of elements in the collection of numbers.
- [count()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/count.html) returns the number of elements in a collection.


```kotlin
fun main() {
    val numbers = listOf(6, 42, 10, 4)

    println("Count: ${numbers.count()}")
    println("Max: ${numbers.maxOrNull()}")
    println("Min: ${numbers.minOrNull()}")
    println("Average: ${numbers.average()}")
    println("Sum: ${numbers.sum()}")
}

Count: 4
Max: 42
Min: 4
Average: 15.5
Sum: 62

```

### <span style="color:aquamarine"> Retrieve smallest and Largest value</span>

 functions for retrieving the smallest and the largest elements by certain selector function or custom Comparator:

- [maxByOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-by-or-null.html) and [minByOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-by-or-null.html) take a selector function and return the element for which it returns the largest or the smallest value.

- [maxWithOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/max-with-or-null.html) and [minWithOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/min-with-or-null.html) take a Comparator object and return the largest or smallest element according to that Comparator.

These functions return $\color{orange}{null}$ on empty collections. There are also alternatives for maxByOrNull() and minByOrNull(): maxOf() and minOf(), which do the same but throw a $\color{orange}{NoSuchElementException}$ on empty collections.

```kotlin
  val numbers = listOf(5, 42, 10, 4)
  val min3Remainder = numbers.minByOrNull { it % 3 }
  println(min3Remainder)

  val strings = listOf("one", "two", "three", "four")
  val longestString = strings.maxWithOrNull(compareBy { it.length })
  println(longestString)

42
three
```

- advanced summation function [sumOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sum-of.html) that takes a selector function and returns the $\color{orange}{sum~of}$ its application to all collection elements. Selector can return different numeric types: Int, Long, Double, UInt, and ULong (also BigInteger and BigDecimal on the JVM).

```kotlin
  val numbers = listOf(5, 42, 10, 4)
  println(numbers.sumOf { it * 2 })
  println(numbers.sumOf { it.toDouble() / 2 })

122
30.5
```

### <span style="color:aquamarine"> Fold and reduce﻿</span>

These functons let you transform the whole collection down to a single value with different starting step.

The difference between the two functions is that fold() takes an initial value and uses it as the accumulated value on the first step, whereas the first step of reduce() uses the first and the second elements as operation arguments on the first step.

#### <span style="color:coral"> Reduce functon</span>

The [reduce()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce.html) take $\color{orange}{lambda}$ that take 2 arguments the $\color{orange}{first}$ argument serve as accumulator to hold result and the $\color{orange}{second}$ argument serve as the first item to start the operation and sequntially combined all items into final $\color{orange}{single~value}$ to be return.

#### <span style="color:coral"> Fold function</span>
The [fold()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold.html) function require an $\color{orange}{initial}$ value  to kick off and $\color{orange}{lambda }$ to do reduction process. It uses the value to serve as first value to sequentially accumulate until the last item and then return the result.


```kotlin
  val numbers = listOf(5, 2, 10, 4)

  val sum = numbers.reduce { sum, element -> sum + element }
  println(sum)
  val sumDoubled = numbers.fold(0) { sum, element -> sum + element * 2 }
  println(sumDoubled)

  //val sumDoubledReduce = numbers.reduce { sum, element -> sum + element * 2 } //incorrect: the first element isn't doubled in the result
  //println(sumDoubledReduce)

21
42
```

#### <span style="color:coral">Reverse Oder</span>

To apply a function to elements in the reverse order, use functions [reduceRight()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right.html) and [foldRight()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right.html). They work in a way similar to $\color{orange}{fold()}$ and $\color{orange}{reduce()}$ but start from the last element and then continue backward. Note that the operation $\color{orange}{arguments~reverse~order}$: first goes the element, and then the accumulated value.

```kotlin
  val numbers = listOf(5, 2, 10, 4)
  val sumDoubledRight = numbers.foldRight(0) { element, sum -> sum + element * 2 }
  println(sumDoubledRight)

42
```

#### <span style="color:coral">Using Index</span>
For operations that take element indices as parameters use functions [reduceIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed.html) and [foldIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-indexed.html) passing element $\color{orange}{index}$ as the $\color{orange}{first}$ argument of the operation.

#### <span style="color:coral">Reverse Index</span>
Functions that perform operations to collection elements from $\color{orange}{right~to~left}$ - [reduceRightIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed.html) and [foldRightIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fold-right-indexed.html).

```kotlin
  val numbers = listOf(5, 2, 10, 4)
  val sumEven = numbers.foldIndexed(0) { idx, sum, element -> if (idx % 2 == 0) sum + element else sum }
  println(sumEven)

  val sumEvenRight = numbers.foldRightIndexed(0) { idx, element, sum -> if (idx % 2 == 0) sum + element else sum }
  println(sumEvenRight)

15
15
```

#### <span style="color:coral">Null instead of Exception</span>

All reduce operations throw an $\color{orange}{exception}$ on $\color{orange}{empty}$ collections. To receive $\color{orange}{null}$ instead, use their $\color{orange}{*OrNull()}$ counterparts:

- [reduceOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-or-null.html)
- [reduceRightOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-or-null.html)
- [reduceIndexedOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-indexed-or-null.html)
- [reduceRightIndexedOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reduce-right-indexed-or-null.html)

#### <span style="color:coral">Intermediate Value</span>

For cases where you want to save intermediate accumulator values use functions [runningFold()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold.html), [scan()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/scan.html), or [runningReduce()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce.html).

```kotlin
  val numbers = listOf(0, 1, 2, 3, 4, 5)
  val runningReduceSum = numbers.runningReduce { sum, item -> sum + item }
  val runningFoldSum = numbers.runningFold(10) { sum, item -> sum + item }

Sum of first N elements with runningReduce:
N = 1: 0
N = 2: 1
N = 3: 3
N = 4: 6
N = 5: 10
N = 6: 15
Sum of first N elements with runningFold:
N = 1: 10
N = 2: 10
N = 3: 11
N = 4: 13
N = 5: 16
N = 6: 20
N = 7: 25
```

#### <span style="color:coral">Intermediate index</span>

If you need an index in the operation parameter, use [runningFoldIndexed() ](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-fold-indexed.html)or [runningReduceIndexed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/running-reduce-indexed.html).


## <span style="color:forestgreen">Mutable Collection operations</span>

These are common operations that all mutable collections support.


### <span style="color:aquamarine"> Adding elements﻿</span>
To add a single element to a list or a set, use the [add()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html) function which appended item to the end of the collection.

```kotlin
  val numbers = mutableListOf(1, 2, 3, 4)
  numbers.add(5)
  println(numbers)

[1, 2, 3, 4, 5]
```
The [addAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) adds $\color{orange}{every~element}$ of the argument object to a list or a set. 
- The argument can be an $\color{orange}{Iterable,Sequence,Array}$. 
- The types of the receiver and the argument may be different, for example, you can add all items from a Set to a List.
- for list adds new elements in the same order as they go in the argument
- specifying an element position of the first argument to be inserted

```kotlin
  val numbers = mutableListOf(1, 2, 5, 6)
  numbers.addAll(arrayOf(7, 8))
  println(numbers)
  numbers.addAll(2, setOf(3, 4))
  println(numbers)

[1, 2, 5, 6, 7, 8]
[1, 2, 3, 4, 5, 6, 7, 8]
```

- using the $\color{orange}{in-place}$ version of the plus operator. [plusAssign (+=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) When applied to a mutable collection, $\color{orange}{+=}$ appends the second operand (an element or another collection) to the end of the collection.

```kotlin
val numbers = mutableListOf("one", "two")
numbers += "three"
println(numbers)
numbers += listOf("four", "five")    
println(numbers)

[one, two, three]
[one, two, three, four, five]
```

### <span style="color:aquamarine"> Removing elements</span>

To remove an element from a mutable collection, use the [remove()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove.html) function. $\color{orange}{remove()}$ accepts the element value and removes one occurrence of this value.

```kotlin
  val numbers = mutableListOf(1, 2, 3, 4, 3)
  numbers.remove(3)                    // removes the first `3`
  println(numbers)
  numbers.remove(5)                    // removes nothing
  println(numbers)

[1, 2, 4, 3]
[1, 2, 4, 3]
```
For removing multiple elements at once use :

- [removeAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/remove-all.html) removes all elements that are present in the argument collection. Alternatively, you can call it with a predicate as an argument; in this case the function removes all elements for which the predicate yields true.

- [retainAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/retain-all.html) is the opposite of removeAll(): it removes all elements except the ones from the argument collection. When used with a predicate, it leaves only elements that match it.

- [clear()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/clear.html) removes all elements from a list and leaves it empty.

```kotlin 
  val numbers = mutableListOf(1, 2, 3, 4)
  println(numbers)
  numbers.retainAll { it >= 3 }
  println(numbers)
  numbers.clear()
  println(numbers)

  val numbersSet = mutableSetOf("one", "two", "three", "four")
  numbersSet.removeAll(setOf("one", "two"))
  println(numbersSet)

[1, 2, 3, 4]
[3, 4]
[]
[three, four]
```
 - Or use [minusAssign (-=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) operator – the in-place version of minus. The second argument can be a single instance of the element type or another collection. With a single element on the right-hand side, -= removes the first occurrence of it. In turn, if it's a collection, all occurrences of its elements are removed. For example, if a list contains duplicate elements, they are removed at once. The second operand can contain elements that are not present in the collection. Such elements don't affect the operation execution.

```kotlin
  val numbers = mutableListOf("one", "two", "three", "three", "four")
  numbers -= "three"
  println(numbers)
  numbers -= listOf("four", "five")    
  //numbers -= listOf("four")    // does the same as above
  println(numbers)    

[one, two, three, four]
[one, two, three]

```


### <span style="color:aquamarine"> Updating elements</span>


  $\color{orange}{Lists}$ and $\color{orange}{Maps}$ also provide operations for updating elements. They are described in List-specific Operations and Map Specific Operations. For $\color{orange}{Sets}$, updating $\color{orange}{doesn't~make ~sense}$ since it's actually removing an element and adding another one.





## <span style="color:forestgreen">List operations</span>

$\color{orange}{List}$ provide common ways to manipulate items in its collection.
$\color{orange}{elementAt()}$, $\color{orange}{first()}$, $\color{orange}{last()}$. It also support $\color{orange}{Index}$ access to the elements of lists provides a powerful set of operations for lists.


### <span style="color:aquamarine">Retrieve elements by index</span>

The simplest way to read an element is retrieving it by index with the [get()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/get.html) function with the index passed in the argument or the shorthand [index] syntax.

If the list size is less than the specified index, an $\color{orange}{exception}$ is thrown. 

To avoid exceptions use:

- [getOrElse()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) lets you provide $\color{orange}{lambda}$ for calculating the $\color{orange}{default}$ value to return if the index isn't present in the collection.

- [getOrNull()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-null.html) returns $\color{orange}{null}$ as the default value.

```kotlin
  val numbers = listOf(1, 2, 3, 4)
  println(numbers.get(0))
  println(numbers[0])
  //numbers.get(5)                         // exception!
  println(numbers.getOrNull(5))             // null
  println(numbers.getOrElse(5, {it}))        // 5

1
1
null
5

```


### <span style="color:aquamarine">Retrieving list parts</span>
In addition to common operations for Retrieving Collection Parts. $\color{orange}{Lists}$ provide the [subList()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/sub-list.html) function that returns a $\color{orange}{view}$ a r$\color{orange}{eference}$ of the specified elements range as a list. Thus, if an element of the original collection changes, it also changes in the previously created sublists and vice versa.

```kotlin
  val numbers = (0..13).toList()
  println(numbers.subList(3, 6))

[3, 4, 5]
```

### <span style="color:aquamarine">Find element positions</span>

#### <span style="color:coral">Linear search</span>

For linear search support by functions [indexOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of.html) and [lastIndexOf()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/last-index-of.html). They return the first and the last position of an element equal to the given argument in the list. If there are no such elements, both functions return -1.

```kotlin
  val numbers = listOf(1, 2, 3, 4, 2, 5)
  println(numbers.indexOf(2))
  println(numbers.lastIndexOf(2))

1
4
```

Or functions that take a predicate and search for elements matching it:

- [indexOfFirst()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-first.html) returns the index of the $\color{orange}{first}$ element matching the predicate or $\color{orange}{-1}$ if there are no such elements.

- [indexOfLast()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index-of-last.html) returns the index of the $\color{orange}{last}$ element matching the predicate or $\color{orange}{-1}$ if there are no such elements.

```kotlin
  val numbers = mutableListOf(1, 2, 3, 4)
  println(numbers.indexOfFirst { it > 2})
  println(numbers.indexOfLast { it % 2 == 1})

2
2
```


#### <span style="color:coral">Binary search in sorted lists</span>

Another way to search elements in lists is $\color{orange}{binary ~search}$. It works significantly faster than other built-in search functions but requires the list to be $\color{orange}{sorted}$ in ascending order according to a certain ordering: natural or another one provided in the function parameter. Otherwise, the result is undefined.

To search an element in a sorted list, call the [binarySearch()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/binary-search.html) function passing the $\color{orange}{value}$ as an argument. If such an element exists, the function returns its $\color{orange}{index}$; otherwise, it returns (-insertionPoint - 1) where $\color{orange}{insertionPoint}$ is the index where this element should be inserted so that the list remains sorted. If there is more than one element with the given value, the search can return $\color{orange}{any}$ of their indices.

You can also specify an $\color{orange}{index~range}$ to search in: in this case, the function searches only between two provided indices.

```kotlin
  val numbers = mutableListOf("one", "two", "three", "four")
  numbers.sort()
  println(numbers)
  println(numbers.binarySearch("two"))  // 3
  println(numbers.binarySearch("z")) // -5
  println(numbers.binarySearch("two", 0, 2))  // -3

[four, one, three, two]
3
-5
-3
```
Comparator binary search

When list elements $\color{orange}{aren't~Comparable}$, you should provide a $\color{orange}{Comparator}$ to use in the binary search. The list must be $\color{orange}{sorted}$ in ascending order according to this Comparator. Let's have a look at an example:

```kotlin
  val productList = listOf(
      Product("WebStorm", 49.0),
      Product("AppCode", 99.0),
      Product("DotTrace", 129.0),
      Product("ReSharper", 149.0))

  println(productList.binarySearch(Product("AppCode", 99.0), compareBy<Product> { it.price }.thenBy { it.name }))

1
```
Here's a list of Product instances that aren't Comparable and a Comparator that defines the order: product p1 precedes product p2 if p1 's price is less than p2 's price. So, having a list sorted ascending according to this order, we use binarySearch() to find the index of the specified Product.

$\color{orange}{Custom}$ comparators are also handy when a list uses an $\color{orange}{order~different}$ from natural one, for example, a case-insensitive order for String elements.

```kotlin
  val colors = listOf("Blue", "green", "ORANGE", "Red", "yellow")
  println(colors.binarySearch("RED", String.CASE_INSENSITIVE_ORDER)) 

3
```

#### <span style="color:coral">Comparison binary search</span>

Binary search with comparison function lets you find elements without providing $\color{orange}{explicit~search}$ values. Instead, it takes a $\color{orange}{comparison~function}$ mapping elements to Int values and searches for the element where the function returns $\color{orange}{zero}$. The list must be $\color{orange}{sorted}$ in the a $\color{orange}{scending}$ order according to the provided function; in other words, the return values of comparison must grow from one list element to the next one.

```kotlin
  data class Product(val name: String, val price: Double)

  fun priceComparison(product: Product, price: Double) = sign(product.price - price).toInt()

  fun main() {
      val productList = listOf(
          Product("WebStorm", 49.0),
          Product("AppCode", 99.0),
          Product("DotTrace", 129.0),
          Product("ReSharper", 149.0))

      println(productList.binarySearch { priceComparison(it, 99.0) })
  }

1
```
Both comparator and comparison binary search can be performed for list ranges as well.


### <span style="color:aquamarine">Mutable List write operations</span>
mutable lists support specific write operations use the index to access elements to broaden the list modification capabilities.

#### <span style="color:coral">Add</span>
  To add elements to a specific $\color{orange}{position in a list, use 
- [add()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/add.html) and 
- [addAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/add-all.html) 
- providing the $\color{orange}{position}$ where to insertion item  as an additional argument. All elements that come after the position will be shift to the right.

```kotlin
  val numbers = mutableListOf("one", "five", "six")
  numbers.add(1, "two")
  numbers.addAll(2, listOf("three", "four"))
  println(numbers)

[one, two, three, four, five, six]
```

#### <span style="color:coral">Update</span>

Lists also offer a function to $\color{orange}{replace}$ an element at a given $\color{orange}{position}$ 
- [set()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/set.html) and its operator form $\color{orange}{[]}$. $\color{orange}{set()}$ doesn't change the indexes of other elements.

```kotlin
  val numbers = mutableListOf("one", "five", "three")
  numbers[1] =  "two"
  println(numbers)

[one, two, three]
```
- [fill()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/fill.html) simply $\color{orange}{replaces}$ all the collection elements with the specified $\color{orange}{value}$.

```kotlin
  val numbers = mutableListOf(1, 2, 3, 4)
  numbers.fill(3)
  println(numbers)

[3, 3, 3, 3]
```

#### <span style="color:coral">Remove</span>

To $\color{orange}{remove}$ an element at a specific $\color{orange}{position}$ from a list, use the 
- [removeAt()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-list/remove-at.html) provide the $\color{orange}{position}$ as an argument. All indices of elements that come after the element being removed will be $\color{orange}{decrease}$ by one

```kotlin
  val numbers = mutableListOf(1, 2, 3, 4, 3)    
  numbers.removeAt(1)
  println(numbers)

[1, 3, 4, 3]
```

#### <span style="color:coral">Sort</span>

The mutable collection provide $\color{orange}{in-place}$ sorting functions.
With similar names without ed e.g.sort* instead of sorted* in the names of all sorting functions: 
- [sort()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort.html), 
- [sortDescending()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-descending.html), 
- [sortBy()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/sort-by.html)

- [shuffle()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/shuffle.html)

- [reverse()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/reverse.html) 

- [asReversed()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/as-reversed.html) called on a mutable list returns another mutable list which is a $\color{orange}{reversed~view}$ of the original list. Changes in that view are $\color{orange}{reflected}$ in the original list.

```kotlin
  val numbers = mutableListOf("one", "two", "three", "four")

  numbers.sort()
  println("Sort into ascending: $numbers")
  numbers.sortDescending()
  println("Sort into descending: $numbers")

  numbers.sortBy { it.length }
  println("Sort into ascending by length: $numbers")
  numbers.sortByDescending { it.last() }
  println("Sort into descending by the last letter: $numbers")

  numbers.sortWith(compareBy<String> { it.length }.thenBy { it })
  println("Sort by Comparator: $numbers")

  numbers.shuffle()
  println("Shuffle: $numbers")

  numbers.reverse()
  println("Reverse: $numbers")

Sort into ascending: [four, one, three, two]
Sort into descending: [two, three, one, four]
Sort into ascending by length: [two, one, four, three]
Sort into descending by the last letter: [four, two, one, three]
Sort by Comparator: [one, two, four, three]
Shuffle: [two, three, one, four]
Reverse: [four, one, three, two]
```


## <span style="color:forestgreen">Set operations</span>

 Kotlin provide extension functions for popular operations on $\color{orange}{sets}$: finding $\color{orange}{intersections}$, $\color{orange}{merging}$, or $\color{orange}{subtracting}$ collections from each other.

To merge two collections into one, use the 
- [union()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/union.html) function. It can be used in the $\color{orange}{infix}$ form a union b. Note that for $\color{orange}{ordered}$ collections the $\color{orange}{order~of~operands~is~important}$: in the resulting collection, the elements of the $\color{orange}{first}$ operand go before the elements of the $\color{orange}{second}$.

To find an $\color{orange}{intersection}$ between two collections (elements $\color{orange}{present}$ in $\color{orange}{both}$ of them), use 
- [intersect()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/intersect.html). 

To find collection elements $\color{orange}{not~present}$ in another collection, 
- use [subtract()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/subtract.html). 
- Both these functions can be called in the $\color{orange}{infix}$ form as well, for example, a intersect b.

```kotlin
  val numbers = setOf("one", "two", "three")

  println(numbers union setOf("four", "five"))
  println(setOf("four", "five") union numbers)

  println(numbers intersect setOf("two", "one"))
  println(numbers subtract setOf("three", "four"))
  println(numbers subtract setOf("four", "three")) // same output

[one, two, three, four, five]
[four, five, one, two, three]
[one, two]
[one, two]
[one, two]
```
Note that set operations are supported by List as well. However, the $\color{orange}{result}$ of set operations on lists is still a $\color{orange}{Set}$, so all the $\color{orange}{duplicate}$ elements are $\color{orange}{removed}$.

## <span style="color:forestgreen">Map operations</span>

For retrieving a $\color{orange}{value}$ from a $\color{orange}{map}$, must provide its  $\color{orange}{key [key] as an argument of the 
- [get()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/get.html) function.If the given key is not found, it returns $\color{orange}{null}$
- [getValue()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-value.html) which throws an $\color{orange}{exception}$ if the key is not found in the map
- [getOrElse()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-else.html) works the same way as for lists: the $\color{orange}{values}$ for non-existent keys are returned from the given $\color{orange}{lambda}$ function.
- [getOrDefault()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/get-or-default.html) returns the specified $\color{orange}{default}$
```kotlin
  val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
  println(numbersMap.get("one"))
  println(numbersMap["one"])
  println(numbersMap.getOrDefault("four", 10))
  println(numbersMap["five"])               // null
  //numbersMap.getValue("six")      // exception!

1
1
10
null
```

To perform operations on $\color{orange}{all~keys}$ or $\color{orange}{all~values}$ of a $\color{orange}{map}$, you can retrieve them from the properties keys and values accordingly. keys is a set of all map keys and values is a collection of all map values.

```kotlin
  val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
  println(numbersMap.keys)
  println(numbersMap.values)

[one, two, three]
[1, 2, 3]
```

### <span style="color:aquamarine">Filter</span>


When calling [filter()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter.html) on a map, pass to it a predicate with a Pair as an argument. This enables you to use both the key and the value in the filtering predicate.

```kotlin
  val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
  val filteredMap = numbersMap.filter { (key, value) -> key.endsWith("1") && value > 10}
  println(filteredMap)

{key11=11}
```
There are also two specific ways for $\color{orange}{filtering~maps}$: by $\color{orange}{keys}$ and by $\color{orange}{values}$. For each way, there is a function: 
- [filterKeys()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-keys.html) and 
- [filterValues()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/filter-values.html). 
- Both return a $\color{orange}{new~map}$ of entries which match the given predicate. The $\color{orange}{predicate}$ for $\color{orange}{filterKeys()}$ checks only the element keys, the one for $\color{orange}{filterValues() }$checks only values.

```kotlin
  val numbersMap = mapOf("key1" to 1, "key2" to 2, "key3" to 3, "key11" to 11)
  val filteredKeysMap = numbersMap.filterKeys { it.endsWith("1") }
  val filteredValuesMap = numbersMap.filterValues { it < 10 }

  println(filteredKeysMap)
  println(filteredValuesMap)


{key1=1, key11=11}
{key1=1, key2=2, key3=3}
```

### <span style="color:aquamarine">Plus and minus operators</span>
Due to the key access to elements, [plus (+)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus.html) and [minus (-)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus.html) operators work for maps differently than for other collections. 
- $\color{orange}{plus}$ returns a $\color{orange}{Map}$ that contains elements of its both operands: a Map on the left and a Pair or another $\color{orange}{Map}$ on the right. When the right-hand side operand contains entries with keys present in the left-hand side $\color{orange}{Map}$, the result map contains the entries from the right side.

```kotlin
  val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
  println(numbersMap + Pair("four", 4))
  println(numbersMap + Pair("one", 10))
  println(numbersMap + mapOf("five" to 5, "one" to 11))

{one=1, two=2, three=3, four=4}
{one=10, two=2, three=3}
{one=11, two=2, three=3, five=5}
```
- $\color{orange}{minus}$ creates a $\color{orange}{Map}$ from entries of a $\color{orange}{Map}$ on the left except those with keys from the right-hand side operand. So, the right-hand side operand can be either a single key or a collection of keys: list, set, and so on.
  
```kotlin
  val numbersMap = mapOf("one" to 1, "two" to 2, "three" to 3)
  println(numbersMap - "one")
  println(numbersMap - listOf("two", "four"))

{two=2, three=3}
{one=1, three=3}
```

### <span style="color:aquamarine">Map write operations</span>
 These operations let you change the $\color{orange}{map~content}$ using the key-based access to the values.

There are certain $\color{orange}{rules}$ that define write operations on maps:

- $\color{orange}{Values}$ can be $\color{orange}{updated}$. In turn, $\color{orange}{keys~never~change}$: once you add an entry, its key is constant.

- For $\color{orange}{a~key}$, there is always a $\color{orange}{single~value}$ associated with it. You can add and remove whole entries.


#### <span style="color:coral">Add and update entries</span>

To $\color{orange}{add}$ a new key-value pair to a $\color{orange}{mutable~map}$, use [put()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/put.html). When a new entry is put into a $\color{orange}{LinkedHashMap}$ (the default map implementation), it is added so that it $\color{orange}{comes~last}$ when iterating the map. In $\color{orange}{sorted~maps}$, the $\color{orange}{positions}$ of new elements are defined by the $\color{orange}{order}$ of their keys.


```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2)
  numbersMap.put("three", 3)
  println(numbersMap)

{one=1, two=2, three=3}
```
To add $\color{orange}{multiple}$ entries at a time, use [putAll()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/put-all.html). Its argument can be a $\color{orange}{Map}$ or a group of $\color{orange}{Pair}$ s: Iterable, Sequence, or Array.

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
  numbersMap.putAll(setOf("four" to 4, "five" to 5))
  println(numbersMap)

{one=1, two=2, three=3, four=4, five=5}
```
Both $\color{orange}{put()}$ and $\color{orange}{putAll()}$ $\color{orange}{overwrite}$ the values if the given keys already $\color{orange}{exist}$ in the map. Thus, you can use them to update values of map entries.

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2)
  val previousValue = numbersMap.put("one", 11)
  println("value associated with 'one', before: $previousValue, after: ${numbersMap["one"]}")
  println(numbersMap)

value associated with 'one', before: 1, after: 11
{one=11, two=2}
```
You can also add new entries to maps using the shorthand operator form. There are two ways:

- [plusAssign (+=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/plus-assign.html) operator.
- the $\color{orange}{[]}$ operator alias for set().

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2)
  numbersMap["three"] = 3     // calls numbersMap.put("three", 3)
  numbersMap += mapOf("four" to 4, "five" to 5)
  println(numbersMap)

{one=1, two=2, three=3, four=4, five=5}
```
When called with the key present in the map, operators overwrite the values of the corresponding entries.

#### <span style="color:coral">Remove entries</span>

To remove an entry from a mutable map, use the 
- [remove()](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-mutable-map/remove.html) function. When calling $\color{orange}{remove()}$, you can pass either a $\color{orange}{key,or whole~key-value-pair}$. If you specify both the key and value, the element with this key will be removed only if its value $\color{orange}{matches}$ the second argument.

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
  numbersMap.remove("one")
  println(numbersMap)
  numbersMap.remove("three", 4)            //doesn't remove anything
  println(numbersMap)

{two=2, three=3}
{two=2, three=3}
```

You can also remove entries from a mutable map by their $\color{orange}{keys~or~values}$. To do this, call $\color{orange}{remove()}$ on the map's keys or values providing the key or the value of an entry. 

When called on values, $\color{orange}{remove()}$ removes only the $\color{orange}{first}$ entry with the given value.

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3, "threeAgain" to 3)
  numbersMap.keys.remove("one")
  println(numbersMap)
  numbersMap.values.remove(3)
  println(numbersMap)

{two=2, three=3, threeAgain=3}
{two=2, threeAgain=3}
```
The [minusAssign (-=)](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/minus-assign.html) operator is also available for $\color{orange}{mutable~maps}$.

```kotlin
  val numbersMap = mutableMapOf("one" to 1, "two" to 2, "three" to 3)
  numbersMap -= "two"
  println(numbersMap)
  numbersMap -= "five"             //doesn't remove anything
  println(numbersMap)

{one=1, three=3}
{one=1, three=3}
```