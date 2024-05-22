<script setup lang="ts">
import { ref } from 'vue'

// create a function that  takes n and returns  an array of 25 elements with n values of 1 and other 0
// The order needs to be random
const createBoxes = (n: number) => {
  const arr = new Array(25).fill(0)
  for (let i = 0; i < n; i++) {
    arr[i] = 1
  }
  return arr.sort(() => Math.random() - 0.5)
}

const minesN = ref(3)
const arr = ref([])


const wins = ref(0)
const losses = ref(0)
const gameStopped = ref(false)
const gameLive = ref(false)

const generateArray = () => {
  if (minesN.value <= 0 || minesN.value > 25) return alert('Between 1 and 25 mines allowed')
  const boxes = createBoxes(Math.round(minesN.value))
  arr.value = boxes.map(value => ({ placeholder: "❓", value: value, clicked: false, emoji: value === 1 ? "💣" : "👍" }))
  gameStopped.value = false
  gameLive.value = true
}


// Click
const checkClick = (item) => {
  if (item.clicked) return
  if (gameStopped.value) return

  item.clicked = true
  item.value = item.emoji
  item.placeholder = null

  setTimeout(() => {}, 500)


  if (item.value === '💣') {
    losses.value++
    gameStopped.value = true
    gameLive.value = false
    const conf = confirm('Game Over!')
    if (conf) arr.value = []
    return
  }

  // check if all the boxes that are not bombs are clicked
  if (arr.value.filter(item => item.value !== 1).every(item => item.clicked)) {
    wins.value++
    gameLive.value = false
    gameStopped.value = true
    const conf = confirm('You won!')
    if (conf) arr.value = []
    return
  }
}

const cashout = () => {
  if (gameStopped.value) return
  if (!gameLive.value) return 

  // if none of the boxes are clicked
  if (arr.value.every(item => !item.clicked)) return

  gameStopped.value = true
  gameLive.value = false

  wins.value++
  arr.value = []

  // confirm('Cashout?') && generateArray()
}
</script>

<template>
  <main>
    <div class="controls">
      <input
        type="number"
        placeholder="mines"
        v-model="minesN"
        class="input-mines"
      >
      <button class="start-btn" @click="generateArray">Start</button>
    </div>

    <!-- draw me a grid of 5X5  -->
    <div class="grid">
      <div
        v-for="(item, index) in arr"
        :key="index"
        class="box"
        :class="{ active: item.clicked === 1 }"
        @click="checkClick(item)"
      >
        {{ item.placeholder || item.value }}
      </div>
    </div>


    <div class="cashout">
      <button class="cashout-btn" @click="cashout">Cashout</button>
    </div>

    <div class="score">
      <span class="wins">WINS: {{ wins }}</span>
      <span class="losses">LOSSES: {{ losses }}</span>
    </div>
  </main>

</template>

<style>
  /* create a grid of 5x5 element that needs to be equal on all devices using flex not grid */
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
  .cashout-btn {
    padding: 10px 20px;
    font-size: 1.5rem;
    /* background-color: green; */
    border-radius: 2px;
    /* color: white; */
    /* border: none; */
    cursor: pointer;
    margin-top: 20px;
  }
</style>

