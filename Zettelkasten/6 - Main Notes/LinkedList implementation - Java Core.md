
28-10-2025 11:04

Status: #adult

Tags: [[Java Core]]

---
# LinkedList implementation - Java Core

**Singly linked list** - nodes содержат только указатели на next;

**Doubly linked list** - nodes содержат указатели и на next и на previous. 


#### Doubly linked list:
```java
public class LinkedListImpl<T> {  
    private Node<T> begin;  
    private Node<T> end;  
  
    public LinkedListImpl(){  
        this.begin = null;  
        this.end = null;  
    }  
  
    public LinkedListImpl(Collection<T> collection){  
        for (Iterator<T> iter = collection.iterator(); iter.hasNext(); ){  
            T value = iter.next();  
            this.add(value);  
        }  
    }  
  
  
    private class Node<T> {  
        private T value;  
        private Node<T> prev;  
        private Node<T> next;  
  
        Node(T value){  
            this.value = value;  
            this.next = null;  
            this.prev = null;  
        }  
  
        Node(T value, Node<T> prev, Node<T> next){  
            this.value = value;  
            this.next = next;  
            this.prev = prev;  
        }  
    }  
  
    public void add(T element){  
		Node<T> node = this.new Node<>(element);
        if(begin == null){  
            begin = node;  
            end = begin;  
        } else {  
            node.prev = end;  
            end.next = node;  
            end = node;  
        }  
    }  
  
    public T get(int index){  
        if(begin == null)  
            return null;  
        Node<T> ptr = begin;  
        for(int i = 0; i < index; i++){  
            ptr = ptr.next;  
        }  
        return ptr.value;  
    }  
  
    public boolean remove(int index){  
        Node<T> ptr = begin;  
        for(int i = 0; i < index; i++){  
            ptr = ptr.next;  
        }  
        if(ptr == null){  
            return false;  
        } else {  
            Node<T> previousNode = ptr.prev;  
            Node<T> nextNode = ptr.next;  
            previousNode.next = nextNode;  
            nextNode.prev = previousNode;  
            return true;  
        }  
    }  
}
```

----
#### [[LinkedList implementation - Java Core - Flashcards|Link to flashcards]]



---
### References:

- [[Inner Classes Java]]