<template>
  <div class="main-content">
    <el-card v-for="item in testItems" :key="item.desc" class="test-card" @click="navigateTo(item.url)">
      <div class="card-content">
        <h3>{{ item.desc }}</h3>
        <p>{{ item.url }}</p>
      </div>
    </el-card>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onUnmounted, watch } from 'vue';
import { useRouter } from 'vue-router';

interface TestItem {
  desc: string;
  url: string;
}

export default defineComponent({
  name: 'Main',
  setup() {
    const testItems = ref<TestItem[]>([]);
    const router = useRouter();

    const navigateTo = (url: string) => {
      router.push(url);
    };

    const updateTestItems = (event: CustomEvent<{ detail: TestItem[] }>) => {
      console.log('updateTestItems called'); // 确认函数是否调用
      const newItems = event.detail;
      console.log('Received test items:', newItems);
      if (Array.isArray(newItems) && newItems.every(item => 'desc' in item && 'url' in item)) {
        testItems.value = newItems;
        console.log('Updated test items:', testItems.value);
      } else {
        console.error('Invalid data format:', newItems);
      }
    };

    onMounted(() => {
      window.addEventListener('updateTestItems', updateTestItems as EventListener);
      console.log('Event listener added'); // 确认事件监听器是否添加
    });

    onUnmounted(() => {
      window.removeEventListener('updateTestItems', updateTestItems as EventListener);
      console.log('Event listener removed'); // 确认事件监听器是否移除
    });

    // 添加 watch 监听 testItems 的变化
    watch(testItems, (newVal, oldVal) => {
      console.log('testItems updated:', newVal);
      // 在这里可以执行一些操作，例如更新 UI 或者触发其他逻辑
    });

    return {
      testItems,
      navigateTo
    };
  }
});
</script>

<style scoped>
.main-content {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  padding: 16px;
  background-color: #F5F7FA;
}

.test-card {
  width: 200px;
  cursor: pointer;
  transition: transform 0.2s;
  background-color: white;
  border: 1px solid #dcdcdc;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  visibility: visible; /* 确保卡片可见 */
  opacity: 1; /* 确保卡片不透明 */
}

.test-card:hover {
  transform: scale(1.05);
}

.card-content {
  padding: 16px;
}

.card-content h3 {
  margin: 0;
  font-size: 18px;
  color: #304156;
}

.card-content p {
  margin: 8px 0 0;
  font-size: 14px;
  color: #606266;
}
</style>
