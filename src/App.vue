<script setup lang="ts">
import { reactive, ref } from 'vue'

// Helper function to generate boxes
const generateBoxes = (n: number): Array<number> => {
  const arr = new Array(25).fill(0)
  for (let i = 0; i < n; i++) {
    arr[i] = 1
  }
  return arr.sort(() => Math.random() - 0.5)
}

interface Box {
  placeholder: string;
  mine: boolean;
  clicked: boolean;
}

interface Score {
  wins: number;
  losses: number;
}

const boxes = ref<Box[]>([])
const numberMines = ref<number>(3)
const gameIsLive = ref<boolean>(false)

const score = reactive<Score>({
  wins: 0,
  losses: 0,
})

const createBoxes = () => {
  if (gameIsLive.value) return

  boxes.value = generateBoxes(numberMines.value).map((item) => ({
    placeholder: '',
    mine: item === 1,
    clicked: false,
  }))
  gameIsLive.value = true
}

const checkClick = (item: Box) => {
  if (!gameIsLive.value) return
  if (item.clicked) return

  item.clicked = true

  if (item.mine) {
    score.losses++
    gameIsLive.value = false

    showMines()
    alert(" 💥 You hit a mine! 💥 ")
  } else {
    const isWin = boxes.value.every((item) => item.mine || item.clicked)
    if (isWin) {
      score.wins++
      gameIsLive.value = false

      showMines()
      alert(" 🎉 You won! 🎉 ")
    }
  }
}

const cashout = () => {
  if (!gameIsLive.value) return
  // check if at least one box is clicked
  if (!boxes.value.some((item) => item.clicked)) return

  gameIsLive.value = false
  score.wins++

  showMines()
  alert(" 🏃‍♂️ You cashed out! 🏃‍♂️ ")
}

const showMines = () => {
  boxes.value = boxes.value.map((item) => (item.mine ? { ...item, clicked: true } : item))
}
</script>

<template>
  <main>
    <div class="controls">
      <!-- A select box with 24 number from 1 to 24 -->
      <select v-model="numberMines" class="input-mines">
        <option v-for="n in 24" :key="n" :value="n">{{ n }}</option>
      </select>
      <button class="start-btn" @click="createBoxes" :disabled="gameIsLive">Start</button>
    </div>

    <!-- draw me a grid of 5X5  -->
    <div class="grid">
      <div
        v-for="(box, index) in boxes"
        :key="index"
        class="box"
        :class="{ active: box.clicked && !box.mine, 'is-mine': box.mine && box.clicked }"
        @click="checkClick(box)"
      >
        {{ box.clicked ? (box.mine ? '💣' : '🌴') : ''}}
      </div>
    </div>


    <div class="cashout">
      <button class="cashout-btn" @click="cashout" :disabled="!gameIsLive">Cashout</button>
    </div>

    <div class="score">
      <span class="wins">WINS: {{ score.wins }}</span>
      <span class="losses">LOSSES: {{ score.losses }}</span>
    </div>
  </main>

</template>

<style>
  .grid {
    display: flex;
    flex-wrap: wrap;
    width: 350px;
    height: 350px;
    margin: auto;
    margin-top: 100px;
    gap: 10px;
    background-color: #131313;
  }

  .box {
    width: 60px;
    height: 60px;
    display: flex;
    justify-content: center;
    align-items: center;
    border: 1px solid #ccc;
    font-size: 1.5rem;
    cursor: pointer;
  }

  .box:hover {
    background-color: rgb(50, 50, 50);
  }
  .box:active {
    background-color: lightcoral;
  }

  .box.active {
    background-color: greenyellow;
  }
  .is-mine {
    background-color: red;
  }

  .controls {
    margin: auto;
    margin-top: 20px;
    display: flex;
    width: fit-content;
  }
  .start-btn {
    padding: 10px 20px;
    font-size: 1.5rem;
    background-color: lightcoral;
    color: white;
    border: none;
    cursor: pointer;
  }
  .start-btn:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  .score {
    text-align: center;
    display: flex;
    justify-content: center;
    gap: 12px;
    font-size: 1.5rem;
    margin-top: 12px;
  }
  .wins {
    color: green;
  }
  .losses {
    color: red;
  }
  .cashout {
    display: flex;
    justify-content: center;
  }
  .cashout-btn:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  .cashout-btn {
    background-color: green;
    padding: 10px 20px;
    font-size: 1.5rem;
    border-radius: 2px;
    cursor: pointer;
    margin-top: 20px;
  }
  .input-mines {
    width: 200px;
  }
</style>

