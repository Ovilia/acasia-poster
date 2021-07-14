<template>
  <el-row type="flex" class="poster-container" justify="center">
    <el-col :span="6">
      <img class="poster-template" src="poster-template.png">
    </el-col>
    <el-col :span="6" class="poster-control">
      this is control
    </el-col>
  </el-row>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({})
</script>

<style>
body {
  background-color: #222;
}

.poster-container {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}

  .poster-template {
    height: 100%;
  }

.poster-control {
  color: #eee;
}
</style>
