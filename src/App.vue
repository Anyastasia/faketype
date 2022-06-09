<script>
import TextBox from "./components/TextBox.vue"
export default {
    components: {
      TextBox,
    },
    data() {
      return {
        language: [
          'english', 'fake',
        ],
        timer : [
          60, 120
        ],
        numberOfWords: [
          10, 25, 50, 100,
        ],
        activeMode: false,
        activeTimer: false,
        activeNumberOfWords: false,
        mode: 'fake',
        time: 60,
        noOfWords: 25,
      }
    }, 
    methods: {
      toggleActive(mode) {
        return { 
          'activeMode': (this.mode === mode)
        }
      },
      toggleTimer(xtime) {
        return {
          'activeTimer': (this.time === xtime)
        }
      },
      toggleNumberOfWords(n) {
        return {
          'activeNumberOfWords': (this.noOfWords === n)
        }
      }
    }
    
}
</script>

<template>
  <div class="container">
    <div class="column w-100">
      <div class="row my-1 center">
          <button v-for="lang in language" :key="lang" class="text" :class="toggleActive(lang)" @click="mode = lang">{{lang}}</button>
      </div>
      <div class="row my-1">
          <div class="number-words">
            <button v-for="n in numberOfWords" :key="n" class="text" :class="toggleNumberOfWords(n)" @click="noOfWords = n">{{n}}</button>
          </div>
          <div class="time">
            <button v-for="xtime in timer" :key="xtime" class="text" :class="toggleTimer(xtime)" @click="time = xtime">{{xtime}}</button>
          </div>
      </div>
    </div>
    <TextBox :mode='mode' :timer="time" :numberOfWords="noOfWords"/>
  </div>
</template>

<style>

@import url('https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap');

:root {
  --bg-color: #f0f0f0;
  --text-center: #D3d3d3;
  --active: purple;
  --correct: 'green';
  --incorrect: 'red';
  --font: 'Roboto Mono', monospace;
}

* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
body {
  font-family: var(--font);
  background-color: var(--bg-color);
}
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  border-radius: 10px;
  min-height: 100vh;
}

.activeMode {
  color: var(--active);
}

.activeTimer {
  color: var(--active);
}

.activeNumberOfWords {
  color: var(--active);
}

.column {
  display: flex;
  flex-direction: column;
  width: 45%;
}

.row {
  display: flex;
  flex-direction: row;
}

.center {
  justify-content: center;
}
.number-words {
  margin-right: auto;
  padding-inline-start: 1.5rem;
}

.time {
  padding-inline-end: 1.5rem;
}
button.text {
  border: none;
  background-color: inherit;
  font-family: var(--font);
  margin-inline: 1rem;
}

.my-1 {
  margin-block-end: 0.5rem;
}



</style>
