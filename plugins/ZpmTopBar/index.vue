<template>
    <div class="zpm-top-bar" :class="['zpm-top-nav-' + options.isFixed]" :style="[options.styles]" v-if="!options.bgColor">
        <div class="zpm-top-bar-return">
            <image src="//img09.zhaopin.cn/2012/other/mobile/weex/searchJobResult/returnback3X.png" class="returnback"></image>
        </div>
        <text class="zpm-top-bar-title" :style="{color: options.color || '#FFFFFF'}">{{options.title}}</text>
        <div class="returnbox-area" @click="goBack"></div>
        <div class="zpm-top-bar-arrow"></div>
    </div>
</template>
<script>
    export default {
      name: 'ZpmTopBar',
      data () {
        return {
          ref: 'test',
          options: {
            title: '导航',
            isFixed: false,
            bgColor: '',
            color: '',
            styles: {}
          },
          deviceInfo: weex.config.env,
          dpr: weex.config.env.dpr || weex.config.env.scale || 1
        };
      },
      created () {
        const that = this
        that.$root.changeTitle = function (args) {
          Object.assign(that.options, args)
          that.changeTitle(that.$refs[that.ref])
        }
      },
      methods: {
        changeTitle (el) {
          const that = this
        },
        goBack () {
          const navigator = weex.requireModule('navigator')
          navigator.pop();
        }
      }
    }
</script>
<style scoped>
    .zpm-top-bar{
        flex-direction: row;
        justify-content: space-between;
        align-items:center;
        padding-bottom: 14px;
        background-color: #ffffff;
        opacity: 1;
        height: 140px;
        width: 750px;
        padding-top: 65px;
        border-bottom:1px solid #e6e6e6;
    }
    .zpm-top-bar-true {
        position: sticky;
        left: 0;
        top: 0;
    }
    .zpm-top-bar-return{
        position:absolute;
        left:0;
        bottom:21px;
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
    .returnback{
        width: 44px;
        height: 44px;
    }
    .zpm-top-bar-title{
        flex-direction: row;
        justify-content: center;
        align-items: center;
        text-align: center;
        text-overflow: ellipsis;
        width: 750px;
        font-size: 36px;
        color: #000;
        line-height: 58px;
        lines:1;
    }
    .zpm-top-bar-arrow{
        position: absolute;
        bottom: 0;
        right:32px;
        width: 64px;
        height: 96px;
        background-color: blue;
    }
</style>