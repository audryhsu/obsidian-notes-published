---
creation date: 2021-12-07 17:24
aliases: 
tags: 🖥️
---

# [Jest](Jest.md)
`jest` is the most popular open source testing library for JavaScript created by Facebook, a successor to Mocha

Features:
- fast
- performs snapshot testing
- works out of the box due to opinionated

##### Test Basics
- a test suite contains many tests; a test can contain many assertions (e.g. `expect` statements)
- `toBe` and `toEqual` matchers test for value equality and object equality (compares properties) respectively.
- `toThrow` can assert that an error is thrown, but remember to use a function expression so that actual error isn't raised before the test runs.
 - follow [Launch School/130 - Advanced JS/Testing > SEAT Methodology](./Testing.md#SEAT%20Methodology)
- Use code coverage (see the coverage report) to gauge test quality and where additional tests may be needed

### Testing Functions
#### [Mocking](https://jestjs.io/docs/mock-function-api)
Allows you to test functionality that depends on database, network requests, access to files, or any external system *without actually invoking side effect

1. Import module you want to mock into test file
2. `jest.mock('axios')`
3. Use `.mockResolvedValue(<mocked response>)` to mock the response (aka fake response)

`jest.mock()` replaces every function in `axios` module with empty "mock" functions that do nothing and return `undefined`


#### Spying on packages
- Jest can spy on execution of function without affecting how that method works by examining function arguments and whether or not it was called
- can mock entire package by creating `__mock__` folder

```js
expect().toHaveBeenCalled() // check if a spied function has been called
expect().toHaveBeenCalledTimes() // count how many times a spied function has been called
expect().toHaveBeenCalledWith() //check if the function has been called with a specific set of parameters
expect().toHaveBeenLastCalledWith() //check the parameters of the last time the function has been invoked
```

# Using Jest
Install and config the module as a development dependency
```js
$ npm install jest --save-dev
$ touch jest.config.js
```
- Jest requires `.test.js` filenames so it knows which to run.
- Also requires a configuration file in the same directory as the `.test.js`

#### Run jest in npm project
1. Write  tests in a `/test/test.js` file 
2. Add "test" script to npm and run. Use verbose flag to get descriptive error messages
```json
"scripts": {
	"test": "jest --verbose"
	//...
}

$ npm test
```

#### Test suites and units 
- `describe` blocks are used for grouping tests into logical collections, as all tests within a describe block share setup or teardown operations
- each test nested within a `describe` block is one "unit"
	- For debugging purposes, we can run a single test or describe block by using the `-t` flag 
	- Alternatively, we can use jest's `test.only()` method
```bash
# run specific test suite or unit
$ npm test -- -t 'name of the test unit/suit'

# run tests in a specific test.js file
$ npm test -- <test_folder/myfile.test.js>
```


#### Setup objects for testing
Helper setup methods are scoped to a describe block, or can be defined outside of a describe block and apply to *all tests* in the file
- use the `beforeEach` function to create fresh objects before each `test` function.
	- this is handy when you want to work with the same seed data of a databse over and over again
	- there is also the `afterEach` method to repeat teardown
	- also `beforeAll` and `afterAll`


> [!Testing Car Class]-
> ```js
> //car.test.js
> const Car = require('./car');
> 
> describe('The Car class', () => {
>   let car;
>   beforeEach(() => {
>     car = new Car();
>   });
> 
>   test('has four wheels', () => {
>     expect(car.wheels).toBe(4);
>   });
> 
>   test('two newly created cars are object equal', () => {
>     let car2 = new Car();
> 
>     expect(car).toEqual(car2);
>   });
> 
>   test('a newly created car does not have doors', () => {
>     expect(car.doors).toBeUndefined();
>   });
> 
>   test('raises an error when called drive on it', () => {
>     expect(() => car.drive()).toThrow();
>   });
> 
>   test('raises a TypeError when called drive on it', () => {
>     expect(() => car.drive()).toThrow(TypeError);
>   });
> 
>   test('a new car has no mileage info', () => {
>     expect(car.mileageInfo).toBeNull();
>   });
> 
>   test('wheels is truthy', () => {
>     expect(car.wheels).toBeTruthy();
>   });
> 
>   test('array contains car', () => {
>     let arr = [1, 2, 3];
>     arr.push(car);
> 
>     expect(arr).toContain(car);
>   });
> 
>   test('car has wheels', () => {
>     expect(car.wheels).not.toBeUndefined();
>   });
> });
> ```
> 

## Coverage
shows information about the percentage of statements, branches, functions, and lines that we've tested:
```js
jest --coverage todolist.test.js


--------------|----------|----------|----------|----------|-------------------|
File          |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
--------------|----------|----------|----------|----------|-------------------|
All files     |    79.31 |       80 |    73.68 |       84 |                   |
 todo.js      |    90.91 |      100 |    83.33 |    90.91 |                25 |
 todolist.js  |     76.6 |       75 |    71.88 |    82.05 |... 3,97,98,99,108 |
--------------|----------|----------|----------|----------|-------------------|


```

The "% Lines" column shows the most useful data: this is the percentage of program lines that got executed at least once during testing.


---
Tags: [Programming](Programming.md) - [JavaScript](./JavaScript.md)

Reference: [Full List of Assertions](https://jestjs.io/docs/expect)
https://flaviocopes.com/jest/

Related: 