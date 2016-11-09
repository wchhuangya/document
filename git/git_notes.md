---
title: git notes 
tags: git,notes
grammar_cjkRuby: true
---

[toc]

## Git 基础
---
1. 直接记录快照，而非差异比较
2. 几乎所有操作都是本地执行
3. 保证完整性（使用 `SHA-1` 散列）
4. 一般只添加数据，即几乎不执行任何不可逆操作，或几乎不进行数据清除操作
5. 三种状态
	1. 已提交：数据已安全的保存在本地数据库中
	2. 已修改：已修改了文件，但是学没有保存到数据库中
	3. 已暂存：对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中
6. 三个工作区域
	1. `Git` 仓库：`Git` 用来保存项目的元数据和对象数据库的地方
	2. 工作目录：对项目的某个版本单独提取出来的内容
	3. 暂存区域：是一个文件，保存了下次将提交的文件列表信息
7. 工作流程
	1. 在工作目录中修改文件
	2. 暂存文件，将文件的快照放入暂存区域
	3. 提交更新，找到暂存区域的文件，将快照永久性存储到 `Git` 仓库目录

## 安装
---
`MAC` 系统下有多种安装方式，最简单的就是安装 `Xcode Command Line Tools`。安装完成后，在 `Terminal` 里尝试首次运行 `git` 命令即可。

## .gitignore 格式规范
---
1. 所有空行或以 `#` 号开头的行都会被 `git` 忽略
2. 可以使用标准的 `glob` 模式匹配
3. 匹配模式可以以 `/` 开头防止递归
4. 匹配模式可以以 `/` 结尾指定目录
5. 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号 `!` 取反

> 说明：所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（*）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 使用两个星号（*) 表示匹配任意中间目录，比如a/**/z 可以匹配 a/z, a/b/z 或 a/b/c/z等。

> 提示：GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表，你可以在 https://github.com/github/gitignore 找到它

## 命令
---
### 设置全局使用的用户名和邮箱

``` xml?linenums=true
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```
### 设置全局使用的文本编辑器

``` xml?linenums=true
$ git config --global core.editor emacs
```
### 检查所有的配置信息

``` xml?linenums=true
$ git config --list
```
### 检查单项的配置信息

``` xml?linenums
$ git config user.name
```
### 获取帮助信息（三个版本）

``` xml?linenums=true
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```
### 在现有目录中初始化仓库

``` xml?linenums=true
$ git init
```
### 提交文件

``` xml?linenums=true
$ git add *.c
$ git commit -m 'initial project version'
```
`git add` 命令实现对指定文件的跟踪，`git commit` 命令实现数据提交

> 注意：`git add` 这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“添加内容到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。

### 克隆现有仓库

``` xml?linenums=true
$ git clone [url]
```
> 注：该操作与 `SVN` 的 `checkout` 操作完全不一样，它是系统的一个重要特性。该操作会把仓库中每个文件的每个版本都拉取下来，相当于仓库的克隆版本。

### 克隆现在仓库并自定义本地仓库名称

``` xml?linenums=true
$ git clone [url] [new_name]
```
### 检查当前文件状态

``` xml?linenums=true
$ git status [-s]/[-short]
```
> 注意：使用 `git status -s` 命令或 `git status --short` 命令，将得到一种更为紧凑的格式输出

### 查看尚未暂存的文件更新了哪些部分

``` xml?linenums=true
$ git diff
```
### 查看已暂存的将要添加到下次提交里的内容

``` xml?linenums=true
$ git diff --staged
or
$ git diff --cached
```
> 注意：`git diff --staged` 适用于 `Git 1.6.1` 及更高版本，它的效果比 `--cached` 更好。

### 查看系统支持哪些 Git Diff 插件

``` xml?linenums=true
$ git difftool --tool-help 
```

> 说明：如果你喜欢通过图形化的方式或其它格式输出方式的话，可以使用 `git difftool` 命令来用 `Araxis` ，`emerge` 或 `vimdiff` 等软件输出 `diff` 分析结果。 使用 `git difftool --tool-help` 命令来看你的系统支持哪些 `Git Diff` 插件

### commit 命令高级用法















