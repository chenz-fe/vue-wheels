# vue-wheels

[toc]

## MiniVueRouter

这是一个超级精简版的VueRouter，实现hash模式下，hash改变组件切换的功能，原理就是利用了 Vue.js 的响应式机制触发router-view组件的重新渲染。
后续可能还会扩充一些功能，例如插件安装方法，路由钩子函数，增加history模式支持等。

### 代码
https://github.com/dora-zc/vue-wheels/tree/master/MiniVueRouter


### 目录结构

.
├── index.html
└── myVueRouter.js

路由在模板中的用法应该是下面这样：

```html
<!-- index.html -->

<div id="app">
   <router-link to="#/">Home</router-link>
   <router-link to="#/book">Book</router-link>
   <router-link to="#/movie">Movie</router-link>
   <router-view></router-view>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script src="./myVueRouter.js"></script>
```

这就需要通过render函数动态生成router-link和router-view两个组件。

js中的调用方法是这样：

```js
// index.html
const Home = {
  template: '<div>这是Home组件</div>'
}
const Book = {
  template: '<div>这是Book组件</div>'
}
const Movie = {
  template: '<div>这是Movie组件</div>'
}

const routes = [
  {
    path: '/',
    component: Home
  }, {
    path: '/book',
    component: Book
  }, {
    path: '/movie',
    component: Movie
  }
];

const router = new VueRouter(Vue, {routes});

new Vue({el: '#app', router});
```

下面是自己实现的超级迷你小路由：

```js
// myVueRouter.js
class VueRouter {
  constructor(Vue, options) {
    this.$options = options;
    this.routeMap = {};
    this.app = new Vue({
      data: {
        current: '#/'
      }
    });

    this.init();
    this.createRouteMap(this.$options);
    this.initComponent(Vue);
  }

  // 初始化 hashchange
  init() {
    window.addEventListener('load', this.onHashChange.bind(this), false);
    window.addEventListener('hashchange', this.onHashChange.bind(this), false);
  }

  // 创建路由映射表
  createRouteMap(options) {
    options
      .routes
      .forEach(item => {
        this.routeMap[item.path] = item.component;
      });
  }

  // 注册组件
  initComponent(Vue) {
    Vue.component('router-link', {
      props: {
        to: String
      },
      template: '<a :href="to"><slot></slot></a>'
    });

    const _this = this;
    Vue.component('router-view', {
      render(h) {
        var component = _this.routeMap[_this.app.current];
        return h(component);
      }
    });
  }

  // 获取当前 hash 串
  getHash() {
    return window
      .location
      .hash
      .slice(1) || '/';
  }

  // 设置当前路径
  onHashChange() {
    this.app.current = this.getHash();
  }
}
```


