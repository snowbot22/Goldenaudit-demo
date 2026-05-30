<script setup>
import { ref, computed } from 'vue'
import mockData from '../data/mockData.json'
import ContentSlide from './ContentSlide.vue'
import QuestionSlide from './QuestionSlide.vue'
import ScoreSummary from './ScoreSummary.vue'

const slides = mockData.slides
const currentSlideIndex = ref(0)
const answers = ref({})
const isFinished = ref(false)

const currentSlide = computed(() => slides[currentSlideIndex.value])

const isLastSlide = computed(() => currentSlideIndex.value === slides.length - 1)
const isFirstSlide = computed(() => currentSlideIndex.value === 0)

const maxScore = computed(() => {
  return slides
    .filter(s => s.type === 'question')
    .reduce((total, s) => {
      const maxOptionScore = Math.max(...s.options.map(o => o.score))
      return total + maxOptionScore
    }, 0)
})

const currentScore = computed(() => {
  let score = 0
  for (const slideId in answers.value) {
    const slide = slides.find(s => s.id == slideId)
    if (slide && slide.type === 'question') {
      const selectedOption = answers.value[slideId]
      if (selectedOption) {
        score += selectedOption.score
      }
    }
  }
  return score
})

const canAdvance = computed(() => {
  if (currentSlide.value.type === 'question') {
    return !!answers.value[currentSlide.value.id]
  }
  return true
})

const nextSlide = () => {
  if (canAdvance.value) {
    if (isLastSlide.value) {
      isFinished.value = true
    } else {
      currentSlideIndex.value++
    }
  }
}

const prevSlide = () => {
  if (!isFirstSlide.value) {
    currentSlideIndex.value--
  }
}

const handleRestart = () => {
  currentSlideIndex.value = 0
  answers.value = {}
  isFinished.value = false
}

const progress = computed(() => {
  return ((currentSlideIndex.value + 1) / slides.length) * 100
})
</script>

<template>
  <div class="slide-player glass-panel">
    
    <template v-if="!isFinished">
      <!-- Progress Bar -->
      <div class="progress-container">
        <div class="progress-bar" :style="{ width: `${progress}%` }"></div>
      </div>

      <!-- Slide Viewport -->
      <div class="viewport">
        <Transition name="slide" mode="out-in">
          <component 
            :is="currentSlide.type === 'content' ? ContentSlide : QuestionSlide"
            :key="currentSlide.id"
            :slide="currentSlide"
            v-model="answers[currentSlide.id]"
          />
        </Transition>
      </div>

      <!-- Controls -->
      <div class="controls">
        <button 
          class="btn-text" 
          @click="prevSlide" 
          :disabled="isFirstSlide"
          :style="{ visibility: isFirstSlide ? 'hidden' : 'visible' }"
        >
          Previous
        </button>
        
        <div class="indicator">
          {{ currentSlideIndex + 1 }} / {{ slides.length }}
        </div>
        
        <button 
          class="btn-primary" 
          @click="nextSlide"
          :disabled="!canAdvance"
        >
          {{ isLastSlide ? 'Finish' : 'Next' }}
        </button>
      </div>
    </template>

    <template v-else>
      <ScoreSummary 
        :score="currentScore" 
        :maxScore="maxScore"
        @restart="handleRestart"
      />
    </template>
  </div>
</template>

<style scoped>
.slide-player {
  width: 100%;
  max-width: 800px;
  height: 600px;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
}

.progress-container {
  height: 4px;
  background: var(--color-border);
  width: 100%;
  border-radius: var(--radius-lg) var(--radius-lg) 0 0;
}

.progress-bar {
  height: 100%;
  background: var(--color-primary);
  transition: width 0.3s ease;
}

.viewport {
  flex-grow: 1;
  overflow-y: auto;
  overflow-x: hidden;
  position: relative;
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem 2rem;
  border-top: 1px solid var(--color-border);
  background: #f8fafc;
  border-radius: 0 0 var(--radius-lg) var(--radius-lg);
}

.btn-text {
  background: transparent;
  color: var(--color-text-muted);
  font-weight: 600;
  padding: 0.5rem 1rem;
  transition: color var(--transition-fast);
}

.btn-text:hover {
  color: var(--color-text);
}

.indicator {
  font-weight: 600;
  color: var(--color-text-muted);
  font-size: 0.9rem;
  letter-spacing: 2px;
}
</style>
