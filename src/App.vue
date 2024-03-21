<script setup>
import { ref } from 'vue';

const images = []
const tiles = ref([])
const disabled = ref(true)
const modal = ref(null)
let selected = null
let attempts = ref(0)
let pairs = 8

// for(let i = 1; i <= pairs; i++) images.push(i, i)

// while(images.length > 0) {
//   const index = Math.floor(Math.random() * images.length)

//   tiles.value.push({
//     src: images.splice(index, 1)[0],
//     flipped: false
//   })
// }

for(let i = 1; i <= pairs; i++) {
  tiles.value.push(
    { id: `tile-${i}-1`, src: i, flipped: true },
    { id: `tile-${i}-2`, src: i, flipped: true }
  )
}

const shuffle = () => {
  for (let i = tiles.value.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1));
    
    [tiles.value[i], tiles.value[j]] = [tiles.value[j], tiles.value[i]];
  }
}

setTimeout(() => {
  tiles.value.forEach(tile => tile.flipped = false)

  setTimeout(() => {
    shuffle()

    let count = 4

    let timer = setInterval(() => {
      shuffle()

      count--

      if(count == 0) {
        clearInterval(timer)
        disabled.value = false
      }
    }, 500)
  }, 1000)
}, 3000)

const flip = tile => {
  tile.flipped = true

  if(selected) {
    attempts.value++

    if(selected.src == tile.src) {
      selected = null
      pairs--

      if(pairs == 0) modal.value.showModal()
    }
    else {
      disabled.value = true

      setTimeout(() => {
        selected.flipped = false
        tile.flipped = false
        disabled.value = false
        selected = null
      }, 3000)
    }
  }
  else {
    selected = tile
  }
}
</script>

<template>
  <!-- <div class="grid place-items-center h-screen">
    <div class="max-w-xl grid grid-cols-4 gap-4">
      <button v-for="tile in tiles" @click="flip(tile)" :disabled="tile.flipped || disabled">
        <img :src="`/images/${tile.flipped ? tile.src : 0}.png`">
      </button>
    </div>
  </div> -->

  <div class="grid place-items-center h-screen">
    <div class="max-w-xl grid grid-cols-4 gap-4">
      <TransitionGroup>
        <div v-for="tile in tiles" :key="tile.id" class="card transition-transform duration-500">
          <button @click="flip(tile)" :disabled="tile.flipped || disabled" :class="{ flipped: tile.flipped }" class="card-content transition-transform duration-1000">
            <img src="/images/0.png" class="card-front">
            <img :src="`/images/${tile.src}.png`" class="card-back absolute inset-0">
          </button>
        </div>
      </TransitionGroup>
    </div>
  </div>
  <dialog ref="modal" class="bg-white p-8 backdrop:backdrop-blur-sm rounded-2xl shadow-md">You beat it in {{ attempts }} attempts!</dialog>
</template>

<style>
.card {
  perspective: 1000px;
}

button {
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2);
  transform-style: preserve-3d;
}

.card-front, .card-back {
  backface-visibility: hidden;
}

.card-back, .flipped {
  transform: rotateY(180deg);
}
</style>