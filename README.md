# Bible_test
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>성경 구절 암기 시험지</title>
    <!-- 글자 단위 비교를 위한 구글 diff 라이브러리 로드 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/diff_match_patch/20121119/diff_match_patch.js"></script>
    <style>
        :root {
            --primary-color: #4a6fa5;
            --bg-color: #f4f6f9;
        }
        body {
            font-family: 'Malgun Gothic', sans-serif;
            background-color: var(--bg-color);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }
        .container {
            max-width: 600px;
            width: 100%;
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }
        h1 {
            text-align: center;
            color: var(--primary-color);
            font-size: 24px;
            margin-bottom: 30px;
        }
        .quiz-box, .result-box {
            margin-bottom: 20px;
        }
        .question {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 15px;
            color: #333;
            background: #eef2f7;
            padding: 15px;
            border-left: 5px solid var(--primary-color);
            border-radius: 4px;
        }
        textarea {
            width: 100%;
            height: 120px;
            padding: 12px;
            box-sizing: border-box;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 16px;
            resize: none;
            margin-bottom: 15px;
        }
        textarea:focus {
            border-color: var(--primary-color);
            outline: none;
        }
        button {
            width: 100%;
            padding: 15px;
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.2s;
        }
        button:hover {
            background-color: #3b5984;
        }
        .hidden {
            display: none;
        }
        /* 결과 채점 스타일 */
        .score {
            font-size: 20px;
            font-weight: bold;
            text-align: center;
            margin-bottom: 20px;
        }
        .diff-output {
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            background: #fafafa;
            line-height: 1.8;
            font-size: 16px;
            white-space: pre-wrap;
        }
        /* 틀린 글자 (삭제되어야 할 것) */
        del {
            color: #e74c3c;
            background-color: #fde8e7;
            text-decoration: line-through;
            font-weight: bold;
            padding: 0 2px;
        }
        /* 빠진 글자 (추가되어야 할 정답) */
        ins {
            color: #2e7d32;
            background-color: #e8f5e9;
            text-decoration: underline;
            font-weight: bold;
            padding: 0 2px;
        }
        .guide-text {
            font-size: 14px;
            color: #666;
            margin-top: 15px;
            text-align: center;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>📖 성경 암기 시험지</h1>

    <!-- 시험 보는 화면 -->
    <div id="quiz-section" class="quiz-box">
        <div id="question" class="question">문제를 불러오는 중...</div>
        <textarea id="user-input" placeholder="여기에 성경 구절을 토씨 하나 틀리지 않게 입력하세요."></textarea>
        <button onclick="submitAnswer()">시험지 제출하기</button>
    </div>

    <!-- 결과 화면 -->
    <div id="result-section" class="result-box hidden">
        <div id="score-display" class="score"></div>
        <h3>채점 결과 (오답 분석):</h3>
        <div id="diff-display" class="diff-output"></div>
        
        <p class="guide-text">
            <span style="color: #e74c3c; font-weight: bold;">빨간 취소선</span>은 잘못 입력한 글자, 
            <span style="color: #2e7d32; font-weight: bold;">초록 밑줄</span>은 빠뜨린 글자입니다.
        </p>
        <button onclick="nextQuestion()" style="margin-top: 20px; background-color: #2ecc71;">다음 문제 풀기</button>
    </div>
</div>

<script>
    // 테스트용 성경 구절 데이터베이스 (필요한 만큼 늘리시면 됩니다)
    const bibleData = [
        { id: 1, address: "요한복음 3장 16절", text: "하나님이 세상을 이처럼 사랑하사 독생자를 주셨으니 이는 그를 믿는 자마다 멸망하지 않고 영생을 얻게 하려 하심이라" },
        { id: 2, address: "창세기 1장 1절", text: "태초에 하나님이 천지를 창조하시니라" },
        { id: 3, address: "로마서 8장 28절", text: "우리가 알거니와 하나님을 사랑하는 자 곧 그의 뜻대로 부르심을 입은 자들에게는 모든 것이 합력하여 선을 이루느니라" }
    ];

    let currentQuestion = {};

    // 시험 시작 및 문제 출제
    function loadQuestion() {
        // 랜덤으로 문제 추출
        const randomIndex = Math.floor(Math.random() * bibleData.length);
        currentQuestion = bibleData[randomIndex];
        
        document.getElementById('question').innerText = `[문제] 다음 구절을 작성하세요: \n${currentQuestion.address}`;
        document.getElementById('user-input').value = '';
        
        // 화면 초기화
        document.getElementById('quiz-section').classList.remove('hidden');
        document.getElementById('result-section').classList.add('hidden');
    }

    // 채점하기 버튼 동작
    function submitAnswer() {
        const userAnswer = document.getElementById('user-input').value.trim();
        const correctAnswer = currentQuestion.text.trim();

        if (!userAnswer) {
            alert("답안을 입력해주세요!");
            return;
        }

        // 100% 일치 여부 확인
        const isPerfect = (userAnswer === correctAnswer);
        const scoreDisplay = document.getElementById('score-display');
        
        if (isPerfect) {
            scoreDisplay.innerHTML = "🎉 <span style='color: #2ecc71;'>100점 만점!</span> 완벽하게 암기하셨습니다.";
        } else {
            scoreDisplay.innerHTML = "❌ <span style='color: #e74c3c;'>틀린 부분이 있습니다.</span> 다시 확인해보세요.";
        }

        // 구글 diff_match_patch를 이용해 글자 단위 비교 진행
        const dmp = new diff_match_patch();
        const diffs = dmp.diff_main(userAnswer, correctAnswer);
        dmp.diff_cleanupSemantic(diffs); // 사람이 읽기 편하게 그룹화

        // 비교 결과를 HTML 태그로 변환
        // diff_main 결과 구조: [-1: 삭제(잘못 입력한것), 1: 추가(빠진 정답), 0: 일치]
        let htmlResult = "";
        diffs.forEach(diff => {
            const operation = diff[0];
            const text = diff[1];
            
            if (operation === -1) {
                htmlResult += `<del>${text}</del>`; // 사용자가 잘못 쓴 글자 -> 빨간 취소선
            } else if (operation === 1) {
                htmlResult += `<ins>${text}</ins>`; // 원본에 있어야 할 글자 -> 초록 밑줄
            } else {
                htmlResult += text; // 맞춘 글자 -> 일반 텍스트
            }
        });

        // 화면 전환 및 결과 출력
        document.getElementById('diff-display').innerHTML = htmlResult;
        document.getElementById('quiz-section').classList.add('hidden');
        document.getElementById('result-section').classList.remove('hidden');
    }

    // 다음 문제로 넘어가기
    function nextQuestion() {
        loadQuestion();
    }

    // 페이지 로드 시 첫 문제 실행
    window.onload = loadQuestion;
</script>

</body>
</html>
