# panel

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

## 重要文件
+ `src/router/index.js`: 页面路由逻辑
+ `src/store/index.js`: Vuex 状态控制逻辑，跨页面的数据要由 Vuex 共享
+ `src/views/*.vue`: 每个页面的源码
  + `LabelSpace.vue`: TSNE 可视化页面
  + `Groupview.vue`: Heatmap 页面