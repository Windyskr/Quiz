<template>
  <view class="answer-card">
    <view class="status-bar">
      <view class="status-item">
        <view class="status-dot answered"></view>
        <text>已答</text>
      </view>
      <view class="status-item">
        <view class="status-dot unanswered"></view>
        <text>未答</text>
      </view>
      <view class="close-button" @click="closeCard">×</view>
    </view>
    <view class="instruction">
      <text>点击题目序号可跳转到该题哦！</text>
    </view>
    <view class="question-grid" :style="gridStyle">
      <view
          v-for="n in totalQuestions"
          :key="n"
          class="question-number"
          :class="{ answered: isAnswered(n) }"
          @click="goToQuestion(n)"
      >
        {{ n }}
      </view>
    </view>
    <view class="submit-button" @click="submitQuiz">
      交卷并查看结果
    </view>
  </view>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  answeredQuestions: {
    type: Array,
    default: () => []
  },
  totalQuestions: {
    type: Number,
    required: true
  }
});

const emit = defineEmits(['close', 'goToQuestion', 'submit']);

const isAnswered = (questionNumber) => {
  return props.answeredQuestions.includes(questionNumber);
};

const closeCard = () => {
  emit('close');
};

const goToQuestion = (questionNumber) => {
  emit('goToQuestion', questionNumber);
};

const submitQuiz = () => {
  emit('submit');
};

const gridStyle = computed(() => {
  const columns = props.totalQuestions <= 5 ? props.totalQuestions : 5;
  return {
    gridTemplateColumns: `repeat(${columns}, 1fr)`
  };
});
</script>

<style scoped>
.answer-card {
  background-color: white;
  border-radius: 10px 10px 0 0;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.status-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.status-item {
  display: flex;
  align-items: center;
}

.status-dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin-right: 5px;
}

.answered {
  background-color: #f87171;
}

.unanswered {
  background-color: #e5e5e5;
}

.close-button {
  font-size: 24px;
  cursor: pointer;
}

.instruction {
  text-align: center;
  margin-bottom: 15px;
  font-size: 14px;
  color: #666;
}

.question-grid {
  display: grid;
  gap: 10px;
  margin-bottom: 20px;
}

.question-number {
  width: 40px;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  border: 1px solid #e5e5e5;
  font-size: 16px;
  cursor: pointer;
}

.question-number.answered {
  background-color: #f87171;
  color: white;
  border-color: #f87171;
}

.submit-button {
  background-color: #f87171;
  color: white;
  text-align: center;
  padding: 15px;
  border-radius: 25px;
  font-size: 16px;
  cursor: pointer;
}
</style>