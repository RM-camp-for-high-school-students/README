# 疆来计划 - GitHub Organization

[toc]

## 概述

该组织是面向所有假期营营员和助教的**私有**组织。主要负责前期协作开发过程中的**代码托管**、营期中的**代码维护**和最后的代码**汇总**。

为什么私有？公有库会被~~公司网安抓~~啊。。。

为什么要用这玩意？在助教的前期准备过程中，不免需要使用git进行协作开发。在营期过程中，需要对bug随时修复并快速同步到营员。而gitee的私有仓库有协作人员数量限制，无法在后期使用。



## 权限管理

简单来说，组织中的权限管理分为3级

- Organisation Owner（最高权限）
  - 可以邀请成员并配置权限
  - 可以创建Team并分配repo，设置repo权限
  - 可以增减Team中的人员
- Team tutor（普通助教）
  - 可以查阅、修改所有往期假期营的代码
- Team member（营员）
  - 可以查看代码、提交issue



## 最高权限手册

### 职责

负责邀请助教和营员进入组织，进入Team，相应的配置各Team不同的repo权限。详细见下。

### 权限索取

为保障安全，原则上尽可能少的分配Owner权限。可以通过联系已经是Owner的进行设置。保证每次营期中都有1位owner来处理各种情况。

### ==注意==

每位Owner都有权限赋予和废除任何一个人Owner的权限，因此，非特殊情况不要废除他人Owner的权限。

### 1、人员邀请&分配Team

收集营员和助教的github用户名或邮箱。

进入Teams页面。

**营员**

1. 为当年营员新建一个Team，命名遵循（年份+wc/sc+_member)，并填写简介。
2. 在Team（`e.g. 2022wc_member`）页面中的Member中，选择`add a member`，根据邮箱或用户名邀请用户
3. 营员接受后自动加入团队![image-20220129233344790](https://s2.loli.net/2022/01/29/OjB381drMyp2Hna.png)

**助教**

1. 所有历年助教均在名为tutor的Team中。
2. 在tutor页面中的Member中，选择`add a member`，根据邮箱或用户名邀请用户
3. 等待确认自动加入

### 2、初始化项目&权限分配

1. 新建一个Private repository（空仓库），命名中应含有年份时间等信息。
2. 在仓库的设置页面配置权限：
   - 给tutor团队赋予**Admin**权限
   - 给对应的营员团队赋予**Read**权限![image-20220129234006308](https://s2.loli.net/2022/01/29/qiB2FGcz4W5MpH7.png)
3. 配置完成



## 助教手册

### 职责

在营期准备阶段开发所需功能包、维护代码仓库（使用git会非常方便）

### 1、获得权限

为管理员提供github的用户名或邮箱，等待验证进入助教团队

### 2、新建仓库

助教可自行新建项目，但需按照`最高权限手册->2、初始化项目&权限分配`中的引导按需分配权限。

### 3、日常维护

- 使用git进行协同开发。[git 入门教程](https://github.com/RM-camp-for-high-school-students/README/blob/main/git%20%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B.md)
- 不熟悉git指令的助教可以使用github desktop for Linux，具体教程见[Github Desktop 使用说明](https://github.com/RM-camp-for-high-school-students/README/blob/main/Github%20Desktop%20%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E.md)



## 营员手册

### 职责

关注项目页面，留意代码更新。

### 1、申请加入

1. 向助教发送自己的github用户名或邮箱
2. 关注[组织页面](https://github.com/RM-camp-for-high-school-students)或邮箱状态，留意页面上方通知。![image-20220129231030616](https://s2.loli.net/2022/01/29/zKcnEQ4ZG5CNpM2.png)

### 2、下载代码

可以直接在页面中下载压缩包，也可使用`git clone	`下载。

### 3、提交issue

如发现代码问题，可以在issue页面提交问题。

### 4、开源

营员在营期结束时可自主选择是否开源，如想开源：

1. 在主页新建仓库
2. 配置权限：参考`最高权限手册->2、初始化项目&权限分配`，将权限分配给当届营员和助教
