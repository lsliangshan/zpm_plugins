<template>
    <div class="zpm-toast-container" style="width: 750px;" :class="['zpm-toast-container-' + options.position]" :style="{height: deviceInfo.deviceWidth + 'px'}">
        <div class="zpm-toast-inner" :ref="ref" :class="['zpm-toast-inner-' + options.position, 'zpm-toast-inner-' + options.type]">
            <text class="zpm-toast-content" :style="{color: options.color || '#FFFFFF'}">{{options.content}}</text>
        </div>
    </div>
</template>
<style scoped>
    .zpm-toast-container {
        position: absolute;
        width: 750px;
        left: 0;
        top: 0;
        justify-content: center;
        align-items: center;
        pointer-events: none;
    }
    .zpm-toast-inner {
        padding: 20px;
        box-sizing: border-box;
        flex-direction: column;
        opacity: 0;
    }
    .zpm-toast-inner-top {
        position: fixed;
        width: 750px;
        top: 40px;
    }
    .zpm-toast-container-center {
        padding-left: 150px;
        padding-right: 150px;
    }
    .zpm-toast-inner-center {
        /*max-width: 500px;*/
        border-radius: 5px;
    }
    .zpm-toast-inner-bottom {
        position: fixed;
        width: 750px;
        bottom: 0;
    }
    .zpm-toast-inner-black {
        background-color: rgba(0, 0, 0, 0.9);
    }

    .zpm-toast-inner-success {
        background-color: rgba(92, 184, 92, 0.9);
    }

    .zpm-toast-inner-info {
        background-color: rgba(91, 192, 222, 0.9);
    }

    .zpm-toast-inner-warning {
        background-color: rgba(240, 173, 78, 0.9);
    }

    .zpm-toast-inner-error {
        background-color: rgba(217, 83, 79, 0.9);
    }
    .zpm-toast-content {
        font-size: 26px;
        font-weight: 300;
        width: auto!important;
    }
</style>
<script>
  const animation = weex.requireModule('animation')
  export default {
    name: 'ZpmToast',
    data () {
      return {
        ref: 'test',
        options: {
          type: 'info',
          position: 'center',
          content: '提示',
          duration: 3000,
          bgColor: '',
          color: '',
          shown: false,
          timeout: null
        },
        deviceInfo: weex.config.env
      };
    },
    computed: {
    },
    components: {},
    created () {
      const that = this
      that.$root.showToast = function (args) {
        Object.assign(that.options, args, {
          shown: true
        })
      }
      that.$root.hideToast = function () {
        Object.assign(that.options, {
          shown: false
        })
      }
    },
    methods: {
      show (el) {
        const that = this
        animation.transition(el, {
          styles: {
            opacity: 0
          },
          duration: 1,
          delay: 0,
          timingFunction: 'ease'
        }, function () {
          animation.transition(el, {
            styles: {
              opacity: 1
            },
            duration: 300,
            delay: 0,
            timingFunction: 'ease'
          }, function () {
            if (that.options.timeout) {
              clearTimeout(that.options.timeout)
            }
            that.options.timeout = setTimeout(function () {
              that.hide(el)
            }, that.options.duration)
          })
        })
      },
      hide (el) {
        if (this.options.timeout) {
          clearTimeout(this.options.timeout)
        }
        animation.transition(el, {
          styles: {
            opacity: 0
          },
          duration: 300,
          delay: 0,
          timingFunction: 'ease'
        })
      }
    },
    watch: {
      'options.shown': function (value) {
        const that = this
        let toastEl = this.$refs[this.ref]
        if (value) {
          that.show(toastEl)
        } else {
          that.hide(toastEl)
        }
      }
    }
  };
</script>
