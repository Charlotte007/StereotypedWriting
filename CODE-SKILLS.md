### 对象中部分属性覆盖
+ object.assgin(target, data) 会导致vue中对象失去响应吗？（vue中待测试）
+ target = {...target, ...data} 会重新创建对象

### 接口请求时，获取对象中的部分属性
+ {...target, a: undefined, b: undefined} 覆盖无用数据，设置为undefined

### TODO： table-column 中每列数据中，无意义数据的占位符；（前提：没有slot）
+ 占位图
+ 名称，时间等默认 '- -'
+ 数字默认，0
+ 前置填充

### H5适配的考虑
+ 单位的使用，px, rpx, urpx , em, rem , vw , vh
+ 横屏/竖屏
+ 安全区
  + TabBar
  + StatusBar，自定义navbar时，状态栏的颜色
  + NavBar
+ 导航栏的适配
  + 页面内返回 OR 外部应用返回

### 异步与事件
+ 高频率事件，对异步请求的影响
  + 防抖
  + 数据对应状态；如 tab频繁切换，会不会对当前的列表造成影响，导致数据混淆

### 全局的数据，最好封装全局组件
  + 数据字典选项配置
    + 全局数据字典store
    + 全局的 <dict-select /> 根据code获取全部
    + 数据字典的变动，同步 store数据，保持全局一致