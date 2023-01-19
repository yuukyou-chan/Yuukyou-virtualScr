# Yuukyou-virtualScroll
## 插件背景
在联想云实习的时候有同事遇到客户提的一个bug是长滚动列表太卡了。由于客户使用的老版管理控制台是基于Vue2.0，公司的Vue2.0组件库是并没有实现虚拟滚动列表，Vue3.0新版中实现了。
由于组件库庞大且复杂，这个问题也拖了很久了，同事给leader的方案是用将Vue3.0翻译成2.0，但是这个工程量可想而知。
于是我便尝试了自己封装虚拟滚动列表，这个插件便诞生了。
## 插件使用
你可以使用 ` npm i yuukyou-virtualscroll ` 命令进行进行插件安装
### 插件提供的接口
'''
    //所有获取数据列表
    allDataList: { default: () => [], type: Array },
    //上下环缓存区数目
    bufferSize: { default: () => 5, type: Number },
    //单行显示高度
    blockHeight: { default: () => 100, type: Number },
    //是否正在请求批量数据状态
    onRequesting: { default: () => true, type: Boolean },
    //数据加载显示的区域
    msg: { default: () => "小二正在努力，请耐心等待...", type: String }
'''
