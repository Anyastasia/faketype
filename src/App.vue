<script>
import TextBox from "./components/TextBox.vue"
export default {

    components: {
      TextBox,
    },
    
}
</script>

<template>
  <div class="container">
     <button class="text">Button</button>
    <TextBox/>
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

button.text {
  border: none;
  background-color: inherit;
  font-family: var(--font);
}

</style>
