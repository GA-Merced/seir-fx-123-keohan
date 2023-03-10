---
track: "Second Language"
title: "Ruby Enumerable"
week: 1
day: 1
type: "lecture"
---

# Ruby Enumerable

## Lesson Objectives

1. Describe what an enumerable is
1. Common Enumerable Searching Methods
1. Common Enumerable Sorting Methods
1. The Any? and All? Methods
1. The Reduce Method

## Describe what an enumerable is

An enumerable is just a way to iterate over an array and perform some action on it

## Common Enumerable Searching Methods

* **NOTE** - These methods do not alter the original array.

### Select, detect, reject

* **Select** - Returns an array containing all elements of an array for which the given block returns a true value.

``` ruby
p [1,2,3,4,5].select { |i| i > 3 } #returns [4,5]
```

* **Detect** - Returns the *first* element of an array for which the block is true.

``` ruby
p [1,2,3,4,5].detect { |i| i > 3 } #returns [4]
```

* **Reject** - Returns an array for all elements of an array for which the given block returns false.

``` ruby
p [1,2,3,4,5].reject { |i| i > 3 } #returns [1,2,3]
```

### Grep

* Returns an array of every element in the array for which Pattern === element

``` ruby
p [1,3,5,10,15].grep (1..5) #returns [1,3,5]
p [0.3, "three", :three, "thirty-three"].grep /three/ #returns ["three", :three, "thirty-three"]
```

* If an optional block is supplied, each matching element is passed to it, and the block’s result is stored in the output array.

``` ruby
p [1,3,5,10,15].grep (1..5) {|i| i * 3} #returns [3,9,15]
```

## Enumerable Sorting Methods

### Sort and Sort_by

* **Sorting With Strings**  

``` ruby
p ['squirtle', 'mew', 'charmeleon', 'pikachu'].sort
p ['squirtle', 'mew', 'charmeleon', 'pikachu'].sort_by { |word| word.length}
```

* **Sorting With Integers**  

``` ruby
p [2, 5, 1, 3].sort #returns [1, 2, 3, 5]
```

* **Sorting With Strings and Integers**  

``` ruby
p [2, "hello", 1, "what"].sort #ArgumentError: comparison of Fixnum with String failed
p [2, "hello", 1, "what"].sort_by(&:to_i) #["hello", "what", 1, 2]
p [2, "hello", 1, "what"].sort_by(&:to_s) #[1, 2, "hello", "what"]
```  

## Any? and All?

* **Any?** - Passes each element of the collection to the given block. The method returns true if the block returns a value other than false or nil on at least one of the elements.

``` ruby
# %w() is a function that will return an array of the given "words"
p %w(mew pikachu).any? { |word| word.length <= 3 } #returns true
```

* **All?** - Passes each element of the collection to the given block. The method returns true if the block returns a value other than false or nil on *all* of the elements.

``` ruby
p %w(mew pikachu).all? { |word| word.length <= 3 } #returns false
p %w(mew cat her the).all? { |word| word.length <= 3 } #returns true
```

## Reduce

Reduce takes a collection and reduces it down to a single element. It applies an operation to each element, maintaining a running “total.”  

 ``` ruby
p (5..10).reduce(:+) #returns 45
p (1..4).reduce(:*) #returns 24
p [5, 6, 7].reduce(5, :+) #returns 23
```
