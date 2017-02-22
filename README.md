# My Java Notes

## Unicode Encoding
```java
char symbol = '\u00A9'; // ©
```
An unicode character table can be find [here](https://unicode-table.com/en/#control-character).
Java's *char* is a UTF-16 code unit. For characters with code-point > 0xFFFF it will be encoded with 2 *char* s (a surrogate pair).

## Array, List, ArrayList, LinkedList
An *Array* (System.Array) is fixed in size once it is allocated. You can't add items to it or remove items from it. Also, all the elements must be the same type. As a result, it is type safe, and is also the most efficient of the three, both in terms of memory and performance. Also, System.Array supports multiple dimensions (i.e. it has a Rank property) while List and ArrayList do not (although you can create a List of Lists or an ArrayList of ArrayLists, if you want to).
```java
int[] arr1 = {1, 2, 7, 11, 15};
int[] arr2 = new int[5];
for (int i=0; i<arr2.length; i++) {
  arr2[i] = i;
}
```
There are two general-purpose List implementations — ArrayList and LinkedList.

An ArrayList is a flexible array which contains a list of objects, it is said to be non-generic class. You can add and remove items from it and it automatically deals with allocating space. If you store value types in it, they are boxed and unboxed, which can be a bit inefficient. Also, it is not type-safe.
```java
ArrayList al = new ArrayList();
al.add(50);
al.add(50.0);
al.add("50.0");
System.out.println(al); //[50, 50.0, 50.0]
```

A List<> leverages generics; it is essentially a type-safe version of ArrayList. This means there is no boxing or unboxing (which improves performance) and if you attempt to add an item of the wrong type it'll generate a compile-time error.

```java
List<?> myList = new ArrayList<?>();
ArrayList<?> myList = new ArrayList<?>();
```
For the example above, almost always the first one is preferred over the second one. The first has the advantage that the implementation of the List can change (to a LinkedList for example), without affecting the rest of the code. This will be a difficult task to do with an ArrayList, not only because you will need to change ArrayList to LinkedList everywhere, but also because you may have used ArrayList specific methods. Since you are referring mylist as List<Integer> while it is still ArrayList<Integer>, hence you can use the methods available in the List interface ONLY. This is better statement, if you are using cross class.method functionality.

### ArrayList vs LinkedList
You can read about List implementations [here](http://docs.oracle.com/javase/tutorial/collections/implementations/list.html).

| LinkedList                                 | ArrayList                                   |
| -----------------------------------        | ----------------------------------          |
| Implemented with doubly linked list        | Implemented with dynamically re-sizing array|
| get(i) is O(n/4) average                   | get(i) is O(1) **MAIN BENEFIT**             |
| add(E) is O(1)                             | add(E) is O(1) amortized, but O(n) worst-case since the array must be resized and copied|
| add(i, E) is O(n/4) average                | add(i, E) is O(n/2) average                 |
| add(0, E) is O(1) **MAIN BENEFIT**         |                                             |
| remove(i) is O(n/4) average                | remove(i) is O(n/2) average                 |
| Iterator.remove() is O(1) **MAIN BENEFIT** | Iterator.remove() is O(2) average           |
| ListIterator.add(E element) is O(1)  **MAIN BENEFIT** | ListIterator.add(E element) is O(2) average|

LinkedList<E> allows for constant-time insertions or removals using iterators, but only sequential access of elements. In other words, you can walk the list forwards or backwards, but finding a position in the list takes time proportional to the size of the list. Javadoc says "operations that index into the list will traverse the list from the beginning or the end, whichever is closer", so those methods are O(n/4) on average, though O(1) for index = 0.

ArrayList<E>, on the other hand, allow fast random read access, so you can grab any element in constant time. But adding or removing from anywhere but the end requires shifting all the latter elements over, either to make an opening or fill the gap. Also, if you add more elements than the capacity of the underlying array, a new array (1.5 times the size) is allocated, and the old array is copied to the new one, so adding to an ArrayList is O(n) in the worst case but constant on average.

So depending on the operations you intend to do, you should choose the implementations accordingly. Iterating over either kind of List is practically equally cheap. (Iterating over an ArrayList is technically faster, but unless you're doing something really performance-sensitive, you shouldn't worry about this -- they're both constants.)

The main benefits of using a LinkedList arise when you re-use existing iterators to insert and remove elements. These operations can then be done in O(1) by changing the list locally only. In an array list, the remainder of the array needs to be moved (i.e. copied). On the other side, seeking in a LinkedList means following the links in O(n), whereas in an ArrayList the desired position can be computed mathematically and accessed in O(1).

Another benefit of using a LinkedList arise when you add or remove from the head of the list, since those operations are O(1), while they are O(n) for ArrayList. Note that ArrayDeque may be a good alternative to LinkedList for adding and removing from the head, but it is not a List.

Also, if you have large lists, keep in mind that memory usage is also different. Each element of a LinkedList has more overhead since pointers to the next and previous elements are also stored. ArrayLists don't have this overhead. However, ArrayLists take up as much memory as is allocated for the capacity, regardless of whether elements have actually been added.

The default initial capacity of an ArrayList is pretty small (10 from Java 1.4 - 1.8). But since the underlying implementation is an array, the array must be resized if you add a lot of elements. To avoid the high cost of resizing when you know you're going to add a lot of elements, construct the ArrayList with a higher initial capacity.
