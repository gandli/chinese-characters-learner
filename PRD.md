# 七千通用汉字学习工具 - 产品需求文档 (PRD)

## 1. 项目概述
**项目名称**：七千通用汉字学习工具
**英文名**：Common Chinese Characters Learner
**简介**：基于《现代汉语通用字表》的七千汉字学习工具，支持学习、复习、测验、挑战四大模式，帮助用户系统学习和掌握常用汉字。

## 2. 核心功能
### 2.1 学习模式
- **交互逻辑**：
  - 点击字框，随机显示一个汉字（来自七千通用汉字库）。
  - 显示该字的**读音**（拼音）和**释义**。
  - 支持**语音播报**（可选）。
- **数据来源**：
  - 从[汉典网站](https://www.zdic.net/)或开源数据集（如[chinese-dictionary](https://github.com/skishore/chinese-dictionary)）导入七千汉字的**字形、读音、释义**数据。

### 2.2 复习模式
- **交互逻辑**：
  - 显示**本批次学习过的汉字**，并标注读音。
  - 点击某个字，下方大字区域显示其**详细释义**。
  - 逐次点击下方大字，可**遍历显示所有学过的字**。
  - **切换开关**：显示/隐藏「未掌握的字」（灰色标记）。
- **数据管理**：
  - 记录用户的**学习进度**和**掌握状态**（如「已掌握」「待复习」）。

### 2.3 测验模式
- **交互逻辑**：
  - 随机或按顺序显示**本批次汉字**，要求用户输入拼音（可不带音调）。
  - **多音字**：输入任一正确读音即算正确。
  - **反馈机制**：
    - 输入正确：字体显示**绿色** + 显示所有正确读音。
    - 输入错误：字体显示**红色** + 显示正确读音。
  - **导航**：按回车或点击字块进入下一题。

### 2.4 挑战模式
- **统计学原理**：
  - 从**七千字全库**中随机抽取汉字，用户输入拼音。
  - 基于**无放回简单随机抽样**，根据答对比例估计用户的**识字量**。
  - **置信区间**：使用正态分布近似计算，答题量越大，估计越精确。
  - 如果用户答完七千字，置信区间宽度为零（100%掌握）。
- **数据可视化**：
  - 实时显示**答对率**、**估计识字量**、**置信区间**。

## 3. 技术方案
### 3.1 前端框架
- **推荐**：Vue 3 + Vite（轻量、响应式、易上手）。
- **UI 组件**：
  - 字卡片（显示汉字、读音、释义）。
  - 输入框（测验/挑战模式）。
  - 进度条（复习模式）。
  - 统计图表（挑战模式，使用 ECharts 或 Chart.js）。

### 3.2 数据管理
- **汉字数据**：
  - 格式化为 JSON：
    ```json
    {
      "字": "汉",
      "拼音": ["hàn", "hán"],
      "释义": "1. 中华民族的通称。2. 男人。..."
    }
    ```
- **用户数据**：
  - 使用 IndexedDB 存储学习进度、答题记录。
  - 示例：
    ```json
    {
      "userId": "123",
      "learnedChars": ["汉", "字", "学"],
      "mastery": {"汉": true, "字": false},
      "testHistory": [{"char": "汉", "input": "han", "correct": true}]
    }
    ```

### 3.3 后端（可选）
- **纯前端**：如果数据量小，可直接使用静态 JSON 文件 + 前端逻辑。
- **后端服务**（如需云同步）：
  - 使用 Supabase 或 Firebase 存储用户数据。
  - API 接口：
    - `GET /chars`：获取汉字列表。
    - `POST /progress`：更新学习进度。

### 3.4 拼音验证逻辑
- **多音字处理**：
  - 预存所有可能的拼音（如「行」：xíng/háng）。
  - 用户输入任一正确拼音即算正确。
- **模糊匹配**：
  - 忽略音调（如输入 `han` 匹配 `hàn/hán`）。
  - 使用正则表达式验证：
    ```javascript
    const isCorrect = userInput === charData.拼音.some(pinyin => pinyin.replace(/[1-4]/g, ''));
    ```

### 3.5 统计学估计（挑战模式）
- **无放回抽样**：
  - 从 N=7000 的总体中随机抽取 n 个样本（汉字）。
  - 答对 k 个，估计掌握比例：`p = k/n`。
  - 置信区间（95%）：
    ```javascript
    const z = 1.96; // 95%置信水平
    const marginOfError = z * Math.sqrt(p * (1 - p) / n);
    const confidenceInterval = [p - marginOfError, p + marginOfError];
    ```
- **识字量估计**：
  - 估计掌握字数：`estimated = p * 7000`。
  - 置信区间：`[7000*(p - marginOfError), 7000*(p + marginOfError)]`。

## 4. 原型开发步骤
1. **数据准备**：
   - 抓取或手动整理七千汉字的 JSON 数据。
   - 示例脚本（Python）：
     ```python
     import requests
     from bs4 import BeautifulSoup

     def scrape_zdic(char):
         url = f"https://www.zdic.net/hans/{char}"
         response = requests.get(url)
         soup = BeautifulSoup(response.text, 'html.parser')
         pinyin = soup.select_one('.z_d2_py').text
         definition = soup.select_one('.z_d2_k').text
         return {"字": char, "拼音": [pinyin], "释义": definition}
     ```

2. **前端搭建**：
   - 使用 Vue 3 搭建基本界面：
     ```bash
     npm create vue@latest hanzi-learner
     cd hanzi-learner
     npm install echarts
     ```
   - 核心组件：
     - `CharCard.vue`：显示汉字卡片。
     - `QuizInput.vue`：测验输入框。
     - `StatsChart.vue`：挑战模式统计图。

3. **交互逻辑**：
   - 学习模式：随机显示汉字 + 读音/释义。
   - 复习模式：遍历已学汉字，支持隐藏/显示未掌握字。
   - 测验模式：验证拼音输入，给出反馈。
   - 挑战模式：随机抽取汉字，计算置信区间。

4. **部署**：
   - 静态部署：Vercel/Netlify/GitHub Pages。
   - 后端（可选）：Supabase 或 Firebase。

## 5. 扩展功能（可选）
- **生成字帖**：将学过的汉字生成 PDF 字帖（使用 [pdf-lib](https://pdf-lib.js.org/)）。
- **语音识别**：支持语音输入拼音（使用 Web Speech API）。
- **社交分享**：分享学习进度到微信/微博。
- **小程序版本**：使用 UniApp 或 Taro 开发微信小程序。

## 6. 如何开始？
1. **确认数据源**：
   - 是否需要我帮忙抓取汉典网站的七千汉字数据？
   - 或者老板已有现成的数据文件？

2. **技术栈选择**：
   - 喜欢用 **Vue** 还是 **React**？
   - 需要后端吗？还是纯前端？

3. **优先级**：
   - 是否先实现一个 **MVP**（如仅学习+测验模式）？

---