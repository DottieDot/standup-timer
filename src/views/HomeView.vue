<template>
  <timer />
</template>

<script lang="ts">
import { defineComponent } from 'vue'

// Components
import Timer from '../components/Timer.vue'

export default defineComponent({
  name: 'HomeView',

  components: {
    Timer
  }
})
</script>
