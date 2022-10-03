<template>
  <h2>Таблица рекордов</h2>
  <div
    v-if="records.length"
    class="records"
  >
    <div
      v-for="(record, index) in records"
      :key="index"
      class="record"
    >
      <span class="record__index">
        {{ index + 1 }}
      </span>
      <span class="record__date">
        {{ record.date }}
      </span>
      <span class="record__score">
        {{ record.score }}
      </span>
    </div>
  </div>
  <div
    v-else
    class="records"
  >
    Здесь пока пусто...
  </div>
  <div
    class="button"
    @click="emit('changeView', 'Menu')"
  >
    Главное меню
  </div>
</template>

<script setup lang="ts">

const emit = defineEmits<{(e: 'changeView', view: 'Menu'): void
}>();

type Record = {
  date: string;
  score: number;
}

const records: Record[] = JSON.parse(localStorage.getItem('records') as string) || [];
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.records {
  margin-bottom: 15px;
}

.record {
  display: flex;
  padding: 10px 0;
  font-weight: bold;
  font-size: 16px;
  border-bottom: 1px solid #fd4e4e;

  &:last-child {
    border-bottom: none;
  }

  &__index {
    width: 25px;
    margin-right: 30px;
  }

  &__date {
    flex-grow: 1;
    margin-right: 30px;
    text-align: left;
  }

  &__score {
    flex-grow: 1;
    text-align: right;
  }
}
</style>
