---
layout: post
title: React Fundamentals with Tic Tac Toe
description: Learn fundamentals of React by making an interactive Tic Tac Toe game in React
date:   2020-09-12
---
In this post we will be building an interactive Tic Tac Toe game in React to learn the fundamentals of React. However, let's get familiar with React core concepts theoratically first.

### What is React
A Javascript library that is used to build intuitive, modern and beautiful user interfaces.


### Nuts and Bolts of React
React is made up of three major concepts that needs to be understood:
1. Components
2. Props
3. State

#### Components
These are the small and isolated chunks of code. In simple english, component are used to tell React what to display on the screen. Everything in React is made up of components, the heading, buttons etc.

Some important aspects to know about components are:
* Must start with an upper case letter
* Include extends `React.Component` (see example below)
* include a `render()` method which returns the HTML

So let's understand with a very simple example. If you want to place a heading on a web page saying `My name is Zain`, then it will be in its own component like below:
```
class Person extends React.Component {
    render () {
        return (
            <h1>My name is Zain</h1>;
        );
    }
}
``` 
The above code simply says that this is a component called `Person` and it returns heading one, `<h1>`, element in the render method. The `render` method returns a view that is shown to the user. The above code is equivalent to:
```
return React.createElement('div',{ className="container" },
    React.createElement('h1', this is a simple web page!)
);
```
which clearly shows that `<div>` above are converted to  `React.createElement('div')` in compile time. 

As `render` method returns a React element (displays the description of what to show to the user), the syntax of it isn't used by React developers. Instead `<div />` is used which is a lot easier and cleaner to write.

You use the above component in the `container` element or any other element like below:
```
class Creature extends React.Component {
    render () {
        return (
            // This is called a React element
            <div className="containter">
                <Person />
            </div>
        );
    }
}
```



#### Props
These are the parameters that a components takes in. `Props` are short for properties. So, if we wanted to add a name `Zain` as a `prop` (property) to our Person component it would be like:
```
class Creature extends React.Component {
    render () {
        return (
            // This is called a React element
            <div className="containter">
                <Person name="Zain"/>
            </div>
        );
    }
}
```