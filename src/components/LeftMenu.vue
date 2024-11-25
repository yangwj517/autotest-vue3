<template>
  <el-menu :router="false" :default-active="activePath" class="sidebar-menu">
    <el-menu-item v-for="item in menuItems" :key="item.id" :index="item.url" @click="handleSelect(item.url)">
      <span>{{ item.name }}</span>
    </el-menu-item>
  </el-menu>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import axios from 'axios';
import { useRoute } from 'vue-router';

interface MenuItem {
  id: number;
  name: string;
  url: string;
}

export default defineComponent({
  name: 'LeftMenu',
  setup() {
    const menuItems = ref<MenuItem[]>([]);
    const activePath = ref<string>('');
    const route = useRoute();

    const fetchMenuItems = async () => {
      try {
        const response = await axios.get<MenuItem[]>('http://localhost:8080/index');
        menuItems.value = response.data;
        activePath.value = route.path; // 设置当前激活的路径
      } catch (error) {
        console.error('Error fetching menu items:', error);
      }
    };

    const handleSelect = async (url: string) => {
      try {
        const response = await axios.get(`http://localhost:8080${url}`);
        const testItems = response.data;
        console.log('Test Items:', testItems);
        // 发送事件，通知主内容区域更新数据
        window.dispatchEvent(new CustomEvent('updateTestItems', { detail: testItems }));
        console.log("事件派发成功");
      } catch (error) {
        console.error('Error fetching test items:', error);
      }
    };

    onMounted(() => {
      fetchMenuItems();
    });

    return {
      menuItems,
      activePath,
      handleSelect
    };
  }
});
</script>

<style scoped>
.sidebar-menu {
  height: 100%;
  background-color: #ffffff;
  color: #BFCBD9;
  border-right: none;
}
</style>