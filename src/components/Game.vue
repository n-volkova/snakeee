<template>
  <div class="game">
    <div
      v-if="gameOver"
      class="game-over"
    >
      Game Over :(
    </div>
  </div>
  <h2 class="score">
    СЧЁТ: {{ score }}
  </h2>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import {
  Application, Container, Texture, Sprite, IPointData,
} from 'pixi.js';

type SnakeConfig = {
  body: IPointData[];
  dx: number;
  dy: number;
  length: number;
}

type Record = {
  date: string;
  score: number;
}

const WIDTH: number = 500;
const HEIGHT: number = WIDTH;

const TILE_NUMBER: number = 20;
const TILE_SIZE: number = WIDTH / 20;

const snakeConfig: SnakeConfig = {
  body: [],
  dx: TILE_SIZE,
  dy: 0,
  length: 3,
};

const snakeHead: IPointData = {
  x: TILE_SIZE * (TILE_NUMBER / 2 - 1),
  y: TILE_SIZE * (TILE_NUMBER / 2 - 1),
};

const app: Application = new Application({
  width: WIDTH,
  height: HEIGHT,
  backgroundColor: 0xfadee9,
});

const snakeContainer: Container = new Container();
const food: Sprite = Sprite.from(Texture.WHITE);

const score = ref<number>(0);
const gameOver = ref<boolean>(false);

const emit = defineEmits<{(e: 'changeView', view: 'Records'): void
}>();

const getRandomInteger = (min: number, max: number): number => Math.floor(
  Math.random() * (max - min),
) + min;

const replaceFood = (): void => {
  food.position = {
    x: getRandomInteger(0, TILE_NUMBER) * TILE_SIZE,
    y: getRandomInteger(0, TILE_NUMBER) * TILE_SIZE,
  };
  if (snakeConfig.body.some((bodyTile) => bodyTile.x === food.position.x
    && bodyTile.y === food.position.y)) {
    replaceFood();
  }
};

const initFood = (): void => {
  food.width = TILE_SIZE;
  food.height = TILE_SIZE;
  food.tint = 0x6bd391;
  food.position = {
    x: TILE_SIZE * (TILE_NUMBER / 2 - 1),
    y: TILE_SIZE * (TILE_NUMBER / 2 - 1),
  };
  app.stage.addChild(food);
  replaceFood();
};

const handleWallCollision = (): void => {
  if (snakeHead.x < 0) {
    snakeHead.x = WIDTH - TILE_SIZE;
  } else if (snakeHead.x >= WIDTH) {
    snakeHead.x = 0;
  }
  if (snakeHead.y < 0) {
    snakeHead.y = HEIGHT - TILE_SIZE;
  } else if (snakeHead.y >= HEIGHT) {
    snakeHead.y = 0;
  }
};

const handleFoodCollision = (): void => {
  if (snakeHead.x === food.x && snakeHead.y === food.y) {
    snakeConfig.length += 1;
    score.value += 1;
    replaceFood();
  }
};

const saveRecords = (): void => {
  const records: Record[] = JSON.parse(localStorage.getItem('records') as string) || [];
  records.push({
    date: new Intl.DateTimeFormat('ru-RU', { dateStyle: 'short', timeStyle: 'short' }).format(new Date()),
    score: score.value,
  });
  if (records.length > 1) {
    records.sort((a, b) => b.score - a.score);
  }
  localStorage.setItem('records', JSON.stringify(records.slice(0, 10)));
};

const handleSelfCollision = (): void => {
  if (snakeConfig.body.slice(1)
    .some((bodyTile) => bodyTile.x === snakeHead.x && bodyTile.y === snakeHead.y)) {
    app.ticker.stop();
    gameOver.value = true;
    saveRecords();
    setTimeout(() => {
      emit('changeView', 'Records');
    }, 3000);
  }
};

const gameLoop = (): void => {
  snakeContainer.removeChildren();
  snakeHead.x += snakeConfig.dx;
  snakeHead.y += snakeConfig.dy;

  handleWallCollision();

  snakeConfig.body.unshift({ x: snakeHead.x, y: snakeHead.y });
  if (snakeConfig.body.length > snakeConfig.length) {
    snakeConfig.body.pop();
  }

  let snakeTile: Sprite;
  snakeConfig.body.forEach((tile: IPointData) => {
    snakeTile = Sprite.from(Texture.WHITE);
    snakeTile.width = TILE_SIZE;
    snakeTile.height = TILE_SIZE;
    snakeTile.tint = 0xf5427e;
    snakeTile.position = {
      x: tile.x,
      y: tile.y,
    };
    snakeContainer.addChild(snakeTile);
  });
  app.stage.addChild(snakeContainer);

  handleFoodCollision();
  handleSelfCollision();
};

const handleDirections = (e: KeyboardEvent): void => {
  if ((e.code === 'ArrowLeft' || e.code === 'KeyA') && snakeConfig.dx === 0) {
    snakeConfig.dx = -TILE_SIZE;
    snakeConfig.dy = 0;
  } else if ((e.code === 'ArrowUp' || e.code === 'KeyW') && snakeConfig.dy === 0) {
    snakeConfig.dy = -TILE_SIZE;
    snakeConfig.dx = 0;
  } else if ((e.code === 'ArrowRight' || e.code === 'KeyD') && snakeConfig.dx === 0) {
    snakeConfig.dx = TILE_SIZE;
    snakeConfig.dy = 0;
  } else if ((e.code === 'ArrowDown' || e.code === 'KeyS') && snakeConfig.dy === 0) {
    snakeConfig.dy = TILE_SIZE;
    snakeConfig.dx = 0;
  }
};

onMounted((): void => {
  const container: HTMLElement = document.querySelector('.game') as HTMLElement;
  container.appendChild(app.view);
  initFood();
  app.ticker.maxFPS = 10;
  app.ticker.add(() => gameLoop());

  document.addEventListener('keydown', handleDirections);
});

onBeforeUnmount((): void => {
  document.removeEventListener('keydown', handleDirections);
});
</script>

<style lang="scss">
.game {
  position: relative;
}

.game-over {
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  font-size: 40px;
  font-weight: bold;
  color: #fd4e4e;
  background-color: rgba(255, 255, 255, 0.3);
}
</style>
