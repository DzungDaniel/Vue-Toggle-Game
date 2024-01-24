<template>
  <div id="app-content" display="flex" justify-content="center" align-items="center">
    <img alt="Vue logo" src="./assets/logo.png">
    <SizeEditor :num-cols="numCols" :num-rows="numRows" 
                @reset-matrix="(data) => handleReset(data.col, data.row)"> 
    </SizeEditor>
    <div display="flex" justify-content="center" align-items="center">
      <table id="colorMatrix" display="flex" justify-content="center" align-items="center">
        <tr v-for="(row, i) in matrix.value" :key="i">
          <td v-for="(cell, j) in row" :key="j"
              :style="getStyle(i,j)"
              @click="highLightChunk(i,j)"
              width = '30px' height = '30px'
          ></td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script setup>
import {onMounted, reactive, ref} from 'vue';
import SizeEditor from './components/SizeEditor.vue';
var numRows = ref(5);
var numCols = ref(5);
var matrix = reactive([]);

var visited = reactive(new Set());
var click_count = ref(0);

class CellData
{
    constructor(y, x, color)
    {
        this.y = y;
        this.x = x;
        this.color = color;
    }
}

function getRandomColor() 
{
    const COLORS = ['#FF0000', '#00FF00', '#0000FF', '#FFFF00', '#FF00FF'];
    const randomIndex = Math.floor(Math.random() * COLORS.length);
    return COLORS[randomIndex];
}

function getStyle(i,j)
{
  const cellData = matrix.value[i][j];
  let borderStyle = visited.has(cellData) ? '3px solid black' : '3px solid transparent';
  return {'background-color': cellData.color,
          'border': borderStyle};
}

function getAdjs(cellData)
{
    const adjs = [];
    const directions = [
        { y: -1, x: 0 }, // Top
        { y: 0, x: 1 },  // Right
        { y: 1, x: 0 },  // Bottom
        { y: 0, x: -1 }  // Left
    ];

    directions.forEach(dir => {
        const x = dir.x + cellData.x;
        const y = dir.y + cellData.y;
        let valid = x >= 0 && x < numCols.value &&
                    y >= 0 && y < numRows.value;
        if (valid) adjs.push(matrix.value[y][x])
    });

    return adjs;
}

function dfs(cellData)
{
    if (visited.has(cellData)) 
        return;
    visited.add(cellData);
    
    const adjs = getAdjs(cellData);
    adjs.forEach(neighbor => {
        if (neighbor.color === cellData.color) 
            dfs(neighbor);
    });
}

function highLightChunk(i, j)
{
    const cellData = matrix.value[i][j];
    click_count.value++;
    console.log(click_count.value);
    visited.clear();
    dfs(cellData);
}

function handleReset(col, row)
{
  console.log(col);
  console.log(row);
  numCols.value = col;
  numRows.value = row;
  resetMatrix();
}

function resetMatrix()
{
  matrix.value = [];

  // INIT DATA MATRIX
  for (let i = 0; i < numRows.value; i++) 
  {
      const row = [];
      for (let j = 0; j < numCols.value; j++) 
      {
          const color = getRandomColor();
          row.push(new CellData(i, j, color));
      }
      matrix.value.push(row);
  }
}

onMounted(() => resetMatrix());

</script>

<style>
#app-content {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>