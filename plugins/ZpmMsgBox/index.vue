<template>
    <!--<div class="zpm-msg-container" ref="box" :style="{ width:deviceInfo.deviceWidth+'px', height:deviceInfo.deviceHeight+'px', transform: 'translate3D('+ 0 +'px, '+ deviceInfo.deviceHeight +'px, '+ 0 +'px)'}">-->
    <div class="zpm-msg-container" ref="box" :style="[styles, { width:deviceInfo.deviceWidth/2+'px', height:deviceInfo.deviceHeight/2+'px'}]">
        <div class="msg-content">
        </div>
        <div class="close-wrap" :style="{width:deviceInfo.deviceWidth/2+'px'}">
            <div class="close-btn" @touchend="hide">
                <div class="left-line"></div>
                <div class="right-line"></div>
            </div>
        </div>
    </div>
</template>
<style scoped>
    .zpm-msg-container {
        position: fixed;
        left: 0;
        top: 0;
        justify-content: center;
        align-items: center;
        /*pointer-events: none;*/
        background-color: rgba(255, 255, 255, 1);
    }
    .close-wrap{
        /*width:750px;*/
        height:100px;
        position: absolute;
        bottom: 0;
        left:0;
        background-color: rgba(255, 255, 255, 1);
        flex-direction: row;
        align-items: center;
        justify-content: center;
    }
    .close-btn{
        width: 100px;
        height: 100px;
        background-color: transparent;
        flex-direction: row;
        justify-content: center;
        align-items: center;
    }
    .left-line {
        height: 50px;
        width: 2px;
        background-color:black;
        transform: rotate(45deg);
    }
    .right-line {
        height:50px;
        width:2px;
        background-color:black;
        transform: rotate(-45deg);
    }
</style>
<script>
  const animation = weex.requireModule('animation');
  export default {
    name: 'ZpmMsgBox',
    data () {
      return {
        options: {
          type: '',
          animationIn: 'slideLeft',
          animationOut: 'slideDown',
          content: '',
          bgColor: '',
          color: ''
        },
        styles: {},
        deviceInfo: weex.config.env,
        el: '',
        bottom: {
          width: ''
        }
      };
    },
    computed: {
    },
    components: {},
    created () {
      console.log(this.$refs.box);
      const that = this
      that.$root.showMsgBox = function (args) {
        console.log(that.deviceInfo);
        Object.assign(that.options, args);
        that.initPosition(that.options);
        console.log(that.styles);
        setTimeout(function () {
          that.show();
        }, 0);
      }
    },
    mounted () {
      this.el = this.$refs.box;
    },
    methods: {
      initPosition (options) {
        let stylesObj = {};
        let animationStyle = options.animationIn;
        switch (animationStyle) {
          case 'slideUp':
            stylesObj.transform = `translate3d(0, ${this.getHeight(1)}px, 0)`;
            break
          default: stylesObj.transform = `translate3d(0, ${this.getHeight(1)}px, 0)`;
        }
        this.styles = stylesObj;
      },
      getHeight (percent) {
        let _per
        try {
          _per = parseFloat(percent)
        } catch (err) {
          _per = 1
        }
        let _h = 0
        if (this.deviceInfo.platform.toLowerCase() === 'web') {
          _h = this.deviceInfo.deviceHeight / this.deviceInfo.dpr
        } else {
          _h = 750 / this.deviceInfo.deviceWidth * this.deviceInfo.deviceHeight
        }
        return parseFloat(_per) * _h
      },
      show () {
        console.log('animation----1');
        animation.transition(this.el, {
          styles: {
            transform: 'translate3d(0, 0, 0)',
            transformOrigin: 'center center'
          },
          duration: 600,
          timingFunction: 'ease-in',
          needLayout: false,
          delay: 0
        }, function () {
          console.log('animation----2');
        })
      },
      hide () {
        console.log('animation----3');
        animation.transition(this.el, {
          styles: {
            transform: `translate3D(0, ${this.deviceInfo.deviceHeight}px, 0)`,
            transformOrigin: 'center center'
          },
          duration: 900, // ms
          timingFunction: 'ease',
          needLayout: false,
          delay: 0 // ms
        }, function () {
          console.log('animation----4');
        })
      }
    },
    watch: {
    }
  };
</script>
