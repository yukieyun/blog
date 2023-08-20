---
title: 打工记（二）
author: 云五
type: post
date: 2023-08-20T13:23:23+00:00
description: |
  这家公司的根本问题就是——核心业务非常难变现。即使有一些名气，用户量也挺大，也上市了，但在盈利上存在很大阻碍。后来因为一个意外的机会，我看到了这家公司的财务报表和各轮投资估值，发现它最后一轮的投资机构甚至没有从这家公司的 IPO 里赚到钱，上市仅仅是帮他们减少了损失。
url: /roam/gravedigger-of-capitalism-02/
categories:
  - 漫游
tags:
  - 打工
  - 草台班子
  - 临时工
  - contractor
  - 搬砖

---

上期写完后突然想起来第一家公司还有一点没写。

我一直对各种类型公司的成长管理很有兴趣，创业不只是找一个idea 开始做到盈利，扩张中能否让公司“稳定”下来很考验人。因为创业公司早期招人可能是创始人们亲力亲为，八仙过海各显神通，能从什么门路招到人干活就去什么门路。但有一定规模和知名度后，就要开始批发式招人；起初公司管理可能很扁平，人多了也必须要制定级别了，有一些刚冒头时很耀眼的 tech startup 就垮在这一步。

插播推荐创业圣经（之一） The Hard Thing About Hard Things：

![The Hard Thing About Hard Thing](https://media.go5.dev/go5media/media_attachments/files/110/922/661/080/364/263/original/691a5e25f6f9990c.png)

## 为什么有些公司数年不更换面试题

这家公司在工作环境各方面都很好，制度方面也比较健全，我闲来无事时就疯狂阅读公司里的各种文档，比如面试流程 😂 据我观察不少这一规模的公司会使用很稳定且很小的面试题库，原因很简单：要起到客观衡量面试者的作用，起码一定量的面试者都必须使用同一题目，并观测大家在各方面的表现（是否给出 solution、能否把 solution 转化成 code、是否考虑到 edge case、考虑到哪些 edge case……）再做出对比。

规模千人左右的公司，往往还没有足够的能力吸引极其大量的候选人，如果使用多套题，就无法用统一标准来衡量；或者需要大量精力投入去制定规则，即一道题的方方面面如何考核；同时也要投入大量精力让公司的程序员去学习衡量标准，这些都是人力成本。在候选人样本不够多的时候，面试官的主观偏差的影响可能比题目泄漏的影响更大——毕竟面试的标准，并不只是把答案写出来；更何况根据我做面试官的经验，即使一家公司三年都用同一套题，80%的面试者也是毫无准备来面试的。

## 程序员的打怪升级路

另一个我感兴趣的话题是公司对程序员的培养，因为那段时间在她乡和毛象都有不少人转码，要找 Junior Developer 的工作。不少人上岸后感觉工作中仍然什么都不会，更觉压力山大，不理解自己为什么能找到工作（？）

我在这家公司的文档里，找到了他们对程序员级别和相应工作能力的表述，其中 Junior Developer 的范围是刚毕业到有一两年工作经验的程序员，对他们的能力期许是：在有足够明确 instructions 时能独立或寻求少量帮助完成一项小的任务（比如写一个 api）；在碰到问题时能及时/主动寻求帮助；在工作初期需要有更有经验的程序员以比较高的频率（一周数次甚至可能每天）对他们进行指导，随着对工作的熟悉，指导频率降低，能够在需求较为模糊时，在更有经验的程序员指导下完成一些小至中等的任务。

到了 Intermediate Developer，就在 Junior 的基础上每一点都提高了——能独立或在少量指导下，拆分一个中等任务（如 component / module）并完成，能发现开发过程中可能碰到的问题并寻求帮助，对自己手里的任务完成时间有较好的把握；开始做一些小项目的系统设计。

解释清楚了 Junior 和 Intermediate，就很容易理解 Senior Developer 要做什么了——能（相对独立）拆分一个中到大项目，带队完成，对项目进度有较好的掌控；能指导 Junior / Intermediate 的程序员工作。

## 第一家公司的优点和问题

这家公司从公司的氛围和工作环境来说都挺让我满意的，上一篇里我有提到招我的时候本来是要做以后端为主的 full stack ，可是进去后一直在写 data pipeline，公司有基于 airflow 做二次开发，能够处理各种各样类型格式的 pipeline。但我对SQL也不熟，平时顶多写写 select 和 join 很少用到上千行的 SQL 还要去跑一些 model。但只要有问题，不管是在组内 Channel 还是找到代码里以前经手的人，都能很快速地得到帮助，完全不认识的外组程序员只要我讲清楚来意和需求，他们也都很乐意开一个 zoom meeting 跟我把问题讲清楚，让我能够快速地上手我本来不怎么做的任务。

做前端很少但还是做了一个前端的 task，结果这一做就出了一个 oncall 事故。好在事故级别不高，处理的人也没有责怪我，只是说这一部分当时 test case 就没有写得太完备，要记一个任务等专门做前端的人来写更好的 test case 来防止这一类问题。

一家公司不可能完美无缺，否则不至于一年之内走掉那么多人……这家公司的根本问题就是——核心业务非常难变现。即使有一些名气，用户量也挺大，也上市了，但在盈利上存在很大阻碍。后来因为一个意外的机会，我看到了这家公司的财务报表和各轮投资估值，发现它最后一轮的投资机构甚至没有从这家公司的 IPO 里赚到钱，上市仅仅是帮他们减少了损失。

我签的合同只拿时薪，公司的股价并不影响我的收入所以无所谓。但对于陪伴公司成长的很多程序员来说，几年的心血投入进去了，等到公司上市之后股价并没有如想像中那样一飞冲天，或者哪怕保持原样对他们来说也OK，但后来一年股价跳水，很多程序员并没有得到他们预期中的回报，心灰意冷要找一家更有前途的公司也就是理所当然的了。

我的打工史里有另一家公司和这一家非常类似——差不多的规模，也比较友好的氛围，但迟迟未能盈利，如果考虑长期在这里工作的话主要风险是不知道公司哪天倒闭。

---

以为就漏写了“一点”，结果哐哐又啰嗦了这么多，第二家客户就只有下一期再写了，下一家客户是 to B 的医保管理公司。






