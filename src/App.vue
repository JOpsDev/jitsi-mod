<template>
  <div id="app">
    <div class="grid">
    <video v-for="track in videoTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay class="video"/>
    <audio v-for="track in audioTracks" :key="`track-${track.getId()}`" :ref="track.getId()" autoplay />
    </div>
  </div>
</template>

<script>

import { connect, createAndJoinRoom, isModTrack } from './utils/jitsiUtils.js'
import JitsiMeetJS from '@lyno/lib-jitsi-meet';

export default {
  name: 'App',

  data() {
    return {
      videoTracks: [],
      audioTracks: [],
      conference: undefined,
      connection: undefined
    }
  },

  methods: {
    addTrack(track, conference) {

      if (!isModTrack(track, conference)) {
        console.log("Track skipped");
        return;
      }

      conference.selectParticipant(track.getParticipantId());
      if (track.getType() === 'video') {
        this.videoTracks.push(track);
      } else if (track.getType() === 'audio') {
        this.audioTracks.push(track);
      }
      this.$nextTick().then(() => {
        track.attach(this.$refs[track.getId()][0]);
      })
    }
  },

  mounted() {
    let urlParams = new URLSearchParams(window.location.search);
    const roomName = urlParams.get('room');
    connect(roomName).then(connection => {
      this.connection = connection;
      return createAndJoinRoom(connection, roomName);
    })
        .then(conference => {
          this.conference = conference;
          conference.on(JitsiMeetJS.events.conference.TRACK_ADDED, track => this.addTrack(track, conference));
          /*createTracksAndAddToRoom(room);*/
        })
        .catch(error => console.error(error));

  },

  beforeDestroy() {
    if (this.conference) {
      console.log("leaving conference");
      this.conference.leave();
      this.conference = undefined;
    }
    if (this.connection) {
      console.log("disconnecting connection now");
      this.connection.disconnect();
      this.connection = undefined;
    }
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
}
.grid {
  display: grid;
  grid-template-columns: repeat(2, 50%);
  grid-column-gap: 10px;
}
.video {
  display: inline-block;
  position: absolute;
  left: 0;
  top: 0;
  z-index: 1;
  width: 100%;
  height: 100%;
  overflow: hidden;
  object-fit: contain;
}
</style>
