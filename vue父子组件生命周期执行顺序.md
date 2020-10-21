# vue 生命周期

- beforeCreate 创建 dom 前执行

- created 创建 dom 后执行

- beforeMount 挂载前执行

- computed 计算属性（不是生命周期函数, 但是这里会先执行， 以提供数据给子组件）

- sub component 子组件创建挂载(递归以上流程)

- mounted 挂载后执行

- routeView 仅作为普通 dom 挂载, 父组件挂载结束后， routeView 跳转的组件才会开始执行生命周期
