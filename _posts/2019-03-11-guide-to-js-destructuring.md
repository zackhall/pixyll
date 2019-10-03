---
layout:     post
title:      Quick Review of JS Destructuring
date:       2019-03-11
summary:    >-
  Declaring variables using JavaScript destructuring can help you write compact,
  cleaner code.
categories: javascript tutorials es2015
---

JavaScript destructuring allows you to quickly unpack values from JavaScript properties and arrays without declaring them one-by-one with the traditional dot syntax.

## Unpacking JS Object Properties

In this first example, let's extract some simple properties from a JS object. Borrowing an example from my real-life, let's say we're building a wedding RSVP app. In the example below, we'll extract the id and RSVP status from the guest invitation.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'pending',
}

var { id, rsvpStatus } = invite
// id === 1
// rsvpStatus === pending
```

This is synonymous with the longer dot notation.

```javascript
var id = invite.id
var rsvpStatus = invite.rsvpStatus
```

### Accessing Nested Object Properties

If we want to access nested objects, we can do that by simply matching the structure of the object.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'pending',
}

var { name: { givenName, surname } = invite
// givenName === 'Zack'
// surname === 'Hall'
```

### Renaming Object Properties
If you'd like to give the newly declared variable a different name than that which is used in the original object, you can do so by providing the new name separated with a colon.

I often forget the order for renaming an object with destructuring. Do I place the property name before the new variable name? Or vice versa? To remember this, I think of it as a mini-function, with the original name as the input on the left of the colon and the desired name as the output on the right.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'pending',
}

var { rsvpStatus: status } = invite
// status === 'pending'
// rsvpStatus is undeclared
```

You can do this for nested objects as well.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'pending',
}

var { name: { givenName: firstName, surname: lastName } = invite
// firstName === 'Zack'
// lastName === 'Hall'
```

### Providing defaults

Another helpful feature of destructuring is the ability to provide default values.  To provide a default value, simple provide the default after an equals sign.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'confirmed',
}

var { rsvpStatus = 'pending', foodPreference = 'chicken' } = invite
// rsvpStatus === 'confirmed'
// foodPreference === 'chicken' since no value was specified
```


This is especially helpful when you have a function which accepts an object as a parameter.

```javascript
function createRSVP({ name, rsvpStatus = 'pending' }) {
  ...
}

```

### Combining defaults and renaming

Yep, you guessed it. You can of course provide a default and rename the property at the same time.

```javascript
var invite = {
  id: 1,
  name: {
    givenName: 'Zack',
    surname: 'Hall',
  rsvpStatus: 'confirmed',
}

var { rsvpStatus: status = 'pending' } = invite
/// status === 'confirmed'
```

## Array Destructuring

Array destructuring allows you to unpack objects from an array based on their positioning. I find this to be less common. One particularly useful use case though is combining destructuring with the `split` function on a string. Take the following example where we might be gathering data from a CSV input source.

```javascript
// Example CSV Data
// ID,Name,Status
var csv = [
  '1,Zack,pending',
  '2,Vivian,confirmed',
  '3,Eric,confirmed',
]

csv.forEach((line) => {
  var [id, name, status] = line
  // use data here
})
```



