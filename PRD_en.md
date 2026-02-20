# Common Chinese Characters Learner - Product Requirements Document (PRD)

## 1. Project Overview
**Project Name**: Common Chinese Characters Learner
**Description**: A web-based tool for learning the 7,000 common Chinese characters from the *Modern Chinese Common Character Table*. Supports four modes: **Learn, Review, Quiz, and Challenge**, with statistical estimation of character mastery.

## 2. Core Features
### 2.1 Learn Mode
- **Interaction Logic**:
  - Click a character box to display a random character from the 7,000-character set.
  - Shows the character's **pinyin** and **definition**.
  - Optional **audio pronunciation**.
- **Data Source**:
  - Imported from [Zdic.net](https://www.zdic.net/) or open-source datasets like [chinese-dictionary](https://github.com/skishore/chinese-dictionary).

### 2.2 Review Mode
- **Interaction Logic**:
  - Displays characters learned in the current session, with their pinyin.
  - Click a character to show its detailed definition.
  - Traverse through learned characters by clicking the large character display.
  - Toggle to show/hide unmastered characters (grayed out).
- **Data Management**:
  - Tracks user progress and mastery status (e.g., "mastered" or "needs review").

### 2.3 Quiz Mode
- **Interaction Logic**:
  - Randomly or sequentially displays characters from the current batch.
  - User inputs pinyin (tones optional).
  - **Multi-tone Characters**: Any correct pinyin is accepted.
  - **Feedback**:
    - Correct: Green text + displays all correct pinyin.
    - Incorrect: Red text + displays correct pinyin.
  - Navigate with Enter or by clicking the character box.

### 2.4 Challenge Mode
- **Statistical Principle**:
  - Randomly selects characters from the full 7,000-character set.
  - Estimates user's character mastery based on the proportion of correct answers.
  - **Confidence Interval**: Uses normal distribution approximation.
  - The more questions answered, the more accurate the estimate.
- **Data Visualization**:
  - Real-time display of **accuracy rate**, **estimated mastery**, and **confidence interval**.

## 3. Technical Implementation
### 3.1 Frontend Framework
- **Recommended**: Vue 3 + Vite (lightweight, responsive, easy to use).
- **UI Components**:
  - Character cards (display character, pinyin, definition).
  - Input fields (quiz/challenge mode).
  - Progress bars (review mode).
  - Charts (ECharts/Chart.js for challenge mode visualization).

### 3.2 Data Management
- **Character Data**:
  - Formatted as JSON:
    ```json
    {
      "character": "汉",
      "pinyin": ["hàn", "hán"],
      "definition": "1. The Chinese people. 2. Man..."
    }
    ```
- **User Data**:
  - Stored in IndexedDB (learning progress, quiz history).
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
- **Pure Frontend**: Use static JSON files + frontend logic if data volume is small.
- **Backend Service** (for cloud sync):
  - Use Supabase or Firebase to store user data.
  - API Endpoints:
    - `GET /chars`: Fetch character list.
    - `POST /progress`: Update learning progress.

### 3.4 Pinyin Validation Logic
- **Multi-tone Handling**:
  - Store all possible pinyin (e.g., "行": xíng/háng).
  - User input matches any correct pinyin.
- **Fuzzy Matching**:
  - Ignore tones (e.g., input `han` matches `hàn/hán`).
  - Regex validation:
    ```javascript
    const isCorrect = userInput === charData.pinyin.some(pinyin => pinyin.replace(/[1-4]/g, ''));
    ```

### 3.5 Statistical Estimation (Challenge Mode)
- **Sampling Without Replacement**:
  - Randomly sample from N=7,000 characters.
  - Estimate mastery proportion: `p = k/n` (k = correct answers, n = total questions).
  - **Confidence Interval** (95%):
    ```javascript
    const z = 1.96; // 95% confidence level
    const marginOfError = z * Math.sqrt(p * (1 - p) / n);
    const confidenceInterval = [p - marginOfError, p + marginOfError];
    ```
- **Mastery Estimation**:
  - Estimated characters known: `estimated = p * 7000`.
  - Confidence interval: `[7000*(p - marginOfError), 7000*(p + marginOfError)]`.

## 4. Prototype Development Steps
1. **Data Preparation**:
   - Scrape or manually curate 7,000-character JSON dataset.
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
   - Use Vue 3 to build the interface:
     ```bash
     npm create vue@latest hanzi-learner
     cd hanzi-learner
     npm install echarts
     ```
   - Core components:
     - `CharCard.vue`: Character display.
     - `QuizInput.vue`: Quiz input field.
     - `StatsChart.vue`: Challenge mode stats.

3. **Interaction Logic**:
   - Learn: Random character + pinyin/definition.
   - Review: Traverse learned characters, toggle unmastered visibility.
   - Quiz: Validate pinyin input, provide feedback.
   - Challenge: Random sampling, calculate confidence intervals.

4. **Deployment**:
   - Static hosting: Vercel/Netlify/GitHub Pages.
   - Backend (optional): Supabase/Firebase.

## 5. Extensions (Optional)
- **Generate Calligraphy Sheets**: Export learned characters as PDF (using [pdf-lib](https://pdf-lib.js.org/)).
- **Voice Input**: Support voice-to-text for pinyin (Web Speech API).
- **Social Sharing**: Share progress on WeChat/Weibo.
- **Mini Program**: Develop a WeChat mini program using UniApp/Taro.

## 6. Next Steps
1. **Data Source**:
   - Should I scrape character data from Zdic.net?
   - Or do you have an existing dataset?

2. **Tech Stack**:
   - Confirm: Vue 3 or React?
   - Need a backend (Supabase/Firebase)?

3. **Priority**:
   - Start with an MVP (e.g., Learn + Quiz modes)?

---