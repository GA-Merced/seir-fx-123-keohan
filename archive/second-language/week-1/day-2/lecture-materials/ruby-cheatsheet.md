---
track: "Second Language"
title: "Ruby - Basics"
week: 1
day: 1
type: "lecture"
---
# Ruby - Basics

## Lesson Objectives

1. Basic Syntax
1. Working with the terminal
1. Primitives
1. Methods
1. Flow of Control

## Basic Syntax

- We don't need the keyword `var` anymore
- We don't need to end statements with semi-colons
- Single line comments are a `#`
- most variables use `snake_case` naming conventions

```ruby
long_variable_name = 1 #assign long_variable_name the value of 1. Note snake_case naming convention
```

## Working with the terminal

- The equivalent of console.log() is `puts`
- functions in ruby don't need parentheses

```ruby
puts('hi'); #JavaScript way
puts 'hi'
p 'hi' #shorter syntax
```

We can also retrieve values from user input using `gets`

```ruby
some_var = gets
puts some_var
```

## Primitives

There are primitive data types (int, string, etc), just like in JavaScript

### Types

Ruby gives us the standard types, plus a few extras

#### Strings

```ruby
puts "Ruby" #double quotes
puts 'Python' #single quotes

puts "Ruby".size #string method to get length of string
puts "Ruby".upcase #uppercase a string

puts 23.to_s #convert a number to a string

foo = "bar"
puts "oh hai #{foo}" #string interpolation
puts 'oh hai #{foo}' #string interpolation doesn't work with single quotes

#multi line strings use a different syntax
long_string = <<-EOF
asdfkajsdf;alsdf
asdfkajsdfsdf
asdfjkasdfa
sdfjasdf
EOF
puts long_string
```

<hr>

###### Difference between Puts and Prints
[Puts vs Prints](https://www.rubyguides.com/2018/10/puts-vs-print/)

<hr>

#### Booleans

just like normal:

```ruby
a = false;
b = true;
p a && b #false
p a || b #true
```

#### Integers

```ruby
puts 5 / 2 #rounds
puts 122 #normal
puts 0x7a #hex value
puts 0172 #octal
puts 0b1111010 #binary
puts 23_482_345_629 #long numbers: substitute _ for ,
```

#### Floats

```ruby
p 5.0 / 2 #force a float
p 5.fdiv 2 #float division
p 12.to_f #convert to float
p sprintf "%.4f" % (1/3.0) #format a value
p 1.2e-3 #scientific notation
```

#### [BigDecimal](https://ruby-doc.org/stdlib-2.5.1/libdoc/bigdecimal/rdoc/BigDecimal.html)

```ruby
puts 1.2 - 1.0 #outputs 0.19999999999999996
```

```ruby
require 'bigdecimal'
puts BigDecimal("1.2") - BigDecimal("1.0") #outputs 0.2E0 - scientific notation
```

```ruby
require ('bigdecimal');
puts BigDecimal('0.0000000000001') #output in scientific notation
```

#### [Rational Numbers](https://ruby-doc.org/core-2.4.0/Rational.html)

```ruby
p 2.to_r #outputs (2/1)
p Rational 0.5 #outputs (1/2)
p 2/3r #can use fraction notation
p 2/3r+1 #can combine fraction notation with other math
```

#### nil

```ruby
a = nil #same as null
puts a
```

#### [Symbols](https://www.rubyguides.com/2018/02/ruby-symbols/)

Symbols can be substituted for strings

```ruby
light = 'on' #could do this
light = :on #better to do this
```

When using values that won't change, we can use symbols.  Symbols are like strings, but are faster for the following reasons.

- For each symbol value, there is only one instance.  Symbols with the same value reference the same chunk of memory.

	```ruby
	p :name.object_id
	p :name.object_id #:name is the same object in memory as :name on the previous line
	p "name".object_id
	p "name".object_id #this is a different string, and therefore a different place in memory than "name" above
	```

- Cannot be changed at runtime

	```ruby
	foo = :bar
	foo = :baz #completely different symbol than :bar
	```

- Smaller than String

	```ruby
	p :name.methods.size
	p "name".methods.size
	```

#### Arrays

```ruby
a = [1,4,6]
b = Array(1..6) # 1-6 - called a range
c = Array(1...6) # 1-5
p a
p b
p c
```

#### Hashes

Hashes are essentially like JS objects, but without methods

```ruby
domains = { 'location' => "United States" } #could do this
domains = { :location => "United States" } #better to do this

domains = { 'de' => "Germany", 1 => "Slovakia", :us => "United States" }
p domains
p domains['de']
p domains[1]
p domains[:us]
```

### Everything is an Object

Every possible value has properties and methods:

```ruby
p true.class, false.class
p "Ruby".class
p 1.class
p 4.5.class
p 3_463_456_457.class
p :age.class
p [1, 2, 3].class
p({ :name => "Jane", :age => 17 }.class)
```

### Conversion

Can easily convert between data types:

```ruby
p '4'.to_i #to integer
p 4.to_s #to string
p 5.to_f #to float
p 0.5.to_r #to rational
p "Jane".to_sym #to symbol
```

## Methods

Methods are not objects like in JavaScript

```ruby
def h
	puts 'Hello World!'
end
h()
h
```

with params:

```ruby
def h(name)
	puts "Hello #{name}!"
end
h("Matt")
h "Matt"
```

## Flow of Control

### If/Else

```ruby
a = 5
if a > 1
	p 'greater than 1'
elsif a == 1
	p 'equals 1'
else
	p 'less than 1'
end
```

Can also do the opposite

```ruby
a = 5
unless a >= 1
	p "less than 1"
end
```

Can write it in one line

```ruby
a = 1
puts 'equals 1' if a == 1
puts 'not greater than 1' unless a > 1
```

### While

```ruby
a = 0
while a < 5 do
	p a
	a+=1 //++ does not exist. must do +=1 or another increment number
end
```

while equivalent of unless is `until`

```ruby
a = 0
until a == 5 do
	p a
	a+=1
end
```

### For

```ruby
for i in 0...5 do
	puts i
end
```