<template>
  <div class="main-content">
    <el-card v-for="item in testItems" :key="item.url" class="test-card" @click="fetchAndDisplay(item.url)">
      <div class="card-content">
        <h3 class="card-title">{{ item.name }}</h3>
      </div>
    </el-card>
    <el-dialog v-model="dialogVisible" width="80%">
      <div class="dialog-content">
        <p class="dialog-message">{{ dialogMessage }}</p>
        <p class="error-count">错误条数: {{ errorCount }}</p>
        <div class="test-cards">
          <el-card v-for="(testCase, index) in dialogData" :key="index" :class="{ 'highlight': testCase.fields['预期结果'] !== testCase.fields['测试结果'], 'normal': testCase.fields['预期结果'] === testCase.fields['测试结果'] }" class="test-case-card">
            <div class="test-case-content">
              <p><strong>时间:</strong> {{ testCase.time }}</p>
              <p><strong>测试用例名称:</strong> {{ testCase.testCaseName }}</p>
              <table class="fields-table">
                <tbody>
                <tr v-for="(value, key) in testCase.fields" :key="key">
                  <td class="label-cell">{{ key }}:</td>
                  <td :class="{ 'highlight-value': testCase.fields['预期结果'] !== testCase.fields['测试结果'] && key === '测试信息' }">{{ value }}</td>
                </tr>
                </tbody>
              </table>
            </div>
          </el-card>
        </div>
      </div>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="dialogVisible = false">关闭</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, onBeforeUnmount } from 'vue';
import axios from 'axios';

export default defineComponent({
  name: 'MainContent',
  setup() {
    const testItems = ref<any[]>([]);
    const dialogVisible = ref(false);
    const dialogMessage = ref('');
    const dialogData = ref<any[]>([]);
    const errorCount = ref(0);

    const fetchAndDisplay = async (url: string) => {
      try {
        const response = await axios.get(`http://localhost:8080${url}`);
        const { code, msg, data } = response.data;
        dialogMessage.value = msg;
        dialogData.value = data;

        // 计算错误条数
        errorCount.value = data.filter(testCase => testCase.fields['预期结果'] !== testCase.fields['测试结果']).length;

        dialogVisible.value = true;
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };

    // 监听来自侧边栏的事件
    const handleUpdateTestItems = (event: CustomEvent) => {
      testItems.value = event.detail;
      console.log('Received test items:', testItems.value);
    };

    onMounted(() => {
      window.addEventListener('updateTestItems', handleUpdateTestItems);
    });

    onBeforeUnmount(() => {
      window.removeEventListener('updateTestItems', handleUpdateTestItems);
    });

    return {
      testItems,
      dialogVisible,
      dialogMessage,
      dialogData,
      errorCount,
      fetchAndDisplay
    };
  }
});
</script>

<style scoped>
.main-content {
  display: flex;
  flex-wrap: wrap;
  gap: 24px;
  padding: 24px;
  background-color: #f0f2f5;
}

.test-card {
  width: 250px;
  background: linear-gradient(135deg, #ffffff 0%, #f0f0f0 100%);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.2s, box-shadow 0.2s;
  cursor: pointer;
}

.test-card:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.card-content {
  padding: 24px;
  text-align: center;
}

.card-title {
  font-size: 18px;
  font-weight: bold;
  color: #333333;
  margin: 0;
  transition: color 0.2s;
}

.test-card:hover .card-title {
  color: #007bff;
}

.dialog-content {
  padding: 24px;
}

.dialog-message {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 16px;
  color: #333333;
}

.error-count {
  font-size: 16px;
  font-weight: bold;
  color: #ff4d4d;
  margin-bottom: 16px;
}

.test-cards {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-bottom: 16px;
}

.test-case-card {
  width: 100%;
  background: #ffffff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
  transition: box-shadow 0.2s;
  border-left: 4px solid transparent;
}

.test-case-card.highlight {
  background: #ffcccc;
  border-left-color: #ff4d4d;
}

.test-case-card.normal {
  background: #e6f7ff;
  border-left-color: #007bff;
}

.test-case-content {
  padding: 16px;
}

.fields-table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  margin-top: 16px;
}

.fields-table td {
  padding: 8px;
  border: none;
  color: #555555;
  font-size: 14px;
  text-align: center; /* 居中显示 */
  border-radius: 8px; /* 圆角 */
  background-color: transparent; /* 透明背景 */
}

.fields-table strong {
  color: #333333;
}

.label-cell {
  width: 30%; /* 调整列宽 */
  text-align: right; /* 右对齐 */
  padding-right: 16px; /* 右侧内边距 */
}

/* 高亮“测试信息”对应的值 */
.highlight-value {
  background-color: #ffffff !important; /* 白色背景 */
  color: #DC143C !important; /* 黑色字体 */
  font-weight: bold;
  font-size: 33px; /* 增大字体大小 */
}
</style>