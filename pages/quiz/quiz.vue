<template>
  <view class="quiz-container">
    <view v-if="error">{{ error }}</view>
    <template v-else>
      <view class="quiz-header">
        <text class="quiz-progress">{{ currentQuestion }}/{{ totalQuestions }}</text>
        <text class="quiz-timer">{{ formatTime(timer) }}</text>
      </view>
      <swiper class="quiz-swiper" :current="currentQuestion - 1" @change="onSwiperChange">
        <swiper-item v-for="(question, index) in questions" :key="index">
          <view class="quiz-question">
            <text>{{ question.question }}</text>
          </view>
          <view class="quiz-options">
            <view
                v-for="(option, optionIndex) in question.options"
                :key="optionIndex"
                class="quiz-option"
                :class="{ 'selected': question.selectedOption === optionIndex }"
                @click="selectOption(index, optionIndex)"
            >
              <text>{{ String.fromCharCode(65 + optionIndex) }}. {{ option }}</text>
            </view>
          </view>
        </swiper-item>
      </swiper>
      <view class="quiz-actions">
        <button class="share-btn">分享</button>
        <button class="answer-card-btn" @click="showAnswerCard = true">答题卡</button>
      </view>
    </template>

    <AnswerCard
        v-if="showAnswerCard"
        :answeredQuestions="answeredQuestions"
        @close="showAnswerCard = false"
        @goToQuestion="goToQuestion"
        @submit="submitQuiz"
    />
  </view>
</template>


<script setup>
import { ref, onUnmounted, watch, computed } from 'vue';
import { onLoad, onShow } from '@dcloudio/uni-app';
import AnswerCard from './AnswerCard.vue';

const quizTitle = ref('');
const currentQuestion = ref(1);
const totalQuestions = ref(3);
const timer = ref(0);
const error = ref('');

const questions = ref([
  {
    question: '假如地球重力加速度减为现在的一半，下列数值不会发生变化的是:',
    options: [
      '鱼在相同水深下受到的压强',
      '船在水中的吃水深度',
      '人在体重计上的称量结果',
      '人可以举起的石块的最大质量'
    ],
    selectedOption: null
  },
  {
    question: '下列哪项不是计算机的输入设备？',
    options: [
      '键盘',
      '鼠标',
      '显示器',
      '扫描仪'
    ],
    selectedOption: null
  },
  {
    question: '以下哪种编程语言不是面向对象的？',
    options: [
      'Java',
      'C++',
      'C',
      'Python'
    ],
    selectedOption: null
  }
]);

let timerInterval;

onLoad((option) => {
  if (option && option.title) {
    quizTitle.value = decodeURIComponent(option.title);
    setNavigationBarTitle(quizTitle.value);
  } else {
    error.value = '加载题目失败，请返回重试';
  }
});

onShow(() => {
  startTimer();
});

onUnmounted(() => {
  clearInterval(timerInterval);
});

watch(currentQuestion, (newVal) => {
  setNavigationBarTitle(`${quizTitle.value} (${newVal}/${totalQuestions.value})`);
});

const startTimer = () => {
  timerInterval = setInterval(() => {
    timer.value++;
  }, 1000);
};

const formatTime = (seconds) => {
  const minutes = Math.floor(seconds / 60);
  const remainingSeconds = seconds % 60;
  return `${String(minutes).padStart(2, '0')}:${String(remainingSeconds).padStart(2, '0')}`;
};

const selectOption = (questionIndex, optionIndex) => {
  questions.value[questionIndex].selectedOption = optionIndex;
};

const onSwiperChange = (e) => {
  currentQuestion.value = e.detail.current + 1;
};

const setNavigationBarTitle = (title) => {
  uni.setNavigationBarTitle({
    title: title
  });
};

const showAnswerCard = ref(false);

const answeredQuestions = computed(() => {
  return questions.value
      .map((q, index) => q.selectedOption !== null ? index + 1 : null)
      .filter(q => q !== null);
});

const goToQuestion = (questionNumber) => {
  currentQuestion.value = questionNumber;
  showAnswerCard.value = false;
};

const submitQuiz = () => {
  showAnswerCard.value = false;
};
</script>


<style>
.quiz-container {
  padding: 20rpx;
  height: calc(100vh - 150rpx);
  display: flex;
  flex-direction: column;
}

.quiz-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20rpx;
}

.quiz-swiper {
  flex: 1;
}

.quiz-question {
  margin-bottom: 20rpx;
  font-size: 18px;
  font-weight: bold;
}

.quiz-option {
  background-color: #f0f0f0;
  padding: 20rpx;
  margin-bottom: 10rpx;
  border-radius: 5rpx;
  border: 2rpx solid transparent;
}

.quiz-option.selected {
  border-color: #ffa500;
  background-color: #fff5e6;
}

.quiz-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20rpx;
}

.share-btn, .answer-card-btn {
  padding: 10rpx 20rpx;
  border-radius: 25rpx;
  width: 45%;
  text-align: center;
}

.share-btn {
  background-color: #ffd700;
}

.answer-card-btn {
  background-color: #f87171;
  color: white;
}
</style>
