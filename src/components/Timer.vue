<template>
  <div>
    <video
      ref="video"
      id="video"
      autoplay
      muted
    ></video>
  </div>
</template>

<style lang="scss">
#video {
  opacity: 0;
  position: absolute;
  pointer-events: none;
}
</style>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Watch } from 'vue-property-decorator'

@Options({
  props: {
    duration: {
      type: Number,
      required: true,
      validator: (nr: number) => nr > 0
    },
    paused: {
      type: Boolean,
      default: () => false
    }
  }
})
export default class Timer extends Vue {
  canvas: HTMLCanvasElement | null = null;
  ctx: CanvasRenderingContext2D | null = null;
  video: HTMLVideoElement | null = null
  lastTick = 0
  elapsed = 0

  paused!: boolean
  duration!: number

  public mounted() {
    const canvas = document.createElement('canvas')
    const video = this.$refs.video as HTMLVideoElement
    const ctx = canvas.getContext('2d')
    if (ctx && video) {
      ctx.font = '50px Roboto'
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'

      const stream = canvas.captureStream()
      video.srcObject = stream

      this.canvas = canvas
      this.ctx = ctx
      this.video = video
      this.start()
    }
  }

  public requestPip() {
    this.video?.requestPictureInPicture()
  }

  @Watch('duration')
  public onDurationChanged() {
    this.elapsed = 0
  }

  @Watch('paused')
  public onPausedChanged(paused: boolean) {
    if (!paused) {
      this.start()
    }
  }

  public reset() {
    this.elapsed = 0
    if (this.paused) {
      this.start()
    }
  }

  private start() {
    this.lastTick = Date.now()
    this.requestFrame()
  }

  private clear() {
    if (this.ctx && this.canvas && this.video) {
      const color = getComputedStyle(this.video).getPropertyValue('--v-theme-background')
      this.ctx.fillStyle = `rgb(${color})`
      this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height)
    }
  }

  private displayText() {
    if (this.ctx && this.canvas && this.video) {
      const color = getComputedStyle(this.video).getPropertyValue('--v-theme-on-surface')
      this.ctx.fillStyle = `rgb(${color})`

      const remaining = new Date(this.timeRemaining)
      const minutes = remaining.getMinutes().toString().padStart(2, '0')
      const seconds = remaining.getSeconds().toString().padStart(2, '0')
      this.ctx.fillText(`${minutes}:${seconds}`, this.canvas.width / 2, this.canvas.height / 2)
    }
  }

  private updateTimer() {
    if (!this.paused && this.timeRemaining > 0) {
      const delta = Date.now() - this.lastTick
      this.lastTick = Date.now()
      this.elapsed += delta
    }
  }

  private anim() {
    this.updateTimer()
    this.clear()
    this.displayText()

    if (!this.paused) {
      this.requestFrame()
    }
  }

  private requestFrame() {
    requestAnimationFrame(() => this.anim())
  }

  private get timeRemaining() {
    return Math.max(this.duration - this.elapsed, 0)
  }
}
</script>
