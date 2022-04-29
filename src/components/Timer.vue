<template>
  <div>
    <video
      id="video"
      autoplay
      muted
    ></video>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class Timer extends Vue {
  canvas: HTMLCanvasElement | null = null;
  ctx: CanvasRenderingContext2D | null = null;

  public mounted() {
    const canvas = document.createElement('canvas')
    const video = document.getElementById('video') as HTMLVideoElement
    const ctx = canvas.getContext('2d')
    if (ctx && video) {
      ctx.font = '50px Roboto'
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'

      const stream = canvas.captureStream()
      video.srcObject = stream

      this.canvas = canvas
      this.ctx = ctx
      this.requestFrame()
    }
  }

  private clear() {
    if (this.ctx && this.canvas) {
      this.ctx.fillStyle = 'white'
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height)
    }
  }

  private displayText() {
    if (this.ctx && this.canvas) {
      this.ctx.fillStyle = 'black'

      const time = new Date().toLocaleTimeString()
      this.ctx.fillText(time, this.canvas.width / 2, this.canvas.height / 2)
    }
  }

  private anim() {
    this.clear()
    this.displayText()
    this.requestFrame()
  }

  private requestFrame() {
    requestAnimationFrame(() => this.anim())
  }
}
</script>
