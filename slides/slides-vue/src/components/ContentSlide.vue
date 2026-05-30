<script setup>
defineProps({
  slide: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <div class="content-slide">
    <div class="image-container" v-if="slide.imageUrl">
      <img :src="slide.imageUrl" :alt="slide.title" />
      <div class="overlay"></div>
    </div>
    <div class="text-content">
      <h2>{{ slide.title }}</h2>
      <p>{{ slide.text }}</p>
    </div>
  </div>
</template>

<style scoped>
.content-slide {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.image-container {
  position: relative;
  width: 100%;
  height: 250px;
  overflow: hidden;
  border-radius: var(--radius-md) var(--radius-md) 0 0;
  flex-shrink: 0;
}

.image-container img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.5s ease;
}

.content-slide:hover .image-container img {
  transform: scale(1.05);
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(to bottom, rgba(255, 255, 255, 0) 0%, rgba(255, 255, 255, 0.5) 100%);
}

.text-content {
  padding: 2rem;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

h2 {
  font-size: 2rem;
  margin-bottom: 1rem;
  color: var(--color-primary);
}

p {
  font-size: 1.1rem;
  line-height: 1.6;
  color: var(--color-text-muted);
}
</style>
