---
title: Chrome扩展插件开发（一）模块概述
date: 2024.1.25
updated: 2023.1.25
tags: 
- 前端 
- Chrome扩展程序
- Vue
categories: 
- 技术向
keywords:  
- Vue
- 前端
- Chrome扩展程序
description: IE,你的太阳落山了
toc: true
toc_number: false
katex: true
top_img: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/Opera_System/202401251729975.jpeg
cover: https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/Opera_System/202401251714162.jpg
---

## 写在前面

Chrome插件是一种用于增强Google Chrome浏览器功能的小型程序。它们允许开发者使用HTML、CSS、JavaScript等Web技术来创建能够修改浏览器行为、添加新功能或与网页交互的扩展。我将以自己花了两天写的一个小项目为例，在接下来的几篇博客中介绍一个插件的开发过程和踩坑经历。

这篇博客主要参考自[Google官方教程](https://developer.chrome.com/docs/extensions?hl=zh-cn)和[2023金秋版：基于Vite4+Vue3的Chrome插件开发教程](https://zhuanlan.zhihu.com/p/651319607)，在此表示感谢。

项目开源仓库地址在[这里](https://github.com/ZYL-DF/glados-daily-checkin)，欢迎品鉴

> IE，你的太阳落山了

---

## Chrome插件的基本构成

chrome插件由以下几部分构成：

- manifest.json - 配置文件
- popup - 点击插件按钮弹出页面
- content script - 插入目标页面执行脚本
- service worker script - 在Chrome后台一直运行的脚本



### manifest.json

manifest.json必须放在插件项目根目录，里面包含了插件的各种配置信息，其中也包括了popup、content script、service worker script等文件的存放路径。

> 说到这里，本次介绍的插件版本为Manifest V3，是最新版扩展程序平台，提供了支持Promise，Service Worker等更加便利的方法。



### popup

作为一个独立的弹出页面，有自己的html、css、js，可以按照常规项目来开发。



### content script

content script是注入到目标页面中执行的js脚本，可以获取目标页面的Dom并进行修改。但是，content script的JavaScript与目标页面是互相隔离的。也就是说，content script与目标页面的JavaScript不会出现互相污染的问题，同时，也不能调用对方的方法。

注意，以上只是js作用域的隔离，通过content script向目标页面加入的DOM可以应用目标页面的css，从而造成css互相污染。



### service worker script

service worker script 常驻在浏览器后台Service Workers运行，没有实际页面。一般把全局的、需要一直运行的代码放在这里。重要的是，service worker script的权限非常高，除了可以调用几乎所有Chrome Extension API外，**还可以发起跨域请求**。

> 在Manifest V2中，这个部分被称为background script，似乎更能从字面含义上理解。**本文之后对这部分的介绍也使用background script的称谓**。
>
> 与service worker（V3）最大的区别是，V2的background会一直在后台运行，这无疑会占用部分资源。V3的service worker仅在需要的时候运行。



---

## 项目构建与基本结构

通过以上介绍，可以认识到一个Chrome插件需要manifest.json 、popup、content script、service worker script四个部分组成。实际上，这与普通网页的开发差别不大。

这次，我们使用`Vite4`+`Vue3`来进行开发，前置要求是安装`nodeJS`。同时，使用`ant-design-vue`作为UI库，使用`WebStorm`作为开发工具。



### Vite项目创建

先进入想要创建项目的目录，在这个目录下执行安装命令。

如果使用`npm`，执行：

```shell
npm create vite@latest
```

执行后，会要求填写项目名称

```shell
Project name: glados-daily-checkin
```

然后，会要求选择框架，选择`Vue`：

```shell
? Select a framework: » - Use arrow-keys. Return to submit.
    Vanilla
>   Vue
    React
    Preact
    Lit
    Svelte
    Solid
    Qwik
    Others
```

最后，选择开发语言，本教程选择TypeScript：

```shell
? Select a variant: » - Use arrow-keys. Return to submit.
>   TypeScript
    JavaScript
    Customize with create-vue ↗
    Nuxt ↗
```

如果一切正常，此时我们便完成了一个`vite`项目的创建。



查看和运行创建好的项目：

```shell
cd glados-daily-checkin
npm install
npm run dev
```

打开以下网址就可以看到我们创建好的项目：

```text
http://localhost:5173/
```



<img src="https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/Opera_System/202401251433223.png" alt="image-20240125143351151" style="zoom:67%;" />

---

### ant-design-vue 引入

本项目使用`ant-design-vue`作为UI库，因此需要引入：

```shell
npm install ant-design-vue@4.x --save
```



#### 自动按需引入组件

[unplugin-vue-components](https://github.com/antfu/unplugin-vue-components) 

如果你使用的是 `Vite` ，推荐使用 `unplugin-vue-components`

```bash
$ npm install unplugin-vue-components -D
// vite.config.js
import { defineConfig } from 'vite';
import Components from 'unplugin-vue-components/vite';
import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers';
export default defineConfig({
  plugins: [
    // ...
    Components({
      resolvers: [
        AntDesignVueResolver({
          importStyle: false, // css in js
        }),
      ],
    }),
  ],
});
```

然后你可以在代码中直接引入 `ant-design-vue` 的组件，插件会自动将代码转化为 `import { Button } from 'ant-design-vue'` 的形式。

```jsx
import { Button } from 'ant-design-vue';
```



---

###  Chrome插件基本结构配置

上述步骤创建得到的文件结构应该是这样的

```
glados-daily-checkin
├── README.md
├── index.html
├── package-lock.json
├── package.json
├── public
│   └── vite.svg
├── src
│   ├── App.vue
│   ├── assets
│   │   └── vue.svg
│   ├── components
│   │   └── HelloWorld.vue
│   ├── main.ts
│   ├── style.css
│   └── vite-env.d.ts
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```



为了适应插件的开发，可以将这个结构先简化为这样:

```
glados-daily-checkin
├── README.md
├── index.html
├── package-lock.json
├── package.json
├── public
│   └── favicon.ico
├── src
│   ├── App.vue
│   ├── main.ts
│   └── vite-env.d.ts
├── tsconfig.json
├── tsconfig.node.json
└── vite.config.ts
```

> vite-env.d.ts是vite的环境变量配置，可根据实际项目需求选择是否删除



当然，此时项目会因为缺少被删除的文件而报错，需要进行以下修改：

#### App.vue

```vue
<template>
Anyway the wind blows, doesn't really matter to me
</template>

<script setup lang="ts">

</script>

<style scoped>

</style>
```



#### index.html

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Glados Daily Checkin</title>
  </head>
  <body>
    <div id="app"></div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>

```

> 注意link中的/favicon.ico，他应该放在public文件夹下，是一个自己添加的图标或矢量文件



#### main.ts

```typescript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```



重新运行项目，可以看到页面发生了变更

![image-20240125151203663](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/Opera_System/202401251512698.png)

---

现在让我们对结构进行魔改，以适应插件开发的需要：

```
glados-daily-checkin
├── README.md
├── components.d.ts
├── globalConfig.js
├── index.html
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   ├── images
│   │   └── app.png
│   └── manifest.json
├── src
│   ├── background
│   │   └── index.js
│   ├── content
│   │   ├── index.css
│   │   └── index.js
│   └── popup
│       ├── main.ts
│       └── popup.vue
├── tsconfig.json
├── tsconfig.node.json
├── vite.background.config.ts
├── vite.content.config.ts
└── vite.popup.config.ts
```



变化最大的是src文件夹。在其中创建了三个文件夹background、content、popup，与插件的三个部分对应。注意每个模块都有对应的index.js，而popup文件夹下是以main.ts+popup.vue的文件形式出现的，这意味着我们实际上在popup文件夹中创建了一个vue项目。事实上，在三个文件夹中都可以分别独立地创建对应的vue文件以模块化开发。

那么决定插件各模块入口的文件，也就是manifest.json，被我放置在了public中。manifest.json文件内容如下，实际开发时需要去掉注释：

```json
{
    "name": "Glados自动签到插件",
    // 插件名字（必要）
    "version": "1.0",
    // 插件版本（必要）
    "description": "支持浏览器启动时自动签到,查看用户当前点数与剩余天数,若签到失败请重新登录一次",
    // 插件描述（必要）
    "manifest_version": 3,
    // manifest版本（必要）
    "background": {
        // service worker指定入口文件 （根目录为最终build生成的插件包目录）
        "service_worker": "background.js"
    },
    "content_scripts": [
        // content指定入口文件 （根目录为最终build生成的插件包目录）
    ],
    "permissions": ["storage","declarativeContent","cookies","notifications"], // 权限控制（这里开启了储存，无需权限根据网页内容采取行动，cookies，提醒框四个权限）
    "host_permissions":["<all_urls>"],
    // 插件涉及的外部请求地址（这里允许全部网页应用该扩展）
    
    "web_accessible_resources": [
        // 如果向目标页面插入图片或者js，需要在这里授权插件本地资源（以下仅为示例）
        {
            "resources": [ "/images/app.png" ],
            "matches": ["<all_urls>"]
        }
    ],
    "content_security_policy": {
        // content插入安全控制
        "extension_pages": "script-src 'self'; object-src 'self'"
    },
    // popup页面配置
    "action": {
        // popup页面的路径（根目录为最终build生成的插件包目录）
        "default_popup": "index.html",
        "default_icon": {
            // 图标（分尺寸）
            "16": "/images/app.png",
            "32": "/images/app.png",
            "48": "/images/app.png",
            "128": "/images/app.png"
        },
        // 浏览器插件按钮hover显示的文字
        "default_title": "Glados-Daily-Checkin"
    },
    "icons": {
        // 图标（分尺寸）
        "16": "/images/app.png",
        "32": "/images/app.png",
        "48": "/images/app.png",
        "128": "/images/app.png"
    }
}

```



可以发现，配置文件中三个模块的入口文件与我们现在的文件名称和位置均有不同。这主要是因为最后build出来的文件包并没有模块文件夹的划分，而是以独立的js文件存在。同样的，vue文件也会在最后变成不同的文件，与我们开发时的文件结构有所不同。build文件结构如下：

```
build
├── assets
│   ├── index-siNZXYyQ.css
│   └── index-ty4cvEqm.js
├── background.js
├── content.css
├── content.js
├── favicon.ico
├── images
│   └── app.png
├── index.html
└── manifest.json
```



对比可以得出，文件的转换逻辑如下：

```
[popup]
src/popup/main.ts + popup.vue => assets/index-siNZXYyQ.css + index-ty4cvEqm.js

[content]
src/content/index.js + index.css => content.js + content.css

[background]
src/background/index.js => background.js

[public]
文件夹中文件均移动至根目录下
```





因此，我们需要对vite的输出配置进行更改，这样在最后build时才能将各个模块文件夹中的文件更名整合到一起：

#### globalConfig.ts

这个ts文件主要用于定义目录名

```typescript
// Chrome Extension 最终build目录
export const CRX_OUTDIR = 'build'
// 临时build content script的目录
export const CRX_CONTENT_OUTDIR = '_build_content'
// 临时build background script的目录
export const CRX_BACKGROUND_OUTDIR = '_build_background'

```



#### vite.popup.config.ts

```typescript
import {defineConfig} from 'vite'
import vue from '@vitejs/plugin-vue'
import path from "path"
// @ts-ignore
import {CRX_OUTDIR} from "./globalConfig.js";
import Components from 'unplugin-vue-components/vite';
import { AntDesignVueResolver } from 'unplugin-vue-components/resolvers';

// https://vitejs.dev/config/
export default defineConfig({
    build: {
      outDir: CRX_OUTDIR // build输出目录
    },
    server: {
        port: 10086,
    },
    resolve: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
        }
    },
    plugins: [
        vue(),
        Components({
            resolvers: [
                AntDesignVueResolver({
                    importStyle: false, // css in js
                }),
            ],
        }),
    ],
})

```



#### vite.content.config.ts

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
// @ts-ignore
import path from 'path'
import { CRX_CONTENT_OUTDIR } from './globalConfig.js'

// https://vitejs.dev/config/
export default defineConfig({
    build: {
        // 输出目录
        outDir: CRX_CONTENT_OUTDIR,
        lib: {
            entry: [path.resolve(__dirname, 'src/content/index.js')],
            // content script不支持ES6，因此不用使用es模式，需要改为cjs模式
            formats: ['cjs'],
            // 设置生成文件的文件名
            fileName: () => {
                // 将文件后缀名强制定为js，否则会生成cjs的后缀名
                return 'content.js'
            },
        },
        rollupOptions: {
            output: {
                assetFileNames: (assetInfo) => {
                    // 附属文件命名，content script会生成配套的css
                    return 'content.css'
                },
            },
        },
    },
    resolve: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
        },
    },
    // 解决代码中包含process.env.NODE_ENV导致无法使用的问题
    define: {
        'process.env.NODE_ENV': null,
    },
    plugins: [vue()],
})
```



#### vite.background.config.ts

```typescript
import {defineConfig} from 'vite'
import vue from '@vitejs/plugin-vue'
// @ts-ignore
import path from "path"
import {CRX_BACKGROUND_OUTDIR} from './globalConfig.js'


// https://vitejs.dev/config/
export default defineConfig({
    build: {
        outDir: CRX_BACKGROUND_OUTDIR,
        lib: {
            entry: [path.resolve(__dirname, 'src/background/index.js')],
            // background script不支持ES6，因此不用使用es模式，需要改为cjs模式
            formats: ['cjs'],
            // 设置生成文件的文件名
            fileName: () => {
                // 将文件后缀名强制定为js，否则会生成cjs的后缀名
                return 'background.js'
            }
        },
    },
    resolve: {
        alias: {
            '@': path.resolve(__dirname, 'src'),
        },
    },

    plugins: [vue()],
})
```



#### build.js

这个脚本文件用于将临时生成的文件进行最终合并

```typescript
import fs from 'fs'
// @ts-ignore
import path from 'path'
import { CRX_OUTDIR, CRX_CONTENT_OUTDIR, CRX_BACKGROUND_OUTDIR } from './globalConfig.js'

// 拷贝目录文件
const copyDirectory = (srcDir, destDir) => {
    // 判断目标目录是否存在，不存在则创建
    if (!fs.existsSync(destDir)) {
        fs.mkdirSync(destDir)
    }

    fs.readdirSync(srcDir).forEach((file) => {
        const srcPath = path.join(srcDir, file)
        const destPath = path.join(destDir, file)

        if (fs.lstatSync(srcPath).isDirectory()) {
            // 递归复制子目录
            copyDirectory(srcPath, destPath)
        } else {
            // 复制文件
            fs.copyFileSync(srcPath, destPath)
        }
    })
}

// 删除目录及文件
const deleteDirectory = (dir) => {
    if(fs.existsSync(dir)) {
        fs.readdirSync(dir).forEach((file) => {
            const curPath = path.join(dir, file)
            if (fs.lstatSync(curPath).isDirectory()) {
                // 递归删除子目录
                deleteDirectory(curPath)
            } else {
                // 删除文件
                fs.unlinkSync(curPath)
            }
        })
        // 删除空目录
        fs.rmdirSync(dir)
    }
}

// 源目录：content script临时生成目录
const contentOutDir = path.resolve(process.cwd(), CRX_CONTENT_OUTDIR)
// 源目录：background script临时生成目录
const backgroundOutDir = path.resolve(process.cwd(), CRX_BACKGROUND_OUTDIR)
// 目标目录：Chrome Extension 最终build目录
const outDir = path.resolve(process.cwd(), CRX_OUTDIR)
// 将复制源目录内的文件和目录全部复制到目标目录中
copyDirectory(contentOutDir, outDir)
copyDirectory(backgroundOutDir, outDir)
// 删除源目录
deleteDirectory(contentOutDir)
deleteDirectory(backgroundOutDir)
```



#### popup/main.ts

以上配置完成后，对popup文件夹内main.ts文件进行调整，让它能够使用popup.vue文件：

```typescript
import { createApp } from 'vue'
// @ts-ignore
import Popup from "@/popup/popup.vue";

const app = createApp(Popup)
app.mount('#app')
```



#### index.html

然后，需要修改根目录下的index.html文件，让他指向popup文件夹的main.ts文件，也就是将popup模块作为插件总入口：

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Glados Daily Checkin</title>
  </head>
  <body style="margin: 0">
    <div id="app"></div>
    <script type="module" src="/src/popup/main.ts"></script>
  </body>
</html>
```



最后，修改package.json文件中的script设置，让各个文件联动打包：

#### package.json

```json
{
  ...
  "scripts": {
    "dev": "vite -c vite.popup.config.ts",
    "build": "vite build -c vite.popup.config.ts && vite build -c vite.content.config.ts && vite build -c vite.background.config.ts && node build.js",
  },
  ...
}
```



现在，项目基本配置完成。但你可能会疑惑dev为什么只对popup模块生效。这是因为manifest V3版本从安全性考虑禁止了插件包的页面热更新。也就是说，每次都需要build才能更新一次插件。这对于popup这种需要调试css、html的实际页面来说无疑是非常拖后腿的。因此，可以单独开启对popup的dev构建选项，此时可以直接在浏览器页面预览和调试popup页面。

那么更大的疑惑来了：content作为插入页面的模块，也需要时刻调试，他怎么实现热更新呢？

考虑到content作为脚本，可以通过以下的方式在popup.vue中调用：

```
<script setup>
// 在popup页面调试content script，仅用于开发环境，build前记得要注释掉。
import '@/content'
</script>
```



如果一切顺利，你可以使用`npm run dev`运行项目，使用以下的命令构建最终程序包：

```shell
npm run build
```



---

## 开发辅助工具

这里介绍可以提高效率和代码质量的几个工具



###  chrome-types

如果你使用 VSCode 或 WebStorm 等代码编辑器进行开发，可以通过`npm`软件包 [chrome-types](https://www.npmjs.com/package/chrome-types) 来利用完成自动填充与类型检查功能。当 Chromium 源代码发生更改时，此 npm 软件包会自动更新。

```shell
npm i chrome-types
```



如果你使用WebStorm进行开发，可以直接在WebStorm - Settings - Language & Frameworks - JavaScript - Libraries 下安装chrome库即可

![img](https://ichirinko-blog-img-1.oss-cn-shenzhen.aliyuncs.com/Pic_res/Opera_System/202401251703092.png)



### Vue Devtools

Vue 官方发布的调试浏览器插件，可以安装在 Chrome 和 Firefox 等浏览器上，直接内嵌在开发者工具中，使用体验流畅。

可以在[官网](https://devtools.vuejs.org/guide/installation.html)下载安装

> 不多说了，自己试试看就知道了



---

## 总结

这篇博客详细介绍了一个Chrome扩展程序开发的项目配置与构建细节，这只是踏出的第一步。下一篇博客将具体阐述popup模块的开发过程，以及目标网站的请求处理，敬请期待。