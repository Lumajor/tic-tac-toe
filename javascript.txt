const gameBoard = (() => {
    let board = [];
    let plays = 0;
    let currentTurnCursor = "";
    let currentTurnName = "";
    const gridContainer = document.getElementById('gridContainer');

    //reset the board, set plays to 0, and remove the previous grid
    const deleteGrid = () => {
        board = []
        plays = 0;
        while (gridContainer.firstChild) {
            gridContainer.removeChild(gridContainer.firstChild);
        };
    };

    const createGrid = () => {
        deleteGrid();
        //create 9 square grid, append a div to each div to assign ID's to div
        for (i = 1; i < 10; i++) {
            board.push("");
            let gridSquare = document.createElement('div'); 
            board.forEach(element => 
                gridSquare.innerHTML = element,
                gridSquare.classList.add('grid-element'),
                gridSquare.setAttribute("id", "grid-element-" + [i]),
                gridContainer.appendChild(gridSquare));
        };

    };
    const updateGrid = (clickedSquare) => {
        //check if spot is already taken, if not place value in array and update the UI, change
        //user's cursor for next turn.
        if (board[clickedSquare - 1] == "") {
            board.splice(clickedSquare - 1, 1, gameBoard.currentTurnCursor);
            let updateSquare = document.getElementById("grid-element-" + clickedSquare);
            updateSquare.innerHTML = gameBoard.currentTurnCursor; 
            plays++;
            _checkWinner();
            displayController.switchTurn();
            displayController.displayText();
            //user.changeCursor();
        }
        //if its taken, send alert to user to choose another spot.  
        else {
            alert("That square has already been selected, please choose another.");
        };
    };

    //if someone wins, alert the user, reset the grid and the navbar
    const _winOccured = () => {
        const displayButton = document.getElementById('display-button');
        alert(gameBoard.currentTurnName + ' wins.');
        createGrid();
        displayController.switchTurn();
        displayController.displayText();
    };

    const _checkWinner = () => {
        //account for all winning possibilites, reset grid when winner is determined.
        if (board[0] == board[1] && board[0] == board[2] && board[0] != "") {
            _winOccured();
        }
        if (board[0] == board[4] && board[0] == board[8] && board[0] != "") {
            _winOccured();
        }
        if (board[0] == board[3] && board[0] == board[6] && board[0] != "") {
            _winOccured();
        }
        if (board[1] == board[4] && board[1] == board[7] && board[1] != "") {
            _winOccured();
        }
        if (board[2] == board[5] && board[2] == board[8] && board[2] != "") {
            _winOccured();
        }
        if (board[3] == board[4] && board[3] == board[5] && board[3] != "") {
            _winOccured();
        }
        if (board[6] == board[7] && board[6] == board[8] && board[6] != "") {
            _winOccured();
        }
        if (board[6] == board[4] && board[6] == board[2] && board[6] != "") {
            _winOccured();
        }
        if (plays == 7) {
            alert("cat's game.");
            createGrid();
        }
    };
    return {createGrid, updateGrid, board, currentTurnCursor, currentTurnName};
})();

const displayController = (() => {
    let playersLoaded = false;
    const container = document.getElementById('gridContainer');
    const displayPlayer = document.getElementById('display-player');
    const displayButton =  document.getElementById('display-button');
    const placeCursor = (square) => {
        if (displayController.playersLoaded == undefined) {
            alert("You need to insert players before you can start playing");
        }
        else {
            console.log("you clicked " + square);
            gameBoard.updateGrid(square);
        };
    };
    const displayText = () => {
        if (displayController.playersLoaded == undefined) {
            displayPlayer.innerHTML = "Enter player names to begin playing.";
        }
        else if (displayController.playersLoaded == true) {
            displayPlayer.innerHTML = "It's " + gameBoard.currentTurnName + "'s turn"
        }
    }
    const switchTurn = () => {
        if (gameBoard.currentTurnCursor == playerOne.cursor) {
            return gameBoard.currentTurnCursor = playerTwo.cursor,
            gameBoard.currentTurnName = playerTwo.name;
        }
        else {
            return gameBoard.currentTurnCursor = playerOne.cursor,
            gameBoard.currentTurnName = playerOne.name;
        }
    };
    return {placeCursor, displayText, switchTurn};
})();

const Player = (name) => {
    let cursor = "";
    const navbar = document.getElementById('navbar');
    const displayPlayer = document.getElementById('display-player');
    return {name, cursor};
};


const eventListeners = (() => {
    gridContainer.addEventListener("click", function(e) {
        if (e.target.matches(".grid-element")) {
            displayController.placeCursor(e.target.id.substring(13, e.target.id.length));
        };
    });
    
    document.addEventListener('DOMContentLoaded', function() {
        gameBoard.createGrid();
        displayController.displayText();
    });

    submitPlayers.addEventListener("click", function(e) {
        if (e.target.matches("#submitPlayers")) {
            return playerOne = Player(playerOneInput.value), 
            playerTwo = Player(playerTwoInput.value),
            playerOne.cursor = "X",
            playerTwo.cursor = "O",
            gameBoard.currentTurnCursor = playerOne.cursor,
            gameBoard.currentTurnName = playerOne.name,
            e.target.style.borderWidth = "1px",
            displayController.playersLoaded = true,
            displayController.displayText();
        };
    });

    gridContainer.addEventListener('mouseover', function (e){
        if (e.target.matches('.grid-element')) {
            e.target.style.borderWidth = "3px";

            setTimeout(function() {
                e.target.style.borderWidth = "2px";
            }, 500);
            
        }

    });

})();