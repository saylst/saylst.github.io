---
layout:     post
title:      Comparator
date:       2016-06-20
author:     Saylst
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - 博客
    - Java
---

# Comparator

**背景**：对一个用户（包括name、score两个属性）List按score大小排序，要求成绩相同时，保持List中的原有顺序。

***

**参数:**

1.ascending == 1  从低到高   
2.ascending == 0  从高到低

**注:** 如果参数不是0或1，则直接返回-1

***

**结果:**

排序成功则返回0

    public static int sortUser(int ascending)
    {
       final int op = ascending;
       Collections.sort(users, new Comparator<User>() {
          @Override
          public int compare(User u1, User u2) {
             if(u1.score > u2.score){
                return op==1 ? 1 : -1;
             }else if(u1.score < u2.score){
                return op==1 ? -1 : 1;
             }else{
                return 0;
             }
          }
       });
       return 0;
    } 

