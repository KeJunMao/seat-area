<template>
  <div class="screen-warpper" :style="{ height }">
    <slot name="screen-outside"></slot>
    <div ref="screen" class="screen" :style="{ height }">
      <div
        ref="el"
        class="seat-area"
        :class="{ animate: animate }"
        :style="{ transform: transformStyle }"
      >
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
import Hammer from 'hammerjs'
export default {
  name: 'SeatArea',
  props: {
    height: {
      type: String,
      default: '50vw',
    },
  },
  data() {
    return {
      mc: null,
      transform: {
        translate: {
          x: 0,
          y: 0,
        },
        scale: 1,
      },
      animate: false,
      DEFAULT_SCALE: 1,
      DEFAULT_X: 0,
      DEFAULT_Y: 0,
      START_X: 0,
      START_Y: 0,
      START_SCALE: 1,
    }
  },
  computed: {
    screen() {
      return this.$refs.screen
    },
    el() {
      return this.$refs.el
    },
    transformStyle() {
      return [
        'translate(' +
          this.transform.translate.x +
          'px, ' +
          this.transform.translate.y +
          'px)',
        'scale(' + this.transform.scale + ', ' + this.transform.scale + ')',
      ].join(' ')
    },
    elLongside() {
      return this.el.offsetWidth > this.el.offsetHeight
        ? 'offsetWidth'
        : 'offsetHeight'
    },
    screenShortside() {
      return this.screen.offsetWidth > this.screen.offsetHeight
        ? 'offsetHeight'
        : 'offsetWidth'
    },
  },
  mounted() {
    this.START_X = Math.round(
      (this.screen.offsetWidth - this.el.offsetWidth) / 2
    )
    this.DEFAULT_X = this.START_X
    this.START_Y = Math.round(
      (this.screen.offsetHeight - this.el.offsetHeight) / 2
    )
    this.DEFAULT_Y = this.START_Y
    const scale =
      (this.screen[this.screenShortside] - 30) / this.el[this.elLongside]
    this.START_SCALE = scale > 1 ? 1 : scale
    this.DEFAULT_SCALE = this.START_SCALE

    this.mc = new Hammer.Manager(this.screen)
    this.mc.add(new Hammer.Pan({ threshold: 0, pointers: 0 }))
    this.mc
      .add(new Hammer.Pinch({ threshold: 0 }))
      .recognizeWith([this.mc.get('pan')])

    this.mc.on('panstart panmove', this.onPan)
    this.mc.on('pinchstart pinchmove', this.onPinch)

    this.mc.on('hammer.input', (ev) => {
      if (ev.isFinal) {
        this.springbackElement()
      }
    })

    this.initElement()
  },
  methods: {
    setTransform({
      x = this.transform.translate.x,
      y = this.transform.translate.y,
      scale = this.transform.scale,
    }) {
      this.transform = {
        ...this.transform,
        translate: {
          x,
          y,
        },
        scale,
      }
      this.$emit('transform', {
        x,
        y,
        scale,
        dx: this.DEFAULT_X,
        dy: this.DEFAULT_Y,
      })
    },
    onPan(ev) {
      if (ev.type === 'panstart') {
        this.START_X = this.transform.translate.x || this.START_X
        this.START_Y = this.transform.translate.y || this.START_Y
      }
      this.animate = false
      this.setTransform({
        x: this.START_X + ev.deltaX,
        y: this.START_Y + ev.deltaY,
      })
      this.$emit('hammer')
    },
    onPinch(ev) {
      if (ev.type === 'pinchstart') {
        this.START_SCALE = this.transform.scale || this.START_SCALE
      }
      this.animate = false
      const scale = this.START_SCALE * ev.scale
      this.setTransform({
        scale,
      })
      this.$emit('hammer')
    },
    initElement() {
      this.setTransform({
        x: this.START_X,
        y: this.START_Y,
        scale: this.START_SCALE,
      })
    },
    springbackElement() {
      this.animate = true
      this.$emit('springback')
      const scale = this.transform.scale
      if (scale >= 1) {
        this.setTransform({
          scale: 1,
        })
      } else if (scale <= this.DEFAULT_SCALE) {
        this.setTransform({
          scale: this.DEFAULT_SCALE,
        })
      }

      const offsetX =
        (this.el.offsetWidth * this.transform.scale -
          this.screen.offsetWidth +
          30) /
        2
      const offsetY =
        (this.el.offsetHeight * this.transform.scale -
          this.screen.offsetHeight +
          30) /
        2
      const baseX = (this.screen.offsetWidth - this.el.offsetWidth) / 2
      const baseY = (this.screen.offsetHeight - this.el.offsetHeight) / 2

      const x1 = baseX - offsetX
      const x2 = baseX + offsetX
      const y1 = baseY - offsetY
      const y2 = baseY + offsetY

      const minX = Math.min(x1, x2)
      const maxX = Math.max(x1, x2)
      const minY = Math.min(y1, y2)
      const maxY = Math.max(y1, y2)

      const x = this.transform.translate.x
      const y = this.transform.translate.y

      if (x < minX) {
        this.setTransform({
          x: minX,
        })
      } else if (x > maxX) {
        this.setTransform({
          x: maxX,
        })
      }
      if (y < minY) {
        this.setTransform({
          y: minY,
        })
      } else if (y > maxY) {
        this.setTransform({
          y: maxY,
        })
      }
    },
  },
}
</script>

<style>
.screen-warpper {
  box-sizing: border-box;
  overflow: hidden;
  position: relative;
  transition: height 0.3s;
  z-index: 0;
}

.hideSeats .screen-warpper {
  height: 0 !important;
}

.screen {
  border-radius: 2px;
  box-sizing: border-box;
  position: relative;
}

.seat-area {
  position: absolute;
}

.animate {
  transition: all 0.3s;
}
</style>
