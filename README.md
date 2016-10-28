Linked Lists

Linked lists are data structures designed to store ordered collections of data. That should sound pretty familiar; linked lists are designed to do exactly the same job as arrays. So why would you use one rather than the other? As you'll see, the two have subtly different performance characteristics.

Implementing a linked list
Linked lists work by storing a series of nodes. Each node consists of a value, and a pointer to the next node in the sequence. This is called a singly-linked list. Nodes in a doubly-linked lists also contain a pointer to the previous node. The nodes can be spread out in the memory - the don't have to be stored contiguously.

Initialization and insertion
Let's start building a linked list implementation:

var LinkedList = function() {
    this.length = 0;
    this.head = null;
};
The constructor is nice and straightforward. The linked list has a length, and a head. The head will always contain the first node. In this case we start with an empty first node, represented by null.

LinkedList.prototype.insert = function(index, value) {
    if (index < 0 || index > this.length) {
        throw new Error('Index error');
    }

    var newNode = {
        value: value
    };

    if (index == 0) {
        newNode.next = this.head;
        this.head = newNode;
    }
    else {
        // Find the node which we want to insert after
        var node = this._find(index - 1);
        newNode.next = node.next;
        node.next = newNode;
    }

    this.length++;
};

LinkedList.prototype._find = function(index) {
    var node = this.head;
    for (var i=0; i<index; i++) {
        node = node.next;
    }
    return node;
};
To insert a value into the linked list, you first create a new node represented by a JavaScript object. This will contain two properties: value, which will hold the value, and next, which will hold the link to the next node.

If you are inserting the value into the start of the list, then you set the new node's next value to the current head of the list, and you set the node to be the new head of the list.

If you are inserting the value into the middle of the list, then you find the node before where it wants to be inserted using the _find method. This simply iterates through the nodes jumping to next next one each time until the correct node has been found.

You then set the new node's next value to the previous node's next value, and you set the previous node's next value to the new node. Basically you are dropping a new node into the list and updating the links to and from the new node.

So, what about performance? When inserting at the front of the list you get a best case of O(1), because you just update the two links. The average and worst case are O(n), because _find needs to iterate over the nodes one by one.

Notice how you spend far less time writing to the memory than you do when inserting into an array. This means that insertions in linked lists are often an order of magnitude faster, despite the fact that both algorithms have O(n) average case complexity.

Retrieval
Retrieving values from the linked list is straightforward. You can just traverse the list using the _find method, and return the value:

LinkedList.prototype.get = function(index) {
    if (index < 0 || index >= this.length) {
        throw new Error('Index error');
    }

    return this._find(index).value;
};
Removal
Removing items from the list is just the inverse of insertion:

LinkedList.prototype.remove = function(index) {
    if (index < 0 || index >= this.length) {
        throw new Error('Index error');
    }

    if (index == 0) {
        this.head = this.head.next;
    }
    else {
        // Find the node before the one we want to remove
        var node = this._find(index - 1);
        node.next = node.next.next;
    }

    this.length--;
};
If you are removing the first item then you update the head reference to point to the second node in the list. Otherwise you find the node before the one you are removing, and update its next pointer to skip over the removed node.

Again, you can see that the best-case performance is O(1), and the average and worst cases are O(n) due to the _find iteration.

Cache locality
As you have seen, there are slight but significant differences in how linked lists and arrays perform. It seems like it shouldn't make too much difference which one you choose. So why do we tend to use arrays more often than linked lists? There is one final performance characteristic which hasn't been discussed yet: cache locality. And to understand that, you need to understand a little bit about how your CPU works.

In our current model, when the CPU tries to access data it loads it directly from the RAM. Memory access is relatively slow however, so in order to speed things up CPU manufacturers started introducing small memory caches into the processor itself. When the processor accesses data from the memory, it also loads some of the data stored nearby into the caches. The theory is that if you have been accessing some data then the likelihood is that you will want to access nearby data also. And because the CPU can access data from its caches more quickly than it can pull data from the RAM, this will make your code run faster.

This theory works fantastically well for arrays. Because they are stored in contiguous memory, then a big chunk of the array will get loaded into the caches, giving you very fast access to the values. In contrast, because the nodes in linked lists are spread throughout the memory then it is unlikely that subsequent nodes will get loaded into the caches. As a result the CPU has to load far more data from the RAM, with a resulting performance penalty.

Interview questions
Write an algorithm to find the middle element of a linked list without using the .length property
Write an algorithm to find the third element from the end of a linked list without using the .length property
Write an algorithm to reverse a linked list
Write an algorithm to find whether a linked list has a cycle (i.e. whether a node in the list has its next value pointing to an earlier node in the list)
