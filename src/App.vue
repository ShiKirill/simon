<template>
  <div id="app">
    <Game />
  </div>
</template>
>

<script>
import Game from './components/Game';

export default {
  name: 'App',
  components: {
    Game,
  },
};
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,300;0,400;0,500;0,700;1,100&display=swap');

body {
  margin: 0;
  font-family: 'Roboto', sans-serif;
  overflow-x: hidden;
}

#app {
  box-sizing: border-box;
  width: 100vw;
  min-width: 18rem;
  min-height: 100vh;
  padding: 2rem 0;
  background: #303030;
}

html {
  @media screen and (max-width: 600px) {
    font-size: 80%;
  }

  @media screen and (max-width: 450px) {
    font-size: 60%;
  }

  @media screen and (max-width: 350px) {
    font-size: 50%;
  }

  @media screen and (min-width: 1400px) {
    font-size: 150%;
  }

  @media screen and (min-width: 1800px) {
    font-size: 200%;
  }
}
</style>
