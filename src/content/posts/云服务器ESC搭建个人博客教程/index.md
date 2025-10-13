---
title: 云服务器ESC搭建个人博客教程
# 文章标题(必需)
published: 2025-10-13
# 文章发布日期，格式为YYYY-MM-DD
pinned: false
# 是否置顶文章，true表示置顶
description: 个人使用阿里云云服务器ESC搭建个人博客的完整步骤
# 文章描述(必需)
tags: [云服务器, 项目部署]
# 文章标签数组，用于标记文章主题
  # 分类 1：计算机基础 → Tags 选项
      # 操作系统相关：进程 / 线程、Linux 命令、内存管理、死锁、PV 操作、操作系统实验
      # 计算机网络相关：TCP/IP、HTTP/HTTPS、Socket 编程、Wireshark 抓包、子网划分
      # 数据库相关：SQL 语法、MySQL 基础、事务 ACID、索引、ER 图、数据库实验
      # 组成原理相关：CPU 架构、Cache 缓存、指令系统、IO 接口
  # 分类 2：编程语言 → Tags 选项
      # Python 相关：Python 基础、Pandas、requests、matplotlib、Django、Flask、正则表达式
      # Java 相关：Java 语法、SpringBoot、MyBatis、Maven、ArrayList/HashMap
      # C/C++ 相关：C 语言指针、C++ 类与对象、STL 容器、Linux 下 C 编程、内存泄漏
      # JavaScript 相关：JS 基础、DOM 操作、Axios、ES6 语法、Vue 基础（若侧重 JS + 前端）
  # 分类 3：开发方向尝试 → Tags 选项
      # 前端相关：HTML/CSS、Vue3、React、Pinia、Element UI、响应式布局、前端调试
      # 后端相关：SpringBoot、Node.js、RESTful API、MySQL、Redis、Postman、接口测试
      # 移动端相关：Flutter、Android Jetpack、Swift 基础、跨平台开发、App 调试
      # 通用开发：VSCode 插件、Git 基础（版本控制）、项目部署（本地）
  # 分类 4：算法与数据结构 → Tags 选项
      # 数据结构相关：链表、二叉树、栈 / 队列、哈希表、图（DFS/BFS）、堆
      # 算法相关：动态规划（DP）、贪心算法、二分查找、排序算法（快排 / 归并）、回溯算法
      # 刷题相关：LeetCode、蓝桥杯、ACM 入门、题解、递归、滑动窗口、两数之和（经典题）
  # 分类 5：项目记录 → Tags 选项
      # 课程项目相关：操作系统课设、数据库课设、计网课设、图书管理系统、进程调度模拟
      # 个人项目相关：Python 爬虫项目、Vue 个人博客、SpringBoot 接口项目、数据可视化（ECharts）
      # 竞赛项目相关：蓝桥杯省赛、ACM 区域赛、数学建模、挑战杯、项目部署
      # 项目细节：用户登录功能、MySQL 存储、bug 修复、界面设计、JSON 数据
  # 分类 6：我的日常 → Tags 选项
      # 学习方法：编程入门技巧、错题整理、知识点复盘、小组学习
      # 资源推荐：计算机书籍、B 站教程、VSCode 插件、GitHub 开源项目、Postman
      # 大学生活：实习准备、简历优化、技术面试、408 复习（考研）、期末复习、实验室经历
  # 分类 7：个性化（如 AI / 嵌入式） → Tags 选项
    # AI 方向：机器学习、深度学习、TensorFlow、PyTorch、线性回归、图像识别、数据集处理
    # 嵌入式方向：STM32、51 单片机、Arduino、C 语言嵌入式、传感器（温湿度）、STM32CubeMX
    # 其他方向（如大数据）：Hadoop、Spark、数据清洗、Hive
category: 开发方向尝试
# 文章分类，用于组织文章（计算机基础、编程语言、开发方向尝试、算法与数据结构、项目记录、我的日常、自己的个性化方面）
licenseName: "Unlicensed"
# 文章许可证名称
  # 常见的许可证名称：
  # "MIT"
  # "Apache-2.0"
  # "CC BY 4.0"
  # "CC BY-SA 4.0"
  # "Unlicensed"
author: qiqimora
# 文章作者姓名

# sourceLink: "https://github.com/zhangsan/vue3-guide"
# # 文章源链接，通常指向GitHub仓库或原始来源

draft: true
# 是否为草稿，true表示草稿，false表示正式发布
date: 2025-10-13
# 文章创建日期

# image:
#   url: './cover.jpg'
#   # 文章封面图片URL
#   alt: '文章封面'
#   # 文章封面图片替代文本

pubDate: 2025-10-13
# 文章发布日期(与published类似)

# encrypted: true
# # 是否加密文章，true表示加密
# password: 'qiqimora'
# # 文章加密密码
---

<!-- 管理图片和其他资源
使用这种方法，你可以将文章相关的所有资源都放在同一个文件夹中，便于管理：

src/content/posts/my-complex-post/
├── index.md
├── image1.png
├── image2.jpg
└── data.json
在文章中引用图片时，可以直接使用相对路径：

![图片描述](image1.png)
注意像这样直接填写文件的名字,这样才能让RSS正常构建图片的路径 
-->

<!-- 下面是正文 -->
# 记录一次使用阿里云云服务器ESC搭建个人博客

在这篇文章中，我将分享我使用阿里云云服务器ESC搭建个人博客的完整步骤。

## 选择云服务器
首先，需要有一台云服务器，这里我选择了阿里云的一台2核2G的试用云服务器ESC。当然其他的云服务器大同小异，可以拿来参考。

![创建试用云服务器ESC](image1.png)
