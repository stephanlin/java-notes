# My Java Notes

## Unicode Encoding
```java
char symbol = '\u00A9'; // ©
```
An unicode character table can be find [here](https://unicode-table.com/en/#control-character).
Java's *char* is a UTF-16 code unit. For characters with code-point > 0xFFFF it will be encoded with 2 *char* s (a surrogate pair).

## Array, ArrayList, List
An *Array* (System.Array) is fixed in size once it is allocated. You can't add items to it or remove items from it. Also, all the elements must be the same type. As a result, it is type safe, and is also the most efficient of the three, both in terms of memory and performance. Also, System.Array supports multiple dimensions (i.e. it has a Rank property) while List and ArrayList do not (although you can create a List of Lists or an ArrayList of ArrayLists, if you want to).

An *ArrayList* is a flexible array which contains a list of objects, it is said to be non-generic class. You can add and remove items from it and it automatically deals with allocating space. If you store value types in it, they are boxed and unboxed, which can be a bit inefficient. Also, it is not type-safe.

A *List<>* leverages generics; it is essentially a type-safe version of ArrayList. This means there is no boxing or unboxing (which improves performance) and if you attempt to add an item of the wrong type it'll generate a compile-time error.
