<template>
  <section class="section">
    <div class="container">
      <h1 class="title has-text-primary">Interview Preparation</h1>
      <p class="subtitle">Practice with AI - Get instant feedback on your answers.</p>

      <!-- SETUP PHASE -->
      <div v-if="!interviewStarted" class="box">
        <!-- TECH AND LEVEL SELECTION (COMPACT) -->
        <div class="columns is-vcentered">
          <div class="column is-narrow">
            <div class="field">
              <label class="label">Technology</label>
              <div class="tags are-medium">
                <span v-for="t in techOptions" :key="t" class="tag is-clickable"
                  :class="{ 'is-dark': tech === t, 'is-light': tech !== t }" @click="tech = t">
                  {{ t }}
                </span>
              </div>
            </div>
          </div>

          <div class="column">
            <div class="field">
              <label class="label">Level</label>
              <div class="tags are-medium">
                <span v-for="lvl in levels" :key="lvl" class="tag is-clickable"
                  :class="{ 'is-dark': level === lvl, 'is-light': level !== lvl }" @click="level = lvl">
                  {{ lvl }}
                </span>
              </div>
            </div>
          </div>
        </div>

        <!-- DESCRIPTION INPUT -->
        <div class="field mt-4">
          <label class="label">What would you like to focus on?</label>
          <div class="control">
            <textarea class="textarea"
              placeholder="e.g. I want to prepare for system design questions focusing on scalability and microservices architecture"
              v-model="description" rows="3"></textarea>
          </div>
          <p class="help">Describe what you'd like to practice or any specific topics</p>
        </div>

        <!-- START BUTTON -->
        <div class="field mt-5">
          <div class="control">
            <button class="button is-primary is-fullwidth is-medium" @click="startInterview"
              :disabled="!tech || !level || !description.trim() || isLoading" :class="{ 'is-loading': isLoading }">
              <span class="icon">
                <i class="fas fa-play"></i>
              </span>
              <span>Start Interview</span>
            </button>
          </div>
        </div>
      </div>

      <!-- INTERVIEW PHASE -->
      <div v-else class="box">
        <!-- Progress -->
        <div class="mb-5">
          <div class="level">
            <div class="level-left">
              <div class="level-item">
                <div>
                  <p class="heading">Technology</p>
                  <p class="title is-6">{{ tech }}</p>
                </div>
              </div>
              <div class="level-item">
                <div>
                  <p class="heading">Level</p>
                  <p class="title is-6">{{ level }}</p>
                </div>
              </div>
              <div class="level-item">
                <div>
                  <p class="heading">Question</p>
                  <p class="title is-6">{{ currentQuestionIndex + 1 }} / {{ totalQuestions }}</p>
                </div>
              </div>
            </div>
          </div>
          <progress class="progress is-primary" :value="currentQuestionIndex + 1" :max="totalQuestions"></progress>
        </div>

        <!-- Previous Q&A History (Collapsible) -->
        <div v-if="conversationHistory.length > 0" class="mb-4">
          <button class="button is-small is-light" @click="showHistory = !showHistory">
            <span class="icon">
              <i class="fas" :class="showHistory ? 'fa-chevron-up' : 'fa-chevron-down'"></i>
            </span>
            <span>{{ showHistory ? 'Hide' : 'Show' }} Previous Questions ({{ conversationHistory.length }})</span>
          </button>

          <div v-if="showHistory" class="mt-3">
            <div v-for="(item, idx) in conversationHistory" :key="idx" class="message is-small">
              <div class="message-header">
                <p>Question {{ idx + 1 }}</p>
              </div>
              <div class="message-body">
                <p class="mb-2"><strong>Q:</strong> {{ item.question }}</p>
                <p class="mb-2"><strong>Your Answer:</strong> {{ item.answer }}</p>
                <details>
                  <summary class="has-text-link" style="cursor: pointer;">View Feedback</summary>
                  <div class="content mt-2" v-html="item.formattedFeedback"></div>
                </details>
              </div>
            </div>
          </div>
        </div>

        <!-- Current Question -->
        <div v-if="currentQuestion && !showingFeedback" class="content">
          <div class="notification is-info is-light">
            <p class="title is-5">Question {{ currentQuestionIndex + 1 }}</p>
            <p class="is-size-5">{{ currentQuestion }}</p>
          </div>

          <!-- Answer Input -->
          <div class="field mt-4">
            <label class="label">Your Answer</label>
            <div class="control">
              <textarea class="textarea" v-model="currentAnswer" placeholder="Type your answer here..." rows="6"
                :disabled="isLoading"></textarea>
            </div>
          </div>

          <!-- Submit Button -->
          <div class="field">
            <div class="control">
              <button class="button is-primary" @click="submitAnswer" :disabled="!currentAnswer.trim() || isLoading"
                :class="{ 'is-loading': isLoading }">
                <span class="icon">
                  <i class="fas fa-paper-plane"></i>
                </span>
                <span>Submit Answer</span>
              </button>
            </div>
          </div>
        </div>

        <!-- Feedback Display -->
        <div v-if="showingFeedback" class="content">
          <!-- User's Answer -->
          <div class="message is-info">
            <div class="message-header">
              <p>Question {{ currentQuestionIndex + 1 }}</p>
            </div>
            <div class="message-body">
              <p class="mb-3"><strong>Q:</strong> {{ currentQuestion }}</p>
              <p><strong>Your Answer:</strong></p>
              <p class="mt-2">{{ currentAnswer }}</p>
            </div>
          </div>

          <!-- AI Feedback -->
          <div class="message is-success">
            <div class="message-header">
              <p>AI Feedback</p>
            </div>
            <div class="message-body">
              <div v-html="feedbackHtml"></div>
            </div>
          </div>

          <!-- Next Button -->
          <div class="field mt-4">
            <div class="control">
              <button v-if="currentQuestionIndex < totalQuestions - 1" class="button is-primary" @click="nextQuestion">
                <span class="icon">
                  <i class="fas fa-arrow-right"></i>
                </span>
                <span>Next Question</span>
              </button>
              <button v-else class="button is-success" @click="completeInterview">
                <span class="icon">
                  <i class="fas fa-check"></i>
                </span>
                <span>Complete Interview</span>
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Completion Screen -->
      <div v-if="interviewComplete" class="box has-text-centered">
        <div class="content">
          <span class="icon is-large has-text-success">
            <i class="fas fa-trophy fa-3x"></i>
          </span>
          <h2 class="title is-3 mt-4">Interview Complete! 🎉</h2>
          <p class="subtitle">You've answered {{ totalQuestions }} questions.</p>

          <!-- Review All Questions -->
          <div class="box has-text-left mt-5">
            <h3 class="title is-5">Interview Summary</h3>
            <div v-for="(item, idx) in conversationHistory" :key="idx" class="message">
              <div class="message-header">
                <p>Question {{ idx + 1 }}</p>
              </div>
              <div class="message-body">
                <p class="mb-2"><strong>Q:</strong> {{ item.question }}</p>
                <p class="mb-2"><strong>Your Answer:</strong> {{ item.answer }}</p>
                <details>
                  <summary class="has-text-link" style="cursor: pointer;">View Feedback</summary>
                  <div class="content mt-2" v-html="item.formattedFeedback"></div>
                </details>
              </div>
            </div>
          </div>

          <button class="button is-primary is-medium mt-4" @click="resetInterview">
            <span class="icon">
              <i class="fas fa-redo"></i>
            </span>
            <span>Start New Interview</span>
          </button>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref } from 'vue'

const techOptions = ['Ruby', 'Python', 'JavaScript', 'Go', 'Rust', 'TypeScript']
const levels = ['Junior', 'Mid', 'Senior']

// n8n webhook URLs - UPDATE THESE WITH YOUR ACTUAL WEBHOOK URLS
const N8N_GENERATE_QUESTION_URL = 'YOUR_N8N_WEBHOOK_URL_FOR_QUESTIONS'
const N8N_GET_FEEDBACK_URL = 'YOUR_N8N_WEBHOOK_URL_FOR_FEEDBACK'

// Setup
const tech = ref('Ruby')
const level = ref('Senior')
const description = ref('I want to prepare for system design questions focusing on scalability')

// Interview State
const interviewStarted = ref(false)
const interviewComplete = ref(false)
const isLoading = ref(false)
const showHistory = ref(false)

// Questions
const totalQuestions = ref(5)
const currentQuestionIndex = ref(0)
const currentQuestion = ref('')
const currentAnswer = ref('')
const showingFeedback = ref(false)
const feedbackHtml = ref('')

// Conversation History - stores all Q&A pairs
const conversationHistory = ref([])

// Start the interview
async function startInterview() {
  isLoading.value = true

  try {
    const question = await generateQuestion()
    currentQuestion.value = question
    interviewStarted.value = true
    currentQuestionIndex.value = 0
  } catch (error) {
    console.error('Error starting interview:', error)
    alert('Failed to start interview. Please try again.')
  } finally {
    isLoading.value = false
  }
}

// Generate a question via n8n workflow
async function generateQuestion() {
  const response = await fetch(N8N_GENERATE_QUESTION_URL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      tech: tech.value,
      level: level.value,
      description: description.value,
      questionNumber: currentQuestionIndex.value + 1,
      totalQuestions: totalQuestions.value,
      // Send conversation history for context
      conversationHistory: conversationHistory.value.map(item => ({
        question: item.question,
        answer: item.answer,
        feedback: item.feedback
      }))
    })
  })

  if (!response.ok) {
    throw new Error('Failed to generate question')
  }

  const data = await response.json()
  return data.question || data.message || data.text
}

// Submit answer and get feedback
async function submitAnswer() {
  isLoading.value = true

  try {
    const feedback = await getFeedback()
    feedbackHtml.value = formatFeedback(feedback)

    // Save to conversation history
    conversationHistory.value.push({
      question: currentQuestion.value,
      answer: currentAnswer.value,
      feedback: feedback,
      formattedFeedback: feedbackHtml.value
    })

    showingFeedback.value = true
  } catch (error) {
    console.error('Error getting feedback:', error)
    alert('Failed to get feedback. Please try again.')
  } finally {
    isLoading.value = false
  }
}

// Get AI feedback via n8n workflow
async function getFeedback() {
  const response = await fetch(N8N_GET_FEEDBACK_URL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      question: currentQuestion.value,
      answer: currentAnswer.value,
      tech: tech.value,
      level: level.value,
      // Send conversation history for context
      conversationHistory: conversationHistory.value.map(item => ({
        question: item.question,
        answer: item.answer,
        feedback: item.feedback
      }))
    })
  })

  if (!response.ok) {
    throw new Error('Failed to get feedback')
  }

  const data = await response.json()
  return data.feedback || data.message || data.text
}

// Format feedback with HTML
function formatFeedback(text) {
  return text
    .split('\n\n')
    .map(para => `<p class="mb-3">${para.replace(/\n/g, '<br>')}</p>`)
    .join('')
}

// Move to next question
async function nextQuestion() {
  isLoading.value = true
  showingFeedback.value = false
  currentAnswer.value = ''
  currentQuestionIndex.value++

  try {
    const question = await generateQuestion()
    currentQuestion.value = question
  } catch (error) {
    console.error('Error generating question:', error)
    alert('Failed to generate next question. Please try again.')
  } finally {
    isLoading.value = false
  }
}

// Complete the interview
function completeInterview() {
  interviewComplete.value = true
  interviewStarted.value = false
}

// Reset everything
function resetInterview() {
  interviewStarted.value = false
  interviewComplete.value = false
  currentQuestionIndex.value = 0
  currentQuestion.value = ''
  currentAnswer.value = ''
  showingFeedback.value = false
  feedbackHtml.value = ''
  conversationHistory.value = []
  showHistory.value = false
  description.value = ''
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

.message {
  margin-bottom: 1rem;
}

details summary {
  user-select: none;
}

details[open] summary {
  margin-bottom: 0.5rem;
}
</style>