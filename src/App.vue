<template>
  <header>
    <IconBook class="logo" />
    <div class="wrapper">
      <GuessTheBookVue />
    </div>
  </header>

  <main>
    <GuessBookBasedOnFirstLine />
  </main>
</template>

<script setup lang="ts">
import GuessTheBookVue from './components/GuessTheBook.vue'
import GuessBookBasedOnFirstLine from './components/GuessBookBasedOnFirstLine.vue'
import IconBook from '@/components/icons/IconBook.vue'
</script>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 0.8rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 0.8rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
