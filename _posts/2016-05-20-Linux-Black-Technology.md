---
layout: post
title:  "Linux Black Technology"
date:   2016-05-20 21:39:26 +0800
excerpt: "强行黑科技23333"
tags: [topic=linux, language=english, origin=true]
---

### PDF合并分割

    pdftk 123.pdf 456.pdf output 123456.pdf
    pdftk *.pdf output 123456.pdf
    pdftk A=123.pdf B=456.pdf cat A1-2 B4-5 output 1245.pdf
    pdftk 123.pdf output 123fixed.pdf
    pdftk A=123.pdf 456.pdf input_pw A=password cat output 123456.pdf
