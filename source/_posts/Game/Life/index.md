---
title: Life game
abbrlink: 62fa798f
categories:
  - Game
  - Life
cover: tetris-randomizers.jpg
excerpt: ' Conway''s Game of Life for HTML5 Canvas Conway''s Game of Life for HTML5 Canvas Conway''s Game of Life for HTML5 Canvas Conway''s Game of Life for HTML5 Canvas Conway''s Game of Life for HTML5 Canvas Conway''s Game of Life for HTML5 Canvas'
---
# Life game

```js
// Conway's Game of Life for HTML5 Canvas
// By Simon Laroche

var FPS = 5;
var paused = true;
var gameStarted = false;
var gLoop;
var generations = 0;
var population = 0;

var lifeForms = [
    [
        [28, 28],
        [29, 28],
        [30, 28],
        [28, 29],
        [28, 30],
        [30, 29],
        [30, 30]
    ], //Arch
    [
        [29, 28],
        [30, 29],
        [30, 30],
        [29, 30],
        [28, 30]
    ], //Glider
    [
        [29, 28],
        [30, 28],
        [28, 29],
        [29, 29],
        [29, 30]
    ], //R-pentomino
    [
        [32, 27],
        [26, 28],
        [27, 28],
        [27, 29],
        [31, 29],
        [32, 29],
        [33, 29]
    ] //Diehard
];

var start = document.getElementById('start');
var reset = document.getElementById('reset');
var random = document.getElementById('random');
var stats = document.getElementById('s');
var wrapper = document.getElementById('w');
var canvas = document.getElementById('c');
var ctx = canvas.getContext('2d');

canvas.width = 600;
canvas.height = 600;
wrapper.style.width = canvas.width + 'px';
stats.style.width = canvas.width - 20 + 'px';

function newGrid() {
    var cells = new Array(canvas.width / 10);
    for (var i = 0; i < canvas.width / 10; i++) {
        cells[i] = new Array(canvas.height / 10);
    }
    return cells;
}

//fix
function neighbours(x, y) {
    var count = 0;
    var width = canvas.width / 10;
    var height = canvas.height / 10;
    for (var i = -1; i <= 1; i++) {
        for (var j = -1; j <= 1; j++) {
            // skip checking the same cell.
            if (i == 0 && j == 0) {
                continue;
            }
            // get the xy of a neighbor with grid wrap around.
            var nx = x + i;
            if (nx < 0) {
                nx += width;
            }
            if (nx >= width) {
                nx -= width;
            }

            var ny = y + j;
            if (ny < 0) {
                ny += height;
            }
            if (ny >= height) {
                ny -= height;
            }

            // Check if neighbor is alive.
            if (cells[nx][ny]) {
                count++;
            }
        }
    }

}

// function neighbours(x, y) {
//     var count = 0;
//     // TODO Test for out of bounds properly.
//     if (x > 0 && y > 0 && x < canvas.width / 10 - 1 && y < canvas.width / 10 - 1) {
//         if (cells[x - 1][y - 1]) {
//             count++;
//         }
//         if (cells[x - 1][y + 1]) {
//             count++;
//         }
//         if (cells[x + 1][y + 1]) {
//             count++;
//         }
//         if (cells[x + 1][y - 1]) {
//             count++;
//         }
//         if (cells[x][y + 1]) {
//             count++;
//         }
//         if (cells[x][y - 1]) {
//             count++;
//         }
//         if (cells[x - 1][y]) {
//             count++;
//         }
//         if (cells[x + 1][y]) {
//             count++;
//         }
//     }
//     return count;
// }

function clear() {
    ctx.fillStyle = '#eee';
    ctx.beginPath();
    ctx.rect(0, 0, canvas.width, canvas.height);
    ctx.closePath();
    ctx.fill();

    function grid(increment, color) {
        ctx.fillStyle = color;
        for (var i = 0; i < canvas.width + 1; i += increment) {
            ctx.fillRect(i - 1, 0, 2, canvas.height);
        }
        for (var i = 0; i < canvas.height + 1; i += increment) {
            ctx.fillRect(0, i - 1, canvas.width, 2);
        }
    }
    grid(10, '#ddd');
    grid(100, '#ccc');
}

function draw() {
    ctx.fillStyle = '#585858';
    for (var i = 0; i < cells.length; i++) {
        for (var j = 0; j < cells[i].length; j++) {
            if (cells[i][j]) {
                //ctx.fillRect(i * 10 + 1, j * 10 + 1, 8, 8); // square cells
                ctx.beginPath();
                ctx.arc(i * 10 + 5, j * 10 + 5, 4, 0, Math.PI * 2, true);
                ctx.fill();
            }
        }
    }
}

function update() {
    population = 0;
    cellsCopy = new Array();
    for (var x = 0; x < cells.length; x++) {
        cellsCopy.push(cells[x].slice());
        for (var y = 0; y < cells[x].length; y++) {
            if (neighbours(x, y) < 2 || neighbours(x, y) > 3) {
                cellsCopy[x][y] = false;
            } else if (neighbours(x, y) == 3) {
                cellsCopy[x][y] = true;
            }
            if (cellsCopy[x][y]) {
                population++;
            }
        }
    }
    cells = cellsCopy;
    generations++
}

function play() {
    gameStarted = true;
    if (paused) {
        gLoop = setInterval(gameLoop, 1000 / FPS);
        paused = false;
        start.innerHTML = 'Pause';
    } else {
        clearInterval(gLoop);
        paused = true;
        start.innerHTML = 'Play';
    }
}

function resetGame() {
    paused = false;
    play();
    cells = newGrid();
    generations = 0;
    population = 0;
    refresh();
}

function getLife(index) {
    var life = lifeForms[index] || lifeForms[~~(Math.random() * lifeForms.length)];
    for (var x = 0; x < life.length; x++) {
        cells[life[x][0]][life[x][1]] = true;
    }
}

canvas.addEventListener('click', function(e) {
    if (paused) {
        var mx = ~~((e.pageX - canvas.offsetLeft) / 10);
        var my = ~~((e.pageY - canvas.offsetTop) / 10);
        if (cells[mx][my]) {
            cells[mx][my] = false;
        } else {
            cells[mx][my] = true;
        }
        refresh();
    }
}, false);

start.addEventListener('click', play, false);

random.addEventListener('click', function() {
    resetGame();
    getLife();
    refresh();
}, false);

reset.addEventListener('click', resetGame, false);

function refresh() {
    clear();
    draw();
    stats.innerHTML = 'Generations: ' + generations +
        ' Population: ' + population;
}

function gameLoop() {
    refresh();
    update();
}

cells = newGrid();
getLife(0);
refresh();
```
