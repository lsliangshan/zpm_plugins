<template>
    <div class="zpm_movable_container"
         :ref="realRef"
         :style="{transform: `translate(${calcWeb(left)}px, ${calcWeb(top)}px) scale(${eleScale})`, width: `${calcWeb(width)}px`, height: `${calcWeb(height)}px`, zIndex: topMovable ? '999999' : '1'}"
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
        overflow: hidden;
    }
</style>
<script>
  const animation = weex.requireModule('animation')
  function S4 () {
    return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1)
  }
  function getUUID (prefix) {
    let pf = prefix || ''
    return pf + (S4() + S4() + '-' + S4() + '-' + S4() + '-' + S4() + '-' + S4() + S4() + S4())
  }
  /**
   * x:     初始横坐标
   * y:     初始纵坐标
   * axis:  可拖动方向，可选 all、x、y
   * from:  拖动范围起点  [startX, startY]
   * to:    拖动范围终点  [endX, endY]
   * size:  拖动区域尺寸, [width, height]
   */
  export default {
    name: 'ZpmMovable',
    props: {
      x: {
        type: [String, Number],
        default: 0
      },
      y: {
        type: [String, Number],
        default: 0
      },
      axis: {
        type: String,
        default: 'all',
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
      },
      size: {
        type: [String, Array],
        default: [100, 100]
      },
      dataRef: {
        type: String,
        default: ''
      },
      startRef: {
        type: String,
        default: ''
      },
      endRef: {
        type: String,
        default: ''
      },
      dataModelOrigin: {
        type: Object
      },
      scale: {
        type: [String, Number],
        default: 1
      }
    },
    data () {
      return {
        topMovable: false,
        width: 100,
        height: 100,
        eleX: 0,
        eleY: 0,
        left: 0,
        top: 0,
        startX: 0,
        startY: 0,
        deltaX: 0,
        deltaY: 0,
        minX: 0,
        minY: 0,
        maxX: 0,
        maxY: 0,
        startRefX: 0,
        startRefY: 0,
        endRefX: 0,
        endRefY: 0,
        eleScale: 1
      };
    },
    computed: {
      realRef () {
        return this.dataRef || 'zpmMovable-' + getUUID()
      }
    },
    methods: {
      calcToRealPx (px) {
        // 将750设计图上的尺寸 转变为 真实尺寸
        const deviceInfo = weex.config.env
        let dpr = (deviceInfo.platform.toLowerCase() === 'web') ? deviceInfo.dpr : deviceInfo.scale
        return parseFloat(px * deviceInfo.deviceWidth / dpr / 750)
      },
      calcToDesignPx (px) {
        // 将 真实尺寸 转变为 750设计图上的尺寸
        const deviceInfo = weex.config.env
        let dpr = (deviceInfo.platform.toLowerCase() === 'web') ? deviceInfo.dpr : deviceInfo.scale
        return parseFloat(750 * px / (deviceInfo.deviceWidth / dpr))
      },
      calcWeb (px) {
        const deviceInfo = weex.config.env
        return (deviceInfo.platform.toLowerCase() === 'web') ? parseFloat(px * deviceInfo.deviceWidth / deviceInfo.dpr / 750) : px
      },
      calcDesignWeb (px) {
        const deviceInfo = weex.config.env
        return (deviceInfo.platform.toLowerCase() === 'web') ? parseFloat(750 * px / (deviceInfo.deviceWidth / deviceInfo.dpr)) : px
      },
      touchStart (e) {
        // 禁止页面滚动
        global.eventHub.$emit('disable-scroll')
        this.topMovable = true
        let movableRef = this.$refs[this.realRef]
        this.eleScale = this.scale
        animation.transition(movableRef, {
          styles: {
            transform: `translate(${this.left}px, ${this.top}px) scale(${this.eleScale})`
          },
          duration: 100,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)',
          delay: 0
        })
        if (this.startRef) {
          let _startRef = this.$root.ZpmMovable[this.startRef]
          if (this.axis !== 'y') this.minX = _startRef.left + _startRef.size[0]
          if (this.axis !== 'x') this.minY = _startRef.top + _startRef.size[1]
        }
        if (this.endRef) {
          let _endRef = this.$root.ZpmMovable[this.endRef]
          if (this.axis !== 'y') this.maxX = _endRef.left - _endRef.size[0]
          if (this.axis !== 'x') this.maxY = _endRef.top - _endRef.size[1]
        }
        if (this.axis !== 'y') this.startX = e.changedTouches[0].pageX
        if (this.axis !== 'x') this.startY = e.changedTouches[0].pageY
      },
      touchMove (e) {
        if (this.axis !== 'y') this.deltaX = e.changedTouches[0].pageX - this.startX
        if (this.axis !== 'x') this.deltaY = e.changedTouches[0].pageY - this.startY
      },
      touchEnd (e) {
        // 恢复页面滚动
        global.eventHub.$emit('reset-scroll')
        this.topMovable = false
        let movableRef = this.$refs[this.realRef]
        let _left = this.left
        let _top = this.top
        if (_left < this.minX) {
          _left = this.minX
        } else if (_left > this.maxX) {
          _left = this.maxX
        } else {
        }
        if (_top < this.minY) {
          _top = this.minY
        } else if (_top > this.maxY) {
          _top = this.maxY
        } else {
        }
        this.eleX = _left
        this.eleY = _top
        this.startX = 0
        this.startY = 0
        this.deltaX = 0
        this.deltaY = 0
        if (this.dataModelOrigin && this.dataModelOrigin[this.realRef]) {
          this.dataModelOrigin[this.realRef].x = _left
          this.dataModelOrigin[this.realRef].y = _top
        }
        this.eleScale = 1
        animation.transition(movableRef, {
          styles: {
            transform: `translate(${_left}px, ${_top}px) scale(${this.eleScale})`
          },
          duration: 100,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)',
          delay: 0
        })
        this.$emit('zpmend', {ref: this.realRef})
      }
    },
    components: {},
    created () {
      const that = this
      that.$root.zpmMovableTo = function (ref, args) {
        let instance = that.$root.ZpmMovable[ref]
        if (!instance) {
          return
        }
        if (instance.startRef) {
          let _startRef = that.$root.ZpmMovable[instance.startRef]
          if (instance.axis !== 'y') instance.minX = _startRef.left + _startRef.size[0]
          if (instance.axis !== 'x') instance.minY = _startRef.top + _startRef.size[1]
        }
        if (instance.endRef) {
          let _endRef = that.$root.ZpmMovable[instance.endRef]
          if (instance.axis !== 'y') instance.maxX = _endRef.left - _endRef.size[0]
          if (instance.axis !== 'x') instance.maxY = _endRef.top - _endRef.size[1]
        }
        let _left = args.left || instance.left
        let _top = args.top || instance.top
        if (_left <= instance.minX) {
          _left = instance.minX
        } else if (_left >= instance.maxX) {
          _left = instance.maxX
        } else {}
        if (_top <= instance.minY) {
          _top = instance.minY
        } else if (_top >= instance.maxY) {
          _top = instance.maxY
        } else {}
        animation.transition(instance.$refs[ref], {
          styles: {
            transform: `translate(${_left}px, ${_top}px) scale(${instance.scale})`
          },
          duration: 100,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)',
          delay: 0
        }, () => {
          instance.left = _left
          instance.top = _top
          instance.eleX = _left
          instance.eleY = _top
        })
      }
      this.$nextTick(() => {
        this.left = this.x
        this.top = this.y
        this.eleX = this.left
        this.eleY = this.top
        let _from = this.from
        let _to = this.to
        if (typeof this.from === 'string') _from = JSON.parse(_from)
        if (typeof this.to === 'string') _to = JSON.parse(_to)
        this.minX = _from[0]
        this.minY = _from[1]
        this.maxX = _to[0]
        this.maxY = _to[1]

        let _size = this.size
        if (typeof this.size === 'string') _size = JSON.parse(_size)
        this.width = _size[0]
        this.height = _size[1]

        // 将当前实例挂载到root上
        let _instance = {}
        _instance[this.realRef] = this
        this.$root.ZpmMovable = Object.assign({}, this.$root.ZpmMovable, _instance)
      })
    },
    watch: {
      'deltaX': function (value) {
        let _tempX = this.eleX + value
        if (_tempX >= this.minX - this.width && _tempX <= this.maxX + this.width) {
          this.left = this.eleX + value
        }
      },
      'deltaY': function (value) {
        let _tempY = this.eleY + value
        if (_tempY >= this.minY - this.height && _tempY <= this.maxY + this.height) {
          this.top = this.eleY + value
        }
      }
    }
  };
</script>
