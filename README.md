# My Java Notes

## char

### Unicode Encoding
```java
char symbol = '\u00A9'; // ©
```
An unicode character table can be find [here](https://unicode-table.com/en/#control-character).
Java's *char* is a UTF-16 code unit. For characters with code-point > 0xFFFF it will be encoded with 2 *char* s (a surrogate pair).

### Check if a char is a letter or number
```java
Character.isDigit(string.charAt(index))
Character.isLetter(string.charAt(index))
// or
private static boolean isLetterOrDigit(char c) {
    return (c >= 'a' && c <= 'z') ||
           (c >= 'A' && c <= 'Z') ||
           (c >= '0' && c <= '9');
}
```
### Convert char to int
```java
int i = Character.getNumericValue('9');
int i = Integer.parseInt(String.valueOf('9');
int i = '9' - '0';
```

## Array
An Array (System.Array) is fixed in size once it is allocated. You can't add items to it or remove items from it. Also, all the elements must be the same type. As a result, it is type safe, and is also the most efficient of the three, both in terms of memory and performance. Also, System.Array supports multiple dimensions (i.e. it has a Rank property) while List and ArrayList do not (although you can create a List of Lists or an ArrayList of ArrayLists, if you want to).
```java
int[] arr1 = {1, 2, 7, 11, 15};
int[] arr2 = new int[5];
for (int i=0; i<arr2.length; i++) {
  arr2[i] = i;
}
```

## List

List is an interface.

### ArrayList vs. LinkedList
There are two general-purpose List implementations — ArrayList and LinkedList.
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

```java
List<?> myList = new ArrayList<?>();
ArrayList<?> myList = new ArrayList<?>();
```
For the example above, almost always the first one is preferred over the second one. The first has the advantage that the implementation of the List can change (to a LinkedList for example), without affecting the rest of the code. This will be a difficult task to do with an ArrayList, not only because you will need to change ArrayList to LinkedList everywhere, but also because you may have used ArrayList specific methods. Since you are referring mylist as List<Integer> while it is still ArrayList<Integer>, hence you can use the methods available in the List interface ONLY. This is better statement, if you are using cross class.method functionality.


## Map
You can read about Map implementations [here](http://docs.oracle.com/javase/tutorial/collections/implementations/map.html).

The three general-purpose Map implementations — **HashMap**, **TreeMap** and **LinkedHashMap**. If you need SortedMap operations or key-ordered Collection-view iteration, use TreeMap; if you want maximum speed and don't care about iteration order, use HashMap; if you want near-HashMap performance and insertion-order iteration, use LinkedHashMap. 

LinkedHashMap provides two capabilities that are not available with LinkedHashSet. When you create a LinkedHashMap, you can order it based on key access rather than insertion. In other words, merely looking up the value associated with a key brings that key to the end of the map. Also, LinkedHashMap provides the removeEldestEntry method, which may be overridden to impose a policy for removing stale mappings automatically when new mappings are added to the map. This makes it very easy to implement a custom cache.

### HashMap vs. Hashtable

* Hashtable is synchronized, whereas HashMap is not. This makes **HashMap better for non-threaded applications**, as unsynchronized Objects typically perform better than synchronized ones. (Note: **synchronized methods can't be called in the same time from multiple threads**).
* Hashtable does not allow null keys or values.  HashMap allows one null key and any number of null values.
* One of HashMap's subclasses is LinkedHashMap, so in the event that you'd want predictable iteration order (which is insertion order by default), you could easily swap out the HashMap for a LinkedHashMap. This wouldn't be as easy if you were using Hashtable. Hashtable is the only class other than vector which uses enumerator to iterate the values of HashTable object.
* Hashtable is a subclass of Dictionary class which is now obsolete in Jdk 1.7, so, it is not used anymore. It is better off externally synchronizing a HashMap or using a ConcurrentMap implementation (e.g ConcurrentHashMap).

### Iterate over each entry in a Map

```java
Map<Integer, Integer> tmap = new TreeMap<Integer, Integer>();
tmap.put(5,5);
tmap.put(2,2);
tmap.put(6,6);
tmap.put(1,1);
tmap.put(9,9);
System.out.println(tmap); // {1=1, 2=2, 5=5, 6=6, 9=9}
// example 1
Iterator entries = tmap.entrySet().iterator();
while(entries.hasNext()) {
    Map.Entry thisEntry = (Map.Entry) entries.next();
    System.out.println(thisEntry.getKey());
}
// example 2
for (Map.Entry<Integer, Integer> entry : tmap.entrySet()) {
    System.out.println(entry.getKey());
}
```

## Set
You can read about Map implementations [here](http://docs.oracle.com/javase/tutorial/collections/implementations/set.html).

There are three general-purpose Set implementations — **HashSet**, **TreeSet**, and **LinkedHashSet**. Which of these three to use is generally straightforward. HashSet is much faster than TreeSet (constant-time versus log-time for most operations) but offers no ordering guarantees. If you need to use the operations in the SortedSet interface, or if value-ordered iteration is required, use TreeSet; otherwise, use HashSet. It's a fair bet that you'll end up using HashSet most of the time. LinkedHashSet is in some sense intermediate between HashSet and TreeSet. Implemented as a hash table with a linked list running through it, it provides insertion-ordered iteration (least recently inserted to most recently) and runs nearly as fast as HashSet. The LinkedHashSet implementation spares its clients from the unspecified, generally chaotic ordering provided by HashSet without incurring the increased cost associated with TreeSet.

### Iterate over each entry in a Set

```java
Set<Integer> set = new HashSet<Integer>();
set.add(1);
set.add(9);
set.add(4);
set.add(5);
set.add(2);
System.out.println(set); //[1, 2, 4, 5, 9]
// example 1
Iterator it = set.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
// example 2
for(Integer item : set) {
    System.out.println(item);
}
```
Notice the code above, the `set` is sorted. HashSet uses a HashMap internally. HashMap stores its elements in a hash table using each Object's hashCode() method. For int and double, these are auto-boxed into the Integer and Double classes. When you make a HashSet of ints, it uses Integer's hashCode() method, which just returns the int. So if you add ints, they get stored sorted. But for double, Double's hashCode() method is much more complicated, because of the way doubles are represented in memory.

## Static Keyword
### Static vs. Non-static
* Static methods belong to the class not to the object(instance).
* Static methods are invoked using the class name.
* Non-static methods are invoked using the instance name.
* Static methods cannot access instance variables.

### Why Static?
```java
int x = Math.round(5.6); // round is static
```
If `round` was non-static:
```java
Math m = new Math();
int x = m.round(x);
```
### When to use Static methods?
* If you are writing utility classes and they are not supposed to be changed.
* If the method is not using any instance variable.
* If any operation is not dependent on instance creation.
* If there is some code that can easily be shared by all the instance methods, extract that code into a static method.
* If you are sure that the definition of the method will never be changed or overridden. As static methods can not be overridden.

Static varibles are used when they are common to all instances, a single copy to be shared by all instances of the class. Static variables are initialized only once , at the start of the execution . These variables will be initialized first, before the initialization of any instance variables


## Thread
### Thread vs. Process
* A program in execution is often referred as process. A thread is a subset (part) of the process.
* A process consists of multiple threads. A thread is a smallest part of the process that can execute concurrently with other parts (threads) of the process.
* A process is sometime referred as task, A thread is often referred as lightweight process.
* A process has its own address space. A thread uses the process's address space and share it with the other threads of that process.
* 

|Per process items             | Per thread items|
|------------------------------|-----------------|
|Address space                 | Program counter |
|Global variables              | Registers  |
|Open files                    | Stack  |
|Child processes               | State  |
|Pending alarms                |    |
|Signals and signal handlers   |    |
|Accounting information        |    |

* New threads are easily created. However the creation of new processes require duplication of the parent process.
* Threads have control over the other threads of the same process. A process does not have control over the sibling process, it has control over its child processes only.

## Static Binding vs. Dynamic Binding
* Static binding in Java occurs during Compile time while Dynamic binding occurs during Runtime.
* private, final and static methods and variables uses static binding and bonded by compiler while virtual methods are bonded during runtime based upon runtime object. 
* Static binding uses class information for binding while Dynamic binding uses Object to resolve binding.
* Overloaded methods are bonded using static binding while overridden methods are bonded using dynamic binding at runtime. 

Note: Dynamic method binding does not happend for overloaded methods.

### Dynamic Binding Example
```java
public static void main(String args[]) {
    Vehicle vehicle = new Car(); //here Type is vehicle but object will be Car
    vehicle.start();       //Car's start called because start() is overridden method
}
```

### Method Overloading
A feature that allows a class to have two or more methods having same name, if their argument lists are different.

### Virtual Methods
A virtual function or virtual method is a function or method whose behaviour can be overridden within an inheriting class by a function with the same signature. 

Java interface methods are all "pure virtual" because they are designed to be overridden. For example:
```java
interface Bicycle {         //the function applyBrakes() is virtual because
    void applyBrakes();     //functions in interfaces are designed to be 
}                           //overridden.

class ACMEBicycle implements Bicycle {
    public void applyBrakes(){               //Here we implementing applyBrakes()
       System.out.println("Brakes applied"); //function, proving it is virtual.
    }
}
```

Java Abstract classes contain implicitly "virtual" methods, implemented by classes extending it. For example:
```java
abstract class Dog {                   
    final void bark() {               //bark() is not virtual because it is 
        System.out.println("woof");   //final and if you tried to override it
    }                                 //you would get a compile time error.

    abstract void jump();             //jump() is a virtual function because it
}                                     //is part of an abstract class and isn't
                                      //final.  
class MyDog extends Dog{
    void jump(){
        System.out.println("boing");    //here jump() is being overridden, a 
    }                                   //demonstration that it is virtual.
}
public class Runner {
    public static void main(String[] args) {
        MyDog myDog = new MyDog();       //instantiating myDog
        myDog.jump();                    //calling the overridden function jump()
    }
}
```

You can force a function to NOT be virtual in a generic class by making it final.




