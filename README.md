# IterTools implementation for TypeScript

[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Smoren/itertools-ts/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Smoren/itertools-ts/?branch=master)
[![Coverage Status](https://coveralls.io/repos/github/Smoren/itertools-ts/badge.svg?branch=master)](https://coveralls.io/github/Smoren/itertools-ts?branch=master)
![Build and test](https://github.com/Smoren/itertools-ts/actions/workflows/test_master.yml/badge.svg)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Inspired by Python — designed for TypeScript.

Features
--------

IterTools makes you an iteration superstar by providing two types of tools:

* Loop iteration tools
* Stream iteration tools

**Loop Iteration Tools Example**

```typescript
import { multi } from 'itertools-ts';

for (const [letter, number] of multi.zip(['a', 'b'], [1, 2])) {
  console.log(`${letter}${number}`);  // a1, b2
}
```

**Stream Iteration Tools Example**

```typescript
import { Stream } from 'itertools-ts';

const result = Stream.of([1, 1, 2, 2, 3, 4, 5])
  .distinct()             // [1, 2, 3, 4, 5]
  .map((x) => x**2)       // [1, 4, 9, 16, 25]
  .filter((x) => x < 10)  // [1, 4, 9]
  .toSum();               // 14
```

All functions work on iterable collections and iterators:
* `Array`
* `Set`
* `Map`
* `String`
* `Generator`
* `Iterable`
* `Iterator`

Setup
-----

```bash
npm i itertools-ts
```

Quick Reference
---------------

### Loop Iteration Tools

#### Multi Iteration
| Iterator                     | Description                                                                             | Code Snippet                          |
|------------------------------|-----------------------------------------------------------------------------------------|---------------------------------------|
| [`chain`](#Chain)            | Chain multiple iterables together                                                       | `multi.chain(list1, list2, ...)`      |
| [`zip`](#Zip)                | Iterate multiple collections simultaneously until the shortest iterator completes       | `multi.zip(list1, list2, ...)`        |
| [`zipEqual`](#Zip-Equal)     | Iterate multiple collections of equal length simultaneously, error if lengths not equal | `multi.zipEqual(list1, list2, ...)`   |
| [`zipLongest`](#Zip-Longest) | Iterate multiple collections simultaneously until the longest iterator completes        | `multi.zipLongest(list1, list2, ...)` |

#### Single Iteration
| Iterator                                 | Description                                 | Code Snippet                                            |
|------------------------------------------|---------------------------------------------|---------------------------------------------------------|
| [`chunkwise`](#Chunkwise)                | Iterate by chunks                           | `single.chunkwise(data, chunkSize)`                     |
| [`chunkwiseOverlap`](#Chunkwise-Overlap) | Iterate by overlapped chunks                | `single.chunkwiseOverlap(data, chunkSize, overlapSize)` |
| [`enumerate`](#Enumerate)                | Enumerates elements of collection           | `single.enumerate(data)`                                |
| [`filter`](#Filter)                      | Filter for elements where predicate is true | `single.filter(data, predicate)`                        |
| [`flatMap`](#Flat-Map)                   | Map function onto items and flatten result  | `single.flatMap(data, mapper)`                          |
| [`flatten`](#Flatten)                    | Flatten multidimensional iterable           | `single.flatten(data, [dimensions])`                    |
| [`limit`](#Limit)                        | Iterate up to a limit                       | `single.limit(data, limit)`                             |
| [`map`](#Map)                            | Map function onto each item                 | `single.map(data, mapper)`                              |
| [`pairwise`](#Pairwise)                  | Iterate successive overlapping pairs        | `single.pairwise(data)`                                 |
| [`repeat`](#Repeat)                      | Repeat an item a number of times            | `single.repeat(item, repetitions)`                      |
| [`slice`](#Slice)                        | Extract a slice of the iterable             | `single.slice(data, [start], [count], [step])`          |

#### Reduce
| Reducer                | Description                            | Code Snippet                                  |
|------------------------|----------------------------------------|-----------------------------------------------|
| [`toCount`](#To-Count) | Reduce to length of iterable           | `reduce.toCount(data)`                        |
| [`toMax`](#To-Max)     | Reduce to its greatest element         | `reduce.toMax(numbers, [compareBy])`          |
| [`toMin`](#To-Min)     | Reduce to its smallest element         | `reduce.toMin(numbers, [compareBy])`          |
| [`toSum`](#To-Sum)     | Reduce to the sum of its elements      | `reduce.toSum(numbers)`                       |
| [`toValue`](#To-Value) | Reduce to value using callable reducer | `reduce.toValue(data, reducer, initialValue)` |

#### Set and multiset Iteration
| Iterator                | Description                 | Code Snippet         |
|-------------------------|-----------------------------|----------------------|
| [`distinct`](#Distinct) | Iterate only distinct items | `set.distinct(data)` |

#### Summary
| Summary                      | Description                    | Code Snippet               |
|------------------------------|--------------------------------|----------------------------|
| [`isIterable`](#Is-Iterable) | True if given data is iterable | `summary.isIterable(data)` |
| [`isIterator`](#Is-Iterator) | True if given data is iterator | `summary.isIterator(data)` |

#### Transform
| Iterator                     | Description                       | Code Snippet                 |
|------------------------------|-----------------------------------|------------------------------|
| [`toArray`](#To-Array)       | Transforms collection to array    | `transform.toArray(data)`    |
| [`toIterable`](#To-Iterable) | Transforms collection to iterable | `transform.toIterable(data)` |
| [`toIterator`](#To-Iterator) | Transforms collection to iterator | `transform.toIterator(data)` |

### Stream Iteration Tools
#### Stream Sources
| Source                 | Description                      | Code Snippet          |
|------------------------|----------------------------------|-----------------------|
| [`of`](#Of)            | Create a stream from an iterable | `Stream.of(iterable)` |
| [`ofEmpty`](#Of-Empty) | Create an empty stream           | `Stream.ofEmpty()`    |

#### Stream Operations
| Operation                                  | Description                                                                               | Code Snippet                                  |
|--------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------------------------|
| [`chainWith`](#Chain-With)                 | Chain iterable source withs given iterables together into a single iteration              | `stream.chainWith(...iterables)`              |
| [`chunkwise`](#Chunkwise-1)                | Iterate by chunks                                                                         | `stream.chunkwise(chunkSize)`                 |
| [`chunkwiseOverlap`](#Chunkwise-Overlap-1) | Iterate by overlapped chunks                                                              | `stream.chunkwiseOverlap(chunkSize, overlap)` |
| [`distinct`](#Distinct-1)                  | Filter out elements: iterate only unique items                                            | `stream.distinct()`                           |
| [`enumerate`](#Enumerate-1)                | Enumerates elements of stream                                                             | `stream.enumerate()`                          |
| [`filter`](#Filter-1)                      | Filter for only elements where the predicate function is true                             | `stream.filter(predicate)`                    |
| [`flatMap`](#Flat-Map-1)                   | Map function onto elements and flatten result                                             | `stream.flatMap(mapper)`                      |
| [`flatten`](#Flatten-1)                    | Flatten multidimensional stream                                                           | `stream.flatten([dimensions])`                |
| [`limit`](#Limit-1)                        | Limit the stream's iteration                                                              | `stream.limit(limit)`                         |
| [`map`](#Map-1)                            | Map function onto elements                                                                | `stream.map(mapper)`                          |
| [`pairwise`](#Pairwise-1)                  | Return pairs of elements from iterable source                                             | `stream.pairwise()`                           |
| [`slice`](#Slice-1)                        | Extract a slice of the stream                                                             | `stream.slice([start], [count], [step])`      |
| [`zipWith`](#Zip-With)                     | Iterate iterable source with another iterable collections simultaneously                  | `stream.zipWith(...iterables)`                |
| [`zipLongestWith`](#Zip-Longest-With)      | Iterate iterable source with another iterable collections simultaneously                  | `stream.zipLongestWith(...iterables)`         |
| [`zipEqualWith`](#Zip-Equal-With)          | Iterate iterable source with another iterable collections of equal lengths simultaneously | `stream.zipEqualWith(...iterables)`           |

#### Stream Terminal Operations
##### Transformation Terminal Operations
| Terminal Operation       | Description                      | Code Snippet       |
|--------------------------|----------------------------------|--------------------|
| [`toArray`](#To-Array-1) | Returns array of stream elements | `stream.toArray()` |

##### Reduction Terminal Operations
| Terminal Operation       | Description                                 | Code Snippet                            |
|--------------------------|---------------------------------------------|-----------------------------------------|
| [`toCount`](#To-Count-1) | Reduces stream to its length                | `stream.toCount()`                      |
| [`toMax`](#To-Max-1)     | Reduces stream to its max value             | `stream.toMax([compareBy])`             |
| [`toMin`](#To-Min-1)     | Reduces stream to its min value             | `stream.toMin([compareBy])`             |
| [`toSum`](#To-Sum-1)     | Reduces stream to the sum of its items      | `stream.toSum()`                        |
| [`toValue`](#To-Value-1) | Reduces stream like array.reduce() function | `stream.toValue(reducer, initialValue)` |

Usage
-----

## Multi Iteration
### Chain
Chain multiple iterables together into a single continuous sequence.

```
function *chain(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Iterable<unknown>
```
```typescript
import { multi } from 'itertools-ts';

const prequels = ['Phantom Menace', 'Attack of the Clones', 'Revenge of the Sith'];
const originals = ['A New Hope', 'Empire Strikes Back', 'Return of the Jedi'];

for (const movie of multi.chain(prequels, originals)) {
  console.log(movie);
}
// 'Phantom Menace', 'Attack of the Clones', 'Revenge of the Sith', 'A New Hope', 'Empire Strikes Back', 'Return of the Jedi'
```

### Zip
Iterate multiple iterable collections simultaneously.

```
function *zip(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Iterable<Array<unknown>>
```

```typescript
import { multi } from 'itertools-ts';

const languages = ['PHP', 'Python', 'Java', 'Go'];
const mascots = ['elephant', 'snake', 'bean', 'gopher'];

for (const [language, mascot] of multi.zip(languages, mascots)) {
  console.log(`The ${language} language mascot is an ${mascot}.`);
}
// The PHP language mascot is an elephant.
// ...
```

Zip works with multiple iterable inputs - not limited to just two.
```typescript
import { multi } from 'itertools-ts';

const names          = ['Ryu', 'Ken', 'Chun Li', 'Guile'];
const countries      = ['Japan', 'USA', 'China', 'USA'];
const signatureMoves = ['hadouken', 'shoryuken', 'spinning bird kick', 'sonic boom'];

for (const [name, country, signatureMove] of multi.zip(names, countries, signatureMoves)) {
  const streetFighter = new StreetFighter(name, country, signatureMove);
}
```
Note: For uneven lengths, iteration stops when the shortest iterable is exhausted.

### Zip Longest
Iterate multiple iterable collections simultaneously.

```
function *zipLongest(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Iterable<Array<unknown>>
```

For uneven lengths, the exhausted iterables will produce `undefined` for the remaining iterations.

```typescript
import { multi } from 'itertools-ts';

const letters = ['A', 'B', 'C'];
const numbers = [1, 2];

for (const [letter, number] of multi.zipLongest(letters, numbers)) {
  // ['A', 1], ['B', 2], ['C', undefined]
}
```

### Zip Equal
Iterate multiple iterable collections with equal lengths simultaneously.

Throws `LengthException` if lengths are not equal, meaning that at least one iterator ends before the others.

```
function *zipEqual(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Iterable<Array<unknown>>
```

```typescript
import { multi } from 'itertools-ts';

const letters = ['A', 'B', 'C'];
const numbers = [1, 2, 3];

for (const [letter, number] of multi.zipEqual(letters, numbers)) {
    // ['A', 1], ['B', 2], ['C', 3]
}
```

## Single Iteration
### Chunkwise
Return elements in chunks of a certain size.

```
function *chunkwise<T>(
  data: Iterable<T>|Iterator<T>, 
  chunkSize: number,
): Iterable<Array<T>>
```

Chunk size must be at least 1.

```typescript
import { single } from 'itertools-ts';

const movies = [
    'Phantom Menace', 'Attack of the Clones', 'Revenge of the Sith',
    'A New Hope', 'Empire Strikes Back', 'Return of the Jedi',
    'The Force Awakens', 'The Last Jedi', 'The Rise of Skywalker',
];
const trilogies = [];

for (const trilogy of single.chunkwise(movies, 3)) {
    trilogies.push(trilogy);
}
// [
//     ['Phantom Menace', 'Attack of the Clones', 'Revenge of the Sith'],
//     ['A New Hope', 'Empire Strikes Back', 'Return of the Jedi'],
//     ['The Force Awakens', 'The Last Jedi', 'The Rise of Skywalker]',
// ]
```

### Chunkwise Overlap
Return overlapped chunks of elements.

```
function *chunkwiseOverlap<T>(
  data: Iterable<T>|Iterator<T>,
  chunkSize: number,
  overlapSize: number,
  includeIncompleteTail: boolean = true,
): Iterable<Array<T>>
```

* Chunk size must be at least 1.
* Overlap size must be less than chunk size.

```typescript
import { single } from 'itertools-ts';

const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

for (const chunk of single.chunkwiseOverlap(numbers, 3, 1)) {
  // [1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9], [9, 10]
}
```

### Enumerate
Enumerates elements of given collection.

```
function *enumerate<T>(data: Iterable<T>|Iterator<T>): Iterable<[number, T]>
```

```typescript
import { single } from 'itertools-ts';

const letters = ['a', 'b', 'c', 'd', 'e'];

for (const item of single.enumerate(letters)) {
  // [[0, 'a'], [1, 'b'], [2, 'c'], [3, 'd'], [4, 'e']]
}
```

### Filter
Filter out elements from the iterable only returning elements where the predicate function is true.

```
function *filter<T>(
  data: Iterable<T>|Iterator<T>,
  predicate: (datum: T) => boolean,
): Iterable<T>
```

```typescript
import { single } from 'itertools-ts';

const starWarsEpisodes = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const goodMoviePredicate = (episode) => episode > 3 && episode < 8;

for (const goodMovie of single.filter(starWarsEpisodes, goodMoviePredicate)) {
  console.log(goodMovie);
}
// 4, 5, 6, 7
```

### Flat Map
Map a function only the elements of the iterable and then flatten the results.

```
function *flatMap<TInput, TOutput>(
  data: Iterable<TInput>|Iterator<TInput>,
  mapper: FlatMapper<TInput, TOutput>,
): Iterable<TOutput>
```

```typescript
import { single } from 'itertools-ts';

const data = [1, 2, 3, 4, 5];
const mapper = (item) => [item, -item];

for (number of single.flatMap(data, mapper)) {
  console.log(number);
}
// 1 -1 2 -2 3 -3 4 -4 5 -5
```

### Flatten
Flatten a multidimensional iterable.

```
function *flatten(
  data: Iterable<unknown>|Iterator<unknown>,
  dimensions: number = Infinity,
): Iterable<unknown>
```

```typescript
import { single } from 'itertools-ts';

const multidimensional = [1, [2, 3], [4, 5]];

const flattened = [];
for (const number of single.flatten(multidimensional)) {
    flattened.push(number);
}
// [1, 2, 3, 4, 5]
```

### Limit
Iterate up to a limit.

Stops even if more data available if limit reached.

```
function *limit<T>(data: Iterable<T>|Iterator<T>, count: number): Iterable<T>
```

```typescript
import { single } from 'itertools-ts';

const matrixMovies = ['The Matrix', 'The Matrix Reloaded', 'The Matrix Revolutions', 'The Matrix Resurrections'];
const limit = 1;

for (const goodMovie of single.limit(matrixMovies, limit)) {
    console.log(goodMovie);
}
// 'The Matrix' (and nothing else)
```

### Map
Map a function onto each element.

```
function *map<TInput, TOutput>(
  data: Iterable<TInput>|Iterator<TInput>,
  mapper: (datum: TInput) => TOutput,
): Iterable<TOutput>
```

```typescript
import { single } from 'itertools-ts';

const grades = [100, 99, 95, 98, 100];
const strictParentsOpinion = (g) => (g === 100) ? 'A' : 'F';

for (const actualGrade of single.map(grades, strictParentsOpinion)) {
  console.log(actualGrade);
}
// A, F, F, F, A
```

### Pairwise
Returns successive overlapping pairs.

Returns empty generator if given collection contains fewer than 2 elements.

```
function *pairwise<T>(data: Iterable<T>|Iterator<T>): Iterable<Pair<T>>
```

```typescript
import { single } from 'itertools-ts';

const friends = ['Ross', 'Rachel', 'Chandler', 'Monica', 'Joey', 'Phoebe'];

for (const [leftFriend, rightFriend] of single.pairwise(friends)) {
  console.log(`${leftFriend} and ${rightFriend}`);
}
// Ross and Rachel, Rachel and Chandler, Chandler and Monica, ...
```

### Repeat
Repeat an item.

```
function *repeat<T>(item: T, repetitions: number): Iterable<T>
```

```typescript
import { single } from 'itertools-ts';

data = 'Beetlejuice';
repetitions = 3;

for (const repeated of single.repeat(data, repetitions)) {
  console.log(repeated);
}
// 'Beetlejuice', 'Beetlejuice', 'Beetlejuice'
```

### Slice
Extract a slice of the iterable.

```
function *slice<T>(
  data: Iterable<T>|Iterator<T>,
  start: number = 0,
  count?: number,
  step: number = 1,
): Iterable<T>
```

```typescript
import { single } from 'itertools-ts';

const olympics = [1992, 1994, 1996, 1998, 2000, 2002, 2004, 2006, 2008, 2010, 2012, 2014, 2016, 2018, 2020, 2022];
const winterOlympics = [];

for (const winterYear of single.slice(olympics, 1, 8, 2)) {
    winterOlympics.push(winterYear);
}
// [1994, 1998, 2002, 2006, 2010, 2014, 2018, 2022]
```

## Reduce
### To Count
Reduces iterable to its length.

```
function toCount(data: Iterable<unknown>|Iterator<unknown>): number
```

```typescript
import { reduce } from 'itertools-ts';

const data = [1, 2, 3];

const length = reduce.toCount(data);
// 3
```

### To Max
Reduces to the max value.

```
function toMax<TValue, TComparable>(
  data: Iterable<TValue>|Iterator<TValue>,
  compareBy?: (datum: TValue) => TComparable,
): TValue|undefined
```

- Optional callable param `compareBy` must return comparable value.
- If `compareBy` is not provided then items of given collection must be comparable.
- Returns `undefined` if collection is empty.

```typescript
import { reduce } from 'itertools-ts';

const numbers = [5, 3, 1, 2, 4];

const result = reduce.toMax(numbers);
// 1

const movieRatings = [
  {
    title: 'The Matrix',
    rating: 4.7,
  },
  {
    title: 'The Matrix Reloaded',
    rating: 4.3,
  },
  {
    title: 'The Matrix Revolutions',
    rating: 3.9,
  },
  {
    title: 'The Matrix Resurrections',
    rating: 2.5,
  },
];
const compareBy = (movie) => movie.rating;

const lowestRatedMovie = reduce.toMin(movieRatings, compareBy);
// {
//   title: 'The Matrix',
//   rating: 4.7,
// }
```

### To Min
Reduces to the min value.

```
function toMin<TValue, TComparable>(
  data: Iterable<TValue>|Iterator<TValue>,
  compareBy?: (datum: TValue) => TComparable,
): TValue|undefined
```

- Optional callable param `compareBy` must return comparable value.
- If `compareBy` is not provided then items of given collection must be comparable.
- Returns `undefined` if collection is empty.

```typescript
import { reduce } from 'itertools-ts';

const numbers = [5, 3, 1, 2, 4];

const result = reduce.toMin(numbers);
// 1

const movieRatings = [
  {
    title: 'The Matrix',
    rating: 4.7,
  },
  {
    title: 'The Matrix Reloaded',
    rating: 4.3,
  },
  {
    title: 'The Matrix Revolutions',
    rating: 3.9,
  },
  {
    title: 'The Matrix Resurrections',
    rating: 2.5,
  },
];
const compareBy = (movie) => movie.rating;

const lowestRatedMovie = reduce.toMin(movieRatings, compareBy);
// {
//   title: 'The Matrix Resurrections',
//   rating: 2.5,
// }
```

### To Sum
Reduces to the sum of its elements.

For maps uses values for accumulating sum.

```
function toSum(data: Iterable<number>|Iterator<number>|Map<unknown, number>): number
```

```typescript
import { reduce } from 'itertools-ts';

const parts = [10, 20, 30];

const sum = reduce.toSum(parts);
// 60
```

### To Value
Reduce elements to a single value using reducer function.

```
function toValue<TInput, TOutput>(
  data: Iterable<TInput>|Iterator<TInput>,
  reducer: (carry: TOutput|undefined, datum: TInput) => TOutput,
  initialValue?: TOutput,
): TOutput|undefined
```

```typescript
import { reduce } from 'itertools-ts';

const input = [1, 2, 3, 4, 5];
const sum = (carry, item) => carry + item;

const result = reduce.toValue(input, sum, 0);
// 15
```

## Set and multiset
### Distinct
Filter out elements from the iterable only returning distinct elements.

```
function *distinct<T>(data: Iterable<T>|Iterator<T>): Iterable<T>
```

```typescript
import { set } from 'itertools-ts';

const chessSet = ['rook', 'rook', 'knight', 'knight', 'bishop', 'bishop', 'king', 'queen', 'pawn', 'pawn'];

for (const chessPiece of set.distinct(chessSet)) {
  console.log(chessPiece);
}
// rook, knight, bishop, king, queen, pawn
```

## Summary
### Is Iterable
Returns true if given data is an `Iterable` instance.

```
function isIterable(input: unknown): boolean
```

```typescript
import { summary } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

summary.isIterable(input); // true
summary.isIterable(input[Symbol.iterator]()) // false
summary.isIterable(1); // false
```

### Is Iterator
Returns true if given data is an `Iterator` instance.

```
function isIterator(input: unknown): boolean
```

```typescript
import { summary } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

summary.isIterator(input[Symbol.iterator]()) // true
summary.isIterator(input); // false
summary.isIterator(1); // false
```

## Transform
### To Iterable
Returns `Iterable` instance of given collection or iterator.

Throws `InvalidArgumentError` if given data is not a collection or an iterator.

```
function toIterable<T>(collection: Iterable<T>|Iterator<T>): Iterable<T>
```

```typescript
import { transform } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const result = transform.toIterable(input);
// [1, 2, 3, 4, 5]
```

### To Array
Returns `Array` instance of given collection or iterator.

```
function toArray<T>(collection: Iterable<T>|Iterator<T>): Array<T>
```

```typescript
import { transform } from "itertools-ts";

const iterator = transform.toIterator([1, 2, 3, 4, 5]);

const result = transform.toArray(iterator);
// [1, 2, 3, 4, 5]
```

### To Iterator
Returns `Iterator` instance of given collection or iterator.

Throws `InvalidArgumentError` if given data is not a collection or an iterator.

```
function toIterator<T>(collection: Iterable<T>|Iterator<T>): Iterator<T>
```

```typescript
import { transform } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const result = transform.toIterator(input);
console.log(result.next !== undefined);
// true
```

## Stream
### Stream Sources
#### Of
Creates stream from an iterable.

```
Stream.of(data: Iterable<unknown>|Iterator<unknown>): Stream
```

```typescript
import { Stream } from "itertools-ts";

const iterable = [1, 2, 3];

const result = Stream.of(iterable)
  .chainWith([4, 5, 6], [7, 8, 9])
  .zipEqualWith([1, 2, 3, 4, 5, 6, 7, 8, 9])
  .toArray();
// [[1, 1], [2, 2], [3, 3], [4, 4], [5, 5], [6, 6], [7, 7], [8, 8], [9, 9]]
```

#### Of Empty
Creates stream of nothing.

```
Stream.ofEmpty(): Stream
```

```typescript
import { Stream } from "itertools-ts";

const result = Stream.ofEmpty()
  .chainWith([1, 2, 3])
  .toArray();
// 1, 2, 3
```

### Stream Operations
#### Chain With
Return a stream chaining additional sources together into a single consecutive stream.

```
stream.chainWith(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Stream
```

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3];

const result = Stream.of(input)
  .chainWith([4, 5, 6])
  .chainWith([7, 8, 9])
  .toArray();
// 1, 2, 3, 4, 5, 6, 7, 8, 9
```

#### Chunkwise
Return a stream consisting of chunks of elements from the stream.

```
stream.chunkwise(chunkSize: number): Stream
```

Chunk size must be at least 1.

```typescript
import { Stream } from "itertools-ts";

const friends = ['Ross', 'Rachel', 'Chandler', 'Monica', 'Joey'];

const result = Stream.of(friends)
  .chunkwise(2)
  .toArray();
// ['Ross', 'Rachel'], ['Chandler', 'Monica'], ['Joey']
```

#### Chunkwise Overlap
Return a stream consisting of overlapping chunks of elements from the stream.

```
chunkwiseOverlap(
  chunkSize: number,
  overlapSize: number,
  includeIncompleteTail: boolean = true,
): Stream
```

* Chunk size must be at least 1.
* Overlap size must be less than chunk size.

```typescript
import { Stream } from "itertools-ts";

const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];

const result = Stream.of(numbers)
  .chunkwiseOverlap(3, 1)
  .toArray()
// [1, 2, 3], [3, 4, 5], [5, 6, 7], [7, 8, 9]
```

#### Distinct
Return a stream filtering out elements from the stream only returning distinct elements.

```
stream.distinct(): Stream
```

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 1, 2, 3, 3, '1', '1', '2', '3'];
const stream = Stream.of(input)
  .distinct()
  .toArray();
// 1, 2, 3, '1', '2', '3'
```

#### Enumerate
Enumerates elements of the stream.

```
stream.enumerate(): Stream
```

```typescript
import { Stream } from "itertools-ts";

const input = ['a', 'b', 'c', 'd', 'e'];
const stream = Stream.of(input)
  .enumerate()
  .toArray();
// [[0, 'a'], [1, 'b'], [2, 'c'], [3, 'd'], [4, 'e']]
```

#### Filter
Filter out elements from the stream only keeping elements where there predicate function is true.

```
filter(predicate: (item: unknown) => boolean): Stream
```

```typescript
import { Stream } from "itertools-ts";

const input = [1, -1, 2, -2, 3, -3];

const result = Stream.of(input)
  .filter((value) => value > 0)
  .toArray();
// 1, 2, 3
```

#### Flat Map
Map a function onto the elements of the stream and flatten the results.

```
stream.flatMap(mapper: (datum: unknown) => unknown): Stream
```

```typescript
import { Stream } from "itertools-ts";

const data = [1, 2, 3, 4, 5];
const mapper = (item) => (item % 2 === 0) ? [item, item] : item;

const result = Stream.of(data)
  .flatMap(mapper)
  .toArray();
// [1, 2, 2, 3, 4, 4, 5]
```

#### Flatten
Flatten a multidimensional stream.

```
stream.flatten(dimensions: number = Infinity): Stream
```

```typescript
import { Stream } from "itertools-ts";

const data = [1, [2, 3], [4, 5]];

const result = Stream.of(data)
  .flatten()
  .toArray();
// [1, 2, 3, 4, 5]
```

#### Limit
Return a stream up to a limit.

Stops even if more data available if limit reached.

```
limit(count: number): Stream
```

```typescript
import { Stream } from "itertools-ts";

const matrixMovies = ['The Matrix', 'The Matrix Reloaded', 'The Matrix Revolutions', 'The Matrix Resurrections'];
const limit = 1;

const goodMovies = Stream.of(matrixMovies)
  .limit(limit)
  .toArray();
// 'The Matrix' (and nothing else)
```

#### Map
Return a stream containing the result of mapping a function onto each element of the stream.

```
stream.map(mapper: (datum: unknown) => unknown): Stream
```

```typescript
import { Stream } from "itertools-ts";

const grades = [100, 95, 98, 89, 100];

const result = Stream.of(grades)
  .map((grade) => grade === 100 ? 'A' : 'F')
  .toArray();
// A, F, F, F, A
```

#### Pairwise
Return a stream consisting of pairs of elements from the stream.

```
stream.pairwise(): Stream
```

Returns empty stream if given collection contains less than 2 elements.

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const stream = Stream.of(input)
  .pairwise()
  .toArray();
// [1, 2], [2, 3], [3, 4], [4, 5]
```

#### Slice
Extract a slice of the stream.

```
slice(start: number = 0, count?: number, step: number = 1): Stream
```

```typescript
import { Stream } from "itertools-ts";

const olympics = [1992, 1994, 1996, 1998, 2000, 2002, 2004, 2006, 2008, 2010, 2012, 2014, 2016, 2018, 2020, 2022];

const summerOlympics = Stream.of(olympics)
  .slice(0, 8, 2)
  .toArray();
// [1992, 1996, 2000, 2004, 2008, 2012, 2016, 2020]
```

#### Zip With
Return a stream consisting of multiple iterable collections streamed simultaneously.

```
stream.zipWith(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Stream
```

For uneven lengths, iterations stops when the shortest iterable is exhausted.

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3];

const stream = Stream.of(input)
  .zipWith([4, 5, 6])
  .toArray();
// [1, 4], [2, 5], [3, 6]
```

#### Zip Longest With
Return a stream consisting of multiple iterable collections streamed simultaneously.

```
stream.zipLongestWith(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Stream
```

* Iteration continues until the longest iterable is exhausted.
* For uneven lengths, the exhausted iterables will produce `undefined` for the remaining iterations.

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const stream = Stream.of(input)
  .zipLongestWith([4, 5, 6]);

for (const zipped of stream) {
  // [1, 4], [2, 5], [3, 6], [4, undefined], [5, undefined]
}
```

#### Zip Equal With
Return a stream consisting of multiple iterable collections of equal lengths streamed simultaneously.

```
zipEqualWith(
  ...iterables: Array<Iterable<unknown>|Iterator<unknown>>
): Stream
```

Works like `Stream.zipWith()` method but throws `LengthException` if lengths not equal,
i.e., at least one iterator ends before the others.

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3];

const stream = Stream.of(input)
  .zipEqualWith([4, 5, 6]);

for (const zipped of stream) {
    // [1, 4], [2, 5], [3, 6]
}
```

### Terminal operations
#### Transformation Terminal Operations
##### To Array
Returns an array of stream elements.

```
stream.toArray(): Array<unknown>
```

```typescript
import { Stream } from "itertools-ts";

const result = Stream.of([1, 2, 3, 4, 5])
  .map((x) => x**2)
  .toArray();
// [1, 4, 9, 16, 25]
```

#### Reduce Terminal Operations
##### To Count
Reduces iterable source to its length.

```
toCount(): number
```

```typescript
import { Stream } from "itertools-ts";

const input = [10, 20, 30, 40, 50];

const result = Stream.of(iterable)
  .toCount();
// 5
```

##### To Max
Reduces iterable source to its max value.

```
toMax<TComparable>(compareBy?: (datum: unknown) => TComparable): unknown|undefined
```

- Optional callable param `compareBy` must return comparable value.
- If `compareBy` is not provided then items of given collection must be comparable.
- Returns `undefined` if collection is empty.

```typescript
import { Stream } from "itertools-ts";

const input = [1, -1, 2, -2, 3, -3];

const result = Stream.of(iterable)
  .toMax();
// 3
```

##### To Min
Reduces iterable source to its min value.

```
toMin<TComparable>(compareBy?: (datum: unknown) => TComparable): unknown|undefined
```

- Optional callable param `compareBy` must return comparable value.
- If `compareBy` is not provided then items of given collection must be comparable.
- Returns `undefined` if collection is empty.

```typescript
import { Stream } from "itertools-ts";

const input = [1, -1, 2, -2, 3, -3];

const result = Stream.of(iterable)
  .toMin();
// -3
```

##### To Sum
Reduces iterable source to the sum of its items.

```
stream.toSum(): number
```

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const result = Stream.of(iterable)
  .toSum();
// 15
```

##### To Value
Reduces iterable source like array_reduce() function.

```
toValue<T>(
  reducer: (carry: T|undefined, datum: unknown) => T,
  initialValue?: T,
): T|undefined
```

```typescript
import { Stream } from "itertools-ts";

const input = [1, 2, 3, 4, 5];

const result = Stream.of(input)
  .toValue((carry, item) => carry + item);
// 15
```

Unit testing
------------

```bash
npm i
npm run test
```

License
-------

IterTools TS is licensed under the MIT License.
