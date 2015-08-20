---
layout: post
title: Use Relative Image Reference in README.md
---
So you want to have an image or two in your project README.md, but do not want those images present in your sleek binary-only project side.  
Seperate asset branch to the rescue!  
Push your images there and then use relative reference in your readme like so:

`![your_image](/../assets/relative/path/to/img.jpg?raw=true "Optional Title")`

Check out the [readme here](https://github.com/Epigene/antigua-dark-syntax) for how it is done.

![your_image](https://raw.githubusercontent.com/Epigene/Epigene.github.io/assets/cookies.jpg)
