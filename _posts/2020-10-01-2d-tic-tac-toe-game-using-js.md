---
layout: post
title: 2D Breakout Game using HTML5, CSS and JavaScript
featured: true
author: sujay
tags: [html5, css, javascript, 2d]
image: '/images/posts/2d-tic-tac-toe.png'
permalink: '/tutorial/2d-tic-tac-toe-game-using-js'
---

2D Tic Tac Toe Game using HTML5 , CSS and JavaScript

The Markup
This being a javascript centric project I won't be focusing on the markup a lot, but for those who want similar UI to my one, can follow.

The HTML started with the usual head declaration with linking the stylesheet and declaring the title:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Tic-Tac-Toe</title>
    <link rel="stylesheet" href="style.css" />
  </head>
</html>
```
After this I needed a container to wrap everything in this page and bring it to the center, that was done in the body by:

```html
<body>
  <div class="container"></div>
  <script src="app.js"></script>
</body>
```
I also linked the javascript at this very moment so that I don't forget to do that.

Now in the HTML I created the play-area i.e. the actual board, but as the 9 blocks inside the block will have similar property so I will be asking javascript to render those for me later, but now for seeing how the board will appear, I will be adding them without click-event. And also I added a title to make the board look cool:

```html
<div class="container">
  <h1>Tic-Tac-Toe</h1>
  <div class="play-area">
    <div id="block_0" class="block"></div>
    <div id="block_1" class="block"></div>
    <div id="block_2" class="block"></div>
    <div id="block_3" class="block"></div>
    <div id="block_4" class="block"></div>
    <div id="block_5" class="block"></div>
    <div id="block_6" class="block"></div>
    <div id="block_7" class="block"></div>
    <div id="block_8" class="block"></div>
  </div>
</div>
```

I used these IDs to give the board the tic-tac-toe board look.

Now, I did not want to refresh the page to reset the board for a new match or to start. So I added a button with onClick function which I will be implementing very late to reset the board.

```html
<div class="container">
  <!-- REST OF THE CODE -->
  <h2 id="winner"></h2>
  <button onclick="reset_board()">RESET BOARD</button>
</div>
```
Here I also added an h2 with an id of winner to later add the text of who is the winner.

Now, let's jump to CSS.

To start with I reset the margin and padding and set the box sizing and default font:

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, Helvetica, sans-serif;
}
```
Now, to center the whole game in the middle of the browser I used this styling on the container:
```css
.container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: #eee;
}
The button of reset was some hover effects like so:

button {
  outline: none;
  border: 4px solid green;
  padding: 10px 20px;
  font-size: 1rem;
  font-weight: bold;
  background: none;
  transition: all 0.2s ease-in-out;
}

button:hover {
  cursor: pointer;
  background: green;
  color: white;
}
```
Then there was the CSS to make the original board:
```css
.play-area {
  display: grid;
  width: 300px;
  height: 300px;
  grid-template-columns: auto auto auto;
}
.block {
  display: flex;
  width: 100px;
  height: 100px;
  align-items: center;
  justify-content: center;
  font-size: 3rem;
  font-weight: bold;
  border: 3px solid black;
  transition: background 0.2s ease-in-out;
}

.block:hover {
  cursor: pointer;
  background: #0ff30f;
}
```

At first I made the play area to show up like a grid so that I can place the 9 blocks evenly. Now I gave each block a class of block. So I selected the blocks and gave them a border all around. I also made them to show the symbols in the center by making them to be displayed individually as flex and giving those flex align-items and justify-content property of center. The font size and font weight was set to make the moves more prominent and visible. The transition of background was set so that I could display color change of background if cursor is hovered over that block.

Now talking about hover I set the cursor to pointer and background to a bright green, indicating that the player can place a move there. So to indicate where players can't give a move I decided to mark a block with the class occupied once it has some content in it. So I added this:

```css
.occupied:hover {
  background: #ff3a3a;
}
```
At this point the board looked fine but I wanted the classic look. Now, as the board is laid one by one the, the id of each div is like:

```css
Initial board:
 ___ ___ ___
| 0 | 1 | 2 |
 ___ ___ ___
| 3 | 4 | 5 |
 ___ ___ ___
| 6 | 7 | 8 |
 ___ ___ ___

```

So we need to move out:

top border for divs with ids 0,1,2
left border for divs with ids 0,3,6
bottom border for divs with ids 6,7,8
right border for divs with ids 2,5,8
Then only our board will be like this:

Now board:

```css
  0 | 1 | 2
 ___ ___ ___
  3 | 4 | 5
 ___ ___ ___
  6 | 7 | 8
```
So, I did just that in my CSS:

```css
#block_0,
#block_1,
#block_2 {
  border-top: none;
}

#block_0,
#block_3,
#block_6 {
  border-left: none;
}

#block_6,
#block_7,
#block_8 {
  border-bottom: none;
}

#block_2,
#block_5,
#block_8 {
  border-right: none;
}
```
There are some more CSS to make the app look beautiful, but I will skip them and dive straight into the meat, i.e. the javascript.

Now the app looks like this:



The JavaScript
To start the javascript I made the board in an array in javascript:
```js
let play_board = ["", "", "", "", "", "", "", "", ""];
```
After that instead of hard-coding the functions in each of the elements in HTML, I decided to render the board with JS, and I did that by declaring a render function and calling it:

```js
const board_container = document.querySelector(".play-area");

const render_board = () => {
  board_container.innerHTML = "";
  play_board.forEach((e, i) => {
    board_container.innerHTML += `<div id="block_${i}" class="block" onclick="addPlayerMove(${i})">${play_board[i]}</div>`;
    if (e == player || e == computer) {
      document.querySelector(`#block_${i}`).classList.add("occupied");
    }
  });
};

render_board();

```

Here at first I needed a reference to the container of the board container/play area. So I selected it with document.querySelector(). Then in the render board function I at first removed all the inner contents of our board_container. And after that using the for-each loop on the elements of the play board I added a div for each block with their specific id and their specific move adding function with the ID of the block.

At this point I also added the javascript to add the occupied class to the blocks with elements in it.

Now the next thing was to declare this addPlayerMove function which took the id/index of the div being clicked, placed the move and ask the computer to make its move, but before that I decide that the computer should take the piece "X" and player should place the piece "O". So, I declared these two as constants and started programming the addPlayerMove()

```js
const player = "O";
const computer = "X";

const addPlayerMove = e => {
  if (play_board[e] == "") {
    play_board[e] = player;
    render_board();
    addComputerMove();
  }
};
```

It was as easy as changing that element in the javascript based array board and asking the board to render, and then asking the computer to make its move. The only thing I needed to make sure is that the place was empty to place a move.

Now we need to declare the addComputerMove()

```js
const addComputerMove = () => {
  do {
    selected = Math.floor(Math.random() * 9);
  } while (play_board[selected] != "");
  play_board[selected] = computer;
  render_board();
};
```
To keep this post simple, I asked the computer to select a random block out of 0 to 8, but be sure that there is no move placement done previously.

Congrats! now you can play the game and place moves. But there are some problems. Let's address them one by one.

The first problem is, the computer wants to place a move even after the board is complete. So let's make a function to check if the board is complete or not and have a dedicate boolean for that:

```js
let board_full = false;
const check_board_complete = () => {
  let flag = true;
  play_board.forEach(element => {
    if (element != player && element != computer) {
      flag = false;
    }
  });
  board_full = !flag;
};
```

Here, at first the board_full variable was set to false. Then in the function there is a flag set to true. Then using for-each loop I looped through each element. If an element was found with "" i.e. blank, i.e. no player or computer move, then the flag was set to false. When the loop was complete then the board was full if the flag was true else it was not full. So the value of board_full was just the value of flag.

Now as this checking and rendering will be done after each move, so let's put them together in a function called game_loop():

```js
const game_loop = () => {
  render_board();
  check_board_complete();
};
```
Now, instead of calling render_board() after each player or computer move we will call game_loop().

Now, we need to condition the player and the computer so that they can't place move once the board is complete. This will be done like so:

```js
const addPlayerMove = e => {
  if (!board_full && play_board[e] == "") {
    play_board[e] = player;
    game_loop();
    addComputerMove();
  }
};

const addComputerMove = () => {
  if (!board_full) {
    do {
      selected = Math.floor(Math.random() * 9);
    } while (play_board[selected] != "");
    play_board[selected] = computer;
    game_loop();
  }
};
```

The game plays now fine and there is no javascript error. Now, the game needs to be able to detect if there is any winner and act accordingly.

So, I declared a function to check for winner named, check_for_winner. This function will take help of a function named check_match[declared later]. With the help of check_match this function will determine if the player has won or the computer has won or the match has turned into a draw. Remember that h2 with the id of winner. Now it is the time to get that and set its text according to the winner as well. The function check_for_winner currently looks like this:

```js
const winner_statement = document.getElementById("winner");
const check_for_winner = () => {
  let res = check_match();
  if (res == player) {
    winner.innerText = "Winner is player!!";
    winner.classList.add("playerWin");
    board_full = true;
  } else if (res == computer) {
    winner.innerText = "Winner is computer";
    winner.classList.add("computerWin");
    board_full = true;
  } else if (board_full) {
    winner.innerText = "Draw!";
    winner.classList.add("draw");
  }
};
```

Here I set the innerText of the winner*statement according to the winner and added some class to the h2 accordingly. These classes have some css properties defined as so in style.css:

```css
.playerWin {
  color: green;
}

.computerWin {
  color: red;
}

.draw {
  color: orangered;
}
```

Now we need to define the check match function. There are four kinds of match possible in tic-tac-toe:

In a row
In a column
& 4. Two diagonals
To understand the situation let's draw the index from the board array in the play area:
```
Board:

  0 | 1 | 2
 ___ ___ ___
  3 | 4 | 5
 ___ ___ ___
  6 | 7 | 8
```

So, to check row match we need to check index i, i+1 and i+2 for the elements 0,3,6. So I used a loop to check if these three were equal and were either filled by player or by computer. As this check is repeated for all so I declared a small function for this check of three blocks, where I pass the index and get the result in boolean, if there is a match:
```js
const check_line = (a, b, c) => {
  return (
    play_board[a] == play_board[b] &&
    play_board[b] == play_board[c] &&
    (play_board[a] == player || play_board[a] == computer)
  );
};

```
Now back to checking row. As I said earlier the function check_match will return the symbol of whoever has a match of three in the board. So the code for row check will be like this:
```js
for (i = 0; i < 9; i += 3) {
  if (check_line(i, i + 1, i + 2)) {
    return play_board[i];
  }
}
```

For columns we need to check index i, i+3 and i+6 for the elements 0,1,
2. The code looks like this:
```js
for (i = 0; i < 3; i++) {
  if (check_line(i, i + 3, i + 6)) {
    return play_board[i];
  }
}
```
Now, the check of the diagonals is left, which can be done easily by checking: 0,4,8 and 2,4,6:
```js
if (check_line(0, 4, 8)) {
  return play_board[0];
}
if (check_line(2, 4, 6)) {
  return play_board[2];
}
```
Now the complete code of check_match looks like this:

```js
const check_match = () => {
  for (i = 0; i < 9; i += 3) {
    if (check_line(i, i + 1, i + 2)) {
      return play_board[i];
    }
  }
  for (i = 0; i < 3; i++) {
    if (check_line(i, i + 3, i + 6)) {
      return play_board[i];
    }
  }
  if (check_line(0, 4, 8)) {
    return play_board[0];
  }
  if (check_line(2, 4, 6)) {
    return play_board[2];
  }
  return "";
};
```
In my version of code I have made the backgrounds of the matching blocks green by using document.querySelector(). I will leave that part as an exercise to the reader.

We can now add check_for_winner to our game loop as it is executed in each step:
```js
const game_loop = () => {
  render_board();
  check_board_complete();
  check_for_winner();
};

```
Now the final thing which is left to be implemented is the reset_board function. Here I make the board empty, set the board_full to false and remove the text and styling form the h2 of id winner. And with all these changes I render the board:

```js
const reset_board = () => {
  play_board = ["", "", "", "", "", "", "", "", ""];
  board_full = false;
  winner.classList.remove("playerWin");
  winner.classList.remove("computerWin");
  winner.classList.remove("draw");
  winner.innerText = "";
  render_board();
};
```
One thing to keep in mind while writing this code is that, you can call a function in JavaScript if it is already declared. So the final code looks like this:

```js
const player = "O";
const computer = "X";

let board_full = false;
let play_board = ["", "", "", "", "", "", "", "", ""];

const board_container = document.querySelector(".play-area");

const winner_statement = document.getElementById("winner");

check_board_complete = () => {
  let flag = true;
  play_board.forEach(element => {
    if (element != player && element != computer) {
      flag = false;
    }
  });
  board_full = flag;
};


const check_line = (a, b, c) => {
  return (
    play_board[a] == play_board[b] &&
    play_board[b] == play_board[c] &&
    (play_board[a] == player || play_board[a] == computer)
  );
};

const check_match = () => {
  for (i = 0; i < 9; i += 3) {
    if (check_line(i, i + 1, i + 2)) {
      return play_board[i];
    }
  }
  for (i = 0; i < 3; i++) {
    if (check_line(i, i + 3, i + 6)) {
      return play_board[i];
    }
  }
  if (check_line(0, 4, 8)) {
    return play_board[0];
  }
  if (check_line(2, 4, 6)) {
    return play_board[2];
  }
  return "";
};

const check_for_winner = () => {
  let res = check_match()
  if (res == player) {
    winner.innerText = "Winner is player!!";
    winner.classList.add("playerWin");
    board_full = true
  } else if (res == computer) {
    winner.innerText = "Winner is computer";
    winner.classList.add("computerWin");
    board_full = true
  } else if (board_full) {
    winner.innerText = "Draw!";
    winner.classList.add("draw");
  }
};


const render_board = () => {
  board_container.innerHTML = ""
  play_board.forEach((e, i) => {
    board_container.innerHTML += `<div id="block_${i}" class="block" onclick="addPlayerMove(${i})">${play_board[i]}</div>`
    if (e == player || e == computer) {
      document.querySelector(`#block_${i}`).classList.add("occupied");
    }
  });
};

const game_loop = () => {
  render_board();
  check_board_complete();
  check_for_winner();
}

const addPlayerMove = e => {
  if (!board_full && play_board[e] == "") {
    play_board[e] = player;
    game_loop();
    addComputerMove();
  }
};

const addComputerMove = () => {
  if (!board_full) {
    do {
      selected = Math.floor(Math.random() * 9);
    } while (play_board[selected] != "");
    play_board[selected] = computer;
    game_loop();
  }
};

const reset_board = () => {
  play_board = ["", "", "", "", "", "", "", "", ""];
  board_full = false;
  winner.classList.remove("playerWin");
  winner.classList.remove("computerWin");
  winner.classList.remove("draw");
  winner.innerText = "";
  render_board();
};

//initial render
render_board();
```

Thats all, You can play the game here : https://gamershub.in/games/