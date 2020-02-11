---
layout: post-for-notes
title:  "Progressing Personal Site"
date:   2020-02-11
excerpt: "Record the developing process of this site"
notes: true
---

# Change Log

## 2020-02-11


1. Clone the `jekyll` from [hanyihong0324.github.io](hanyihong0324.github.io);
The script's code is here:

```bash
git clone https://github.com/Clynie/hanyihong0324.github.io.git Clynie.github.io
```


2. Use the shell script, developed by **hanyihong0324** to change profile logo.  
The script's code is here:

```bash
#!/bin/bash
convert $1 -resize 16x16 favicon.ico
convert $1 -resize 32x32 favicon.png
convert $1 -resize 72x72 assets/img/favicons/apple-icon-72x72.png
convert $1 -resize 114x114 assets/img/favicons/apple-icon-114x114.png
convert $1 -resize 144x144 assets/img/favicons/apple-icon-144x144.png
convert $1 -resize 72x72 assets/img/favicons/apple-icon-precomposed.png
convert $1 assets/img/favicons/logo.png
convert $1 assets/img/logo.png
```

3. Add **Notes** in Contents:

```bash
#!/bin/zsh
vi _data/navigation.yml
```
add these lines:
```vim
- title: Notes
  url: /notes/
```
Then
```bash
#!/bin/zsh
mkdir notes
vi notes/index.md
```
add these lines:
```md
---
layout: notes
title: All Notes
excerpt: "A List of notes for install"
notes: true
---
```
Create the `vi _layouts/post-for-notes.html`  
For easy way you may:

```bash
#!/bin/zsh
cp _layouts/post-for-project.html _layouts/post-for-notes.html
```
and relace the `project` instead `notes` in `_layouts/post-for-notes.html` 



### Create note in Note Content:

If you want create a note in Note Content:

```bash
#!/bin/zsh
vi _posts/2016-04-23-Progressing_Personal_Site.md
```
and the First lines should be :
```md
---
layout: post-for-notes
title:  "Progressing Personal Site"
date:   2020-02-11
excerpt: "Record the developing process of this site"
notes: true
---
```

Then Everything goes like markdown



