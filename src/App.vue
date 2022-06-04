<script>
import TextBox from "./components/TextBox.vue"
export default {

    components: {
      TextBox,
    },
    data() {
      return {
        activeFake: true,
        activeEnglish: false,
        mode: 'fake',
        words: [],
        
      }
    }, 
    methods: {
      toggleActive(mode) {

        // 1 - english
        // 2- fake (default)
        switch(mode) {
          case 1:
              this.activeEnglish = true
              this.activeFake = false
              break
          case 2:
              this.activeEnglish = false
              this.activeFake = true
              break
        }
        this.mode = (this.mode == 'fake') ? 'english' : 'fake'
      }
    }
    
}
</script>

<template>
  <div class="container">
    <div class="row">
        <button class="text" :class="{active: activeEnglish}" @click="toggleActive(1)">English</button>
        <button class="text" :class="{active: activeFake}" @click="toggleActive(2)">Fake</button> 
    </div>    
    <TextBox :mode='this.mode'/>
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
  border: solid 1px black;
  min-height: 100vh;
}

.active {
  color: var(--active);
}

.row {
  display: flex;
  flex-direction: row;
}

button.text {
  border: none;
  background-color: inherit;
  font-family: var(--font);
  margin-inline: 1rem;
}

</style>
