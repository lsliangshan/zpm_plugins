<template>
    <!--<div class="zpm_console_container" :style="{height: wrapperHeight + 'px'}">-->
        <!---->
    <!--</div>-->
    <div class="zpm_console_trigger_container" :ref="eleRef.trigger" @click="toggleZpmConsoleWrapper">
        <text>console</text>
        <div class="zpm_console_wrapper" :ref="eleRef.wrapper" :style="{height: wrapperHeight + 'px', transform: `translate(0px, ${wrapperHeight}px)`}">
            <div class="zpm_console_wrapper_header">
                <div class="header_left" @click="clear">
                    <text class="header_left_text">清除</text>
                </div>
                <div class="header_left_tip">
                    <text class="header_left_tip_text">长按可复制</text>
                </div>
                <div class="header_right" @click="hideZpmConsoleWrapper">
                    <text class="header_right_text">&times;</text>
                </div>
            </div>
            <div class="zpm_console_wrapper_content" :style="{height: (wrapperHeight - 100) + 'px'}">
                <scroller v-if="htmlItems.length > 0">
                    <refresh class="copyright_container">
                        <!--<div style="height: 56px;"></div>-->
                        <text class="copyright_text">powered by &copy;ZpmConsole</text>
                        <!--<div style="height: 32px;"></div>-->
                        <!--<text class="pull-down-text">{{pullDownText}}</text>-->
                    </refresh>
                    <div class="zpm_console_item" v-for="(item, index) in htmlItems" :ref="'zpm-console-item-ref-' + index">
                        <div class="zpm_console_item_title" :style="{opacity: (htmlItems.length > 0 ? 1 : 0)}">
                            <div class="zpm_console_item_title_tag" :class="['zpm_console_item_title_tag_' + item.type]">
                                <text class="zpm_console_item_title_tag_text">{{item.type | formatType}}</text>
                            </div>
                            <text class="zpm_console_item_title_text">{{item.title}}</text>
                            <div class="zpm_console_item_number">
                                <text class="zpm_console_item_number_text">{{index + 1}} / {{htmlItems.length}}</text>
                            </div>
                        </div>
                        <div class="zpm_console_item_content" :class="['zpm_console_item_content_border_' + item.type]" :data-index="index" @longpress="copyText">
                            <text class="zpm_console_item_content_text" v-for="(itm, idx) in item.content" :key="itm">{{itm}}</text>
                        </div>
                    </div>
                    <div style="width: 750px; height: 90px;"></div>
                </scroller>
            </div>
        </div>
    </div>
</template>
<style scoped>
    .zpm_console_container {
        position: fixed;
        left: 0;
        bottom: 0;
        width: 750px;
        pointer-events: none;
    }
    .zpm_console_trigger_container {
        position: absolute;
        width: 100px;
        height: 100px;
        right: 10px;
        bottom: 200px;
        opacity: 1;
        background-color: #ff4585;
    }
    .zpm_console_wrapper {
        position: fixed;
        left: 0;
        bottom: 0;
        width: 750px;
        /*height: 800px;*/
        background-color: #f5f5f5;
        border-top-width: 3px;
        border-top-color: rgba(169,134,255,0.38);
        border-top-style: solid;
        /*transform: translate(0px, 0px);*/
    }
    .zpm_console_wrapper_header {
        position: relative;
        width: 750px;
        height: 80px;
        background-color: #ffffff;
        border-bottom-width: 1px;
        border-bottom-color: #f5f5f5;
        border-bottom-style: solid;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
    }
    .header_left {
        width: 100px;
        height: 80px;
        align-items: center;
        justify-content: center;
    }
    .header_left_text {
        font-size: 32px;
        color: #444;
    }
    .header_left:active {
        opacity: 0.7
    }
    .header_left_tip {
        position: absolute;
        width: 500px;
        height: 80px;
        left: 120px;
        top: 0;
        flex-direction: row;
        align-items: center;
        justify-content: flex-start;
    }
    .header_left_tip_text {
        font-size: 24px;
        color: #cccccc;
    }
    .header_right {
        width: 100px;
        height: 80px;
        align-items: center;
        justify-content: center;
    }
    .header_right_text {
        font-size: 60px;
        color: #444;
        font-weight: 200;
    }
    .header_right:active {
        opacity: 0.7
    }
    .zpm_console_wrapper_content {
        width: 750px;
    }
    .zpm_console_item {
        /*padding: 10px;*/
    }
    .zpm_console_item_title {
        position: sticky;
        left: 0;
        top: 0;
        padding: 10px;
        background-color: #ffffff;
        height: 80px;
        flex-direction: row;
        align-items: center;
        justify-content: flex-start;
    }
    .zpm_console_item_title_tag {
        width: 80px;
        height: 50px;
        border-radius: 5px;
        margin-right: 10px;
        flex-direction: row;
        align-items: center;
        justify-content: center;
    }
    .zpm_console_item_title_tag_text {
        font-size: 28px;
        color: #ffffff;
    }
    .zpm_console_item_title_tag_normal {
        background-color: rgba(0, 0, 0, 0.9);
    }
    .zpm_console_item_title_tag_success {
        background-color: rgba(92, 184, 92, 0.9);
    }

    .zpm_console_item_title_tag_info {
        background-color: rgba(91, 192, 222, 0.9);
    }

    .zpm_console_item_title_tag_warning {
        background-color: rgba(240, 173, 78, 0.9);
    }

    .zpm_console_item_title_tag_error {
        background-color: rgba(217, 83, 79, 0.9);
    }
    .zpm_console_item_title_text {
        font-size: 32px;
        color: #000000;
    }
    .zpm_console_item_number {
        position: absolute;
        right: 20px;
        top: 0;
        width: 150px;
        height: 80px;
        flex-direction: row;
        align-items: center;
        justify-content: flex-end;
    }
    .zpm_console_item_number_text {
        font-size: 28px;
        color: #000000;
    }
    .zpm_console_item_content {
        width: 710px;
        margin: 20px;
        border-top-left-radius: 4px;
        padding-left: 20px;
        border-left-width: 8px;
        border-left-style: solid;
    }
    .zpm_console_item_content_border_normal {
        border-left-color: rgba(0, 0, 0, 0.9);
    }
    .zpm_console_item_content_border_normal:active {
        background-color: rgba(0, 0, 0, 0.1);
    }
    .zpm_console_item_content_border_success {
        border-left-color: rgba(92, 184, 92, 0.9);
    }
    .zpm_console_item_content_border_success:active {
        background-color: rgba(92, 184, 92, 0.1);
    }
    .zpm_console_item_content_border_info {
        border-left-color: rgba(91, 192, 222, 0.9);
    }
    .zpm_console_item_content_border_info:active {
        background-color: rgba(91, 192, 222, 0.1);
    }
    .zpm_console_item_content_border_warning {
        border-left-color: rgba(240, 173, 78, 0.9);
    }
    .zpm_console_item_content_border_warning:active {
        background-color: rgba(240, 173, 78, 0.1);
    }
    .zpm_console_item_content_border_error {
        border-left-color: rgba(217, 83, 79, 0.9);
    }
    .zpm_console_item_content_border_error:active {
        background-color: rgba(217, 83, 79, 0.1);
    }
    /*.zpm_console_item_content:active {*/
        /*background-color: rgba(169, 134, 255, 0.1);*/
    /*}*/
    .zpm_console_item_content_text {
        font-size: 28px;
        line-height: 36px;
        color: #888888;
    }

    .copyright_container {
        width: 750px;
        /*height: 134px;*/
        flex-direction: column;
        align-items: center;
    }
    .copyright_text {
        font-size: 24px;
        color: #c8c8c8;
        text-shadow: 0 1px 0 #171717;
    }
</style>
<script>
  const modal = weex.requireModule('modal')
  const animation = weex.requireModule('animation')
  const clipboard = weex.requireModule('clipboard')
  const dom = weex.requireModule('dom')
  export default {
    name: 'ZpmConsole',
    props: ['height'],
    data () {
      return {
        eleRef: {
          wrapper: 'ele-ref-wrapper',
          trigger: 'ele-ref-trigger'
        },
        eleWrapperShown: false,
        wrapperY: 0,
        wrapperHeight: 800,
        stickyEle: false,
        htmlItems: []
      }
    },
    created () {
      const that = this
      if (this.height && !isNaN(this.height)) {
        this.wrapperHeight = Number(this.height)
      }
      this.htmlItems.push({
        title: this.getFormatTime(),
        type: 'normal',
        content: ['在组件的constructor里需要干些什么？', '在组件的其他方法中分别需要做哪些事情？', '有哪些可以直接调用的父类的原型方法？', '组件从注册到渲染到页面上的执行流程是怎样的？']
      })
      this.htmlItems.push({
        title: this.getFormatTime(),
        type: 'success',
        content: ['在组件的constructor里需要干些什么？', '在组件的其他方法中分别需要做哪些事情？', '有哪些可以直接调用的父类的原型方法？', '组件从注册到渲染到页面上的执行流程是怎样的？']
      })
      this.htmlItems.push({
        title: this.getFormatTime(),
        type: 'info',
        content: ['在组件的constructor里需要干些什么？', '在组件的其他方法中分别需要做哪些事情？', '有哪些可以直接调用的父类的原型方法？', '组件从注册到渲染到页面上的执行流程是怎样的？']
      })
      this.htmlItems.push({
        title: this.getFormatTime(),
        type: 'warning',
        content: ['在组件的constructor里需要干些什么？', '在组件的其他方法中分别需要做哪些事情？', '有哪些可以直接调用的父类的原型方法？', '组件从注册到渲染到页面上的执行流程是怎样的？']
      })
      this.htmlItems.push({
        title: this.getFormatTime(),
        type: 'error',
        content: ['在组件的constructor里需要干些什么？', '在组件的其他方法中分别需要做哪些事情？', '有哪些可以直接调用的父类的原型方法？', '组件从注册到渲染到页面上的执行流程是怎样的？']
      })

      this.$root.zpmConsole = function (args) {
        if (!args || !args.content) return
        let type = args.type || 'normal'
        if (!that.eleWrapperShown) {
          that.showZpmConsoleWrapper()
        }
        if (typeof args.content === 'string') {
          that.htmlItems.push({
            title: that.getFormatTime(),
            type: type,
            content: [args.content]
          })
          setTimeout(() => {
            that.scrollToEnd()
          }, 100)
        } else if (Object.prototype.toString.call(args.content) === '[object Array]') {
          that.htmlItems.push({
            title: that.getFormatTime(),
            type: type,
            content: args.content
          })
          setTimeout(() => {
            that.scrollToEnd()
          }, 100)
        } else {}
      }
    },
    methods: {
      getHeight (percent) {
        let _deviceInfo = weex.config.env
        let _per
        try {
          _per = parseFloat(percent)
        } catch (err) {
          _per = 1
        }
        let _h = 0
        if (_deviceInfo.platform.toLowerCase() === 'web') {
          _h = _deviceInfo.deviceHeight / _deviceInfo.dpr
        } else {
          _h = 750 / _deviceInfo.deviceWidth * _deviceInfo.deviceHeight
        }
        return parseFloat(_per) * _h
      },
      showZpmConsoleWrapper () {
        this.eleWrapperShown = true
      },
      hideZpmConsoleWrapper () {
        this.eleWrapperShown = false
      },
      toggleZpmConsoleWrapper () {
        if (this.eleWrapperShown) {
          this.hideZpmConsoleWrapper()
        } else {
          this.showZpmConsoleWrapper()
        }
      },
      getFormatTime (formatStr) {
        let _now = new Date()
        let _formatStr = formatStr || 'YYYY-MM-DD hh:mm:ss'
        return _formatStr.replace('YYYY', _now.getFullYear())
          .replace('MM', (_now.getMonth() + 1 < 10 ? '0' + (_now.getMonth() + 1) : (_now.getMonth() + 1)))
          .replace('DD', (_now.getDate() < 10 ? '0' + _now.getDate() : _now.getDate()))
          .replace('hh', (_now.getHours() < 10 ? '0' + _now.getHours() : _now.getHours()))
          .replace('mm', (_now.getMinutes() < 10 ? '0' + _now.getMinutes() : _now.getMinutes()))
          .replace('ss', (_now.getSeconds() < 10 ? '0' + _now.getSeconds() : _now.getSeconds()))
      },
      copyText (evt) {
        let _index = Number(evt.currentTarget.attr.dataIndex)
        clipboard.setString(this.htmlItems[_index].content.join('\n'))
        modal.toast({
          message: '复制成功'
        })
      },
      scrollToEnd () {
        let _lastEle = this.$refs['zpm-console-item-ref-' + (this.htmlItems.length - 1)]
        dom.scrollToElement(_lastEle[0], {})
      },
      clear () {
        this.htmlItems = []
      }
    },
    components: {},
    watch: {
      'eleWrapperShown': function (value) {
        let _eleWrapper = this.$refs[this.eleRef.wrapper]
        let _eleTrigger = this.$refs[this.eleRef.trigger]
//        animation.transition(_eleTrigger, {
//          styles: {
//            opacity: (value ? 0 : 1)
//          },
//          duration: 200,
//          delay: 0,
//          timingFunction: 'cubic-bezier(.215,.61,.355,1)'
//        })
        animation.transition(_eleWrapper, {
          styles: {
            transform: `translate(0px, ${(value ? '0px' : this.wrapperHeight + 'px')})`
          },
          duration: 300,
          delay: 0,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)'
        }, () => {
          if (value) {
            this.scrollToEnd()
          }
        })
      }
    },
    filters: {
      formatType: function (text) {
        let out = '输出'
        switch (text) {
          case 'normal':
            out = '输出'
            break
          case 'success':
          case 'ok':
            out = '成功'
            break
          case 'info':
            out = '提示'
            break
          case 'warning':
          case 'warn':
            out = '警告'
            break
          case 'error':
          case 'fail':
            out = '错误'
            break
          default:
            out = '输出'
            break
        }
        return out
      }
    }
  }
</script>
