---
title: VIM to edit binary files
date: 2023-05-26 09:57:47
categories:
tags: vim
---

![VIM-BINARY-Screenshot](vim-binary.png)

## 前言

雖然很早之前就知道vim可以搭配xxd變成hex editor，但一直沒有去研究相關設定。

google的話，會告訴你在**讀檔後**鍵入指令`:%!xxd`；而**寫檔前**鍵入指令`:%!xxd -r`

然而大家往往漏說了一個重要的前提：在**讀檔前**應設定`binary` option, 不外幾種方式：

|                    | CMDLINE       | VIM             |
| ------------------ | :------------ | :-------------- |
| CMD開檔並設定bin   | vim -b *file* |                 |
| VIM開檔並設定bin   | vim           | :e ++bin *file* |
| VIM重開檔並設定bin | vim *file*    | :e ++bin        |

binary選項最重要的功能在於：

- 關閉 endofline 選項，由於UNIX規定lines都要以newline結尾，一般情況加會自動補上`\n`字元
- 避免VIM對binary的不當解讀，造成字元的缺失


## 自動化腳本

如果再考慮 xxd 選項、xxd syntax highlight，前置工作其實不少，整個過程變得繁雜。

偉哉的VIM當然考慮過了這一點，在說明書`:h hex-editing`中有提供自動化腳本可供使用！

稍微改寫了一下如下：

```vim
augroup Binary
  au!
  au BufReadPre   *.{bin,exe} let &bin=1
  au BufReadPost  * if &bin | silent %!xxd -g1
  au BufReadPost  * set ft=xxd | endif
  au BufWritePre  * if &bin | %!xxd -r
  au BufWritePre  * endif
  au BufWritePost * if &bin | silent %!xxd -g1
  au BufWritePost * set nomod | endif
augroup END
```

照字面解讀，應該很好理解，這個腳本在`bin`選項被設定時，會在**讀檔後**及**寫檔前**自動呼叫xxd，相當方便！
syntax和xxd選項也會一併設定。
預設副檔名是bin及exe會自動設定`bin`選項，或是使用者在開檔前自行指定該選項亦可。

***VIM真是活到老學到老可以用一輩子的編輯器。***
