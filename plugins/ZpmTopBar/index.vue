<template>
    <div class="zpm-top-bar-container">
        <div class="zpm-top-bar" :style="{'background-color': options.bgColor || '#FFFFFF'}">
            <div class="zpm-top-bar-return">
                <image src="//img09.zhaopin.cn/2012/other/mobile/weex/searchJobResult/returnback3X.png" class="returnback"></image>
            </div>
            <div class="zpm-top-bar-title">
                <div class="zpm-top-bar-maintitle">
                    <text class="zpm-top-bar-maintitle-text" :style="options.mainTitle.style">{{options.mainTitle.text}}</text>
                </div>
                <div class="zpm-top-bar-subtitle" v-if="options.subTitle.text">
                    <text class="zpm-top-bar-subtitle-text" :style="options.subTitle.style">{{options.subTitle.text}}</text>
                </div>
            </div>
            <div class="returnbox-area" @click="goBack"></div>
            <div class="zpm-top-bar-arrow" @click="show" v-if="options.ifShow">
                <image src="//img09.zhaopin.cn/2012/other/mobile/weex/icon_more_black.png" class="iconMorBlack"></image>
            </div>
            <div class="zpm-top-bar-arrow" v-if="!options.ifShow"></div>
        </div>
        <!--<div class="zpm-foot-menu-container" ref="mask" :style="{height: getHeight(1)+'px', opacity: 1, transform: 'translate(0, '+ getHeight(1) +'px)'}" @touchend="hide">-->
        <div class="zpm-foot-menu-container" ref="mask" :style="initStyle" @click="hide">
            <!--<div class="zpm-foot-menu" ref="menuframe" :style="{transform: 'translate(0, '+ menuH +'px)'}">-->
            <div class="zpm-foot-menu" ref="menuframe" :style="footMenuAnimStyle">
                <div class="zpm-foot-menu-frame">
                    <div class="zpm-foot-menu-line" :class="['zpm-foot-menu-line-'+index]" v-for="(line, index) in options.footMenu" :key="index"
                         @click="line.action">
                        <text class="zpm-foot-menu-content" :style="line.style">{{line.name}}</text>
                    </div>
                </div>
                <div class="zpm-foot-menu-cancel" @touchend="hide">
                    <text class="zpm-foot-menu-cancel-txt">取消</text>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const dom = weex.requireModule('dom');
  const animation = weex.requireModule('animation');
  const modal = weex.requireModule('modal');
  export default {
    name: 'ZpmTopBar',
    data() {
      return {
        options: {
          bgColor: '',
          mainTitle: {
            text: '',
            style: {}
          },
          subTitle: {
            text: '',
            style: {}
          },
          ifShow: true,
          footMenu: [
            {
              name: '不看该公司的职位',
              action: function() {
                modal.alert({
                  message: '不看该公司的职位'
                });
              },
              style: {}
            },
            {
              name: '举报职位',
              action: function() {
                modal.alert({
                  message: '举报职位'
                });
              },
              style: {}
            }
          ]
        },
        initStyle: {}, // 默认样式
        footMenuAnimStyle: {}, // menu默认样式
        menuH: 500,
        deviceInfo: weex.config.env,
        dpr: weex.config.env.dpr || weex.config.env.scale || 1
      };
    },
    created() {
      this._initFn();
      const that = this;
      that.$root.initZpmTopBar = function(args) {
        Object.assign(that.options, args);
        that.optionsMenu(that.options);
      };
      that.$root.changeTitle = function(args) {
        Object.assign(that.options, args);
        that.changeTitle(that.options);
      };
      that.$root.changeBgcolor = function(args) {
        Object.assign(that.options, args);
        that.changeBgcolor(that.options);
      };
      that.$root.showMenu = function(args) {
        Object.assign(that.options, args, {
          shown: true
        });
        that.show(that.$refs[that.ref]);
      };
      that.$root.hideMenu = function() {
        Object.assign(that.options, {
          shown: false
        });
        that.hide(that.$refs[that.ref]);
      };
      // that.$root.optionsMenu = function(args) {
      //   Object.assign(that.options, args);
      //   that.optionsMenu(that.options);
      // };
    },
    mounted() {},
    methods: {
      _initFn() {
        let height = this.getHeight(1);
        // 初始化默认样式
        this.initStyle = {
          height: `${height}px`,
          transform: `translate(0, ${height}px)`,
          opacity: 1
        };

        // 初始化menu样式
        this.footMenuAnimStyle = {
          transform: `translate(0, ${this.menuH}px)`
        };
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
      changeTitle(el) {
        const that = this;
        that.options.mainTitle = el.mainTitle;
        that.options.subTitle = el.subTitle;
        // that.options.ifShow = el.ifShow
      },
      changeBgcolor(el) {
        const that = this;
        that.options.bgColor = el.bgColor;
      },
      goBack() {
        const navigator = weex.requireModule('navigator');
        navigator.pop();
      },
      show() {
        const that = this;
        const _maskRef = that.$refs['mask'];
        const _menuframeRef = that.$refs['menuframe'];
        animation.transition(
          _maskRef,
          {
            styles: {
              transform: 'translate(0, 0)'
            },
            duration: 1,
            timingFunction: 'linear',
            delay: 0
          },
          function() {
            animation.transition(_maskRef, {
              styles: {
                opacity: 1
              },
              duration: 300,
              timingFunction: 'cubic-bezier(.215,.61,.355,1)',
              delay: 0
            });
          }
        );

        animation.transition(
          _menuframeRef,
          {
            styles: {
              transform: 'translate(0, 0)'
            },
            duration: 300,
            timingFunction: 'cubic-bezier(.215,.61,.355,1)',
            delay: 0
          },
          function() {}
        );
      },
      hide() {
        const that = this;
        const _maskRef = that.$refs['mask'];
        const _menuframeRef = that.$refs['menuframe'];

        let height = this.getHeight(1);
        let menuH = this.menuH;
        if (this.deviceInfo.platform.toLowerCase() === 'web') {
          height = height * 2;
          menuH = menuH * 2;
        }
        animation.transition(
          _maskRef,
          {
            styles: {
              opacity: 0
            },
            duration: 500,
            delay: 0,
            timingFunction: 'cubic-bezier(.215,.61,.355,1)'
          },
          function() {
            animation.transition(_maskRef, {
              styles: {
                transform: `translate(0, ${height}px)`
              },
              duration: 1,
              delay: 0,
              timingFunction: 'linear'
            });
          }
        );
        animation.transition(
          _menuframeRef,
          {
            styles: {
              transform: `translate(0, ${menuH}px)`
            },
            duration: 500,
            delay: 0,
            timingFunction: 'cubic-bezier(.215,.61,.355,1)'
          },
          function() {}
        );
      },
      optionsMenu(el) {
        const that = this;
        console.log(el);
        that.options.footMenu = el.footMenu;
        that.options.ifShow = el.ifShow;
      }
    }
  };
</script>
<style scoped>
  .zpm-top-bar-container {
    margin-top: 0;
    padding-top: 0;
  }
  .zpm-top-bar {
    position: sticky;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    padding-bottom: 14px;
    padding-top: 65px;
    height: 128px;
    width: 750px;
    background-color: #ffffff;
    opacity: 1;
    border-bottom: 1px solid #e6e6e6;
  }
  .zpm-top-bar-return {
    width: 76px;
    height: 44px;
    justify-content: flex-end;
    flex-direction: row;
  }
  .returnbox-area {
    position: absolute;
    left: 0;
    bottom: 0;
    width: 96px;
    height: 96px;
  }
  .returnback {
    width: 44px;
    height: 44px;
  }
  .zpm-top-bar-title {
    width: 444px;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
  }
  .zpm-top-bar-maintitle {
    width: 444px;
    height: 40px;
    margin-top: 10px;
    margin-bottom: 10px;
    align-items: center;
  }
  .zpm-top-bar-maintitle-text {
    max-width: 444px;
    font-size: 32px;
    font-weight: 400;
    color: #282828;
    line-height: 40px;
    overflow: hidden;
    text-overflow: ellipsis;
    lines: 1;
  }
  .zpm-top-bar-subtitle {
    width: 444px;
    height: 58px;
    align-items: center;
  }
  .zpm-top-bar-subtitle-text {
    max-width: 444px;
    font-size: 32px;
    font-weight: bold;
    color: #ff5c56;
    line-height: 32px;
    overflow: hidden;
    text-overflow: ellipsis;
    lines: 1;
  }
  .zpm-top-bar-arrow {
    justify-content: flex-start;
    flex-direction: row;
    width: 95px;
    height: 63px;
  }
  .iconMorBlack {
    width: 63px;
    height: 63px;
    margin-right: 32px;
  }
  .zpm-foot-menu-container {
    position: fixed;
    left: 0;
    top: 0;
    z-index: 10000;
    width: 750px;
    padding-left: 32px;
    padding-right: 32px;
    background-color: rgba(0, 0, 0, 0.4);
    opacity: 0;
    flex-direction: row;
    justify-content: center;
  }
  .zpm-foot-menu {
    position: absolute;
    bottom: 0px;
    left: 0px;
    margin-bottom: 20px;
    width: 750px;
    padding-left: 32px;
    padding-right: 32px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .zpm-foot-menu-frame {
    width: 686px;
    text-align: center;
  }
  .zpm-foot-menu-line {
    width: 686px;
    height: 110px;
    line-height: 110px;
    font-size: 36px;
    background-color: #ffffff;
    border-bottom-width: 1px;
    border-bottom-style: solid;
    border-bottom-color: #e6e6e6;
    color: #303030;
    align-items: center;
    justify-content: center;
    text-align: center;
  }
  .zpm-foot-menu-line-0 {
    border-top-left-radius: 20px;
    border-top-right-radius: 20px;
  }
  .zpm-foot-menu-line-1 {
    border-bottom-left-radius: 20px;
    border-bottom-right-radius: 20px;
  }
  .zpm-foot-menu-content {
    font-size: 36px;
    font-weight: 400;
    width: auto !important;
  }
  .zpm-foot-menu-cancel {
    margin-top: 20px;
    margin-bottom: 20px;
    width: 686px;
    height: 110px;
    line-height: 110px;
    align-items: center;
    text-align: center;
    justify-content: center;
    background-color: #ffffff;
    border-radius: 14px;
  }
  .zpm-foot-menu-cancel-txt {
    font-size: 36px;
    color: #007aff;
    font-weight: 400;
  }
</style>