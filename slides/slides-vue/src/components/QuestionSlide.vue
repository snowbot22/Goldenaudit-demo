<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  slide: {
    type: Object,
    required: true
  },
  modelValue: {
    type: Object,
    default: null
  }
})

const emit = defineEmits(['update:modelValue', 'answer-selected'])

const selectedOption = ref(props.modelValue)

// Reset selection when slide changes
watch(() => props.slide, () => {
  selectedOption.value = props.modelValue
})

const selectOption = (option) => {
  selectedOption.value = option
  emit('update:modelValue', option)
  emit('answer-selected', option)
}

const isImage = (text) => {
  if (!text) return false;
  return /\.(png|jpe?g|gif|webp|svg)$/i.test(text);
}
</script>

<template>
  <div class="question-slide">
    <div class="question-header">
      <span class="badge">Question</span>
      <h2>{{ slide.title }}</h2>
      <p class="question-text">{{ slide.text }}</p>
    </div>
    
    <div class="options-container">
      <button 
        v-for="option in slide.options" 
        :key="option.id"
        class="option-btn"
        :class="{ 'selected': selectedOption && selectedOption.id === option.id }"
        @click="selectOption(option)"
      >
        <div class="option-marker">{{ option.id }}</div>
        
        <template v-if="isImage(option.text)">
          <div class="option-image-wrapper">
            <!-- For Vite, dynamic paths like this work best if the images are in the 'public' folder -->
            <img :src="option.text" alt="Option image" class="option-image" />
          </div>
        </template>
        <span v-else class="option-text">{{ option.text }}</span>
      </button>
    </div>
  </div>
</template>

<style scoped>
.question-slide {
  padding: 2.5rem;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.question-header {
  margin-bottom: 2rem;
}

.badge {
  display: inline-block;
  background: rgba(15, 76, 129, 0.1);
  color: var(--color-primary);
  padding: 0.25rem 0.75rem;
  border-radius: 999px;
  font-size: 0.875rem;
  font-weight: 600;
  margin-bottom: 1rem;
  border: 1px solid rgba(15, 76, 129, 0.2);
}

h2 {
  font-size: 1.75rem;
  margin-bottom: 1rem;
}

.question-text {
  font-size: 1.2rem;
  color: var(--color-text-muted);
  line-height: 1.5;
}

.options-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-top: auto;
}

.option-btn {
  background: #ffffff;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  padding: 1rem;
  display: flex;
  align-items: center;
  color: var(--color-text);
  transition: all var(--transition-fast);
  text-align: left;
  box-shadow: 0 2px 4px rgba(0,0,0,0.02);
}

.option-btn:hover {
  background: #f8fafc;
  border-color: #cbd5e1;
  transform: translateX(5px);
}

.option-btn.selected {
  background: rgba(15, 76, 129, 0.05);
  border-color: var(--color-primary);
  box-shadow: 0 4px 12px rgba(15, 76, 129, 0.15);
}

.option-marker {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: #f1f5f9;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  margin-right: 1rem;
  flex-shrink: 0;
  transition: all var(--transition-fast);
}

.option-btn.selected .option-marker {
  background: var(--color-primary);
  color: white;
}

.option-text {
  font-size: 1.05rem;
}

.option-image-wrapper {
  flex-grow: 1;
  display: flex;
  align-items: center;
  justify-content: flex-start;
  padding: 0.5rem 0;
}

.option-image {
  max-width: 200px;
  max-height: 150px;
  border-radius: var(--radius-sm);
  border: 1px solid var(--color-border);
  object-fit: contain;
  background: white;
}
</style>
