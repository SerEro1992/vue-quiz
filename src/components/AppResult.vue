<script setup lang="ts">
import { ref } from 'vue'
import type { GameStatus } from './types/GameStatus'

interface IProps {
  word: string
}

defineProps<IProps>()

const gameStatus = ref<GameStatus | null>(null)

const isVisible = ref(false)

const openPopup = (status: GameStatus) => {
  gameStatus.value = status
  isVisible.value = true
}
const closePopup = () => {
  isVisible.value = false
}

defineExpose({
  openPopup,
  closePopup
})

const emit = defineEmits<{
  (e: 'restart'): void
}>()
</script>

<template>
  <!-- Container for final message -->
  <div v-show="isVisible" class="popup-container">
    <div class="popup">
      <h2 v-if="gameStatus === 'win'">Поздравляю, вы победили! 😃</h2>
      <template v-else>
        <h2>Вы проиграли. 😕</h2>
        <h3>...имя: {{ word }}</h3>
      </template>

      <button @click="emit('restart')">Сыграть еще раз</button>
    </div>
  </div>
</template>
