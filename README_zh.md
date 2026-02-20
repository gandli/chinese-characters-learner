# 七千通用汉字学习工具

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/gandli/chinese-characters-learner/blob/master/LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/gandli/chinese-characters-learner)](https://github.com/gandli/chinese-characters-learner/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/gandli/chinese-characters-learner)](https://github.com/gandli/chinese-characters-learner/issues)

## [English](README.md) | 中文

基于《现代汉语通用字表》的七千汉字学习工具，支持**学习、复习、测验、挑战**四大模式，并通过统计学方法估计用户的识字量。

## 功能介绍

### 1. 学习模式
- 点击字框显示随机汉字，附带**拼音**和**释义**。
- 支持**语音播报**（可选）。

### 2. 复习模式
- 显示本批次学过的汉字及其读音。
- 点击某个字，下方显示其详细释义。
- 遍历显示所有学过的字。
- 切换开关：显示/隐藏未掌握的字（灰色标记）。

### 3. 测验模式
- 依次显示本批次汉字，要求用户输入拼音（可不带音调）。
- **多音字**：输入任一正确读音即算正确。
- 反馈机制：
  - 正确：绿色字体 + 显示所有正确读音。
  - 错误：红色字体 + 显示正确读音。
- 按回车或点击字块进入下一题。

### 4. 挑战模式
- 从七千字全库中随机抽取汉字，用户输入拼音。
- 基于**无放回抽样**和**正态分布近似**，估计用户的**识字量**。
- 答题量越大，估计越精确。
- 如果答完七千字，置信区间宽度为零（100%掌握）。

## 技术栈
- **前端**：Vue 3 / React + Vite
- **数据**：JSON（汉字数据） + IndexedDB（用户进度）
- **统计图**：ECharts / Chart.js（挑战模式可视化）
- **部署**：静态托管（Vercel/Netlify/GitHub Pages）

## 快速开始
1. 克隆仓库：
   ```bash
   git clone https://github.com/gandli/chinese-characters-learner.git
   cd chinese-characters-learner
   ```
2. 安装依赖：
   ```bash
   npm install
   ```
3. 启动开发服务器：
   ```bash
   npm run dev
   ```

## 数据来源
- 汉字数据来自[汉典网](https://www.zdic.net/)或开源数据集（如[chinese-dictionary](https://github.com/skishore/chinese-dictionary)）。

## 许可证
MIT