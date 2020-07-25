---
title: webpack
date: 2020-07-19 21:45:00
categories:
    - 框架
tags:
    - webpack
---

## file, chunk, module的区别

`file`是webpack最终生成的单js文件

`chunk`是`webpack`模块分析，代码分割，模块加载时的中间产物，分析打包的过程叫做`chunk`，比如在代码分割的时候，可以根据`chunk`被依赖的次数来决定是否需要提取

`module`项目中每个`require`或者`import`都就可以成为`module`

# webpack文件分割

通过配置`optimization.splitChunks`来切割代码

```javascript
splitChunks: {
    chunks: "all", // 从哪些chunks抽取代码，除了initial, async, all之外，还可通过函数来过滤所需的chunks
    minSize: 30000, // 抽取文件在压缩前的最小大小，默认30000
    maxSize: 0, // 抽取文件在压缩前的最大大小，默认0，表示不限制最大大小
    minChunks: 1, // 被引用次数
    maxAsyncRequests: 5, // 最大的按需加载次数, 默认5
    maxInitialRequests: 3, // 最大的初始化加载次数，默认3
    automaticNameDelimiter: '~'//抽取出来的文件自动生成名字的分割符，默认为~
    name: true, // 抽取出来的名字，默认true，表示自动生成文件名
    cacheGroups: { default: false }, //缓存组，会继承splitChunks的配置，但是test, priorty, reuseExistingChunk只能用于配置缓存组, 可以通过cacheGroups.default: false禁用缓存组，默认缓存组的优先级(priotity)是负数，所有自定义缓存组都可以比它的优先级高，默认自定义缓存组优先级为0
}
```



# webpack常用loader

`file-loader`把文件输出到一个文件夹中，在代码中通过相对URL去引用输出文件（处理图片和字体资源）

`url-loader`与`file-loader`类似，可以设置一个阈值，大于阈值返回URL路径，小于阈值返回文件base64格式（处理图片和字体）

`css-loader, less-loader, sass-loader, postcss-loader`打包样式资源，自动添加浏览器前缀，处理单位转换

`style-loader`打包样式注入js中，插入到html加载css

`babel-loader babel-core`编译一些文件，例如jsx编译为js，es6编译为es5

`source-map-loader`加载映射额外的source map文件，以方便代码调试

`svg-inline-loader`将压缩得到svg内容注入代码中

`image-loader`加载并压缩图片文件

`json-loader`加载json文件

`ts-loader`将`typescript`转换成`javascript`

`eslint-loader, tslint-loader`通过eslint、tslint检查代码规范

`mocha-loader`测试用例

# webpack打包环境