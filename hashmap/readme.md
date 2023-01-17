
In Java, the HashMap class uses a combination of an array and a linked list to implement its internal data structure.

When a new key-value pair is added to the HashMap, the key is first passed through a hash function which generates a unique index for the key. This index is used to store the key-value pair in the underlying array.

If multiple keys are mapped to the same index, a linked list is formed to store these key-value pairs at that index. This linked list is called a "bucket".

When the get() method is called and the key is passed to the hash function, it returns the index at which the key-value pair is stored. If there are multiple key-value pairs at that index, the linked list is searched to find the correct key-value pair.

The HashMap also uses a technique called "rehashing" to resolve collisions. When the number of key-value pairs exceeds a certain threshold, the internal array is resized and all the key-value pairs are rehashed to new indices.

In addition, HashMap also uses a technique called "load factor" to check whether the number of key-value pairs exceeds a certain threshold and if so, it increases the size of the internal array to reduce the number of collisions and improve the performance.

It's worth noting that in Java 8, the implementation of HashMap has been changed to use a technique called "open addressing" which is a variation of the linked list, where instead of forming a linked list of all entries that hash to the same index, the entries are placed in the next available spot in the array, this technique reduces the memory overhead of the HashMap.


```
import java.util.LinkedList;

public class MyHashMap<K, V> {
    private int size;
    private int capacity;
    private LinkedList<Node<K, V>>[] buckets;

    public MyHashMap(int initialCapacity) {
        capacity = initialCapacity;
        size = 0;
        buckets = new LinkedList[capacity];
    }

    public void put(K key, V value) {
        int index = key.hashCode() % capacity;
        if (buckets[index] == null) {
            buckets[index] = new LinkedList<>();
        }
        for (Node<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                node.value = value;
                return;
            }
        }
        buckets[index].add(new Node<>(key, value));
        size++;
    }

    public V get(K key) {
        int index = key.hashCode() % capacity;
        if (buckets[index] == null) {
            return null;
        }
        for (Node<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                return node.value;
            }
        }
        return null;
    }

    public boolean containsKey(K key) {
        int index = key.hashCode() % capacity;
        if (buckets[index] == null) {
            return false;
        }
        for (Node<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                return true;
            }
        }
        return false;
    }

    public void remove(K key) {
        int index = key.hashCode() % capacity;
        if (buckets[index] == null) {
            return;
        }
        for (Node<K, V> node : buckets[index]) {
            if (node.key.equals(key)) {
                buckets[index].remove(node);
                size--;
                return;
            }
        }
    }

    private static class Node<K, V> {
        K key;
        V value;

        public Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }
}


```

This is a basic example of a HashMap implementation in Java. It uses an array of linked lists to store the key-value pairs, and it uses the hashCode() method to generate an index for each key, and a linked list to store the key-value pairs with the same hash value. This basic example doesn't include the load factor and rehashing technique, but it will give you an idea of how the internal implementation works.
