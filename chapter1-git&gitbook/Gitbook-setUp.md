## Gitbook 安装及使用

### 1.安装

#### 1.1 gitbook  安装

```objective-c
npm install gitbook -g
```

#### 1.2 gitbook 命令行工具安装

```objective-c
npm install -g gitbook-cli
```

#### 1.3  查看是否安装成功

```
gitbook -V
```

- 如果显示版本，则表示安装成功 

#### 1.4 卸载

```
npm uninstall -g gitbook
```

## 2. 基本使用

#### 2.1 初始化书籍目录

- 首先，创建一个如下结构的目录

  ```
  $ tree iOSBook //iOSBook 为新建的书籍目录
  iOSBook/
  ├── README.md
  └── SUMMARY.md

  0 directories, 2 files
  ```

  其中：

  - README.md 是对书籍的简单介绍
  - SUMMARY.md 是书籍的目录结构

- 为 SUMMARY.md 添加内容

  ```
  # Summary

  * [Introduction](README.md)
  * [Chapter1](chapter1/README.md)
      * [Section1.1](chapter1/section1.1.md)
      * [Section1.2](chapter1/section1.2.md)
  * [Chapter2](chapter2/README.md)
  ```

- 使用 ``` gitbook init```

  得到如下

  ```
  $ tree
  .
  ├── README.md
  ├── SUMMARY.md
  ├── chapter1
  │   ├── README.md
  │   ├── section1.1.md
  │   └── section1.2.md
  └── chapter2
      └── README.md

  2 directories, 6 files
  ```

- 使用 markdownm 编辑器，便携 md 文件，放入 该文件目录下，并在 SUMMAR.md中添加即可

  - markdown  编辑器

    ```
    Typora
    ```

##### 注意：

- 使用 tree 命令，发现 未找到时

```
$ brew install tree // 安装
```

## 3.查看

#### 3.1 编译并预览书籍

```
$ gitbook serve ／／在 iOSBook  目录下
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 7 plugins are installed 
info: loading plugin "livereload"... OK 
info: loading plugin "highlight"... OK 
info: loading plugin "search"... OK 
info: loading plugin "lunr"... OK 
info: loading plugin "sharing"... OK 
info: loading plugin "fontsettings"... OK 
info: loading plugin "theme-default"... OK 
info: found 5 pages 
info: found 0 asset files 
info: >> generation finished with success in 2.5s ! 

Starting server ...
Serving book on http://localhost:4000
```

- 用浏览器 打开  http://127.0.0.1:4000 查看书籍效果即可

## 4.使用

#### 4.1 光标移动

- 向左

```
command + [    
```

- 向右

```
command + ]
```

#### 4.2 代码块

```
command + alt + c
```

