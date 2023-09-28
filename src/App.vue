<script setup lang="ts">
import { ref, watch } from 'vue'
import AppFigure from './components/AppFigure.vue'
import AppHeader from './components/AppHeader.vue'
import AppMistake from './components/AppMistake.vue'
import AppNotification from './components/AppNotification.vue'
import AppResult from './components/AppResult.vue'
import AppWord from './components/AppWord.vue'
import { useRandomWord } from './composables/useRandomWord'
import { useLetters } from './composables/useLetters'

const { word, getRandomWord } = useRandomWord()
const { letters, correctLetters, wrongLetters, isLose, isWin, addLetter, resetLetters } =
  useLetters(word)

const notification = ref<InstanceType<typeof AppNotification> | null>(null)
const popup = ref<InstanceType<typeof AppResult> | null>(null)

watch(wrongLetters, () => {
  if (isLose.value) {
    popup.value?.openPopup('lose')
  }
})

watch(correctLetters, () => {
  if (isWin.value) {
    popup.value?.openPopup('win')
  }
})

window.addEventListener('keydown', ({ key }) => {
  if (isWin.value || isLose.value) {
    return
  }

  if (letters.value.includes(key)) {
    notification.value?.openPopup()
    setTimeout(() => notification.value?.closePopup(), 2000)
    return
  }

  addLetter(key)
})

const restart = async () => {
  await getRandomWord()
  resetLetters()
  popup.value?.closePopup()
}
</script>

<template>
  <app-header />
  <div class="game-container">
    <app-figure :wrong-letters-count="wrongLetters.length" />
    <app-mistake :wrong-letters="wrongLetters" />
    <app-word :word="word" :correct-letters="correctLetters" />
  </div>

  <app-result ref="popup" :word="word" @restart="restart" />

  <app-notification ref="notification" />
</template>
