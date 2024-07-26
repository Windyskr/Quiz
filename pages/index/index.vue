<template>
  <view class="content">
    <view class="exercise-list">
      <view v-for="(item, index) in exercises" :key="index" class="exercise-item">
        <view class="exercise-header" @click="toggleExpand(index)">
          <text>{{ item.title }}</text>
          <text class="arrow">{{ item.isExpanded ? '∨' : '＞' }}</text>
        </view>
        <view v-if="item.isExpanded" class="exercise-details">
          <view v-for="(subItem, subIndex) in item.subItems" :key="subIndex" class="sub-item" @click="navigateToQuiz(subItem.title)">
            <view class="sub-item-header">
              <text>{{ subItem.title }}</text>
            </view>
            <view class="sub-item-progress">
              <text class="progress-text">完成进度: {{ subItem.progress }}, 正确率{{ subItem.accuracy }}</text>
              <text class="status-text" :style="{ color: subItem.status === '未开始' ? '#FFA500' : '#4CAF50' }">{{ subItem.status }}</text>
            </view>
            <text class="sub-arrow">＞</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue';

const exercises = ref([
  {
    title: '【常识判断】',
    isExpanded: false,
    subItems: [
      { title: '【科技常识】', progress: '0/580', accuracy: '0%', status: '未开始', id: '01' },
      { title: '【人文常识】', progress: '0/506', accuracy: '0%', status: '未开始', id: '02'},
      { title: '【政治常识】', progress: '0/195', accuracy: '0%', status: '未开始' , id: '03'},
      { title: '【历史常识】', progress: '0/100', accuracy: '0%', status: '未开始' , id: '04'},
      { title: '【地理常识】', progress: '0/224', accuracy: '0%', status: '未开始' , id: '05'},
      { title: '【生态常识】', progress: '0/151', accuracy: '0%', status: '未开始' , id: '06'},
    ]
  },
  { title: '【资料分析】', isExpanded: false, subItems: [] },
  { title: '【言语理解与表达】', isExpanded: false, subItems: [] },
]);

const toggleExpand = (index) => {
  exercises.value[index].isExpanded = !exercises.value[index].isExpanded;
};

const navigateToQuiz = (title) => {
  uni.navigateTo({
  	url:"/pages/quiz/quiz?title=" + title
  })
};

</script>

<style>
.content {
  display: flex;
  flex-direction: column;
  background-color: #f8f8f8;
  height: 100vh;
}

.exercise-list {
  padding: 20rpx;
}

.exercise-item {
  background-color: white;
  margin-bottom: 20rpx;
  border-radius: 10rpx;
  overflow: hidden;
}

.exercise-header {
  display: flex;
  justify-content: space-between;
  padding: 30rpx 20rpx;
  font-weight: bold;
}

.exercise-details {
  border-top: 1rpx solid #f0f0f0;
}

.sub-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx;
  border-bottom: 1rpx solid #f0f0f0;
}

.sub-item-header {
  font-weight: bold;
}

.sub-item-progress {
  flex: 1;
  margin-left: 20rpx;
  font-size: 24rpx;
  color: #666;
}

.status-text {
  margin-left: 10rpx;
}

.arrow, .sub-arrow {
  color: #999;
}
</style>