<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Quiz Game</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background-color: #f4f4f4;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
    }

    .main-container, #quiz-container, #leaderboard {
      text-align: center;
      max-width: 800px;
      width: 100%;
      margin: 0 auto;
    }

    h1 {
      font-size: 36px;
      margin-bottom: 20px;
    }

    h2 {
      font-size: 28px;
      margin-bottom: 10px;
    }

    button {
      font-size: 18px;
      padding: 10px 20px;
      margin: 10px;
      cursor: pointer;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
      transition: background-color 0.3s ease;
    }

    button:hover {
      background-color: #45a049;
    }

    .hidden {
      display: none;
    }

    .category-btn {
      background-color: #3498db;
    }

    .option-btn {
      width: 100%;
      margin: 5px;
      padding: 15px;
      cursor: pointer;
      font-size: 18px;
      background-color: #f1f1f1;
      border: 2px solid #3498db;
      border-radius: 5px;
      transition: all 0.3s ease;
    }

    .option-btn:hover {
      background-color: #3498db;
      color: white;
    }

    .lifeline-btn {
      background-color: #e74c3c;
    }

    #score {
      margin-top: 20px;
      font-size: 20px;
      font-weight: bold;
    }

    #leaderboard-list {
      list-style-type: none;
      padding: 0;
    }

    #leaderboard-list li {
      font-size: 18px;
    }

    .result-container {
      font-size: 22px;
      margin-top: 20px;
      padding: 20px;
      border-radius: 5px;
      display: inline-block;
      margin-top: 40px;
      opacity: 0;
      transition: opacity 1s ease-in-out;
    }

    .result-container.correct {
      background-color: #2ecc71;
      color: white;
    }

    .result-container.incorrect {
      background-color: #e74c3c;
      color: white;
    }

    .points-earned {
      font-size: 24px;
      font-weight: bold;
    }

    #next-btn {
      background-color: #2ecc71;
      color: white;
      border-radius: 5px;
      font-size: 20px;
      padding: 15px 20px;
      cursor: pointer;
    }

    #next-btn:hover {
      background-color: #27ae60;
    }

    .result-container.visible {
      opacity: 1;
    }

    #leaderboard {
      background-color: #fff;
      padding: 20px;
      border-radius: 10px;
    }

    #leaderboard h2 {
      font-size: 32px;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <div class="main-container">
    <h1>Welcome to the Quiz Game</h1>
    <div class="categories">
      <button class="category-btn" onclick="startQuiz('football')">Football Quiz</button>
      <button class="category-btn" onclick="startQuiz('cricket')">Cricket Quiz</button>
      <button class="category-btn" onclick="startQuiz('political')">Political Quiz</button>
      <button class="category-btn" onclick="startQuiz('country')">Country Quiz</button>
    </div>

    <div id="quiz-container" class="hidden">
      <h2 id="category-title"></h2>
      <div id="question-container"></div>
      <div id="options-container"></div>
      <button id="next-btn" class="hidden" onclick="nextQuestion()">Next</button>
      <div id="score"></div>
      <div class="result-container hidden" id="result-container"></div>
    </div>

    <div id="leaderboard" class="hidden">
      <h2>Leaderboard</h2>
      <ul id="leaderboard-list"></ul>
    </div>
  </div>

  <script>
    let leaderboard = []; // Store player scores for leaderboard
    const quizData = {
      cricket: [
        { question: "Which team won the 2023 ICC Cricket World Cup?", options: ["India", "Australia", "England", "Pakistan"], answer: "Australia" },
        { question: "Who holds the record for most runs in ODI cricket?", options: ["Sachin Tendulkar", "Virat Kohli", "Ricky Ponting", "Brian Lara"], answer: "Sachin Tendulkar" },
        { question: "Who has the most wickets in ODI cricket?", options: ["Shane Warne", "Muttiah Muralitharan", "Glenn McGrath", "Wasim Akram"], answer: "Muttiah Muralitharan" },
        { question: "Which team won the 2019 ICC Cricket World Cup?", options: ["England", "Australia", "New Zealand", "India"], answer: "England" },
        { question: "Who is the fastest to 8000 runs in ODI cricket?", options: ["Virat Kohli", "AB de Villiers", "Chris Gayle", "Ricky Ponting"], answer: "Virat Kohli" },
        { question: "Which cricketer is known as the 'Master Blaster'?", options: ["Sachin Tendulkar", "Brian Lara", "Shahid Afridi", "AB de Villiers"], answer: "Sachin Tendulkar" },
        { question: "Which country hosted the 2011 Cricket World Cup?", options: ["India", "South Africa", "Australia", "Pakistan"], answer: "India" },
        { question: "Which cricketer has the most sixes in ODI cricket?", options: ["Shahid Afridi", "Chris Gayle", "MS Dhoni", "Martin Guptill"], answer: "Shahid Afridi" },
        { question: "Which country has won the most Cricket World Cups?", options: ["Australia", "India", "West Indies", "England"], answer: "Australia" },
        { question: "Who is known as the 'Wall' of Indian cricket?", options: ["Virat Kohli", "Sachin Tendulkar", "Rahul Dravid", "MS Dhoni"], answer: "Rahul Dravid" },
      ],
      football: [
        { question: "Which team won the 2022 FIFA World Cup?", options: ["France", "Argentina", "Brazil", "Germany"], answer: "Argentina" },
        { question: "Who is known as the King of Football?", options: ["Pele", "Messi", "Ronaldo", "Maradona"], answer: "Messi" },
        { question: "Which club does Cristiano Ronaldo play for?", options: ["Real Madrid", "Juventus", "Manchester United", "Al Nassr"], answer: "Al Nassr" },
        { question: "Who won the Ballon d'Or in 2020?", options: ["Cristiano Ronaldo", "Lionel Messi", "Robert Lewandowski", "Virgil Van Dijk"], answer: "Lionel Messi" },
        { question: "Who won the 2014 FIFA World Cup?", options: ["Brazil", "Argentina", "Germany", "France"], answer: "Germany" },
        { question: "Which country hosted the 2018 FIFA World Cup?", options: ["Russia", "Brazil", "South Africa", "Germany"], answer: "Russia" },
        { question: "Who is the all-time top scorer in the FIFA World Cup?", options: ["Pele", "Miroslav Klose", "Ronaldo", "Messi"], answer: "Miroslav Klose" },
        { question: "Who won the UEFA Champions League 2020?", options: ["Bayern Munich", "Barcelona", "Liverpool", "Real Madrid"], answer: "Bayern Munich" },
        { question: "Who scored the fastest goal in World Cup history?", options: ["Lionel Messi", "Hakan Şükür", "Clint Dempsey", "David Beckham"], answer: "Hakan Şükür" },
        { question: "Which club won the Premier League in 2020?", options: ["Manchester City", "Liverpool", "Chelsea", "Arsenal"], answer: "Liverpool" },
      ],
      // Similar structure for political and country quizzes
    };

    let currentCategory = '';
    let currentQuestionIndex = 0;
    let score = 0;
    let playerName = prompt("Enter your name:");

    // Start quiz based on category
    function startQuiz(category) {
      currentCategory = category;
      score = 0;
      currentQuestionIndex = 0;
      document.querySelector('.categories').classList.add('hidden');
      document.querySelector('#quiz-container').classList.remove('hidden');
      document.querySelector('#category-title').textContent = category.charAt(0).toUpperCase() + category.slice(1) + " Quiz";
      loadQuestion();
    }

    // Load the current question
    function loadQuestion() {
      const questionData = quizData[currentCategory][currentQuestionIndex];
      document.querySelector('#question-container').textContent = questionData.question;
      const optionsContainer = document.querySelector('#options-container');
      optionsContainer.innerHTML = '';
      questionData.options.forEach(option => {
        const btn = document.createElement('button');
        btn.classList.add('option-btn');
        btn.textContent = option;
        btn.onclick = () => checkAnswer(option, questionData.answer);
        optionsContainer.appendChild(btn);
      });
      document.querySelector('#next-btn').classList.add('hidden');
    }

    // Check answer and show result
    function checkAnswer(selected, correctAnswer) {
      const resultContainer = document.querySelector('#result-container');
      resultContainer.classList.remove('hidden');
      if (selected === correctAnswer) {
        resultContainer.textContent = "Correct! Well done!";
        resultContainer.classList.add('correct');
        score += 10;
      } else {
        resultContainer.textContent = `Incorrect! The correct answer was: ${correctAnswer}`;
        resultContainer.classList.add('incorrect');
      }
      document.querySelector('#score').textContent = `Your score: ${score}`;
      document.querySelector('#next-btn').classList.remove('hidden');
    }

    // Next question
    function nextQuestion() {
      currentQuestionIndex++;
      if (currentQuestionIndex < quizData[currentCategory].length) {
        loadQuestion();
        document.querySelector('#result-container').classList.add('hidden');
      } else {
        endGame();
      }
    }

    // End game and display leaderboard
    function endGame() {
      leaderboard.push({ name: playerName, score: score });
      leaderboard.sort((a, b) => b.score - a.score);
      document.querySelector('#quiz-container').classList.add('hidden');
      document.querySelector('#leaderboard').classList.remove('hidden');
      displayLeaderboard();
    }

    // Display leaderboard
    function displayLeaderboard() {
      const leaderboardList = document.querySelector('#leaderboard-list');
      leaderboardList.innerHTML = '';
      leaderboard.slice(0, 5).forEach((entry, index) => {
        const li = document.createElement('li');
        li.textContent = `${entry.name}: ${entry.score} Points`;
        leaderboardList.appendChild(li);
      });
    }
  </script>
</body>
</html>
