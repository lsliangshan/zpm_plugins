<template>
  <div class="zpm-msg-container" ref="box" :style="[styles, animStyle]">
    <div class="msg-content">
      <slot></slot>
      <web v-if="options.type === 'h5'" :src="options.url" :style="[webViweStyle]"></web>
    </div>
    <div class="close-wrap" :style="{width:styles.width}">
      <div class="close-btn" @touchend="hide">
        <div class="left-line"></div>
        <div class="right-line"></div>
      </div>
    </div>
  </div>
</template>
<style>
  .zpm-msg-container {
    position: fixed;
    left: 0;
    top: 0;
    justify-content: center;
    align-items: center;
  }
  .close-wrap {
    height: 120px;
    position: absolute;
    bottom: 0;
    left: 0;
    flex-direction: row;
    align-items: center;
    justify-content: center;
  }
  .close-btn {
    width: 100px;
    height: 100px;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
  .left-line {
    height: 45px;
    width: 2px;
    background-color: #bbb;
    transform: rotate(45deg);
  }
  .right-line {
    height: 45px;
    width: 2px;
    background-color: #bbb;
    transform: rotate(-45deg);
  }
</style>
<script>
  const animation = weex.requireModule('animation');
  export default {
    name: 'ZpmMsgBox',
    data() {
      return {
        // 组件默认样式
        options: {
          type: 'weex', // inner content 类型 weex或h5
          animationIn: 'slideUp',
          bgColor: '',
          timingFunction: 'cubic-bezier(.215,.61,.355,1)', // 动画类型
          duration: 300,
          delay: 0,
          componentName: '', // slot组件名字
          url: '' // type为h5对应的url参数
        },
        styles: {}, // 默认样式
        animStyle: {}, // 动画样式
        webViweStyle: {}, // webViwe样式
        deviceInfo: weex.config.env,
        el: '', // 执行动画的元素
        showType: '' // 动画执行形式 slide或fade
      };
    },
    computed: {},
    components: {},
    created() {},
    mounted() {
      this.el = this.$refs.box;
      let width = this.getRect(1).width;
      let height = this.getRect(1).height;
      // 初始化默认样式
      this.styles = {
        width: `${width}px`,
        height: `${height}px`
        // transformOrigin: 'center center'
      };

      // 初始化动画样式
      this.animStyle = {
        transform: `translate(0, ${height}px)`,
        opacity: 1
      };

      // var param = this.animParam();
      // param.styles = {
      //   transform: `translate(0, ${height * 2}px)`,
      //   transformOrigin: 'center center',
      //   opacity: 1
      // };
      // animation.transition(this.el, param, function() {});

      // ZpmMsgBox组件显示方法
      this.$root.showMsgBox = args => {
        this.options = Object.assign({}, this.options, args);
        // 初始化动画入场的初始位置
        this.initAnimStyle();
        // 传回组件信息
        this.$root.ZpmMsgBox = this.options;
        setTimeout(() => {
          this.show();
        }, 30);
      };

      // ZpmMsgBox组件隐藏方法
      this.$root.hideMsgBox = () => {
        // 传回组件信息
        this.$root.ZpmMsgBox = this.options;
        this.hide();
      };
    },
    methods: {
      // 初始化动画样式
      initAnimStyle() {
        let options = this.options;

        let animationStyle = this.options.animationIn;

        // 初始化背景色
        this.$set(
          this.styles,
          'backgroundColor',
          options.bgColor || 'rgba(255, 255, 255, 1)'
        );
        this.$set(this.styles, 'display', 'block');

        // 判断显示类型，类型：slide|fade
        this.showType = /slide|fade/g.exec(animationStyle)[0];
        // 不传入执行时间，即初始化动画位置时不需要动画
        this.initPosition();
        // 初始化webview的宽高
        setTimeout(() => {
          this.initWebViweStyle();
        }, 0);
      },
      // 初始化show的进入位置或hide的结束位置
      initPosition(isDuration) {
        let animationStyle = this.options.animationIn;
        let stylesObj = {};
        let width = this.getRect(1).width;
        let height = this.getRect(1).height;
        if (this.deviceInfo.platform.toLowerCase() === 'web') {
          width = width * 2;
          height = height * 2;
        }
        // 初始化slide位置或fade透明度
        switch (animationStyle) {
          case 'slideUp':
            stylesObj.transform = `translate(0, ${height}px)`;
            break;
          case 'slideDown':
            stylesObj.transform = `translate(0, ${-height}px)`;
            break;
          case 'slideLeft':
            stylesObj.transform = `translate(${-width}px, 0)`;
            break;
          case 'slideRight':
            stylesObj.transform = `translate(${width}px, 0)`;
            break;
          case 'fadeIn':
            stylesObj.transform = 'translate(0, 0)';
            stylesObj.opacity = 0;
            break;
          default:
            stylesObj.transform = `translate(0, ${height}px)`;
        }
        if (isDuration) {
          let that = this;
          let param = this.animParam(isDuration);
          param.styles = {
            transform: stylesObj.transform,
            transformOrigin: 'center center',
            opacity: stylesObj.opacity
          };
          animation.transition(this.el, param, function() {
            if (animationStyle === 'fadeIn') {
              stylesObj.display = 'none';
              that.animStyle = stylesObj;
            }
          });
        } else {
          // show时更新动画的初始样式
          this.animStyle = stylesObj;
        }
        // this.animStyle = stylesObj;
        // var param = this.animParam(isDuration);
        // param.styles = {
        //   transform: stylesObj.transform,
        //   transformOrigin: 'center center',
        //   opacity: stylesObj.opacity
        // };
        // animation.transition(this.el, param, function() {});
      },
      // 初始化webview的样式函数
      initWebViweStyle() {
        let width = this.getRect(1).width;
        let height = this.getRect(1).height;
        // 初始化webview的样式
        this.webViweStyle = {
          width: `${width}px`,
          height: `${height - 120}px`
        };
      },
      getRect(percent) {
        let _per;
        try {
          _per = parseFloat(percent);
        } catch (err) {
          _per = 1;
        }
        let _h = 0;
        let _w = 0;
        if (this.deviceInfo.platform.toLowerCase() === 'web') {
          _h = this.deviceInfo.deviceHeight / this.deviceInfo.dpr;
          _w = this.deviceInfo.deviceWidth / this.deviceInfo.dpr;
        } else {
          _h = 750 / this.deviceInfo.deviceWidth * this.deviceInfo.deviceHeight;
          _w = 750;
        }
        return {
          height: parseFloat(_per) * _h,
          width: parseFloat(_per) * _w
        };
      },
      show() {
        let param = this.animParam(true);
        param.styles = {
          transform: 'translate(0, 0)',
          // transformOrigin: 'center center',
          opacity: 1
        };
        animation.transition(this.el, param, function() {});
      },
      hide() {
        this.initPosition(true);
      },
      animParam(isDuration) {
        return {
          duration: isDuration ? this.options.duration : 0,
          timingFunction: this.options.timFun,
          needLayout: false,
          delay: this.options.delay
        };
      }
    },
    watch: {}
  };
</script>