---
title: 持续交付的实践与思考
date: 2017-03-09 16:36:48
tags: []
author: xizhibei
---
一直以来都在做团队里的基础性工作，直到最近，成果开始慢慢展现，尤其是上周刚看完《持续交付》这本书后，总结已经做的工作，又有了些新的感悟。

过去一段时间，我都是围绕着 Gitlab 的一些功能来开展的，从最开的代码与应用配置管理，然后到 CI 系统，提升代码质量，到最后完整的持续交付流程提升团队工作效率。

那么我就结合《持续交付》这本书的内容，正文开始。

### 为什么要有持续交付
1. 不敢发布新功能：每次发布都会心惊胆战，因为一下子发布了太多功能，测试又没做好，而这时候产品催着要上线。另外按照以往的经验，由发布而出问题的的概率最高；
2. 线上事故处理时间长：每次出线上事故，不能很快定位问题，如果是由于同事线上改动了什么，就更难定位问题了，而在之后大家可能会狠狠责怪那个同事，事实上，他也很无辜，因为没有人告诉他什么能做，什么不能做，或者该怎么做；
3. 发布周期太长：由于每次功能完全开发完之后才会发布上线，而如果涉及到数据库更改或者迁移，发布周期可能会拖得太长；
4. 协作问题：比如新人来到团队时，由于被限制了各种线上的权限，尤其是发布权限，会导致很多工作开展不顺利，而每次发布求助于其他同事的话，会造成沟通问题，进一步造成了协作上的问题。另一个，往往由于习惯于口头上说下要发布了，然后发布过程对其他人而言是不可见的，若是线上还需要操作些什么的话，几乎对其他人是更不可见的；

### 持续交付能做到什么
1. 让软件构建、部署、测试和发布过程对所有人可见：由此，大家可以学习过程，了解其他人做了什么，在一个规范的流程中工作，促进协作；
2. 改善反馈，更早发现问题：在一个完整的流水线中，可以设置多个『检查点』，简答来说，就是加入多个测试环节，保证代码质量，而在这个过程中，越早发现问题，修复的成本越低；
3. 通过一个完全自动化的过程在任意环境上部署和发布软件的任意版本：在陈浩大神的 [《从 Gitlab 误删除数据库想到的》](http://coolshell.cn/articles/17680.html) 这篇文章里，提到：* 人管代码，代码管机器 *，其实持续交付就能达到这个效果。并且在一种极端情况下，假如你使用的云服务商挂了，你也能在其它云服务商上面快速重建你的整个线上系统（当然，没那么简单）。

### 持续交付的几个原则
1. 为软件发布创建一个可重复且可靠的过程：一键发布与回滚，如果发布出了问题，你几乎可以确定不是发布脚本本身的问题，因为这个脚本已经经过多次检验；
2. 将几乎所有的事情自动化：手工发布及漫长又不可靠，采用自动化脚本，节省时间，节省精力；
3. 把所有的东西纳入版本控制：采用变更管理，方便审计，将所有的变更都在掌控之中，出问题可以快速定位问题；
4. 提交并频繁做让你感到痛苦的事情：小步快走，分散痛苦与风险，
5. 提高内建质量：越早发现问题，修复成本越低，等 QA 或者用户告诉你应用有问题的时候，修复成本非常高，也增加了你计划外的工作；
6. 『Done』意味着『已发布』：没有完成百分之多少这种说法，反推着你将任务分解，将功能尽快发布到生成环境中去，减少了产品反馈时间，提高了竞争力，也减少了风险；
7. 交付是每个成员的责任：流程标准，每个人都可以参与到流程的每一个部分，促进协作，也帮助新人适应与提高；
8. 持续改进：不断演进，改善发布流程，这个流程需要持续的改进，提高吞吐，增加产量，提高质量；

### 持续交付的实践
在目前的团队持续交付流程中，我们在每个环节的实践如下：

1. 提交：单元测试 mocha/ava，以及测试覆盖率 nyc；
2. 编译打包：即打包 docker 镜像，推送到 registry ，目前还没有完全引入 docker 集群，因此部署过程省略；
3. 部署文档：用 ansible 脚本自动将 api 文档以及测试覆盖情况部署至静态网页文档服务器，这个时候可以直接将分支对应的文档给客户端开发，还可以直接查看测试覆盖率情况，确认与改进单元测试；
4. Review：即提交 Merge Request，大家一起 Code Review；
5. 部署应用：需要 Review 通过后，将代码合并至 master 分支，然后手工点击按钮部署；
6. 回滚：假如应用出问题，可以点击按钮一键回滚；

可以看到，目前主要是缺失预发布环境，这一步还需要将环境搭建脚本完善之后才能做到，而目前公司运维团队还是与我们开发团队分离的，因此达到相要的效果还是得做些努力。

#### 总结
目前我已经做到：将应用配置单独管理起来，以及部署相关的脚本都已经实现，因此才能很快地将持续交付流程跑起来。其实可以这么说：应用配置管理与自动部署脚本是持续交付流程的前提与基础。

而持续交付流程一旦完善了，能极大改善我们开发团队的交付能力，甚至倒逼产品与运维团队提高他们的效率：

- 对于产品团队，我们能要求他们将需求拆分尽可能的细，一旦开发完成我们能快速部署，于是他们能更快得到反馈，从而提高产品的试错能力。
- 对于运维团队，我们已经将部署流程优化了，于是他们能更专心于基础设置的维护与改善，同时也可以促进协作，推行全面的配置管理。
 
### Reference
1. https://about.gitlab.com/direction/
2. https://about.gitlab.com/2016/08/26/ci-deployment-and-environments/
3. https://github.com/everpeace/concourse-gitlab-flow
4. https://docs.gitlab.com/ce/ci/environments.html
5. https://about.gitlab.com/2016/08/05/continuous-integration-delivery-and-deployment-with-gitlab/



***
原链接: https://github.com/xizhibei/blog/issues/42