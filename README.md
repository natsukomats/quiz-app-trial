<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>クイズアプリ</title>
    <style>
        body {
            font-family: sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            background-color: #f4f4f4;
            color: #333;
        }
      .quiz-container {
            background-color: #fff;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 90%;
            max-width: 500px;
        }
        h1 {
            color: #0056b3;
            margin-bottom: 20px;
        }
      .question-number {
            font-size: 1.1em;
            margin-bottom: 15px;
            color: #555;
        }
      .question-text {
            font-size: 1.5em;
            margin-bottom: 30px;
            font-weight: bold;
        }
      .options-container button {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 12px 25px;
            margin: 8px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1.1em;
            transition: background-color 0.3s ease;
            width: calc(100% - 16px); /* ボタンの幅を調整 */
            box-sizing: border-box; /* パディングとボーダーを幅に含める */
        }
      .options-container button:hover {
            background-color: #0056b3;
        }
      .options-container button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
        #result {
            margin-top: 25px;
            font-size: 1.3em;
            font-weight: bold;
        }
      .navigation-buttons button {
            background-color: #28a745;
            color: white;
            border: none;
            padding: 10px 20px;
            margin-top: 20px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            transition: background-color 0.3s ease;
        }
      .navigation-buttons button:hover {
            background-color: #218838;
        }
      .navigation-buttons button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
    </style>
</head>
<body>

    <div class="quiz-container">
        <h1>クイズアプリ</h1>
        <p class="question-number" id="question-number"></p>
        <p class="question-text" id="question-text"></p>
        <div class="options-container" id="options-container">
            </div>
        <p id="result"></p>
        <div class="navigation-buttons">
            <button id="next-button" onclick="nextQuestion()" style="display: none;">次の問題へ</button>
            <button id="restart-button" onclick="restartQuiz()" style="display: none;">もう一度プレイ</button>
        </div>
    </div>

    <script>
        const quizData = [
            {
                question: "日本で一番高い山は？",
                options: ["富士山", "南アルプス", "北アルプス"],
                answer: "富士山"
            },
            {
                question: "日本の首都は？",
                options: ["大阪", "東京", "京都"],
                answer: "東京"
            },
            {
                question: "世界で一番大きな海は？",
                options: ["大西洋", "インド洋", "太平洋"],
                answer: "太平洋"
            },
            {
                question: "太陽系の惑星で一番大きいのは？",
                options: ["地球", "木星", "火星"],
                answer: "木星"
            }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let answeredThisQuestion = false; // 現在の問題に回答済みかどうかのフラグ

        const questionNumberElement = document.getElementById('question-number');
        const questionTextElement = document.getElementById('question-text');
        const optionsContainer = document.getElementById('options-container');
        const resultElement = document.getElementById('result');
        const nextButton = document.getElementById('next-button');
        const restartButton = document.getElementById('restart-button');

        // 問題をロードして表示する関数
        function loadQuestion() {
            answeredThisQuestion = false; // 新しい問題がロードされたらフラグをリセット
            resultElement.textContent = ''; // 結果表示をクリア
            nextButton.style.display = 'none'; // 「次の問題へ」ボタンを非表示に
            restartButton.style.display = 'none'; // 「もう一度プレイ」ボタンを非表示に

            const currentQuiz = quizData[currentQuestionIndex];
            questionNumberElement.textContent = `問題 ${currentQuestionIndex + 1} / ${quizData.length}`;
            questionTextElement.textContent = currentQuiz.question;
            optionsContainer.innerHTML = ''; // 選択肢をクリア

            currentQuiz.options.forEach(option => {
                const button = document.createElement('button');
                button.textContent = option;
                button.onclick = () => checkAnswer(option);
                optionsContainer.appendChild(button);
            });
        }

        // クイズ終了時の結果画面を表示する関数
        function showResults() {
            questionNumberElement.textContent = ''; // 問題番号をクリア
            questionTextElement.textContent = 'クイズ終了！'; // クイズ終了メッセージ
            optionsContainer.innerHTML = ''; // 選択肢を非表示に
            resultElement.textContent = `${quizData.length}問中 ${score}問正解しました！`; // 最終結果
            resultElement.style.color = '#0056b3'; // 結果の色をリセット
            nextButton.style.display = 'none'; // 「次の問題へ」ボタンを非表示に
            restartButton.style.display = 'block'; // 「もう一度プレイ」ボタンを表示
        }

        // 回答をチェックする関数
        function checkAnswer(selectedOption) {
            if (answeredThisQuestion) {
                return; // 既に回答済みの場合は何もしない
            }
            answeredThisQuestion = true; // 回答済みフラグを立てる

            const correctAnswer = quizData[currentQuestionIndex].answer;
            const buttons = optionsContainer.querySelectorAll('button');

            buttons.forEach(button => {
                button.disabled = true; // 全てのボタンを無効化
                if (button.textContent === correctAnswer) {
                    button.style.backgroundColor = '#28a745'; // 正解のボタンを緑色に
                } else if (button.textContent === selectedOption) {
                    button.style.backgroundColor = '#dc3545'; // 不正解のボタンを赤色に
                }
            });

            if (selectedOption === correctAnswer) {
                resultElement.textContent = '正解です！';
                resultElement.style.color = '#28a745';
                score++;
            } else {
                resultElement.textContent = `不正解です。正解は「${correctAnswer}」でした。`;
                resultElement.style.color = '#dc3545';
            }

            // 最後の問題かどうかを判断し、次のアクションを決定
            if (currentQuestionIndex < quizData.length - 1) {
                // 最後の問題ではない場合、次の問題へ進むボタンを表示
                nextButton.style.display = 'block';
            } else {
                // 最後の問題に回答した場合、少し待ってから最終結果画面へ移行
                setTimeout(() => {
                    showResults(); // 最終画面をロード
                }, 1500); // 1.5秒後に結果表示
            }
        }

        // 次の問題へ進む関数
        function nextQuestion() {
            currentQuestionIndex++;
            loadQuestion();
        }

        // クイズを最初からやり直す関数
        function restartQuiz() {
            currentQuestionIndex = 0;
            score = 0;
            loadQuestion(); // クイズを最初からロード
        }

        // アプリ起動時に最初の問題をロード
        loadQuestion();
    </script>

</body>
</html>
