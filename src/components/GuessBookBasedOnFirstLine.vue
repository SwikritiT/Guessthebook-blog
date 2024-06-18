<template>
  <BookItem>
    <template #score>
      <span v-if="!showTotalScore" class="score"> Score: {{ score }} </span>
      <span v-if="!showTotalScore" class="remaining"> {{ iteration }}/20 </span>
      <span v-if="showTotalScore" class="total-score"> Total Score: {{ score }}</span>
      <button v-if="showTotalScore" id="restart" class="hidden" @click="restartQuiz">
        Play Again
      </button>
    </template>
    <template v-if="!showTotalScore" #heading>
      <div>“{{ description }}”</div>
    </template>
    <template v-if="!showTotalScore" #options>
      <div class="options" v-for="(book, index) in selectedBooks" :key="index">
        <input
          type="text"
          class="book-titles"
          :class="{
            'input-error': book === selectedIncorrectOption,
            'input-right': book === selectedCorrectOption
          }"
          :value="book"
          @click="checkSelectedOption(book)"
          :disabled="showErrorMessage"
          readonly
        />
      </div>
      <IconRightArrow v-if="showErrorMessage" class="next" @click="nextQuestion" />
    </template>
  </BookItem>
</template>

<script setup lang="ts">
import books from '../util/books.json'
import bookOption from '../util/booktitles.json'
import { ref, computed, onMounted } from 'vue'
import BookItem from '@/components/BookItem.vue'
import IconRightArrow from '@/components/icons/IconRightArrow.vue'

const selectedBook = ref<string | null>(null)
const showNext = ref(false)
const description = ref<string | null>(null)
const optionBooks = ref<string[] | null>(null)
const actualBook = ref<string | null>(null)
const score = ref(0)
const titlesToGuess = ref<[string, string][] | null>(null)
const showErrorMessage = ref(false)
const showTotalScore = ref(false)
const iteration = ref(1) // number of question
const selectedIncorrectOption = ref<string | null>(null)
const selectedCorrectOption = ref<string | null>(null)

function setArrayForQuiz() {
  const entries = Object.entries(books)
  titlesToGuess.value = shuffleArray(entries)
}

function reset() {
  selectedBook.value = null
  showNext.value = false
  description.value = null
  optionBooks.value = null
  actualBook.value = null
  score.value = 0
  titlesToGuess.value = null
  showErrorMessage.value = false
  showTotalScore.value = false
  iteration.value = 1
  selectedIncorrectOption.value = null
  selectedCorrectOption.value = null
}
function displayDescription() {
  if (iteration.value > 20) {
    showTotalScore.value = true
    return
  }
  const [title, firstLine] = (titlesToGuess.value?.[iteration.value] || []) as [string, string]
  description.value = firstLine
  optionBooks.value = getRandomElementsWithExclusion(title)
  actualBook.value = title
}

function getRandomElementsWithExclusion(exclusion: string): string[] {
  // Filter out elements equal to the exclusion string i.e book title
  const filteredArray = bookOption.filter(
    (item) => item.trim().toLowerCase() !== exclusion.trim().toLowerCase()
  )

  if (filteredArray.length < 3) {
    throw new Error('Array size is less than 3 after exclusion.')
  }

  const result: string[] = []
  while (result.length < 3) {
    const randomIndex = Math.floor(Math.random() * filteredArray.length)
    const randomElement = filteredArray.splice(randomIndex, 1)[0]
    result.push(randomElement)
  }

  result.push(exclusion) // Add the excluded string to the result
  return shuffleArray(result)
}

// Fisher-Yates Algorithm for shuffling
function shuffleArray<T>(array: T[]): T[] {
  const shuffledArray = [...array] // Create a copy of the original array

  for (let i = shuffledArray.length - 1; i > 0; i--) {
    // Generate a random index between 0 and i (inclusive)
    const randomIndex = Math.floor(Math.random() * (i + 1))

    // Swap elements using the randomIndex
    const temp = shuffledArray[i]
    shuffledArray[i] = shuffledArray[randomIndex]
    shuffledArray[randomIndex] = temp
  }

  return shuffledArray
}

function checkSelectedOption(book: string) {
  showErrorMessage.value = false
  showNext.value = book === actualBook.value
  if (showNext.value) {
    // reset some of the data
    selectedIncorrectOption.value = null
    showErrorMessage.value = false
    selectedCorrectOption.value = null
    showNext.value = false

    // increase score counter and iteration
    iteration.value = iteration.value + 1
    score.value = score.value + 1
    displayDescription()
  } else {
    selectedIncorrectOption.value = book
    selectedCorrectOption.value = actualBook.value
    showErrorMessage.value = true
  }
}

function nextQuestion() {
  iteration.value = iteration.value + 1
  showErrorMessage.value = false
  selectedIncorrectOption.value = null
  selectedCorrectOption.value = null
  displayDescription()
}

function restartQuiz() {
  reset()
  setArrayForQuiz()
  displayDescription()
}

const selectedBooks = computed(() => optionBooks.value)

onMounted(() => {
  setArrayForQuiz()
  displayDescription()
})
</script>
<style scoped>
.input-error {
  animation-name: horizontal-shaking;
  animation-duration: 0.3s;
  background-color: transparent !important;
  color: #9fe2bf !important;
  border-color: var(--vt-c-error) !important;
}

@keyframes horizontal-shaking {
  0% {
    transform: translateX(0);
  }
  25% {
    transform: translateX(5px);
  }
  50% {
    transform: translateX(-5px);
  }
  75% {
    transform: translateX(5px);
  }
  100% {
    transform: translateX(0);
  }
}

.input-right {
  background-color: #a9b2ac !important;
  color: var(--vt-c-white) !important;
  animation: zoom-in-zoom-out 1s;
}
@keyframes zoom-in-zoom-out {
  0% {
    transform: scale(1, 1);
    background-color: #a9b2ac;
    color: var(--vt-c-white);
  }
  50% {
    transform: scale(1.1, 1.1);
    background-color: #a9b2ac;
    color: var(--vt-c-white);
  }
  100% {
    transform: scale(1, 1);
    background-color: #a9b2ac;
    color: var(--vt-c-white);
  }
}

/* Define a class for the button */
.book-titles {
  width: 100%;
  background-color: transparent;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  padding: 5px 20px;
  margin-top: 15px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 5px;
  color: #9fe2bf;
  font-weight: bold;
  text-decoration: none;
  line-height: 1.5;
  letter-spacing: 1px;
  display: flex;
  flex-wrap: wrap;
  transition:
    background-color 0.2s,
    box-shadow 0.2s;
  cursor: pointer;
  overflow: hidden;
  word-wrap: break-word;
}

.book-titles:focus-visible {
  outline: none;
}

.next {
  margin-top: 10px;
  float: right;
}

.options {
  display: flex;
  align-items: center;
  justify-content: center;
}

.score {
  color: #9fe2bf;
}
.remaining {
  color: #9fe2bf;
  float: right;
}

input {
  margin-top: 5px;
}

.total-score {
  text-align: center;
  animation-name: fade-in;
  animation-duration: 4s;
}
@keyframes fade-in {
  from {
    opacity: 0;
    text-align: center;
  }
  to {
    opacity: 1;
    text-align: center;
  }
}

#restart {
  background-color: transparent;
  box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  padding: 5px 20px;
  margin-top: 15px;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 5px;
  color: var(--color-text);
  font-weight: bold;
  text-decoration: none;
  line-height: 1.5;
  letter-spacing: 1px;
  animation-name: fade-in-restart;
  animation-duration: 6s;
  display: flex;
}

@keyframes fade-in-restart {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@media (min-width: 1024px) {
  .book-titles:focus {
    width: 100%;
    background-color: transparent;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
    padding: 5px 20px;
    margin-top: 15px;
    text-align: center;
    border: 1px solid #ccc;
    border-radius: 5px;
    color: #9fe2bf;
    font-weight: bold;
    text-decoration: none;
    line-height: 1.5;
    letter-spacing: 1px;
    display: flex;
    flex-wrap: wrap;
    transition:
      background-color 0.2s,
      box-shadow 0.2s;
    cursor: pointer;
  }

  .book-titles:hover {
    background-color: #a9b2ac;
    color: var(--vt-c-white);
    cursor: pointer;
  }

  #restart:hover {
    background-color: #a9b2ac;
    color: var(--vt-c-white);
    box-shadow: 4px 4px 6px rgba(0, 0, 0, 0.3);
    cursor: pointer;
  }
}
</style>
