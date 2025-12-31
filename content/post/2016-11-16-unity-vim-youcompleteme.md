---
date: '2016-11-16T00:00:00Z'
title: Unity3D, Vim and YouCompleteMe
---

Recently I had some issues getting these three wonderful things to play nicely
together, but I got there. You need mono installed, and you need to install
YouCompleteMe with clang and omnisharp.

Here's what I did on OSX:

```
brew install mono
cd .vim/bundle/YouCompleteMe
./install.py --clang-completer --omnisharp-completer
```
