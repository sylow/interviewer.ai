<template>
  <section class="hero is-fullheight is-light">
    <div class="hero-body">
      <div class="container has-text-centered">
        <!-- Title -->
        <h1 class="title is-2 has-text-primary">
          Your AI Career Assistant
        </h1>
        <p class="subtitle is-5 mb-6">
          Prepare interviews, craft cover letters, and boost your job search with AI.
        </p>

        <!-- Feature Cards -->
        <div class="columns is-centered is-multiline">
          <!-- Interview Prep -->
          <div class="column is-4">
            <div class="box has-text-centered">
              <span class="icon is-large has-text-info">
                <i class="fas fa-comments fa-2x"></i>
              </span>
              <h2 class="title is-5 mt-3">Interview Preparation</h2>
              <p>
                Practice common questions and get AI feedback on your answers.
              </p>
              <NuxtLink to="/interview" class="button is-info is-light mt-3">
                Start Practice
              </NuxtLink>
            </div>
          </div>

          <!-- Cover Letter Generator -->
          <div class="column is-4">
            <div class="box has-text-centered">
              <span class="icon is-large has-text-primary">
                <i class="fas fa-file-alt fa-2x"></i>
              </span>
              <h2 class="title is-5 mt-3">Cover Letter Generator</h2>
              <p>
                Upload your resume and job post, and let AI craft a tailored letter.
              </p>
              <NuxtLink to="/cover-letter" class="button is-primary is-light mt-3">
                Create Cover Letter
              </NuxtLink>
            </div>
          </div>

          <!-- Resume Enhancer -->
          <div class="column is-4">
            <div class="box has-text-centered">
              <span class="icon is-large has-text-success">
                <i class="fas fa-user-tie fa-2x"></i>
              </span>
              <h2 class="title is-5 mt-3">Resume Enhancer</h2>
              <p>
                Improve your CV using AI suggestions to highlight your strengths.
              </p>
              <NuxtLink to="/resume" class="button is-success is-light mt-3">
                Improve Resume
              </NuxtLink>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
// No logic yet — Nuxt handles routing automatically
</script>

<style scoped>
.hero {
  background: linear-gradient(135deg, #f5f7fa 0%, #e4ecf7 100%);
}
</style>
