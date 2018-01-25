# YSF文档输出工具

## 目录结构
---
  -- guide // 开发指南源文件目录
  -- help  // 产品手册源文件目录
  -- html  // 编译输出目录
  -- guide.ejs // 开发指南编译模板
  -- help.ejs  // 产品手册编译模板

## 使用说明

```js
   npm install -g ysfdoc

   ysfdoc -h

   Options:
        -h, --help              output usage information
        -p, --path [value]      输入文档目录
        -t, --template [value]  模板路径,现仅支持ejs模板
        -s, --suffix [value]    输出文件的后缀, 默认为html

```

```js
    // 编译开发指南目录
    ysfdoc -t ./guide.ejs -p ./guide

    // 编译产品手册目录
    ysfdoc -t ./help.ejs -p ./help
```

