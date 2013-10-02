---
layout: post
title: 使用Emacs编辑markdown文件
tags: 
- emacs
- markdown
status: publish
type: post
published: true
---


我陆陆续续尝试使用Emacs已经该有7、8回了，每次都受不了它繁琐的Ctrl和Meta组合键，但最近不知道哪根筋出了问题，不但编辑器迅速的切换到了Emacs，甚至
操作系统也从Win 7平滑到了Ubuntu。以下记录一些关于Emacs、markdown、Ubuntu、ssh乱七八糟的东东。

老板最近推行markdown来记录技术文档，并且在内网构建了一个markdown的wiki系统。刚好自己也一直在用md，比如现在的这个搭建在github上的静态页面博客。

# Emacs 的安装

在ubuntu下安装Emacs非常简单，甚至可以一起装好ess：

	sudo apt-get install emacs ess

在Emacs里运行R还是很爽的事情。

## Emacs 的设置文档

主要支持语法高亮，以及一些人性化的配置，不多说直接贴.emacs 文件（在/home/user目录，可能需要Ctrl+h显示隐藏文件）

	(r-mode nil)
	;;不要滚动栏，现在都用滚轴鼠标了，可以不用滚动栏了
	(scroll-bar-mode nil)

	;;修改中文文本的行距,3个象素就可以了吧
	(setq-default line-spacing 3)
	;;启用C-x,C-v,C-s这些通用设置
	;(cua-mode t)

	;;颜色主题
	(setq load-path (cons "/home/sunbjt/emacs" load-path))
	(require 'color-theme)
	(color-theme-deep-blue)

	;; Setting English Font
	(set-face-attribute
	'default nil :font "Courier 14")

	;; Chinese Font
	(dolist (charset '(kana han symbol cjk-misc bopomofo))
    (set-fontset-font (frame-parameter nil 'font)
    charset
    (font-spec :family "Microsoft Yahei" :size 16)))

	;; 支持emacs和外部程序的粘贴
	(setq x-select-enable-clipboard t)

	;; 去掉工具栏
	(tool-bar-mode nil)

	;;去掉烦人的警告铃声
	(setq visible-bell nil)

	;;去掉Emacs和gnus启动时的引导界面
	(setq inhibit-startup-message t)
	(setq gnus-inhibit-startup-message t)

	;;当指针到一个括号时，自动显示所匹配的另一个括号
	(show-paren-mode 1)

	;;所有的问题用y/n方式，不用yes/no方式。有点懒，只想输入一个字母
	(fset 'yes-or-no-p 'y-or-n-p)

	(setq
    backup-by-copying t ; 自动备份
    backup-directory-alist
    '(("." . "/home/sunbjt/emacs/bak")) ; 自动备份在目录"D:/bak"下
    delete-old-versions t ; 自动删除旧的备份文件
    kept-new-versions 6 ; 保留最近的6个备份文件
    kept-old-versions 2 ; 保留最早的2个备份文件
    version-control t) ; 多次备份

	;;自动格式化代码
	(dolist (command '(yank yank-pop))
	(eval
	`(defadvice ,command (after indent-region activate)
    (and (not current-prefix-arg)
    (member major-mode
    '(emacs-lisp-mode
    lisp-mode
    clojure-mode
    scheme-mode
    haskell-mode
    ruby-mode
    rspec-mode
    python-mode
    c-mode
    c++-mode
    objc-mode
    latex-mode
    js-mode
    plain-tex-mode))
    (let ((mark-even-if-inactive transient-mark-mode))
    (indent-region (region-beginning) (region-end) nil))))))

这里面有个emacs的颜色主题，需要下载color-theme.el，请自行搜索之，并拷贝至emacs可以找到的目录下。

## 安装markdown-mode和pandoc

下载[markdown-mode.el](http://jblevins.org/projects/markdown-mode/)至 emacs 的load path，并编辑.emacs增加以下内容，保证识别后缀名为text、markdown、md文件：

	(autoload 'markdown-mode "markdown-mode"
		"Major mode for editing Markdown files" t)
	(add-to-list 'auto-mode-alist '("\\.text\\'" . markdown-mode))
	(add-to-list 'auto-mode-alist '("\\.markdown\\'" . markdown-mode))
	(add-to-list 'auto-mode-alist '("\\.md\\'" . markdown-mode))

接着安装pandoc（用于将markdown文件转化为html文件）

	sudo apt-get install pandoc

markdown的那个程序也可以类似的安装，但不能像pandoc一样增加各种参数设置，所以不建议大家使用。

## 修改markdown-mode 的Customization

一般是通过 `M-x customize-mode` 来修改，将Markdown Command 修改为

	pandoc -f markdown -t html -s -c /home/sunbjt/emacs/style.css --mathjax --highlight-style espresso

上面这句话有两个位置需要注意：

- style.css，可以自定义你的生成的html文件的样式
- mathjax参数，在C-c C-c p做浏览器预览时，可以自动加载数学公式

接着是几个组合命令：

- C-c C-c v 和 p一样，是对 md 文件的 browser 端的预览；
- C-c C-c e，重新刷新已打开的预览；
- C-c C-c m，在buffer里看html的源代码

对于css文件，我自己常用的在[这里](/upload/style.css)，基本上是仿github的风格。如果不是在Emacs下书写markdown，可以通过在命令行执行如下代码，以生成自定义的html文件：

	pandoc -s doc.md -c style.css -o report.html

# 翻呀啊墙

为保险起见，请先执行这段R代码：

	id <- c(7, 15,  1,  7,  5, 14, 20)
	paste(letters[id], collapse='')
	
你要用上面那个软件，教程一大堆，唯一需要注意的是Windows和Liunx的放在了一起，只要有Python环境，执行即可穿越。

如果使用商用的ssh，那么用 expect 自动登陆ssh，

	sudo apt-get expect

然后再写一个自动执行登陆的脚本：

	#!/usr/bin/expect
	set timeout 60
	spawn /usr/bin/ssh -p 80 -D 7070 -g username@8.8.8.8
	expect {
	"password:" {
	send "password\r"
	}
	}
	interact {
	timeout 60 { send " "}
	}

就这样，全部工作都在Ubuntu下了~~
