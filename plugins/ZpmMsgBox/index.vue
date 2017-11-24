<template>
    <!--<div class="zpm-msg-container" ref="box" :style="{ width:deviceInfo.deviceWidth+'px', height:deviceInfo.deviceHeight+'px', transform: 'translate3D('+ 0 +'px, '+ deviceInfo.deviceHeight +'px, '+ 0 +'px)'}">-->
    <div class="zpm-msg-container" ref="box" :style="[styles, { width:deviceInfo.deviceWidth/2+'px', height:getHeight(1)+'px'}]">
        <div class="msg-content">
            <slot></slot>
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
    background-color: rgba(0, 0, 0, 0.5);
  }
  .close-wrap {
    /*width:750px;*/
    height: 100px;
    position: absolute;
    bottom: 0;
    left: 0;
    background-color: rgba(255, 255, 255, 1);
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }
  .close-btn {
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
    background-color: black;
    transform: rotate(45deg);
  }
  .right-line {
    height: 50px;
    width: 2px;
    background-color: black;
    transform: rotate(-45deg);
  }
</style>
<script>
  const animation = weex.requireModule('animation');
  export default {
    name: 'ZpmMsgBox',
    data() {
      return {
        options: {
          type: '',
          animationIn: 'slideLeft',
          content: '',
          bgColor: '',
          color: ''
        },
        styles: {},
        deviceInfo: weex.config.env,
        el: '',
        showType: '',
        hideType: ''
      };
    },
    computed: {},
    components: {},
    created() {
      const that = this;
      that.$root.showMsgBox = function(args) {
        Object.assign(that.options, args);

        that.initPosition(that.options);

        console.log(that.styles);

        setTimeout(function() {
          that.show();
        }, 100);
      };
    },
    mounted() {
      this.el = this.$refs.box;
    },
    methods: {
      initPosition(options) {
        let stylesObj = {};
        let animationStyle = options.animationIn;
        // 判断显示类型，类型：slide|fade
        this.showType = /slide|fade/g.exec(options.animationIn)[0];
        // console.log(`类型:${this.showType}`);
        // 初始化slide位置或fade透明度
        switch (animationStyle) {
          case 'slideUp':
            stylesObj.transform = `translate3d(0, ${this.getHeight(1)}px, 0)`;
            break;
          case 'slideDown':
            stylesObj.transform = `translate3d(0, ${-this.getHeight(1)}px, 0)`;
            break;
          case 'slideLeft':
            stylesObj.transform = `translate3d(${-this.getHeight(1)}px, 0, 0)`;
            break;
          case 'slideRight':
            stylesObj.transform = `translate3d(${this.getHeight(1)}px, 0, 0)`;
            break;
          case 'fadeIn':
            stylesObj.transform = 'translate3d(0, 0, 0)';
            stylesObj.opacity = 0;
            break;
          default:
            stylesObj.transform = `translate3d(0, ${this.getHeight(1)}px, 0)`;
        }
        this.styles = stylesObj;
        console.log('uuuuuuuu', this.styles);
        return this.styles;
      },
      getHeight(percent) {
        let _per;
        try {
          _per = parseFloat(percent);
        } catch (err) {
          _per = 1;
        }
        let _h = 0;
        if (this.deviceInfo.platform.toLowerCase() === 'web') {
          _h = this.deviceInfo.deviceHeight / this.deviceInfo.dpr;
        } else {
          _h = 750 / this.deviceInfo.deviceWidth * this.deviceInfo.deviceHeight;
        }
        return parseFloat(_per) * _h;
      },
      show() {
        const that = this;
        console.log('-------', this.showType);
        if (this.showType === 'slide') {
          animation.transition(
            this.el,
            {
              styles: {
                transform: 'translate3d(0, 0, 0)',
                transformOrigin: 'center center'
              },
              duration: 600,
              timingFunction: 'ease-in',
              needLayout: false,
              delay: 0
            },
            function() {
              console.log('animation----2');
            }
          );
        } else if (this.showType === 'fade') {
          console.log('进来');
          animation.transition(
            this.el,
            {
              styles: {
                transform: 'scale(1)'
              },
              duration: 10,
              timingFunction: 'ease',
              needLayout: false,
              delay: 0
            },
            function() {
              animation.transition(
                that.el,
                {
                  styles: {
                    opacity: 1
                  },
                  duration: 300,
                  timingFunction: 'ease',
                  needLayout: false,
                  delay: 0
                },
                function() {
                  console.log('show opacity');
                }
              );
            }
          );
        }
      },
      hide() {
        console.log('+++++++', this.styles);
        const that = this;
        const reCaluStyles = this.initPosition(this.options);
        console.log('sjkjfskhskhf', this.options, reCaluStyles);
        if (this.showType === 'slide') {
          animation.transition(
            this.el,
            {
              styles: reCaluStyles,
              duration: 900, // ms
              timingFunction: 'ease',
              needLayout: false,
              delay: 0 // ms
            },
            function() {
              console.log('hide slide');
            }
          );
        } else if (this.showType === 'fade') {
          console.log('hide opacity input');
          animation.transition(
            this.el,
            {
              styles: {
                opacity: 0
              },
              duration: 300, // ms
              timingFunction: 'ease',
              needLayout: false,
              delay: 0 // ms
            },
            function() {
              animation.transition(that.el, {
                styles: {
                  transform: 'scale(0)'
                },
                duration: 10, // ms
                timingFunction: 'ease',
                needLayout: false,
                delay: 0 // ms
              });
            }
          );
        }
      }
    },
    watch: {}
  };
</script>
