<script setup lang="ts">

import Header from "@/components/Header.vue";
import LeftMenu from "@/components/LeftMenu.vue";
import MainContent from "@/components/MainContent.vue";
</script>

<template>
  <div class="main">
    <el-container style="height: 100%;">
      <el-header style="background-color: #304156; height: 60px;"><Header/></el-header>
      <el-container style="height: calc(100% - 60px); display: flex;">
        <el-aside width="200px" style="background-color: #67C23A;"><LeftMenu/></el-aside>
        <el-main style="background-color:#f0f2f5 ; flex: 1;"><MainContent/></el-main>
      </el-container>
    </el-container>
  </div>
</template>

<style scoped>
.main {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  width: 100%;
}
</style>