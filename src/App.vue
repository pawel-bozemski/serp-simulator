<template>
  <div id="app">
    <div class="container-fluid">
      <Header />
      <Main />
    </div>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Header,
    Main

  }
}
</script>

<style>
body {
  font-family: Roboto, sans-serif;
  box-sizing: border-box
}

</style>
