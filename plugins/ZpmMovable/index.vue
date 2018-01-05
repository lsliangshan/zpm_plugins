<template>
    <div class="zpm_movable_container"
         :ref="realRef"
         :style="{transform: `translate(${calcWeb(deltaX)}px, ${calcWeb(deltaY)}px)`, left: calcWeb(x) + 'px', top: calcWeb(y) + 'px', width: `${calcWeb(width)}px`, height: `${calcWeb(height)}px`}"
         @touchstart="touchStart"
         @touchmove="touchMove"
         @touchend="touchEnd"
    >
        <slot></slot>
    </div>
</template>
<style scoped>
    .zpm_movable_container {
        position: absolute;
        width: 100px;
        height: 100px;
        /*background-color: lightblue;*/
    }
</style>
<script>
  function S4 () {
    return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1)
  }
  function getUUID (prefix) {
    let pf = prefix || ''
    return pf + (S4() + S4() + '-' + S4() + '-' + S4() + '-' + S4() + '-' + S4() + S4() + S4())
  }
  function isEmptyObj (obj) {
    var t;
    for (t in obj)
      return !1
    return !0
  }
  export default {
    name: 'ZpmMovable',
    props: {
      eleRef: {
        type: String
      },
      startRef: {
        type: String
      },
      endRef: {
        type: String
      },
      x: {
        type: [String, Number],
        default: 0
      },
      y: {
        type: [String, Number],
        default: 0
      },
      width: {
        type: [String, Number],
        default: 100
      },
      height: {
        type: [String, Number],
        default: 100
      },
      axis: {
        type: String,
        default: 'x',
        validator: (value) => {
          return ['x', 'y', 'all'].indexOf(value) > -1
        }
      },
      from: {
        type: [String, Array],
        required: true
      },
      to: {
        type: [String, Array],
        required: true
      }
    },
    data () {
      return {
        deltaX: 0,
        deltaY: 0,
        startX: 0,
        startY: 0,
        eleLeft: 0,
        eleTop: 0,
        outOfBounce: 40
      }
    },
    computed: {
      realRef () {
        return this.eleRef || getUUID('movable-ref-')
      },
      ZpmMovable () {
        return this.$root.ZpmMovable
      }
    },
    created () {
      const that = this
//      that.outOfBounce = this.width / 2
      that.minX = Number(this.from[0])
      that.minY = Number(this.from[1])
      that.maxX = Number(this.to[0])
      that.maxY = Number(this.to[1])
      this.$nextTick(() => {
        if (isEmptyObj(that.$root.ZpmMovable)) {
          that.$root.ZpmMovable = {}
        }
        that.$root.ZpmMovable[that.realRef] = {
          box: {
            width: Number(that.width),
            height: Number(that.height),
            left: Number(that.x),
            top: Number(that.y)
          }
        }
        that.$root.ZpmMovable[that.realRef].getRef = function () {
          return that.realRef
        }
        that.$root.ZpmMovable[that.realRef].moveTo = function (opts) {
          if (opts.left) {
            that.deltaX = Number(opts.left) - Number(that.x)
          }
          if (opts.top) {
            that.deltaY = Number(opts.top) - Number(that.y)
          }
        }
      })
    },
    methods: {
      calcWeb (px) {
        const deviceInfo = weex.config.env
        return (deviceInfo.platform.toLowerCase() === 'web') ? parseFloat(px * deviceInfo.deviceWidth / deviceInfo.dpr / 750) : px
      },
      setBox (opts) {
        const that = this
        if (opts.left) {
          that.$root.ZpmMovable[that.realRef].box.left = Number(opts.left)
        }
        if (opts.top) {
          that.$root.ZpmMovable[that.realRef].box.top = Number(opts.top)
        }
      },
      touchStart (e) {
        if (!isEmptyObj(this.ZpmMovable[this.startRef])) {
          if (this.axis !== 'y') {
            this.minX = this.ZpmMovable[this.startRef].box.left
          }
          if (this.axis !== 'x') {
            this.minY = this.ZpmMovable[this.startRef].box.top
          }
        }
        if (!isEmptyObj(this.ZpmMovable[this.endRef])) {
          if (this.axis !== 'y') {
            this.maxX = this.ZpmMovable[this.endRef].box.left
          }
          if (this.axis !== 'x') {
            this.maxY = this.ZpmMovable[this.endRef].box.top
          }
        }
      },
      touchMove (e) {
        this.$emit('zpmmove', {ref: this.realRef})
        if (this.axis !== 'y') {
          let _screenX = e.changedTouches[0].screenX
          let _realX = _screenX - this.eleLeft - Number(this.width) / 2
          if (this.startRef) {
            if (_realX > this.minX + this.outOfBounce && _realX < this.maxX - this.outOfBounce) {
              this.deltaX = _screenX - this.eleLeft - Number(this.x) - Number(this.width) / 2
            }
          } else {
            if (_realX > this.minX && _realX < this.maxX - this.outOfBounce) {
              this.deltaX = _screenX - this.eleLeft - Number(this.x) - Number(this.width) / 2
            }
          }
          this.setBox({
            left: this.deltaX + this.x
          })
        }
        if (this.axis !== 'x') {
          let _screenY = e.changedTouches[0].screenY
          let _realY = _screenY - this.eleTop - Number(this.height) / 2
          if (_realY > this.minY + Number(this.height) - this.outOfBounce && _realY < this.maxY - Number(this.height) + this.outOfBounce) {
            this.deltaY = _screenY - this.eleTop - Number(this.y) - Number(this.height) / 2
          }
          this.setBox({
            top: this.deltaY + Number(this.y)
          })
        }
      },
      touchEnd (e) {
        if (this.axis !== 'y') {
//          this.setBox({
//            left: this.deltaX + this.x
//          })
        }
        if (this.axis !== 'x') {
//          this.setBox({
//            top: this.deltaY + Number(this.y)
//          })
        }
        this.$emit('zpmend', {ref: this.realRef})
      }
    }
  }
</script>
