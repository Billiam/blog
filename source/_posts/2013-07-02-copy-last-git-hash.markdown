---
layout: post
title: "Copy Last Git Hash"
date: 2013-07-02 01:07
comments: false
categories: git
---

Show the most recent git commit hash, and copy it to the clipboard:

```sh Linux, X
git rev-parse HEAD | tee xclip -selection c
```

```sh Windows (mingw or similar)
git rev-parse HEAD | tee /dev/clipboard
```

```bash Mac
git rev-parse HEAD | tee pbcopy
```
