# Common Chinese Characters Learner - Product Requirements Document (PRD)

## 1. Overview
**Project Name**: Common Chinese Characters Learner
**Description**: A web-based tool for learning the 7,000 common Chinese characters from the *Modern Chinese Common Character Table*. Supports four modes: **Learn, Review, Quiz, and Challenge**, with statistical estimation of character mastery.

## 2. Core Features
### 2.1 Learn Mode
- **Interaction**:
  - Click to display a random character with its **pinyin** and **definition**.
  - Optional **audio pronunciation**.
- **Data Source**:
  - Import 7,000 characters from [Zdic.net](https://www.zdic.net/) or open-source datasets like [chinese-dictionary](https://github.com/skishore/chinese-dictionary).

### 2.2 Review Mode
- **Interaction**:
  - Displays characters learned in the current session.
  - Click a character to show its definition.
  - Toggle to show/hide unmastered characters (grayed out).
- **Data Management**:
  - Track user progress and mastery status (e.g., "mastered" or "needs review").

### 2.3 Quiz Mode
- **Interaction**:
  - Randomly or sequentially displays characters from the current batch.
  - User inputs pinyin (tones optional).
  - **Multi-tone characters**: Any correct pinyin is accepted.
  - **Feedback**:
    - Correct: Green text + displays all correct pinyin.
    - Incorrect: Red text + displays correct pinyin.
  - Navigate with Enter or click.

### 2.4 Challenge Mode
- **Statistics**:
  - Randomly selects characters from the full 7,000-character set.
  - Estimates **character mastery** using **sampling without replacement** and **normal distribution confidence intervals**.
  - The more answers, the more accurate the estimate.
  - If all 7,000 characters are answered, the confidence interval width becomes zero (100% mastery).
- **Visualization**:
  - Real-time display of **accuracy rate**, **estimated mastery**, and **confidence interval**.

## 3. Technical Implementation
### 3.1 Frontend
- **Framework**: Vue 3 / React + Vite.
- **UI Components**:
  - Character card (displays character, pinyin, definition).
  - Input field (quiz/challenge mode).
  - Progress bar (review mode).
  - Stats chart (challenge mode, using ECharts/Chart.js).

### 3.2 Data Management
- **Character Data**:
  - Format: JSON
    ```json
    {
      "character": "汉",
      "pinyin": ["hàn", "hán"],
      "definition": "1. Chinese ethnicity. 2. Man..."
    }
    ```
- **User Data**:
  - Store progress in IndexedDB.
  - Example:
    ```json
    {
      "userId": "123",
      "learnedChars": ["汉", "字", "学"],
      "mastery": {"汉": true, "字": false},
      "testHistory": [{"char": "汉", "input": "han", "correct": true}]
    }
    ```

### 3.3 Backend (Optional)
- **Pure Frontend**: Use static JSON + frontend logic if data is small.
- **Backend Service**: Use Supabase/Firebase for cloud sync.
  - API Endpoints:
    - `GET /chars`: Fetch character list.
    - `POST /progress`: Update learning progress.

### 3.4 Pinyin Validation
- **Multi-tone Handling**: Store all possible pinyin (e.g., "行": xíng/háng).
- **Fuzzy Matching**: Ignore tones (e.g., input `han` matches `hàn/hán`).
  ```javascript
  const isCorrect = userInput === charData.pinyin.some(p => p.replace(/[1-4]/g, ''));
  ```

### 3.5 Statistical Estimation (Challenge Mode)
- **Sampling Without Replacement**:
  - Randomly sample `n` characters from `N=7000`.
  - Correct answers: `k`. Estimate mastery: `p = k/n`.
  - 95% Confidence Interval:
    ```javascript
    const z = 1.96; // 95% confidence
    const marginOfError = z * Math.sqrt(p * (1 - p) / n);
    const confidenceInterval = [p - marginOfError, p + marginOfError];
    ```
- **Mastery Estimate**:
  - Estimated characters known: `p * 7000`.
  - Confidence interval: `[7000*(p - marginOfError), 7000*(p + marginOfError)]`.

## 4. Prototype Development Steps
1. **Data Preparation**:
   - Scrape or manually curate 7,000 characters.
   - Example script (Python):
     ```python
     import requests
     from bs4 import BeautifulSoup

     def scrape_zdic(char):
         url = f"https://www.zdic.net/hans/{char}"
         response = requests.get(url)
         soup = BeautifulSoup(response.text, 'html.parser')
         pinyin = soup.select_one('.z_d2_py').text
         definition = soup.select_one('.z_d2_k').text
         return {"character": char, "pinyin": [pinyin], "definition": definition}
     ```

2. **Frontend Setup**:
   - Use Vue 3:
     ```bash
     npm create vue@latest hanzi-learner
     cd hanzi-learner
     npm install echarts
     ```
   - Core components:
     - `CharCard.vue`: Character display.
     - `QuizInput.vue`: Quiz input.
     - `StatsChart.vue`: Challenge stats.

3. **Interaction Logic**:
   - Learn: Random character + pinyin/definition.
   - Review: Traverse learned characters.
   - Quiz: Validate pinyin input.
   - Challenge: Random sampling + confidence intervals.

4. **Deployment**:
   - Static hosting: Vercel/Netlify/GitHub Pages.
   - Backend (optional): Supabase/Firebase.

## 5. Extensions (Optional)
- **Generate Calligraphy Sheets**: Export learned characters as PDF (using [pdf-lib](https://pdf-lib.js.org/)).
- **Voice Input**: Use Web Speech API for pinyin input.
- **Social Sharing**: Share progress on WeChat/Weibo.
- **Mini Program**: Develop a WeChat mini program using UniApp/Taro.

## 6. Next Steps
1. **Data Source**:
   - Need help scraping Zdic.net’s 7,000 characters?
   - Or do you have an existing dataset?

2. **Tech Stack**:
   - Prefer **Vue 3** or **React**?
   - Need a backend (Supabase/Firebase)?

3. **Priority**:
   - Start with an **MVP** (e.g., Learn + Quiz modes)?

---