---
title: pathway博客以及文章阅读（没看懂 自己水平太差）
---

 ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgLXCWMlipdu0gFF6hsiJHbxg1zSaEkdDWfl-8RakQuW__8RPvlOS9KGIScNCytxT4jz9isnx0GLMwbS1G0Q4WdXzT42GszgfwIIAVX1H3J-43lVWWqcb--q9cPsxCsJFFz2dRfpKgEmLe-xfIyBqQuPq1BPYcK9CtAK1_xnhgvgAAx0GeZmODJxGNMYQ/s1600/image8.gif) 

链接：https://ai.googleblog.com/2022/04/pathways-language-model-palm-scaling-to.html

https://blog.google/technology/ai/introducing-pathways-next-generation-ai-architecture/

jeff dean 和 sanjay 是一个一起快乐结对编程好基友

## 0. 前言

简单和常见的分布式 是在单机算完每一个梯度backward后，在调用一个allreduce（把每一个人算的梯度，在各个机器上面做一次相加）

