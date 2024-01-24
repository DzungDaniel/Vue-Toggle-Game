<template>
  <div id="matrix-size-edior">
    <label class="dimension" for="rows">Rows:</label>
    <input class="form-inline rounded-pill border-success" 
           type="number" id="rows" min="1" v-model="numRows">
    <label class="dimension" for="cols">Columns:</label>
    <input class="form-inline rounded-pill border-success"
           type="number" id="cols" min="1" v-model="numCols">
    <button class="dimension btn btn-success rounded-pill px-4 py-2" @click="resetMatrix">Reset Matrix</button>
  </div>
</template>

<script setup>
import { defineEmits, ref} from 'vue';
var numRows = ref(5);
var numCols = ref(5);
const emit = defineEmits('reset-matrix');
const resetMatrix = () => 
{
  const data = {col: numCols.value, row: numRows.value};
  emit('reset-matrix', data);
};

</script>

<style>
  .dimension{
    margin-left: 20px;
  }
</style>
