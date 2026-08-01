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
            bg: #FFFDF3;          /* 전체 배경 - 아주 연한 크림 */
            card: #FFFFFF;        /* 카드 */
            primary: #D98A00;     /* 오렌지/골드 */
            primary-light: #FFF3D2; /* 안내 박스 */
            green: #00A878;       /* 안전/통과 */
            text: #29250F;        /* 메인 글씨 */
            text-light: #756F52;  /* 보조 글씨 */
            border: #F3E4B5;      /* 카드 테두리 */
}
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
            range: "계 3:12~13",
            verses: [
                { num: "12절", text: "이기는 자는 내 하나님 성전에 기둥이 되게 하리니 그가 결코 다시 나가지 아니하리라 내가 하나님의 이름과 하나님의 성 곧 하늘에서 내 하나님께로부터 내려 오는 새 예루살렘의 이름과 나의 새 이름을 그이 위에 기록하리라" },
                { num: "13절", text: "귀 있는 자는 성령이 교회들에게 하시는 말씀을 들을찌어다" }
            ]
        },
        {
            range: "계 12:10~11",
            verses: [
                { num: "10절", text: "내가 또 들으니 하늘에 큰 음성이 있어 가로되 이제 우리 하나님의 구원과 능력과 나라와 또 그의 그리스도의 권세가 이루었으니 우리 형제들을 참소하던 자 곧 우리 하나님 앞에서 밤낮 참소하던 자가 쫓겨 났고" },
                { num: "11절", text: "또 여러 형제가 어린 양의 피와 자기의 증거하는 말을 인하여 저를 이기었으니 그들은 죽기까지 자기 생명을 아끼지 아니하였도다" }
            ]
        },
        {
            range: "계 15:4~5",
            verses: [
                { num: "4절", text: "주여 누가 주의 이름을 두려워하지 아니하며 영화롭게 하지 아니하오리이까 오직 주만 거룩하시니이다 주의 의로우신 일이 나타났으매 만국이 와서 주께 경배하리이다 하더라" },
                { num: "5절", text: "또 이일 후에 내가 보니 하늘에 증거 장막의 성전이 열리며" }
            ]
        },
        {
            range: "계 18:7~8",
            verses: [
                { num: "7절", text: "그가 어떻게 자기를 영화롭게 하였으며 사치하였든지 그만큼 고난과 애통으로 갚아 주라 그가 마음에 말하기를 나는 여황으로 앉은 자요 과부가 아니라 결단코 애통을 당하지 아니하리라 하니" },
                { num: "8절", text: "(그러므로 하루 동안에 그 재앙들이 이르리니 곧 사망과 애통과 흉년이라 그가 또한 불에 살라지리니 그를 심판하신 주 하나님은 강하신 자이심이니라" }
            ]
        },
        {
            range: "계 22:15~16",
            verses: [
                { num: "18절", text: "개들과 술객들과 행음자들과 살인자들과 우상 숭배자들과 및 거짓말을 좋아하며 지어내는 자마다 성밖에 있으리라" },
                { num: "19절", text: "나 예수는 교회들을 위하여 내 사자를 보내어 이것들을 너희에게 증거하게 하였노라 나는 다윗의 뿌리요 자손이니 곧 광명한 새벽별이라 하시더라" }
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

            // 1. 특수 괄호（ ）를 일반 괄호 ( ) 로 통일시킵니다.
                let normalizedUser = userInput.replace(/（/g, '(').replace(/）/g, ')');
                let normalizedCorrect = correctAnswer.replace(/（/g, '(').replace(/）/g, ')');

            // 2. 그 상태에서 띄어쓰기만 제거하여 최종 비교 변수를 만듭니다.
                const cleanUser = normalizedUser.replace(/\s+/g, '');
                const cleanCorrect = normalizedCorrect.replace(/\s+/g, '');

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
