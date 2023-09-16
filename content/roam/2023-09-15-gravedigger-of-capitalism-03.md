---
title: 打工记（三）
author: 云五
type: post
date: 2023-09-16T01:34:32+00:00
description: |
  在深红州支持 LGBTQ+ 的 healthcare startup
url: /roam/gravedigger-of-capitalism-03/
categories:
  - 漫游
tags:
  - 打工
  - 草台班子
  - 临时工
  - contractor
  - 搬砖

---

再来说第二家公司，为了简便以后就叫Company B 吧！第一家算 Company A，希望我顺顺利利打工一直打到 Company Z。

## Company B 概况

Company B 是一家规模还不到100人的Startup，他们做的是 to B 的业务，也就是说他们的核心业务是向公司收钱的——为中小公司提供员工医保方面的管理工作。根据我从其他方面的观察，这几年美国这一类的公司还挺红火的，稍微做起来不愁投资。Company B 是新冠爆发前成立的，种子轮拿到一笔还不错的投资，A轮融资金额是种子轮的10+倍，按行业平均情况粗略估计的话，A轮融资的估值大概在 50m USD 左右。

我去 Company B 是在它们种子轮之后、A轮之前，他们招做Python 后端的人，提的需求和我进去之后做的工作非常吻合高度一致。 Company B 一口气招了好几个合同工，我们每周和一个程序员对接交代我们要做的任务。我在 LinkedIn 上查了一下这个程序员，他毕业后就在 Company B 工作，对公司的业务和结构都非常熟悉，即使不到三年的工作经验，也会给他较大的工作职责，让他来带队负责业务逻辑的重构。这是在小公司的一个优势（但也不是说 startup 里人人都有这样的机会）。

这家公司的业务较为红火，经常在 Slack 里看到 Sales 部门宣布又和什么什么公司谈妥了合同；可能也是因为业务比较好，其他方面就没有太突出，比如他们的系统结构设计就远远不如第一家经过迭代后迭代数次后的稳固（也是正常现象，毕竟一个还在A轮前一个已经上市）。我进去是要做系统重构后的实现，不知道早期系统是什么人做的，可以说从 microservices 设计到 CI/CD 都非常“别具一格”，很像是早期招不到人随便拎了几个人就上，会什么就用什么，又或者搜到什么就拼到一起凑合着用，现在终于发现不太行要重构了……

## 啊！美国的医保！

在这家的打工过程里发现美国的医保制度是真的十分复杂，我们每天要写的新码里包括这些医保管理的逻辑实现（比如一个员工挑选的保险级别里包含什么保险、各种保险里 deductible 多少、是否有配偶子女、一个人有多种医保时优先级如何等等）。有些逻辑即使是公司工作一两年的程序员也不清楚，所以听到象友们吐槽美国的医保制度要扯皮来扯皮去就非常能够理解——因为很有可能为你做医保管理的公司自己都没有那么清楚。

## Company B 的杂七杂八

这家公司总共不到100人，程序员的规模可能只有二三十。给我留下深刻印象的一件事是：在公司 Slack 频道里看到这一年的LGBT Pride 活动的宣传，公司支持员工参与游行活动，给员工的配偶报销路费。作为一个深红州里的startup，有这样严肃的表态，应当还是管理团队的坚持。

另一方面就不得不提到startup 在扩张阶段的管理混乱了，这一家给我的时薪是我打过的几份临时工里最高的（但几份活的时薪差异本身不大），活也是最少的，同时管理也是最混乱的。经常分配了活，做了几天后突然遇到其他问题，让我们停一下，非常“摸着石头过河”，还有时候它们也不知道有什么活要给我做。我猜他们业务确实不错，不然不至于有这么多钱拿来霍霍，在我有两周实在没什么活可做后，他们终于终止了我的合同。这份临时工总结一下可以说是闭着眼睛做的，我自己要写的部分完全是我用熟的东西，也没什么新东西要学；除了写码也没给我什么其他权限，所以我什么文档都看不到（也怀疑到底有多少文档，因为有一次在需求太不明确时我问对接的程序员有没有需求文档，对方说还没定下来……

## 有一点漫长的 gap

这份临时工结束时有点累，因为连续做了这么久的活，我想好好休息一阵再物色新的合同。此时我还没有意识到 The Great Layoff （名字我自己编的，如有雷同，纯属雷同）的严重性，2022年的夏天开始，科技行业一波又一波的裁员开始了，市场上有大量的求职者，公司也在反覆考量自己预算到底有多少。

我休息好了想要重新开始找活的时候就遭遇了很多不顺利：之前的两家公司都是非常轻易谈定的，要找第三个合同工时，我连续面了好多客户，有两家都是临门一脚又飞了。一家是两轮面试都相谈甚欢，第二轮两个面试官跟我说接下来只是走流程的事情，他们两个人都觉得我的经验和技术和他们是最 match 的；第二天就杳无音讯了，又过了一天收到拒信没有任何原因。

还有一家是澳洲的农业科技公司，我从来没有接触过这类公司，很想去看看农业科技公司到底是做什么的。他们的 CTO 和我聊过后觉得我的技术经验也是吻合的，结果过了两周 recruiter 通知我说这家公司的职位取消了。





