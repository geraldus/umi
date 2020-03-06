---
translateHelp: true
---

# Upgrade to Umi 3


This document will help you to upgrade Umi 2.x to Umi version 3.x.

## package.json

Modify `umi` version to `^3.0.0` or higher
```diff
{
  "devDependencies": {
-   "umi": "^2"
+   "umi": "^3"
  }
}
```

Since Umi 3 requires Node 10.13 or above, if you have engines before, you need to confirm the version number.

## tsconfig.json

To have better ts completions you have to specify `@@` as `["src/.umi/*"]`

```diff
+ "@@/*": ["src/.umi/*"]
```

## Change umi-plugin-react to @umijs/preset-react

If you have used `umi-plugin-react` before, here are the modification steps.
如果之前有使用 `umi-plugin-react`，以下是修改的步骤。

First change dependencies in package.json file

```diff
{
  "devDependencies": {
-   "umi-plugin-react": "^1"
+   "@umijs/preset-react": "^1"
  }
}
```

Then, because the configuration of Umi 3 is flat, you need to modify the configuration.

```diff
export default {
- plugins: [
-   ['umi-plugin-react', {
-     dva: {},
-     antd: {},
-     ...
-   }]
- ],
+ dva: {},
+ antd: {},
+ ...
}
```

Pay attention

1. No need to register plugins manually, Umi 3 will automatically register Umi plugins in dependencies
2. Plugin configuration extracted outside

Features changes

* routes, library, dll, hardSource, pwa, hd, fastClick, chunks no longer available
* you can still use dynamicImport, title, scripts, headScripts, metas, and links into Umi
* no other functionality changed

## 配置层

Umi 3 在配置层做了大量精简，以下修改以字母排序，便于查找。

* 删除 browserslist，和 targets 重复了
* 删除 babel，基本用不上
* 修改 cssLoaderOptions 命名为 cssLoader
* 删除 cssLoaderVersion，只保留 css-loader@2 的版本
* 删除 cssPublicPath，css 引用的资源文件用相对路径 `./` 可满足所有场景，没有必要再配
* 删除 disableGlobalVariables，始终无全局变量，无需配置
* 删除 disableRedirectHoist，始终不再做 redirect hoist
* 删除 disableCSSModules 和 cssModulesWithAffix，Umi 3 自动识别 css modules 的使用，无需配置
* 删除 extraBabelIncludes 和 es5ImcompatibleVersions，node\_modules 也走 babel 编译后就没有意义了，无需配置
* 修改 history 格式为 `{ type, options }` ，不再支持 string 格式
* 修改 lessLoaderOptions 命名为 lessLoader
* 删除 minimizer，只保留 terserjs
* 修改 plugins 的格式为字符串，需要先确保依赖的插件是否升级到 Umi 3，然后修改方式参考前面 umi-plugin-react 的修改方式
* 删除 sass，不再支持，后续会以插件的方式提供
* 删除 treeShaking，已内置，无需配置
* 删除 tsConfigFile，没有必要
* 删除 typescript，TypeScript 编译交给 babel 处理后，之前 ts-loader 的配置就没有意义了
* 删除 uglifyJSOptions，没有必要
* 删除 urlLoaderExcludes，没有必要

## 环境变量层

## 代码层

### import all from umi

不再保留 `umi/xxx` 的接口，全部从 umi 中 import。

比如：

```diff
- import Link from 'umi/link';
+ import { Link } from 'umi';
```

### umi/router

改用 `history` 代替。

```diff
- import router from 'umi/router';
+ import { history } from 'umi';

- router.push('/foo');
+ history.push('/foo');
```

### CSS 里引用别名或三方库

需要加 `~` 前缀。

比如：

```diff
# 别名
- background: url(@/assets/logo.png);
+ background: url(~@/assets/logo.png);

# 三方库
- @import url(foo/bar.css');
- @import url(~foo/bar.css');
```

## 遇到问题

Umi v3 做了非常多的细节改进和重构，我们尽可能收集了已知的所有不兼容变化和相关影响，但是有可能还是有一些场景我们没有考虑到。如果你在升级过程中遇到了问题，请到 [Github issues](https://github.com/umijs/umi/issues) 进行反馈。我们会尽快响应和相应改进这篇文档。

也可以加 "Umi 3 升级问题互帮互助" 群，

<img src="https://img.alicdn.com/tfs/TB1pd1ce8r0gK0jSZFnXXbRRXXa-430-430.jpg" width="60" />

扫上方二维码，并回复 **umi 3**。
