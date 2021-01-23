<template>
  <div id="app">
    <h1>Jitsi Mod-e-rator</h1>
  </div>
</template>

<script>
import JitsiMeetJS from '@lyno/lib-jitsi-meet';
import $ from 'jquery';
window.$ = $;

import options from './options/config';

JitsiMeetJS.init();
JitsiMeetJS.setLogLevel(JitsiMeetJS.logLevels.INFO);

var connection = new JitsiMeetJS.JitsiConnection(null, null, options);

function onConnectionSuccess() {
  console.log("onConnectionSuccess", arguments);
  const room = connection.initJitsiConference("vueconf", {});
  room.on(JitsiMeetJS.events.conference.TRACK_ADDED, onRemoteTrack);
  room.on(JitsiMeetJS.events.conference.CONFERENCE_JOINED, () => {
    console.log("onConferenceJoined", arguments);
/*
    JitsiMeetJS.createLocalTracks({
      devices: ['video', 'audio']
    }).then((tracks) => {
      console.log("onLocalTracks", tracks);
      tracks.forEach(track => {
        room.addTrack(track);
      })
    }).catch(error => {
          console.error("There was an error creating the local tracks:", error);
        }
    );
*/
  });
  room.join();
}
function onConnectionFailed() {
  console.log("onConnectionFailed", arguments);
}
function disconnect() {
  console.log("disconnect", arguments);
}

connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_ESTABLISHED, onConnectionSuccess);
connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_FAILED, onConnectionFailed);
connection.addEventListener(JitsiMeetJS.events.connection.CONNECTION_DISCONNECTED, disconnect);

connection.connect();

function onRemoteTrack() {
  console.log("onRemoteTrack", arguments);
}

export default {
  name: 'App',
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
