# 头豹报告编辑器

## backend

- bin
- bll
- conf
- models
  - article.js  reportId:Brief
  - attachment.js section:Section;brief:Brief
  - base.js
  - brief.js outline:Outline
  - element.js brief:Brief slide:Slide
  - eon.js brief:Brief
  - ill.js:
  - individual.js teamIds:Team
  - outline.js brief:Brief;  subjects: Outline;parent:Outline; section:Section;slide:Slide
  - quote.js
  - section.js brief:Brief;outline:Outline
  - size.js
  - sizePro.js
  - slide.js brief:Brief;outline:Outline;elements:Elements
  - table.js
  - team.js memberIds:Individual
- public
- routes
- utils
- views
- app.js
- echart.demo.js

## frontend

### 项目简介

头豹报告编辑器

### 技术栈

vue axions elementui echarts vue-router eventBus pug
非项目框架的npm包：@tinymce/tinymce-vue,core-js,font-awesome....

### 项目大致结构

- apis
- components
- filters
- js
- views
- main.js
- router.js

### 项目详情

- mian.js文件：
  - Vue.prototype.GLOBAL：配置全局变量，
  - Vue.config.productionTip 取消生产环境的提示
  - Vue.prototype.$echarts = echarts 配置echart为全局变量
  - Vue.use 使用element&eventBus
  - filter 全局定义过滤器，Vue.filter(key,filter[key])
  - Vue.prototype.handleResult 处理结果的函数

- router.js文件
  - originalPush ?
  - routes：

```js
/login com:Login
/preview com:Preview
/previewSlide com:PreviewSlide

/ com:Layout
=>redirect:/reports Layout布局 meta{title,icon,fa};ps：vue路由懒加载
  children:
    /reports,com:@/views/report/index ;
    /reports/report com:./views/layout/components/AppMain.vue
      children:/reports/report/brief;/reports/report/outline;xx/eon;xx/content;xx/slide;(hidden)

/size com:Layout
  children:
  /size/Size;com:@/views/size/Size
  /size/SizePro;com:@/views/size/SizePro
  /size/SizePro/From(hidden:true);com:@/views/size/components/index

/table com:Layout
  children:
    /table/index com:@/views/table/Table

/quote com:Layout
  children:
    /quote/index com:@/views/quote/Quote

/team com:Layout
  children:
    /team/team com:@/views/team/Team
    /team/individual com:@/views/team/Individual

/article com:Layout
  children:
    /article/index com:@/views/article/Article

/recycler com:Layout
  children:
    /recycler/index com:@/views/recycler/Recycler

path: "*" 放到最后匹配其他路由，即404页面

```

- App.vue
  - template: login(ref="login") router-view(v-if="isRouterAlive")

- apis
  - commuCore.js:通信核心。
    - 简介：本模块的设计核心思想何作用：对后端程序的接口调用操作；主要负责如：
  错误捕获，调用队列控制，身份信息绑定，通信配置，异常处理。
    - 个人理解：axios的全局基本设置（url/timeout）,请求拦截器(Authorization)，响应拦截器()

  ```js


  ```

  - apis.js 获取行业？返回对象：output.getIndustries = GetIndustries
