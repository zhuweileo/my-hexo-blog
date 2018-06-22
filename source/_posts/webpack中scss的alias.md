---
title: webpack中scss的alias
date: 2018-06-12 18:18:37
tags: 
    - webpack
    - scss
---

   webpack.config.js
   ```
       resolve: {
           alias: {
              styles: path.join(__dirname, 'app/styles') 
          }
       }
   ```
   index.scss

   `@import "~styles/variables";`

   alias 名称前必须加上`~`

