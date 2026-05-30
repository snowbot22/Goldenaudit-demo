<script setup>
import { computed } from 'vue'
import mockData from '../data/mockData.json'

const props = defineProps({
  score: {
    type: Number,
    required: true
  },
  maxScore: {
    type: Number,
    required: true
  }
})

const emit = defineEmits(['restart'])

const scoringResult = computed(() => {
  if (props.score >= mockData.scoring.excellent.min) {
    return { ...mockData.scoring.excellent, type: 'excellent' }
  } else if (props.score >= mockData.scoring.good.min) {
    return { ...mockData.scoring.good, type: 'good' }
  } else {
    return { ...mockData.scoring.needs_improvement, type: 'needs_improvement' }
  }
})

const percentage = computed(() => {
  return props.maxScore > 0 ? Math.round((props.score / props.maxScore) * 100) : 0
})

const restart = () => {
  emit('restart')
}
</script>

<template>
  <div class="score-summary">
    <div class="summary-content">
      <h2>Assessment Complete</h2>
      
      <div class="score-circle" :class="scoringResult.type">
        <svg viewBox="0 0 36 36" class="circular-chart">
          <path class="circle-bg"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
          <path class="circle"
            :stroke-dasharray="`${percentage}, 100`"
            d="M18 2.0845
              a 15.9155 15.9155 0 0 1 0 31.831
              a 15.9155 15.9155 0 0 1 0 -31.831"
          />
        </svg>
        <div class="score-text">
          <span class="score-value">{{ score }}</span>
          <span class="score-max">/ {{ maxScore }}</span>
        </div>
      </div>

      <div class="feedback">
        <h3 :class="scoringResult.type">{{ percentage }}% Score</h3>
        <p>{{ scoringResult.message }}</p>
      </div>

      <button class="btn-primary restart-btn" @click="restart">
        Retake Module
      </button>
    </div>
  </div>
</template>

<style scoped>
.score-summary {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  padding: 2rem;
  text-align: center;
}

.summary-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 400px;
}

h2 {
  font-size: 2rem;
  margin-bottom: 2rem;
}

.score-circle {
  position: relative;
  width: 180px;
  height: 180px;
  margin-bottom: 2rem;
}

.circular-chart {
  display: block;
  margin: 0 auto;
  max-width: 100%;
  max-height: 250px;
}

.circle-bg {
  fill: none;
  stroke: var(--color-border);
  stroke-width: 2.5;
}

.circle {
  fill: none;
  stroke-width: 2.5;
  stroke-linecap: round;
  transition: stroke-dasharray 1s ease-out;
}

.excellent .circle { stroke: var(--color-success); }
.good .circle { stroke: #f59e0b; }
.needs_improvement .circle { stroke: var(--color-error); }

.score-text {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  display: flex;
  flex-direction: column;
  align-items: center;
}

.score-value {
  font-size: 3rem;
  font-weight: 700;
  line-height: 1;
}

.score-max {
  font-size: 1.2rem;
  color: var(--color-text-muted);
  margin-top: 0.25rem;
}

.feedback {
  margin-bottom: 2.5rem;
}

.feedback h3 {
  font-size: 1.5rem;
  margin-bottom: 0.5rem;
}

.feedback h3.excellent { color: var(--color-success); }
.feedback h3.good { color: #f59e0b; }
.feedback h3.needs_improvement { color: var(--color-error); }

.feedback p {
  color: var(--color-text-muted);
  font-size: 1.1rem;
  line-height: 1.5;
}

.restart-btn {
  width: 100%;
  padding: 1rem;
  font-size: 1.1rem;
}
</style>
