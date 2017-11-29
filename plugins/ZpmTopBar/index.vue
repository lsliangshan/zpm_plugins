<template>
    <div class="zpm-top-bar-container">
        <div class="zpm-top-bar" :style="{'background-color': options.bgColor}">
            <div class="zpm-top-bar-return">
                <image src="//img09.zhaopin.cn/2012/other/mobile/weex/searchJobResult/returnback3X.png" class="returnback"></image>
            </div>
            <div class="zpm-top-bar-title">
                <div class="zpm-top-bar-maintitle">
                    <text class="zpm-top-bar-maintitle-text" :style="options.mainTitle.style">{{options.mainTitle.text}}
                    </text>
                </div>
                <div class="zpm-top-bar-subtitle" v-if="options.subTitle.text">
                    <text class="zpm-top-bar-subtitle-text" :style="options.subTitle.style">{{options.subTitle.text}}</text>
                </div>
            </div>
            <div class="returnbox-area" @click="goBack"></div>
            <div class="zpm-top-bar-arrow" @click="show">
                <image src="//img09.zhaopin.cn/2012/other/mobile/weex/icon_more_black.png" class="iconMorBlack"></image>
            </div>
        </div>
        <div class="zpm-foot-menu-container" ref="mask" :style="{height:getHeight(1)+'px'}">
            <div class="zpm-foot-menu" ref="menu">
                <div class="zpm-foot-menu-frame">
                    <div class="zpm-foot-menu-line" v-for="(line, index) in options.footMenu" :key="index"
                         @click="line.action">
                        <text class="zpm-foot-menu-content" :style="line.style">{{line.name}}</text>
                    </div>
                </div>
                <div class="zpm-foot-menu-cancel" @touchend="hide">
                    <text>取消</text>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const animation = weex.requireModule('animation')
    export default {
        name: 'ZpmTopBar',
        data () {
            return {
                options: {
                    bgColor: '',
                    mainTitle: {
                        text: '智联招聘',
                        style: {
                            color: '#282828',
                            fontSize: '36px'
                        }
                    },
                    subTitle: {
                        text: '',
                        style: {
                            color: 'red',
                            fontSize: '30px'
                        }
                    },
                    footMenu: [
                        {
                            name: '不看该公司的职位',
                            action: function () {
                                alert('11')
                            },
                            style: {
                                color: '#007AFF',
                                fontSize: '36px'
                            }
                        },
                        {
                            name: '举报职位',
                            action: function () {
                                alert('22')
                            },
                            style: {
                                color: '#FF3B30',
                                fontSize: '36px'
                            }
                        }
                    ]
                },
                deviceInfo: weex.config.env,
                dpr: weex.config.env.dpr || weex.config.env.scale || 1
            };
        },
        created () {
            const that = this
            that.$root.init = function (args) {
                Object.assign(that.options, args)
                that.options.title = args
            }
            that.$root.changeTitle = function (args) {
                Object.assign(that.options, args)
                that.changeTitle(that.options)
            }
            that.$root.changeBgcolor = function (args) {
                Object.assign(that.options, args)
                that.changeBgcolor(that.options)
            }
            that.$root.showToast = function (args) {
                Object.assign(that.options, args, {
                    shown: true
                })
                that.show(that.$refs[that.ref])
            }
            that.$root.hideToast = function () {
                Object.assign(that.options, {
                    shown: false
                })
                that.hide(that.$refs[that.ref])
            }
            that.$root.optionsMenu = function (args) {
                Object.assign(that.options, args)
                that.optionsMenu(that.options)
            }
        },
        methods: {
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
            changeTitle (el) {
                const that = this
                that.options.mainTitle = el.mainTitle
                that.options.subTitle = el.subTitle
            },
            changeBgcolor (el) {
                const that = this
                that.options.bgColor = el.bgColor
            },
            goBack () {
                const navigator = weex.requireModule('navigator')
                navigator.pop();
            },
            show (el) {
                const that = this
                const _maskRef = that.$refs['mask']
                const _menuRef = that.$refs['menu']
                animation.transition(_maskRef, {
                    styles: {
                        opacity: 1
                    },
                    duration: 1,
                    timingFunction: 'linear',
                    delay: 0
                }, function () {
                })
                animation.transition(_menuRef, {
                    styles: {
                        opacity: 1
                    },
                    duration: 1,
                    timingFunction: 'linear',
                    delay: 0
                }, function () {
                })
            },
            hide (el) {
                const that = this
                const _maskRef = that.$refs['mask']
                const _menuRef = that.$refs['menu']
                console.log(_maskRef)
                animation.transition(_maskRef, {
                    styles: {
                        opacity: 0
                    },
                    duration: 300,
                    delay: 0,
                    timingFunction: 'ease'
                })
                animation.transition(_menuRef, {
                    styles: {
                        opacity: 0
                    },
                    duration: 300,
                    delay: 0,
                    timingFunction: 'ease'
                })
            },
            optionsMenu (el) {
                const that = this
                console.log(el)
                that.options.footMenu = el.footMenu
            }
        }
    }
</script>
<style scoped>
    .zpm-top-bar-container {
        margin-top: 0;
        padding-top: 0;
    }
    .zpm-top-bar{
        position: sticky;
        flex-direction: row;
        justify-content: space-between;
        align-items:center;
        padding-bottom: 14px;
        padding-top: 65px;
        height: 140px;
        width: 750px;
        background-color: #ffffff;
        opacity: 1;
        border-bottom:1px solid #e6e6e6;
    }
    .zpm-top-bar-return{
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
        width: 444px;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
    }
    .zpm-top-bar-maintitle{
        width: 444px;
        height: 58px;
        align-items: center;
    }
    .zpm-top-bar-maintitle-text{
        max-width:444px;
        font-size: 36px;
        color: #282828;
        line-height: 58px;
        overflow: hidden;
        text-overflow: ellipsis;
        lines: 1;
    }
    .zpm-top-bar-subtitle{
        width: 444px;
        height: 58px;
        align-items: center;
    }
    .zpm-top-bar-subtitle-text{
        max-width:444px;
        font-size: 30px;
        color: red;
        line-height: 58px;
        overflow: hidden;
        text-overflow: ellipsis;
        lines: 1;
    }
    .zpm-top-bar-arrow{
        justify-content: flex-start;
        flex-direction: row;
        width: 95px;
        height: 63px;
    }
    .iconMorBlack{
        width:63px;
        height:63px;
        maring-right:32px;
    }
    .zpm-foot-menu-container{
        position: absolute;
        left: 0;
        top: 0;
        width: 750px;
        padding-left: 32px;
        padding-right: 32px;
        background-color: rgba(0, 0, 0, 0.2);
        opacity: 0;
        flex-direction:row;
        justify-content: center;
    }
    .zpm-foot-menu {
        position: fixed;
        bottom: 0px;
        left:0px;
        margin-bottom: 20px;
        width: 750px;
        opacity: 0;
        flex-direction:column;
        justify-content: center;
        align-items: center;
    }
    .zpm-foot-menu-frame{
        width: 608px;
        text-align: center;
    }
    .zpm-foot-menu-line{
        width: 608px;
        height: 60px;
        line-height: 60px;
        font-size: 36px;
        background-color: #ffffff;
        border-bottom-width: 1px;
        border-bottom-style: solid;
        border-bottom-color: #e6e6e6;
        color: #303030;
        align-items: center;
        text-align:center;
        border-radius: 5px;
    }
    .zpm-foot-menu-content {
        font-size: 36px;
        font-weight: 300;
        width: auto!important;
    }
    .zpm-foot-menu-cancel{
        margin-top: 20px;
        width: 608px;
        height: 60px;
        line-height: 60px;
        align-items: center;
        text-align:center;
        background-color: #ffffff;
        border-radius: 5px;
        font-size: 36px;
        color: #007AFF;
    }
</style>