# Usefull Javascript Array Methods

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

## .forEach()
Applies a function on each item in an array.

###Example.

Log each array item to the console.

```js
const emotions = ['happy', 'sad', 'angry'];
emotions.forEach( emotion => console.log(emotion) );
// Will log the following:
// 'happy'
// 'sad'
// 'angry'
```
## .some()

Checks if any item in an array passes the condition. A good usecase would be checking for user privilages. It can also be used similarly to a .forEach() where you would perform an action on each item and break out of loop once a truthy vaue is returned.

### Example
Check if there is at least one 'admin' in an array.

```js
const userPrivileges = ['user', 'user', 'user', 'admin'];
const containsAdmin = userPrivileges.some( element => element === 'admin');
// containsAdmin will be equal to true

```

## .every()
Similar to .some(), but checks if all items in an array pass a function.

Example 

Check if all ratings are equal to or greater than 3 stars.

```js
const ratings = [3, 5, 4, 3, 5];
const goodOverallRating = ratings.every( rating => rating >= 3 );
// goodOverallRating will be equal to true
```

## .includes()
Checks if an array contains a certain value. It's similar to .some(), but instead of looking for a condition to pass, it looks if the array contains a specific value.

### Example

Checks if the array includes an item with string 'waldo'.

```js
const names = ['sophie', 'george', 'waldo', 'stephen', 'henry'];
const includesWaldo = names.includes('waldo');
// includesWaldo will be equal to true
```
## Array.spread
Spreading arrays using the spread operator (...) allows you to expand the elements in an array. It’s useful when concatenating a bunch of arrays together. It’s also a good way to avoid using the splice() method when looking to remove certain elements from an array because it can be combined with the slice() method to prevent direct mutation of an array.

### Example
Combine two arrays

```js
const spreadableOne = [1, 2, 3, 4];
const spreadableTwo = [5, 6, 7, 8];

const combined = [...spreadableOne, ...spreadableTwo];
// combined will be equal to [1, 2, 3, 4, 5, 6, 7, 8]
```
Remove an array element without mutating the original array.
```js
const animals = ['squirrel', 'bear', 'deer', 'salmon', 'rat'];
const mammals = [...animals.slice(0,3), ...animals.slice(4)];
// mammals will be equal to ['squirrel', 'bear', 'deer', 'rat']
```
