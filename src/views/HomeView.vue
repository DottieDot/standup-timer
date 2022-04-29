<template>
  <timer
    ref="timer"
    :duration="60000"
    :paused="this.paused"
  />
  <v-btn v-on:click="this.$refs.timer.requestPip()">PIP</v-btn>
  <v-btn v-on:click="this.$refs.timer.reset()">Restart</v-btn>
  <v-switch
    v-model="this.paused"
    label="Paused"
  />
</template>

<script lang="ts">
import { Vue, Options } from 'vue-class-component'

// Components
import Timer from '../components/Timer.vue'

@Options({
  components: {
    Timer
  }
})
export default class HomeView extends Vue {
  paused = false
}
</script>
