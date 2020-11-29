# Managing Draw Order
To manage draw order, you will most likely want to add a new value to your entity data, called something like `depth`, `z` or `layer` that you then sort by every frame before rendering.

To make this performant and correct, you need to use a [stable](https://en.wikipedia.org/wiki/Sorting_algorithm#Stability) sorting algorithm that is fast on almost sorted lists, since most of the time z-order will not change much from one frame to the next and the list will be mostly in the correct order already.

Stability is required so that when sprites are on the same layer, they do not change order every frame, which would cause very irritating flickering.

A very common and easy to implement choice is [Insertion Sort](https://en.wikipedia.org/wiki/Insertion_sort).
