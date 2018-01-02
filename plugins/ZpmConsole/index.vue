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
                    <text class="header_left_tip_text">长按有惊喜</text>
                </div>
                <div class="header_right" @click="hideZpmConsoleWrapper">
                    <text class="header_right_text">&times;</text>
                </div>
            </div>
            <div class="zpm_console_wrapper_content" :style="{height: (wrapperHeight - 100) + 'px'}">
                <scroller v-if="htmlItems.length > 0">
                    <refresh class="copyright_container">
                        <text class="copyright_text">powered by &copy;ZpmConsole</text>
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
                        <div class="zpm_console_item_content" :class="['zpm_console_item_content_border_' + item.type]" :data-index="index" @longpress="longPressHandler">
                            <text class="zpm_console_item_content_text" v-for="(itm, idx) in item.content" :key="itm">{{itm}}</text>
                        </div>
                    </div>
                    <div style="width: 750px; height: 90px;"></div>
                </scroller>
            </div>
        </div>

        <div class="zpm_console_menu_container" :style="{height: getHeight(1) + 'px', transform: `translate(0px, ${eleMenuShown ? '0px' : getHeight(1) + 'px'})`}" @click="hideMenu">
            <div class="zpm_console_menu" :ref="eleRef.menu" :style="{transform: `scale(${menuScale})`}">
                <div class="zpm_console_menu_item" v-for="(item, index) in menuItems" :class="['zpm_console_menu_item_border_' + (index != 0)]" :data-action="item.action" @click="menuItemHandler">
                    <text class="zpm_console_menu_item_text">{{item.name}}</text>
                </div>
            </div>
        </div>

        <div class="zpm_console_format_data_container" :style="{height: getHeight(1) + 'px', transform: `translate(0px, ${eleFormatShown ? '0px' : getHeight(1) + 'px'})`}">
            <div class="zpm_console_format_data_wrapper" :style="{height: (getHeight(1) - 150) + 'px', transform: `translate(0px, ${formatY}px)`}" :ref="eleRef.format">
                <div class="zpm_console_format_data_header">
                    <div class="zpm_console_format_data_header_left">
                        <div class="zpm_console_item_title_tag" :class="['zpm_console_item_title_tag_' + formatData.type]">
                            <text class="zpm_console_item_title_tag_text">{{formatData.type | formatType}}</text>
                        </div>
                        <text class="zpm_console_item_title_text">{{formatData.title}}</text>
                        <!--<text class="zpm_console_format_data_header_left_text">{{formatData.title}}</text>-->
                    </div>
                    <div class="zpm_console_format_data_header_close" @click="hideFormatContainer">
                        <text class="zpm_console_format_data_header_close_text">&times;</text>
                    </div>
                </div>
                <div class="zpm_console_format_data_content" :style="{height: (getHeight(1) - 91 - 150) + 'px'}">
                    <scroller>
                        <div class="zpm_console_format_data_content_inner">
                            <text class="zpm_console_format_data_content_inner_text" v-for="(itm, idx) in formatData.content" :key="itm">{{itm}}</text>
                        </div>
                    </scroller>
                </div>
            </div>
        </div>
    </div>
</template>
<style scoped>
    /*.zpm_console_container {*/
    /*position: fixed;*/
    /*left: 0;*/
    /*bottom: 0;*/
    /*width: 750px;*/
    /*pointer-events: none;*/
    /*}*/
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
        width: 500px;
        text-overflow: ellipsis;
        lines: 1;
        overflow: hidden;
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
    }
    .zpm_console_item_content_border_normal {
        border-left-width: 8px;
        border-left-style: solid;
        border-left-color: rgba(0, 0, 0, 0.9);
    }
    .zpm_console_item_content_border_normal:active {
        background-color: rgba(0, 0, 0, 0.1);
    }
    .zpm_console_item_content_border_success {
        border-left-width: 8px;
        border-left-style: solid;
        border-left-color: rgba(92, 184, 92, 0.9);
    }
    .zpm_console_item_content_border_success:active {
        background-color: rgba(92, 184, 92, 0.1);
    }
    .zpm_console_item_content_border_info {
        border-left-width: 8px;
        border-left-style: solid;
        border-left-color: rgba(91, 192, 222, 0.9);
    }
    .zpm_console_item_content_border_info:active {
        background-color: rgba(91, 192, 222, 0.1);
    }
    .zpm_console_item_content_border_warning {
        border-left-width: 8px;
        border-left-style: solid;
        border-left-color: rgba(240, 173, 78, 0.9);
    }
    .zpm_console_item_content_border_warning:active {
        background-color: rgba(240, 173, 78, 0.1);
    }
    .zpm_console_item_content_border_error {
        border-left-width: 8px;
        border-left-style: solid;
        border-left-color: rgba(217, 83, 79, 0.9);
    }
    .zpm_console_item_content_border_error:active {
        background-color: rgba(217, 83, 79, 0.1);
    }
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

    .zpm_console_menu_container {
        position: fixed;
        left: 0;
        top: 0;
        width: 750px;
        flex-direction: row;
        align-items: center;
        justify-content: center;
    }
    .zpm_console_menu {
        width: 300px;
        background-color: #ffffff;
        border-radius: 4px;
        border-width: 1px;
        border-color: rgba(169,134,255,0.38);
        border-style: solid;
        /*border-top-width: 10px;*/
    }
    .zpm_console_menu_item {
        width: 300px;
        height: 90px;
        padding-left: 20px;
        padding-right: 20px;
        background-color: #ffffff;
        flex-direction: row;
        align-items: center;
        justify-content: flex-start;
    }
    .zpm_console_menu_item_border_true {
        border-top-width: 1px;
        border-top-color: rgba(169,134,255,0.38);
        border-top-style: solid;
    }
    .zpm_console_menu_item:active {
        background-color: rgba(169,134,255,0.38);
    }
    .zpm_console_menu_item_text {
        /*width: 300px;*/
        /*height: 90px;*/
        /*line-height: 90px;*/
        font-size: 26px;
        color: #333333;
    }
    /*.zpm_console_menu_item_text:active {*/
    /*color: #FFFFFF;*/
    /*}*/
    .zpm_console_format_data_container {
        position: fixed;
        left: 0;
        top: 0;
        width: 750px;
        flex-direction: row;
        /*align-items: center;*/
        justify-content: center;
    }
    .zpm_console_format_data_wrapper {
        margin-top: 50px;
        width: 720px;
        border-width: 1px;
        border-color: rgba(102, 51, 153, 0.38);
        border-style: solid;
        border-radius: 4px;
        background-color: #ffffff;
    }
    .zpm_console_format_data_header {
        width: 720px;
        height: 90px;
        padding-left: 20px;
        padding-right: 20px;
        border-bottom-width: 1px;
        border-bottom-color: rgba(102, 51, 153, 0.38);
        border-bottom-style: solid;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
    }
    .zpm_console_format_data_header_left {
        width: 550px;
        height: 90px;
        flex-direction: row;
        align-items: center;
        justify-content: flex-start;
    }
    .zpm_console_format_data_header_left_text {
        font-size: 28px;
        width: 550px;
        text-overflow: ellipsis;
        overflow: hidden;
        lines: 1;
    }
    .zpm_console_format_data_header_close {
        width: 90px;
        height: 90px;
        flex-direction: row;
        align-items: center;
        justify-content: flex-end;
        margin-top: -12px;
    }
    .zpm_console_format_data_header_close:active {
        opacity: 0.7;
    }
    .zpm_console_format_data_header_close_text {
        font-size: 60px;
        font-weight: 200;
    }
    .zpm_console_format_data_content {
        width: 720px;
    }
    .zpm_console_format_data_content_inner {
        width: 720px;
        margin-top: 30px;
        margin-bottom: 30px;
        padding-left: 10px;
        padding-right: 10px;
    }
    .zpm_console_format_data_content_inner_text {
        font-size: 28px;
        line-height: 36px;
        color: #888888;
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
          trigger: 'ele-ref-trigger',
          menuContainer: 'ele-ref-menu-container',
          menu: 'ele-ref-menu',
          format: 'ele-ref-format'
        },
        eleWrapperShown: false,
        eleMenuShown: false,
        eleFormatShown: false,
        wrapperY: 0,
        wrapperHeight: 800,
        menuScale: 0,
        stickyEle: false,
        formatY: 0,
        htmlItems: [],
        currentItem: {},
        actionKeys: {
          jsonParse: 'JSON_PARSE',
          textCopy: 'TEXT_COPY'
        },
        allMenuItems: [
          {
            name: 'JSON格式化',
            action: 'JSON_PARSE'
          },
          {
            name: '复制文本',
            action: 'TEXT_COPY'
          }
        ],
        menuItems: [],
        formatData: {
          title: '',
          type: 'normal',
          content: []
        }
      }
    },
    computed: {
      actions: function () {
        return {
          jsonParse: {
            name: 'JSON格式化',
            action: this.actionKeys.jsonParse
          },
          textCopy: {
            name: '复制文本',
            action: this.actionKeys.textCopy
          }
        }
      }
    },
    created () {
      const that = this
      if (this.height && !isNaN(this.height)) {
        this.wrapperHeight = Number(this.height)
      }
      this.menuItems = this.allMenuItems

      this.$root.zpmConsole = function (args) {
        try {
          if (!args || !args.content) {
          } else {
            let type = args.type || 'normal'
            if (!that.eleWrapperShown) {
              that.showZpmConsoleWrapper()
            }
            if (typeof args.content === 'string') {
              that.htmlItems.push({
                title: args.title || that.getFormatTime(),
                type: type,
                content: [args.content]
              })
//              setTimeout(() => {
//                that.scrollToEnd()
//              }, 100)
            } else if (Object.prototype.toString.call(args.content) === '[object Array]') {
              that.htmlItems.push({
                title: args.title || that.getFormatTime(),
                type: type,
                content: that.formatContent(args.content)
              })
//              setTimeout(() => {
//                that.scrollToEnd()
//              }, 100)
            } else {}
          }
        } catch (err) {
          that.htmlItems.push({
            title: args.title || that.getFormatTime(),
            type: 'error',
            content: [JSON.stringify(err)]
          })
        }
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
      copyText (args) {
        clipboard.setString(args.content.join('\n'))
        modal.toast({
          message: '复制成功'
        })
      },
      parseJson (obj) {
        return JSON.stringify(obj, null, '\t')
      },
      scrollToEnd () {
        let _lastEle = this.$refs['zpm-console-item-ref-' + (this.htmlItems.length - 1)]
        dom.scrollToElement(_lastEle[0], {})
      },
      clear () {
        this.htmlItems = []
      },
      showMenu () {
        this.eleMenuShown = true
      },
      hideMenu () {
        this.eleMenuShown = false
      },
      showFormatContainer () {
        this.eleFormatShown = true
      },
      hideFormatContainer () {
        this.eleFormatShown = false
      },
      findItemByActionName (actionName) {
        if (!actionName) return -1
        let _allMenuItems = JSON.parse(JSON.stringify(this.allMenuItems))
        let i = 0
        let outIndex = -1
        for (i; i < _allMenuItems.length; i++) {
          if (String(_allMenuItems[i].action) === String(actionName)) {
            outIndex = i
            i = _allMenuItems.length
          }
        }
        return outIndex
      },
      longPressHandler (evt) {
        let _index = Number(evt.currentTarget.attr.dataIndex)
        this.currentItem = this.htmlItems[_index]
        let _jsonTypeCount = this.formatJsonContent(this.currentItem.content, true)
        this.menuItems = []
        if (_jsonTypeCount > 0) {
          this.menuItems.push(this.actions.jsonParse)
        }
        this.menuItems.push(this.actions.textCopy)
        this.showMenu()
      },
      menuItemHandler (evt) {
        this.hideMenu()
        let _action = evt.currentTarget.attr.dataAction
        switch (_action) {
          case this.actionKeys.textCopy:
            // 复制文本
            this.copyText({
              content: this.currentItem.content
            })
            break
          case this.actionKeys.jsonParse:
            // JSON格式化
            try {
              this.formatData = {
                type: this.currentItem.type,
                title: this.currentItem.title,
                content: this.formatJsonContent(this.currentItem.content)
              }
            } catch (err) {
              this.htmlItems.push({
                title: '错误',
                type: 'error',
                content: [JSON.stringify(err)]
              })
            }
            this.showFormatContainer();
            break
          default:
            break
        }
      },
      isJson (str) {
        if (!str) return false
        let objString = Object.prototype.toString
        if (objString.call(str) === '[object Object]' || objString.call(str) === '[object Array]') return true
        if (typeof str !== 'string') return false
        try {
          return JSON.parse(str)
        } catch (err) {
          return false
        }
      },
      formatContent (content) {
        // 过滤content中的null、undefined等空值
        let _content = JSON.parse(JSON.stringify(content))
        let i = 0
        for (i; i < _content.length; i++) {
          if (!_content[i]) {
            _content[i] = JSON.stringify(_content[i])
          }
        }
        return _content
      },
      formatJsonContent (content, count) {
        // count: true，只统计格式合法的个数
        // count: false, 格式化所有数据
        let i = 0
        let outContent = []
        let outCount = 0
        for (i; i < content.length; i++) {
          if (this.isJson(content[i])) {
            if (!count) {
              outContent.push(this.parseJson(JSON.parse(content[i])))
            } else {
              outCount += 1
            }
          } else {
            if (!count) {
              outContent.push('【无法解析的数据格式】')
            }
          }
          if (!count) {
            outContent.push('============================')
          }
        }
        if (!count) {
          outContent.splice(-1)
        }
        return (count ? Number(outCount) : outContent)
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
//          if (value) {
//            this.scrollToEnd()
//          }
        })
      },
      'eleMenuShown': function (value) {
        let _eleMenu = this.$refs[this.eleRef.menu]
        this.menuScale = (value ? 1.2 : 0)
        animation.transition(_eleMenu, {
          styles: {
            transform: `scale(${this.menuScale})`,
            transformOrigin: 'center center'
          },
          duration: 150,
          delay: 0,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)'
        }, () => {
          if (value) {
            this.menuScale = 1
            animation.transition(_eleMenu, {
              styles: {
                transform: `scale(${this.menuScale}, ${this.menuScale})`,
                transformOrigin: 'center center'
              },
              duration: 250,
              delay: 0,
              timingFunction: 'cubic-bezier(.215,.61,.355,1)'
            })
          }
        })
      },
      'eleFormatShown': function (value) {
        let _eleFormat = this.$refs[this.eleRef.format]
        animation.transition(_eleFormat, {
          styles: {
            transform: `translate(0px, ${value ? 0 : this.getHeight(1) + 'px'})`,
            transformOrigin: 'center bottom'
          },
          duration: 300,
          delay: 0,
          timingFunction: 'cubic-bezier(.215,.61,.355,1)'
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
