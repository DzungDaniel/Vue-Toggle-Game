<template>
    <div id="app-content">
      <SizeEditor
        id="size-editor"
        :num-cols="numCols" 
        :num-rows="numRows" 
        @reset-matrix="(data) => handleReset(data.col, data.row)"> 
      </SizeEditor>
      <br>
      <div id="matrix-container">
        <table id="colorMatrix">
          <tr v-for="(row, i) in matrix.value" :key="i">
            <td class="rounded" v-for="(cell, j) in row" :key="j"
                :style="getStyle(i,j)"
                @click="highLightChunk(i,j)"
            ></td>
          </tr>
        </table>
      </div>
    </div>
  </template>
  
  <script setup>
    import {onMounted, reactive, ref} from 'vue';
    import SizeEditor from '../components/SizeEditor.vue';
  
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
              'border': borderStyle,
              'width': '30px',
              'height': '30px'};
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
  
    onMounted(() => {
      resetMatrix();
    });
  </script>
  
  <style>
    #app-content {
      font-family: Avenir, Helvetica, Arial, sans-serif;
      text-align: center;
      align-items: center;
    }
  
    #matrix-container{
      margin: 10px;
      margin-bottom: 50px;
      display: flex;
      text-align: center;
      align-items: center;
      justify-content: center;
    }
  
    #size-editor{
      margin: 10px;
    }
  
    #colorMatrix{
      border-spacing: 5px;
      border-collapse: separate;
    }
  </style>