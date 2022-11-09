## vue3

### Compositon API
#### 解决了什么问题？（优势 VS mixin）
+ data, methods, computed , watch 过于零散，一个功能需要分散到四个属性中查找，实现
+ mixin 存在命名冲突，来源不明，逻辑覆盖 等原因
#### setup
+ 执行顺序：setup（无this） ，beforeCreated（无props，data，methods），created（initProps， initMethods，initData 完成）
#### setup
+ 
#### setup