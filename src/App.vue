<template>
  <div id="app">
    <h1>Jitsi Mod-era-tor</h1>
  </div>
</template>

<script>

import { connect, createAndJoinRoom/*, createTracksAndAddToRoom*/ } from './utils/jitsiUtils.js'

export default {
  name: 'App',

  mounted() {
    connect().then(connection => {
      return createAndJoinRoom(connection, 'vueconf');
    })
        /*.then(room => createTracksAndAddToRoom(room))
        .catch(error => console.error(error))*/;
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
