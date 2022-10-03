<template>
  <Transition
    name="fade"
    mode="out-in"
  >
    <div
      :key="currentViewKey"
      class="view"
    >
      <component
        :is="currentView"
        @change-view="changeView"
      />
    </div>
  </Transition>
</template>

<script setup lang="ts">
import { ref, shallowRef } from 'vue';
import type { ConcreteComponent } from 'vue';

import Menu from './components/Menu.vue';
import Records from './components/Records.vue';
import Game from './components/Game.vue';

const views = ['Menu', 'Records', 'Game'] as const;
type View = typeof views[number];

const viewComponents: { [key in View]: ConcreteComponent } = {
  Menu,
  Records,
  Game,
};

const currentViewKey = ref<View>('Menu');
const currentView = shallowRef<ConcreteComponent>(viewComponents[currentViewKey.value]);

const changeView = (view: View): void => {
  currentViewKey.value = view;
  currentView.value = viewComponents[view];
};
</script>

<style lang="scss">
body {
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity .1s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

h2 {
  margin: 0 auto 15px;
  color: #fd4e4e;
}

.view {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.button {
  width: 200px;
  padding: 14px 20px;
  color: #fff;
  background-image: linear-gradient(45deg, #ff7676 0%, #f54ea2 100%);
  border-radius: 8px;
  font-weight: bold;
  font-size: 18px;
  cursor: pointer;

  &:first-child {
    margin-bottom: 20px;
  }

  &:hover {
    background-size: 200% 100%;
    transition: background-size 1s, background-color 1s;
  }
}
</style>
