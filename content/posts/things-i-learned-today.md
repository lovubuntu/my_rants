---
title: "Things I Learned Today - Chaining in Lodash"
date: 2018-10-24T23:12:58+05:30
draft: true
---
#Things I learned today:

So today I had to implement the following functionality:
I have list of objects from server and users can add/remove objects to it from UI. Once the changes are done, I have to save all the currently selected values by sending it to API. One caveat here is that while saving we have to preserve the attribute values of the objects from the server if they are not removed from the current selection.

##Steps:
1. Get the oldList from server (A)
2. Get the newlySelected items from UI (B)
3. Create a new union array by union of oldList and newList using the id attribute (OldList U NewList)
4. Intersect the union array with newlySelectedItems

In short this is what I had to do:
((A union B) intersection B)

##Data Setup:

```
const oldSelectedList = [
	{id: 1, name: 'apple', serverAttribute1: 'some value', serverAttribute2: 'some value'},
	{id: 2, name: 'orange', serverAttribute1: 'some value', serverAttribute2: 'some value'}
];

const newlySelectedList = [
	{id: 3, name: 'grape'},
	{id: 2, name: 'orange'}
];

//expectedList = [
//	{id: 2, name: 'orange', serverAttribute1: 'some value', serverAttribute2: 'some value'},
//	{id: 3, name: 'grape'}
//];
```

I started off with lodash like this:

```
let _ = require('lodash');

const unionList = _.unionWith(oldSelectedList, newlySelectedList, (o, n) => o.id === n.id);
const finalList = _.intersectionWith(unionList, newlySelectedList, (u, n) => u.id === n.id);
```

Though this gives the correct answer I was not satisfied. I always liked method chaining because you can see the transformation of input in a flow and also there is no need to name the intermediate variables. I was wondering if there exists a way to use it here. And to my surprise I found that lodash has a provision to do that. So now it looks like this.


```
let _ = require('lodash');

const finalList = _.chain(oldSelectedList)
	.unionWith(newlySelectedList, (o, n) => o.id === n.id)
	.intersectionWith(newlySelectedList, (u, n) => u.id === n.id)
	.value();
```
