# Common Chinese Characters Learner

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/gandli/chinese-characters-learner/blob/master/LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/gandli/chinese-characters-learner)](https://github.com/gandli/chinese-characters-learner/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/gandli/chinese-characters-learner)](https://github.com/gandli/chinese-characters-learner/issues)

## English | [中文](README_zh.md)

A web-based tool for learning the 7,000 common Chinese characters from the *Modern Chinese Common Character Table*. Supports four modes: **Learn, Review, Quiz, and Challenge**, with statistical estimation of character mastery.

## Features

### 1. Learn Mode
- Click to display a random character with its **pinyin** and **definition**.
- Optional **audio pronunciation**.

### 2. Review Mode
- Displays characters learned in the current session.
- Click a character to show its definition.
- Toggle to show/hide unmastered characters (grayed out).

### 3. Quiz Mode
- Tests pinyin input for characters in the current batch.
- Supports **multi-tone characters** (any correct pinyin is accepted).
- Green/red feedback for correct/incorrect answers.

### 4. Challenge Mode
- Randomly selects characters from the full 7,000-character set.
- Estimates your **character mastery** using **sampling without replacement** and **normal distribution confidence intervals**.
- The more you answer, the more accurate the estimate.

## Tech Stack
- **Frontend**: Vue 3 / React + Vite
- **Data**: JSON (character data) + IndexedDB (user progress)
- **Stats**: ECharts / Chart.js (for challenge mode visualization)
- **Deployment**: Static hosting (Vercel/Netlify/GitHub Pages)

## Getting Started
1. Clone the repo:
   ```bash
   git clone https://github.com/gandli/chinese-characters-learner.git
   cd chinese-characters-learner
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run the dev server:
   ```bash
   npm run dev
   ```

## Data Source
- Character data from [Zdic.net](https://www.zdic.net/) or open-source datasets like [chinese-dictionary](https://github.com/skishore/chinese-dictionary).

## License
MIT