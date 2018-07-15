---
layout: post
title: Explaining Javascript's reduce method
---

As I practice my Javascript skills on CodeWar, I find the reduce method of Javascript is frequently used to solve array related challenges. Let's explore how it works, and how we can use it in programming.

The reduce() method applies a function against an accumulator and each element in the array (from left to right) to reduce it to a single value.

### A Basic example

You have an array of numbers and you want to add them all up:

{% highlight javascript %}
var numbers = [1,2,3,4];
var reducer = function(sum, currentNumber){
    return sum + currentNumber;
}

//1 + 2 + 3 + 4
console.log(numbers.reduce(reducer));
//output: 10
{% endhighlight %}

In this example, Reducer accept two parameters, the sum and the current number. The reduce method cycles through each number in the array much like it would in a for-loop. When the loop starts the sum is the number on the far left (1) and the current number is the one next to it (2).In this particular example, we want to add the current number to the sum. The calculation is repeated for each number in the array, but each time the current number changes to the next number in the array, moving right. When there are no more numbers left in the array the method returns the sum.

### Finding an Average with the Reduce Method In JavaScript
Instead of logging the sum, you could divide the sum by the length of the array before you return a final value.

The way to do this is by taking advantage of the other arguments in the reduce method. The first of those arguments is the index. Much like a for-loop, the index refers to the number of times the reducer has looped over the array. The last argument is the array itself.

{% highlight javascript %}
var numbers = [42, 56, 82];

var reducer = function(total, number, index, array){
  total += number;

  if( index === array.length-1) {
      return total/array.length;
  }else {
      return total;
  }  
}
//(42+56+82)/3
console.log(numbers.reduce(reducer));
//output: 60
{% endhighlight %}

### Map and Filter as Reductions
If you can use the reduce function to spit out an average then you can use it any way you want.

For example, you could double the total, or half each number before adding them together, or use an if statement inside the reducer to only add numbers that are greater than 50. And you don’t always have to return a single value. You can reduce an array into a new array.

For instance, lets reduce an array of numbers into another array where every number is doubled. To do this we need to set the initial value for our accumulator to an empty array. The initial value is the value of the total parameter when the reduction starts. You set the initial value by adding a comma followed by your initial value inside the parenthesis but after the curly braces. The reason of doing this is to make sure the reducer function iterates over each number in the array. If you don't set the initial value, reduce method will automatically take the first number in the array as initial value, hence the iteration of reducer function will start from the second number in the array, which will produce a unexpected result.

{% highlight javascript %}
var numbers = [1, 2, 3, 4];
var reducer = function(total, number){
    total.push(number*2);
    return total;
}

console.log(numbers.reduce(reducer, []));
//output: [2, 4, 6, 8]
{% endhighlight %}

By setting the initial value to an empty array we can then push each amount into the total. If we want to reduce an array of values into another array where every number is doubled, we need to push the number * 2. Then we return the total when there are no more numbers to push.

### Counting appearance with Reduce in JavaScript
You have a collection of items and you want to know how many of each item are in the collection.

{% highlight javascript %}
var fruitBasket = ['banana', 'cherry', 'orange', 'apple', 'cherry', 'orange', 'apple', 'banana', 'cherry', 'orange', 'fig' ];
var reducer = function(tally, fruit){
  tally[fruit] = (tally[fruit] || 0) + 1 ;
  return tally;
}

console.log(fruitBasket.reduce(reducer, {}));
//output: {apple: 2, banana: 2, cherry: 3, fig: 1, orange: 3}
{% endhighlight %}

To tally items in an array our initial value must be an empty object, not an empty array like it was in the last example.

### Flattening an array of arrays with the Reduce Method In JavaScript​​
We can use reduce to flatten nested amounts into a single array.

We set the initial value to an empty array and then concatenate the current value to the total.
{% highlight javascript %}
var numbers = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
var reducer = function(total, amount){
    return total.concat(amount);
}

console.log(numbers.reduce(reducer, []));
//output: [1, 2, 3, 4, 5, 6, 7, 8, 9]
{% endhighlight %}

More often than not, information is nested in more complicated ways. For instance, lets say we just want all the colors in the data variable below.

{% highlight javascript %}
var data = [
  {a: 'happy', b: 'robin', c: ['blue','green']},
  {a: 'tired', b: 'panther', c: ['green','black','orange','blue']},
  {a: 'sad', b: 'goldfish', c: ['green','red']}
];
{% endhighlight %}

We’re going to step through each object and pull out the colours. We do this by pointing amount.c for each object in the array. We then use a forEach loop to push every value in the nested array into out total.

{% highlight javascript %}
var reducer = function(total, amount){
  amount.c.forEach(function(color){
    total.push(color);
  });

  return total;
}

console.log(data.reduce(reducer, []));
//output: ['blue','green','green','black','orange','blue','green','red']
{% endhighlight %}

If we only need unique number then we can check to see of the number already exists in total before we push it.

{% highlight javascript %}
var reducer = function(total, amount){
  amount.c.forEach(function(color){
    if(total.indexOf(color) === -1){
        total.push(color);
    }
  });

  return total;
}

console.log(data.reduce(reducer, []));
//output: ["blue", "green", "black", "orange", "red"]
{% endhighlight %}

### Piping with Reduce
An interesting aspect of the reduce method in JavaScript is that you can reduce over functions as well as numbers and strings.

Let’s say we have a collection of simple mathematical functions. these functions allow us to increment, decrement, double and halve an amount.

{% highlight javascript %}
function increment(input) { return input + 1;}
function decrement(input) { return input — 1; }
function double(input) { return input * 2; }
function halve(input) { return input / 2; }
{% endhighlight %}

For whatever reason, we need to increment, then double, then decrement an amount.

You could write a function that takes an input, and returns (input + 1) * 2 — 1. The problem is that we know we are going to need to increment the amount three times, then double it, then decrement it, and then halve it at some point in the future. We don’t want to have to rewrite our function every time so we going to use reduce to create a pipeline.

A pipeline is a term used for a list of functions that transform some initial value into a final value. Our pipeline will consist of our three functions in the order that we want to use them.

{% highlight javascript %}
var pipeline = [increment, double, decrement];
{% endhighlight %}

Instead of reducing an array of values we reduce over our pipeline of functions. This works because we set the initial value as the amount we want to transform.

{% highlight javascript %}
var reducer = function(total, func){
    return func(total);
}

console.log(pipeline.reduce(reducer, 1));

// output: 3
{% endhighlight %}

Because the pipeline is an array, it can be easily modified. If we want to decrement something three times, then double it, decrement it , and halve it then we just alter the pipeline.

{% highlight javascript %}
var pipeline = [
  increment,
  increment,
  increment,
  double,
  decrement,
  halve
];
{% endhighlight %}

The reduce function stays exactly the same.

### Mistakes to avoid
If you don’t pass in an initial value, reduce will assume the first item in your array is your initial value. This worked fine in the first few examples because we were adding up a list of numbers.

If you’re trying to tally up fruit, and you leave out the initial value then things get weird. Not entering an initial value is an easy mistake to make and one of the first things you should check when debugging.

Another common mistake is to forget to return the total. You must return something for the reduce function to work. Always double check and make sure that you’re actually returning the value you want.
