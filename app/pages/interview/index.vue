<template>
  <section class="section">
    <div class="container">
      <h1 class="title has-text-primary">Interview Preparation</h1>
      <p class="subtitle">Choose your tech, level, and describe your goal.</p>

      <!-- TECH SELECTION -->
      <div class="box">
        <div class="field">
          <label class="label">Technology</label>
          <div class="tags are-medium">
            <span v-for="t in techOptions" :key="t" class="tag is-clickable"
              :class="{ 'is-primary': selection.name === t, 'is-light': selection.name !== t }"
              @click="selection.name = t">
              {{ t }}
            </span>
          </div>
        </div>

        <!-- LEVEL SELECTION -->
        <div class="field mt-4">
          <label class="label">Level</label>
          <div class="tags are-medium">
            <span v-for="lvl in levels" :key="lvl" class="tag is-clickable"
              :class="{ 'is-link': selection.level === lvl, 'is-light': selection.level !== lvl }"
              @click="selection.level = lvl">
              {{ lvl }}
            </span>
          </div>
        </div>

        <!-- DESCRIPTION INPUT -->
        <div class="field mt-4">
          <label class="label">Description</label>
          <div class="control">
            <input class="input" type="text"
              placeholder="e.g. Object-oriented programming and system design preparation"
              v-model="selection.description" />
          </div>
        </div>
      </div>

      <!-- PREVIEW -->
      <div v-if="selection.name && selection.level && selection.description" class="box mt-5">
        <div class="columns is-vcentered">
          <div class="column is-1 has-text-centered">
            <span class="icon is-large">
              <i :class="getIcon(selection.name)" style="font-size: 2rem;"></i>
            </span>
          </div>
          <div class="column">
            <h2 class="title is-5">{{ selection.name }}</h2>
            <p class="is-size-6 has-text-grey">
              Level: <strong>{{ selection.level }}</strong>
            </p>
            <p>{{ selection.description }}</p>
          </div>
        </div>

        <div class="has-text-centered mt-4">
          <button class="button is-primary is-medium">
            Start Interview Practice
          </button>
        </div>
      </div>

      <div v-else class="has-text-centered mt-6">
        <p class="is-size-5 has-text-grey">Select all and write your goal to begin.</p>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const techOptions = ['Ruby', 'Python', 'JavaScript', 'Go', 'Rust', 'TypeScript']
const levels = ['Junior', 'Mid', 'Senior']

// Default selection
const selection = ref({
  name: 'Ruby',
  level: 'Senior',
  description: 'Object-oriented programming and system design preparation.'
})

function getIcon(name) {
  switch (name.toLowerCase()) {
    case 'ruby':
      return 'fab fa-gem'
    case 'python':
      return 'fab fa-python'
    case 'javascript':
      return 'fab fa-js-square'
    case 'go':
      return 'fas fa-water'
    case 'rust':
      return 'fas fa-cog'
    case 'typescript':
      return 'fas fa-code'
    default:
      return 'fas fa-laptop-code'
  }
}
</script>

<style scoped>
.section {
  background-color: #fafafa;
  min-height: 100vh;
}

.tag {
  margin: 0.25rem;
  cursor: pointer;
  transition: all 0.2s ease;
}

.tag:hover {
  transform: scale(1.05);
}
</style>
