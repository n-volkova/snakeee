<template>
  <div
    class="button"
    @click="emit('changeView', 'Game')"
  >
    Начать игру
  </div>
  <div
    class="button"
    @click="emit('changeView', 'Records')"
  >
    Таблица рекордов
  </div>
</template>

<script setup lang="ts">

const emit = defineEmits<{(e: 'changeView', view: 'Records' | 'Game'): void
}>();
</script>
