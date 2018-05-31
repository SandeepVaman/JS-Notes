# Usefull JS Array and Object Methods

## .filter()
Creates a new array based on whether the items of an array pass a certain condition.

### Example 
Create an array of student ages that meet the legal drinking age

```js
const studentsAge = [17,16,18,19,21,17];
const ableToDrink = studentsAge.filter(age => age > 18);

//ableToDrink will be equal to [19, 21]
```
## .map
Creates a new array by manipulating the values in another array. Great for data manipulation and it is often used in React because it is an immutable method.

### Example

Create an array that adds a $ to the beginning of each number.

```js
const numbers = [2,3,4,5];
const dollars = numbers.map( number => '$' + number);

// dollars will be equal to ['$2', '$3', '#4', '#5']
```
## .reduce()
This often overlooked method uses an accumulator to reduce all items in an array to a singke value. Great for calculating totals. The returned value can be of any type(i.e object, array, string, integer).

### Example
Add up the integers in the array.

```js
const numbers = [5,10,15];
const total = numbers.reduce((accumulator, currentValue) => accumulator + currentValue);

//total will be equal to 30.
```
Refer to [MDN link for more examples](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
