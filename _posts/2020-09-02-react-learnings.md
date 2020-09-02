---
layout: post
title:  React Fundamentals - Part One
description: Learning fundamentals of React by building an interactive Tic Tac Toe game in React
date:   2020-09-02
---
In this blog I will be explaining fundamental concepts of React by building an interactive game called Tic Tac Toe in React that you'll be able to play as well. Just so you know, the scope of this blog is to get you familiar with React only. So, I won't be going through HTML and Javascript code in detail. All the material in here is learnt from [here](https://reactjs.org/tutorial/tutorial.html)

Now, you might be wondering what is React?

### React
Javsacript library for building user interface or UI in short. It's open source and is maintained by Facebook and community of developers. It allows to build complex UI with small components. So we can say that everything in React is a component ( small and isolated chunks of code ). We will discuss components further below.

### Game's Overview
So, one of the feature our interactive Tic Tac Toe game will have is that there will be numbered list on the right side of the 3 x 3 square board which will give the history of all moves happened in the games. It will be also update the games progresses simulataneously.

### Pre-requisites
1. HTML
2. Javascript
3. Basic programming concepts
4. Node.js installed

We will be using arrow functions, classes, let and const statements from ECMASrcipt 6 (ES6). If you are curious what ES6 components compiles to, you can use Babel REPL.

### Set Up React Project
1. Run below command to create a react project in your terminal
```
npx create-react-app my-react-app
```
2. Navigate to the project by running 
```
cd my-react-app
```
3. Open it in your favourite text editor, mine is VS Code.
4. Remove all the files in `src` folder **but not the folder itself**
5. Add the below files to the `src` folder
* `index.css`
* `index.js`
6. Add the below CSS in `index.css`
```
    body {
    font: 14px "Century Gothic", Futura, sans-serif;
    margin: 20px;
    }

    ol, ul {
    padding-left: 30px;
    }

    .board-row:after {
    clear: both;
    content: "";
    display: table;
    }

    .status {
    margin-bottom: 10px;
    }

    .square {
    background: #fff;
    border: 1px solid #999;
    float: left;
    font-size: 24px;
    font-weight: bold;
    line-height: 34px;
    height: 34px;
    margin-right: -1px;
    margin-top: -1px;
    padding: 0;
    text-align: center;
    width: 34px;
    }

    .square:focus {
    outline: none;
    }

    .kbd-navigation .square:focus {
    background: #ddd;
    }

    .game {
    display: flex;
    flex-direction: row;
    }

    .game-info {
    margin-left: 20px;
    }
```
7. Add the below javascript code in `index.js`
```
    class Square extends React.Component {
    render() {
        return (
        <button className="square">
            {/* TODO */}
        </button>
        );
    }
    }

    class Board extends React.Component {
    renderSquare(i) {
        return <Square />;
    }

    render() {
        const status = 'Next player: X';

        return (
        <div>
            <div className="status">{status}</div>
            <div className="board-row">
            {this.renderSquare(0)}
            {this.renderSquare(1)}
            {this.renderSquare(2)}
            </div>
            <div className="board-row">
            {this.renderSquare(3)}
            {this.renderSquare(4)}
            {this.renderSquare(5)}
            </div>
            <div className="board-row">
            {this.renderSquare(6)}
            {this.renderSquare(7)}
            {this.renderSquare(8)}
            </div>
        </div>
        );
    }
    }

    class Game extends React.Component {
    render() {
        return (
        <div className="game">
            <div className="game-board">
            <Board />
            </div>
            <div className="game-info">
            <div>{/* status */}</div>
            <ol>{/* TODO */}</ol>
            </div>
        </div>
        );
    }
    }

    // ========================================

    ReactDOM.render(
    <Game />,
    document.getElementById('root')
    );
```
8. Next, add the below three lines at the top of `index.js` which is in the `src` folder
```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
```
9. Run `npm start` in the project's folder through a terminal, it should automatically open a browser on the locahost (`http://localhost:3000`) and you should see an empty Tic Tac Toe field ( 3 x 3 square board ) with a title **Next Player: X**

In the next part two of this blog, we will learning about Components in React