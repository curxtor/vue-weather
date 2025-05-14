<template>
  <router-view />
</template>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: white;
  }
  body {
    background: linear-gradient(#030617, #030617), url("./assets/images/map.webp") no-repeat;
    background-size: 40%;
    background-position: 30vw 10vh;
    width: 100vw;
    height: 100vh;
    margin: 0;
  }
  nav {
    padding: 30px;
  }
  * {
    font-family: Inter;
    box-sizing: border-box;
  }
  p {
    margin: 0;
  }
  nav a {
    font-weight: bold;
    color: #2c3e50;
  }

  nav a.router-link-exact-active {
    color: #42b983;
  }
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.2s ease;
  }

  .fade-enter-from,
  .fade-leave-active {
    opacity: 0;
  }

  @import url("https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&display=swap");
</style>
