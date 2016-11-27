<div align="center">
  <h3 style="color:#48abd6;font-weight:600;">F B I</h3>
  <p style="font-size:16px;">Node.js 工作流工具</p>
</div>

> 要求: node v6.9.1+, npm v3.10+

[English Version](./README.md)

## 特性

* 任务托管 - 将js文件托管为全局任务
* 模板托管 - 复用项目模板
* node模块管理 - 从项目目录抽离node\_modules目录, 优化模块查找路径
* 轻量, 无第三方依赖

## 安装

```bash
$ npm i -g fbi
```


## 使用

```bash
$ fbi [task]              # 运行一个本地优先的任务
$ fbi [task] -g           # 运行一个全局任务
$ fbi [task] -t           # 运行一个模板任务

使用 'fbi ls' 查看可用任务和模板
```

```bash
$ fbi ata,   add-task [name]          # 添加任务
$ fbi atm,   add-tmpl                 # 将当前目录添加为模板
$ fbi rta,   rm-task  [-t] [name]     # 移除任务
$ fbi rtm,   rm-tmpl  [name]          # 移除模板
$ fbi i,     install                  # 安装依赖
$ fbi ls,    list                     # 查看所有可用任务和模板
$ fbi cat    [task]   [-t, -g]        # 查看任务源码
$ fbi init   [template]               # 初始化模板
$ fbi backup                          # 备份任务与模板
$ fbi recover                         # 从当前目录恢复任务与模板
$ fbi update                          # 更新本地项目
                                      # 该操作会覆盖本地"fbi"文件夹和package.json里的"devDependencies"

$ fbi -h,    --help                   # 显示帮助
$ fbi -v,    --version                # 显示版本号
```

## 所有配置

[所有配置](https://github.com/neikvon/fbi/blob/master/CONFIGURATION.md)

## 快速开始

### 添加任务

[查看完整示例](https://github.com/neikvon/fbi-tasks-demo)

```js
// ./project/fbi/showtime.js

console.log(new Date())
```
```bash
$ cd path/to/project/

$ fbi ata   # short for 'fbi add-task'
```
```bash
$ fbi showtime

# output:
# FBI => Running global task "showtime"...
# 2016-08-03T09:06:28.349Z
```
`fbi` 会把 `./fbi`文件夹内的`.js` 文件拷贝到全局任务目录.

### 添加模板

[查看完整示例](https://github.com/neikvon/fbi-template-webpack-demo)

```bash
|-- proj-name
|--   src
|--     index.html
|--     css/
|--     js/
|--     img/
|--   fbi
|--     config.js
|--     build.js
|--   package.json

```
```js
// config.js
module.exports = {
  template: 'webpack-demo', // required, template name
  templateDescription: 'template description',
  alias: {
    b: 'build' // task name alias
  }
}
```
```js
// build.js
/**
 * global vars:
 * ctx => fbi
 * require => requireResolve
 */
const webpack = require('webpack')
const webpackConfig = {
  ...
}

const isProduction = ctx.taskParams && ctx.taskParams[0] === 'p' // fbi build -p

if (isProduction) {
  ctx.log('env: production')
  webpackConfig['plugins'].push(
    new webpack.optimize.UglifyJsPlugin({ // js ugllify
      compress: {
        warnings: false
      }
    })
  )
}

webpack(webpackConfig, (err, stats) => {
  if (err) {
    ctx.log(err, 0)
  }

  ctx.log(`webpack output:
${stats.toString({
      chunks: false,
      colors: true
    })}`)
})
```

**安装**
```bash
$ npm i               # 安装依赖到本地
```

**测试**
```bash
$ fbi b
```

**添加**
```bash
$ fbi atm             # fbi 会使用 'config.js=>template' 的值作为模板名称
```

**检查**
```bash
$ fbi ls              # 查看可用任务和模板
```
## 示例
1. [tasks-demo](https://github.com/neikvon/fbi-tasks-demo)
1. [template-vue2](https://github.com/neikvon/fbi-template-vue2)
1. [template-webpack2](https://github.com/neikvon/fbi-template-webpack2)
1. [template-webpack1](https://github.com/neikvon/fbi-template-webpack1) ( 兼容 fbi v1.x )
1. [template-fullpack](https://github.com/neikvon/fbi-template-fullpack)
1. [template-restful](https://github.com/neikvon/fbi-template-restful)

## [变更日志](https://github.com/neikvon/fbi/blob/master/CHANGELOG.md)

## 团队协作

* 远程git仓库 ` fbi-data `，仓库目录如下：


  ```
|-- fbi-data
|--   tasks
|--     fbi
|--       task1.js
|--       task2.js
|--       ...
|--     package.json
|--     node_modules
|--
|--   templates
|--     template1
|--       fbi
|--         task1.js
|--         task2.js
|--       src
|--       package.json
|--       node_modules
|--       README.md
|--     template2
|--     ...
  ```

* 克隆仓库
```bash
$ fbi clone git@path/to/remote/fbi-data.git
```
注意：`fbi clone` 需要在支持`git`、 `rm -rf`、 `mkdir`的终端执行，如果你是windows用户，可以使用`git bash`。

* 查看可用任务和模板
```bash
$ fbi ls
```

* 新建项目并运行
```bash
$ cd path/to/empty/folder
$ fbi init template1
$ fbi i
$ fbi [task]
```

* 更新 fbi-data
```bash
$ fbi pull
```

## Q&A
* FBI 怎么识别任务?
  1. `fbi/config.js` => `paths.tasks`  配置项指定的任务目录(默认为`./fbi`)
  1. `.js` 文件
  1. 文件名不包含 `config` 字符

* FBI 怎么识别模版?
  1. fbi/config.js => template  配置项指定的模版名称

* FBI查找任务的顺序是什么?
  1. 本地 => `./fbi/` 目录下的js文件
  1. 是模版 ? => 全局模版任务
  1. 全局 => 全局任务