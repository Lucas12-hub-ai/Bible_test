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
            max-width: 650px;
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
            margin-bottom: 5px;
        }
        .subtitle {
            text-align: center;
            color: #666;
            font-size: 14px;
            margin-bottom: 30px;
        }
        /* 성경 대단원 카드 스타일 */
        .bible-section {
            background: #fdfdfd;
            border: 1px solid #e2e8f0;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 25px;
        }
        .section-title {
            font-size: 18px;
            font-weight: bold;
            color: #2d3748;
            margin-top: 0;
            margin-bottom: 15px;
            padding-bottom: 8px;
            border-bottom: 2px solid var(--primary-color);
            display: inline-block;
        }
        /* 입력 폼 */
        .verse-input-group {
            margin-bottom: 15px;
        }
        .verse-label {
            font-weight: bold;
            font-size: 14px;
            color: #4a5568;
            margin-bottom: 6px;
            display: block;
        }
        textarea {
            width: 100%;
            height: 70px;
            padding: 12px;
            box-sizing: border-box;
            border: 2px solid #e2e8f0;
            border-radius: 8px;
            font-size: 15px;
            resize: none;
            font-family: inherit;
        }
        textarea:focus {
            border-color: var(--primary-color);
            outline: none;
        }
        /* 공통 버튼 */
        button.action-btn {
            width: 100%;
            padding: 15px;
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 20px;
            box-shadow: 0 4px 6px rgba(74, 111, 165, 0.2);
            transition: all 0.2s;
        }
        button.action-btn:hover {
            background-color: #3b5984;
        }
        /* 결과 화면 디자인 */
        .hidden {
            display: none;
        }
        .score-box {
            background: #edf2f7;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            margin-bottom: 25px;
        }
        .score-title {
            font-size: 22px;
            font-weight: bold;
            margin-bottom: 5px;
        }
        .score-detail {
            font-size: 15px;
            color: #4a5568;
        }
        .result-item {
            margin-bottom: 15px;
            padding-left: 10px;
            border-left: 3px solid #cbd5e0;
        }
        .result-verse-title {
            font-weight: bold;
            font-size: 14px;
            color: #718096;
            margin-bottom: 5px;
        }
        .diff-output {
            padding: 12px;
            border: 1px solid #edf2f7;
            border-radius: 8px;
            background: #fafafa;
            line-height: 1.6;
            font-size: 15px;
            white-space: pre-wrap;
        }
        del {
            color: #e74c3c;
            background-color: #fde8e7;
            text-decoration: line-through;
            font-weight: bold;
            padding: 0 2px;
        }
        ins {
            color: #2e7d32;
            background-color: #e8f5e9;
            text-decoration: underline;
            font-weight: bold;
            padding: 0 2px;
        }
        .guide-text {
            font-size: 13px;
            color: #718096;
            margin-top: 15px;
            text-align: center;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>📖 요한계시록 핵심 암기 시험</h1>
    <div class="subtitle">모든 구절(총 5문항, 14개 절)을 암기하여 작성한 뒤 제출해 주세요.</div>

    <!-- 1. 시험 보는 화면 (전체 리스트가 노출됨) -->
    <div id="quiz-section">
        <div id="exam-paper"></div>
        <button class="action-btn" onclick="submitAnswer()">📝 전체 시험지 제출 및 채점</button>
    </div>

    <!-- 2. 결과 채점 화면 -->
    <div id="result-section" class="hidden">
        <div class="score-box">
            <div id="score-title" class="score-title"></div>
            <div id="score-detail" class="score-detail"></div>
        </div>
        
        <h3 style="font-size: 16px; color: #2d3748; margin-bottom: 15px;">🔍 오답 분석 결과:</h3>
        <div id="results-container"></div>
        
        <p class="guide-text">
            <span style="color: #e74c3c; font-weight: bold;">빨간 취소선</span>은 잘못 입력한 글자, 
            <span style="color: #2e7d32; font-weight: bold;">초록 밑줄</span>은 빠뜨린 글자입니다.
        </p>
        <button class="action-btn" onclick="retryQuiz()" style="background-color: #4a5568;">🔄 다시 시험 보기</button>
    </div>
</div>

<script>
    // 지정하신 5가지 성경 구절 데이터셋
    const bibleData = [
        {
            range: "계 1:1~3",
            verses: [
                { num: "1절", text: "예수 그리스도의 계시라 이는 하나님이 그에게 주사 반드시 속히 될 일을 그 종들에게 보이시려고 그 천사를 그 종 요한에게 보내어 지시하신 것이라" },
                { num: "2절", text: "요한은 하나님의 말씀과 예수 그리스도의 증거 곧 자기의 본 것을 다 증거하였느니라" },
                { num: "3절", text: "이 예언의 말씀을 읽는 자와 듣는 자들과 그 가운데 기록한 것을 지키는 자들이 복이 있나니 때가 가까움이라" }
            ]
        },
        {
            range: "계 7:1~4",
            verses: [
                { num: "1절", text: "이 일 후에 내가 네 천사가 땅 네 모퉁이에 선 것을 보니 땅의 사방의 바람을 붙잡아 바람으로 하여금 땅에나 바다에나 각종 나무에 불지 못하게 하더라" },
                { num: "2절", text: "또 보매 다른 천사가 살아계신 하나님의 인을 가지고 해 돋는 데로부터 올라와서 땅과 바다를 해롭게 할 권세를 얻은 네 천사를 향하여 큰 소리로 외쳐" },
                { num: "3절", text: "가로되 우리가 우리 하나님의 종들의 이마에 인치기까지 땅이나 바다나 나무나 해하지 말라 하더라" },
                { num: "4절", text: "내가 인맞은 자의 수를 들으니 이스라엘 자손의 각 지파 중에서 인맞은 자들이 십 사만 사천이니" }
            ]
        },
        {
            range: "계 10:10~11",
            verses: [
                { num: "10절", text: "내가 천사의 손에서 작은 책을 갖다 먹어버리니 내 입에는 꿀 같이 다나 먹은 후에 내 배에서는 쓰게 되더라" },
                { num: "11절", text: "저가 내게 말하기를 네가 많은 백성과 나라와 방언과 임금에게 다시 예언하여야 하리라 하더라" }
            ]
        },
        {
            range: "계 20:4~6",
            verses: [
                { num: "4절", text: "또 내가 보좌들을 보니 거기 앉은 자들이 있어 심판하는 권세를 받았더라 또 내가 보니 예수의 증거와 하나님의 말씀을 인하여 목 베임을 받은 자의 영혼들과 또 짐승과 그의 우상에게 경배하지도 아니하고 이마와 손에 그의 표를 받지도 아니한 자들이 살아서 그리스도로 더불어 천년 동안 왕노릇 하니" },
                { num: "5절", text: "(그 나머지 죽은 자들은 그 천년이 차기까지 살지 못하더라) 이는 첫째 부활이라" },
                { num: "6절", text: "이 첫째 부활에 참예하는 자들은 복이 있고 거룩하도다 둘째 사망이 그들을 다스리는 권세가 없고 도리어 그들이 하나님과 그리스도의 제사장이 되어 천년 동안 그리스도로 더불어 왕노릇 하리라" }
            ]
        },
        {
            range: "계 22:18~19",
            verses: [
                { num: "18절", text: "내가 이 책의 예언의 말씀을 듣는 각인에게 증거하노니 만일 누구든지 이것들 외에 더하면 하나님이 이 책에 기록된 재앙들을 그에게 더하실 터이요" },
                { num: "19절", text: "만일 누구든지 이 책의 예언의 말씀에서 제하여 버리면 하나님이 이 책에 기록된 생명 나무와 및 거룩한 성에 참예함을 제하여 버리시리라" }
            ]
        }
    ];

    // 전체 시험지 화면 구성하기
    function renderExam() {
        const paper = document.getElementById('exam-paper');
        paper.innerHTML = '';

        bibleData.forEach((section, sIdx) => {
            // 성경 범위별로 큰 카드 생성 (예: 계 1:1~3)
            const sectionDiv = document.createElement('div');
            sectionDiv.className = 'bible-section';

            const title = document.createElement('h3');
            title.className = 'section-title';
            title.innerText = `📍 ${section.range}`;
            sectionDiv.appendChild(title);

            // 해당 범위 안에 포함된 절 입력칸들을 순서대로 배치
            section.verses.forEach((verse, vIdx) => {
                const inputGroup = document.createElement('div');
                inputGroup.className = 'verse-input-group';
                
                // 데이터 추적을 위해 section index와 verse index를 조합한 ID 부여
                inputGroup.innerHTML = `
                    <label class="verse-label" for="input-${sIdx}-${vIdx}">${section.range.split(' ')[1]} 중 ${verse.num}</label>
                    <textarea id="input-${sIdx}-${vIdx}" placeholder="${verse.num} 말씀을 입력하세요."></textarea>
                `;
                sectionDiv.appendChild(inputGroup);
            });

            paper.appendChild(sectionDiv);
        });

        // 결과화면은 숨기고 시험 화면 보여주기
        document.getElementById('quiz-section').classList.remove('hidden');
        document.getElementById('result-section').classList.add('hidden');
        window.scrollTo(0, 0);
    }

    // 채점 및 결과 노출
    function submitAnswer() {
        const resultsContainer = document.getElementById('results-container');
        resultsContainer.innerHTML = '';

        let totalVerses = 0;
        let perfectVerses = 0;
        const dmp = new diff_match_patch();

        bibleData.forEach((section, sIdx) => {
            // 결과 화면에도 대단원 이름 카드 추가
            const sectionResultDiv = document.createElement('div');
            sectionResultDiv.className = 'bible-section';
            
            const title = document.createElement('h3');
            title.className = 'section-title';
            title.innerText = `📍 ${section.range}`;
            sectionResultDiv.appendChild(title);

            section.verses.forEach((verse, vIdx) => {
                totalVerses++;
                const userInput = document.getElementById(`input-${sIdx}-${vIdx}`).value.trim();
                const correctAnswer = verse.text.trim();

            // 띄어쓰기(공백)를 모두 제거한 뒤 비교하기 위해 임시 변수 생성
                const cleanUser = userInput.replace(/\s+/g, '');
                const cleanCorrect = correctAnswer.replace(/\s+/g, '');

            // 이제 띄어쓰기가 없다고 가정하고 정답 여부를 판정합니다 (인맞은 == 인 맞은)
                const isCorrect = (cleanUser === cleanCorrect);
                if (isCorrect) {
                    perfectVerses++;
                }

                // 글자 비교 연산
                const diffs = dmp.diff_main(userInput, correctAnswer);
                dmp.diff_cleanupSemantic(diffs);

                let htmlResult = "";
                diffs.forEach(diff => {
                    const operation = diff[0];
                    const text = diff[1];
                    if (operation === -1) {
                        htmlResult += `<del>${text}</del>`;
                    } else if (operation === 1) {
                        htmlResult += `<ins>${text}</ins>`;
                    } else {
                        htmlResult += text;
                    }
                });

                // 결과 항목 생성 (정답 여부 이모지 추가)
                const resultItem = document.createElement('div');
                resultItem.className = 'result-item';
                
                const statusEmoji = isCorrect ? "✅ 정답" : "❌ 틀림";
                const statusColor = isCorrect ? "#2e7d32" : "#e74c3c";

                resultItem.innerHTML = `
                    <div class="result-verse-title">
                        ${verse.num} - <span style="color: ${statusColor}; font-weight: bold;">${statusEmoji}</span>
                    </div>
                    <div class="diff-output">${htmlResult || '<span style="color: #aaa;">(입력 내용 없음)</span>'}</div>
                `;
                sectionResultDiv.appendChild(resultItem);
            });

            resultsContainer.appendChild(sectionResultDiv);
        });

        // 점수 계산 및 총평
        const scoreTitle = document.getElementById('score-title');
        const scoreDetail = document.getElementById('score-detail');

        if (perfectVerses === totalVerses) {
            scoreTitle.innerHTML = "🎉 <span style='color: #2e7d32;'>100점 만점!</span>";
            scoreDetail.innerText = "모든 구절(총 14개 절)을 완벽하게 맞추셨습니다!";
        } else {
            scoreTitle.innerHTML = "📝 <span style='color: #e74c3c;'>오답을 확인해 보세요!</span>";
            scoreDetail.innerHTML = `총 ${totalVerses}개 구절 중 <strong>${perfectVerses}개 구절</strong>을 완벽히 맞췄습니다.`;
        }

        // 화면 전환
        document.getElementById('quiz-section').classList.add('hidden');
        document.getElementById('result-section').classList.remove('hidden');
        window.scrollTo(0, 0);
    }

    // 다시 시험 치기
    function retryQuiz() {
        renderExam();
    }

    // 초기 실행
    window.onload = renderExam;
</script>

</body>
</html>
