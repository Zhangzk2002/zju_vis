<template>
  <div id="app">
    <div class="nav">
      <RouterLink to="/">Home</RouterLink> |
      <RouterLink to="/simple-reactivity">Simple Reactivity</RouterLink> |
      <RouterLink to="/world-population">World Population</RouterLink> |
      <RouterLink to="/simple-time-scale">Simple Time Scale</RouterLink> |
      <RouterLink to="/world-population-multiple-years">World Population Multiple Years</RouterLink>
    </div>
    <router-view />
  </div>
</template>

<style>
#app {
  /* 添加背景 */
  background:url("./assets/background.png");
  width:100%;
  height:100%;
  position:fixed;
  background-size:100% 100%;
  /* 添加背景 */

  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body {
  width: 100%;
  min-height: 100vh;
}
h1 {
  margin-bottom: 0;
  font-size: 60px;
}
h2 {
  font-size: 40px;
}
.nav {
  padding: 32px 0 16px;
}
.nav a {
  font-size: 32px;
  font-weight: bold;
  color: #2c3e50;
}
.nav a.router-link-exact-active {
  color: #42b983;
}
</style>
