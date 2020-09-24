---
layout: post
title: React Fundamentals By Building Tic Tac Toe
description: Learn fundamentals of React by making an interactive Tic Tac Toe game in React
date:   2020-09-20
---
In this post we will be building an interactive Tic Tac Toe game in React to learn the fundamentals of React. However, let's get familiar with React core concepts theoratically first.

### Pre-requisites
* HTML, CSS and Javscript
* Your favourite text editor
* ES6
* [Node](https://nodejs.org/en/) installed

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
* Include `extends React.Component` (see example below)
* Include a `render()` method which returns the HTML

So let's understand with a very simple example. If you want to place a heading on a web page saying `My name is Zain`, then it will be in its own component like below:
<p align="center">
  <img src="/assets/images/2020-09-12/1.png">
</p>
The above code simply says that this is a component called `Person` and it returns heading one, `<h1>`, element in the render method. The `render` method returns a view that is shown to the user. The above code is equivalent to:
<p align="center">
  <img src="/assets/images/2020-09-12/2.png">
</p>
which clearly shows that `<h1>` above are converted to  `React.createElement('h1')` in compile time. 

As `render` method returns a React element (displays the description of what to show to the user), the syntax of it isn't used by React developers. Instead `<div />` is used which is a lot easier and cleaner to write and is called the JSX syntax. Basically you can put any javascript code inside HTML using curly braces `{}`.

You use the above component in the `container` element or any other element like below:
<p align="center">
  <img src="/assets/images/2020-09-12/3.png">
</p>

#### Props
These are the parameters that a component takes in. `props` is short for properties. So, if we wanted to add a name `Zain` as a `prop` (property) to our `Person` component it would be like:
<p align="center">
  <img src="/assets/images/2020-09-12/4.png">
</p>

### State
This is what components use to remember the values of variable at different stages if the values are updated.

Some concepts to know about `state`:
1. State is set in the constructor like the below:
      <p align="center">
        <img src="/assets/images/2020-09-12/initialize-state.png">
      </p>
2. `this.state` is considered private to it's component

Now that we have some theoratical foundation of React, let's build a Tic Tac Toe game using our knowledge so far.

## Set Up Project
1. Run the below command to create a react project
  ```
  npx create-react-app tic-tac-toe
  ```
      <p align="center">
        <img src="/assets/images/2020-09-12/create-react-project.png">
      </p> 
Upon successful creation, you should see the below:
      <p align="center">
        <img src="/assets/images/2020-09-12/project-created-successfully.png">
      </p>
  
2. `cd` into the project folder and open in VS Code ( or your favourite text editor )
      <p align="center">
        <img src="/assets/images/2020-09-12/cd-into-project.png">
      </p>
        
      Your project structure should look like this:
      <p align="center">
        <img src="/assets/images/2020-09-12/project-before-deletion.png">
      </p>
3. Delete everything in `src` folder **but not the folder itself**
      <p align="center">
        <img src="/assets/images/2020-09-12/project-after-deletion.png">
      </p>

4. Add a file called `index.css` in the `src` folder
5. Add the below code in the `index.css` file.
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
  6. Add a file called `index.js` in the `src` folder
  7. Add the below code in the `index.js` file.
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
What the above Javascript consist and does is it has three components, `Sqaure`, `Board` and `Game`. `Square` -> displays one button, `Board` ->  displays 9 squares and `Game` ->  displays the board with values as a placeholder.

7. Add the below three lines in the `index.js` file
```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
```
8. Navigate to the terminal project folder in the terminal and run `npm start`. If everything went smooth you should see the below in browser and note that you can't interact with any component for now.
<p align="center">
  <img src="/assets/images/2020-09-12/localhost-start.png">
</p>
Now that our project is all set up, we will implement the core functionalities features that is used in React frequently and one has to know which are:
* Pass Data Using Props (properties)
* Making Component Interactive
* Playing with State

### Implementing Functionality

#### Pass Data Using Props (Properties)
This is one of the core functionalities you will need when building applications in React. Let's get our hands dirty.

We will pass data from Board to Square component. 

To do this, we will add a prop `value` to the `renderSquare` method in Board component
<p align="center">
  <img src="/assets/images/2020-09-12/pass-prop.png">
</p>
In the the square component replace `{/* TODO */}` with `{this.props.value}` to get the value of `value` from Board component like below
<p align="center">
  <img src="/assets/images/2020-09-12/passed-prop.png">
</p>

If you did everything correct, you should see the value on each square when you run the application with `npm start`.
<p align="center">
  <img src="/assets/images/2020-09-12/after-props.png">
</p>

In this game's example, Board is a parent and Sqaure is a child component which means you have just passed a prop from parent to child component.

#### Making Component Interactive
To play the game, we need to be able to click on the squares, which is why we will make the squares clickable so that when clicked they display 'X'.

1. Add an `onClick` prop to the button that is returned from the square component and display an alert.
    <p align="center">
      <img src="/assets/images/2020-09-12/button-clicked.png">
    </p>  
  Notice how we have used `{}` with `onClick` prop. This is how we write javsacript inside HTML in React.
2. Now go to the browser and when you click a square, you should see an alert.
    <p align="center">
      <img src="/assets/images/2020-09-12/button-clicked-in-browser.png">
    </p>
3. To simplify the code a bit, we will use arrow function from ES6.
    <p align="center">
      <img src="/assets/images/2020-09-12/arrow-function.png">
    </p>
4. We will use `this.state` to make React remember when a square has been clicked, mark it with a "X". So let's add `this.state` to the constructor (that's how states are set in React) and set to `null` initially.
    <p align="center">
      <img src="/assets/images/2020-09-12/state-null.png">
    </p>
    The state will be changed when the square is clicked. Moreover, you have to call `super(props)` in the constructor of the child class
5. Replace `this.props.value` by `this.state.value` in the `<button>`
6. Remove `onClick={...}` add `onClick={() => this.setState(value:'X')}`. Your Sqaure component should look like:
    <p align="center">
      <img src="/assets/images/2020-09-12/update-state.png">
    </p>
      What the above code does is by default, the value of state `value` is `null` and when user clicks the button, this updates the state `value` to "X" with `this.setState`(by calling this, state is updated automatically) and then displays the updated state as the text in the button with `this.state.value`
7. Now go to the browser and click on the squares. You should see "X" appearing.
8. Now we need to add "O" to our games as Tic Tac Toe has to have "Xs" and "0s" to be played. However, we will add them every alternative turns.

#### Determining Winner
In order to determine the winner, we need to keep count of the value of the 9 squares we have in a place. This can be done in two ways:
* **`Board` component to ask each of the square about its state**
    
    We can do this however, the code will not look neat, readable and be prone to many bugs, not to mention the complexity.
* **Store the entire game's state in the `Board` component**

    We will go with this one as it's much neater, readable, simple and easy as the `Board` component will pass the value to `Sqaure` component that will be displayed in each square via `prop`. 

    To action the above decided approach, we need to give the control of the state to the `Board` component which means that `state` will need to be declared in the `Board` component instead of the `Square`.

1. Begin with adding a constructor in the `Board` component and set it to have 9 `nulls` which are associated with 9 squares.
    <p align="center">
      <img src="/assets/images/2020-09-12/lifting-state-up.png">
    </p>
    Basically, the array can be visualized as below and the `renderSqaure()` doesn't change yet.
    ```
    [
      'null', 'null', 'null',
      'null', 'null', 'null',
      'null', 'null', 'null',
    ]
    ```
2. Now modify the `renderSquare` to display each squares its current value. This is done by passing the `state` called `sqaures` as a `prop`.
    <p align="center">
      <img src="/assets/images/2020-09-12/pass-state-as-prop.png">
    </p>
    Every square will be assigned a value of `0`, `X` or `null` for empty squares

#### Changing Square Value
Now, when clicked, we need to change the value of square. It will either be `0` or `X` and `null` for empty squares. Since, the Board component, fills the square and state is private to its own componenet, we can't updated the `Board` component directly from `Square`. So, we will create a function in the `Board` component, pass it to the `Square` component via `prop` and then call the function within the `Square` component 

  1. Assign a function `handleClick` in the `Board` called as a prop to `Board` component
      <p align="center">
        <img src="/assets/images/2020-09-12/function-call.png">
      </p>
  2. Now call the `onClick` function in `Square` component and delete the construct as we don't need it.
      <p align="center">
          <img src="/assets/images/2020-09-12/function-call-in-square.png">
      </p>
  Now, `onClick` method provided by the Board is called when the square is clicked. However, an error should display when you click a square and should look like the below:
      <p align="center">
          <img src="/assets/images/2020-09-12/error.png">
      </p>
  This is because we are using the `handleClick` function without implmeneting it. Let's implement it now.
      <p align="center">
          <img src="/assets/images/2020-09-12/handleclick.png">
      </p>
  What the above code does is creates a new copy of the squares array and the index that is clicked, it marks it with an `X` and updates the states.
  Now, when you click the square it won't display any error. As the state is being stored in the Board component, when the Board's state changes, the Squares will display the update value automatically which is how we will choose the winner. In this scenario, we call the `Square` component a **controlled component** because it is controlled by the `Board` component.

      Let's change the React Component into function component because they are more readable and less tedious.
        <p align="center">
            <img src="/assets/images/2020-09-12/functional-component.png">
        </p>
      As you can tell from the above code, function components:
      * Don't have `render` method
      * Use key work `function` instead of `class`
      * Don't extend anything
      * Takes in `props`

#### Take Turns
  Until now, you might have observed we only have one value `X` displaying in squares however we need `O` to display too. 

  Let's mark the first move to default to `X` in the `Board` component by setting it to true.
  <p align="center">
      <img src="/assets/images/2020-09-12/x-is-next.png">
  </p>
  Now, we need to display `X` in every alternative turn. So, we set NOT (`!`) operator for `setXNext` on every alternative click so that `O` can be displayed as well and save the game's state simultaneousely. Let's update the `handleClick` function to achieve this
  <p align="center">
    <img src="/assets/images/2020-09-12/set-x-next.png">
  </p>
  In the above code, we are checking where `setXNext` flag is true, if it is, then display `X`, if not, displaying `0` and then update the state to the opposite of what it is currently.

  <video width="320" height="240" style="display:block; margin-left:auto; margin-right:auto" controls>
    <source src="/assets/images/2020-09-12/x-and-zero.mov">
  </video>

  To make the game a user friendly, let's change the text on the title too:
  <p align="center">
        <img src="/assets/images/2020-09-12/user-friendly-title.png">
  </p>
  Now your game should look like:

  <video width="320" height="240" style="display:block; margin-left:auto; margin-right:auto" controls>
    <source src="/assets/images/2020-09-12/user-friendly-title.mov">
  </video>


  Next move will be to show when the games ends and declaring the winner. So copy the below code and paste it at the end.
  ```
  function calculateWinner(squares) {
      const lines = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
      ];
      for (let i = 0; i < lines.length; i++) {
        const [a, b, c] = lines[i];
        if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
          return squares[a];
        }
      }
      return null;
    }
  ```
  I won't be explaining the above code as it's not in the scope of this blog. 

  Call the above method `calculateWinner(square)` in the Board component to verify if the player has won. Then display the winner in the title.

  <p align="center">
        <img src="/assets/images/2020-09-12/winner-found.png">
  </p> 
  In the above code, we are calculating the winner from the method `calculateWinner(squares)`, then we check the value of `winnerIs`. If it is true, we end the game showing the winner and if not, we give the next person the turn.

  We still have one fallback in the Board Component.`handleClick` method is always fired even a player has won. We will prevent that by returning it straight way without listening to the click. Add the below lines after we create a new array in `handleClick`.
  ```
    if (calculateWinner(squares) || squares[i]) {
            return;
    }
  ```
  Now your `handleClick` function should look like this

  <p align="center">
        <img src="/assets/images/2020-09-12/handleClick-final.png">
  </p>

  Now you can start playing the game and I should look like

  <video width="320" height="240" style="display:block; margin-left:auto; margin-right:auto" controls>
    <source src="/assets/images/2020-09-12/final-demo.mov">
  </video>

  Hope it was useful to you.