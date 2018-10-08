<template>
  <section class="container">
    <template v-for="columns in cells">
      <CellView v-for="(cell, index) in columns" :key="index"  v-bind:is-bomb="cell.isBomb" v-bind:cell-neighbours="cell.numberOfNeighbours" />
    </template>
  </section>
</template>

<script>
import CellView from "~/components/CellView";

class Cell {
  constructor(xPos, yPos, numberOfNeighbours, isBomb) {
    this.xPos = xPos;
    this.yPos = yPos;
    this.numberOfNeighbours = numberOfNeighbours;
    this.isBomb = isBomb;
  }
}

class Bomb {
  constructor(xPos, yPos) {
    this.xPos = xPos;
    this.yPos = yPos;
  }
}

const numberOfBombs = 1;

const numberOfColumns = 10;
const numberOfRows = 10;

const numberOfCells = numberOfColumns * numberOfRows;

const bombs = initBombs(numberOfBombs, numberOfColumns, numberOfRows);
const cells = initCells(numberOfColumns, numberOfRows, bombs);

export default {
  components: {
    CellView
  },
  data: () => ({
    cells
  })
};

function initBombs(numberOfBombs, numberOfColumns, numberOfRows) {
  const numberOfCells = numberOfColumns * numberOfRows;
  const possibleLocations = [numberOfCells];

  let index = 0;

  for (let xPos = 0; xPos < numberOfColumns; xPos++) {
    for (let yPos = 0; yPos < numberOfRows; yPos++) {
      possibleLocations[index] = new Bomb(xPos, yPos);
      index++;
    }
  }

  shuffle(possibleLocations);
  const bombs = possibleLocations.slice(0, numberOfBombs);

  return bombs;
}

function initCells(numberOfColumns, numberOfRows, bombs) {
  const cells = new Array(numberOfColumns);

  let numberOfNeighbours;
  let isBomb;

  for (let xPos = 0; xPos < numberOfColumns; xPos++) {
    cells[xPos] = new Array(numberOfRows);

    for (let yPos = 0; yPos < numberOfRows; yPos++) {
      numberOfNeighbours = findNumberOfBombsAroundCell(xPos, yPos, bombs); 
      isBomb = checkIfBombInCurrentSpot(xPos, yPos, bombs);
      
      cells[xPos][yPos] = new Cell(xPos, yPos, numberOfNeighbours, isBomb);
    }
  }

  return cells;
}

function checkIfBombInCurrentSpot(xPos, yPos, bombs) {
  let bombInCurrentSpot;

  for (const bomb of bombs) {
    bombInCurrentSpot = bomb.xPos === xPos && bomb.yPos === yPos;

    if (bombInCurrentSpot) {
      return true;
    }
  }

  return false;
}

function findNumberOfBombsAroundCell(xPos, yPos, bombs) {
  // HACK: The accumulator is not set on the first element in the reducer. 
  // Therefore, a fake bomb is added so that the accumulator has a value on the first real bomb
  bombs.unshift(new Bomb(-100, -100));
  
  const topLeft =     bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos - 1, yPos - 1, bomb.xPos, bomb.yPos));
  const top =         bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos + 0, yPos - 1, bomb.xPos, bomb.yPos));
  const topRight =    bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos + 1, yPos - 1, bomb.xPos, bomb.yPos));
  const left =        bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos - 1, yPos + 0, bomb.xPos, bomb.yPos));
  const right =       bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos + 1, yPos + 0, bomb.xPos, bomb.yPos));
  const bottomLeft =  bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos - 1, yPos + 1, bomb.xPos, bomb.yPos));
  const bottom =      bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos + 0, yPos + 1, bomb.xPos, bomb.yPos));
  const bottomRight = bombs.reduce((accumulator, bomb) => accumulator = accumulator === true ? true : bombInCell(xPos + 1, yPos + 1, bomb.xPos, bomb.yPos));

  return topLeft + top + topRight + left + right + bottomLeft + bottom + bottomRight;
}

function bombInCell(cellX, cellY, bombX, bombY) {
  return cellX === bombX && cellY === bombY; 
}



/**
 * Shuffles array in place.
 * @param {Array} a items An array containing the items.
 */
function shuffle(a) {
  for (let i = a.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [a[i], a[j]] = [a[j], a[i]];
  }
  return a;
}
</script>

<style>
.container {
  display: grid;
  grid-template-columns: repeat(10, 2rem [col-start]);
  grid-template-rows: repeat(10, 2rem [col-start]);
}
</style>
