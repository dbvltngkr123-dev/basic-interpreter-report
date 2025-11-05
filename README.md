# basic-interpreter-report

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BASIC Interpreter 소스코드 분석 보고서</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700&family=Fira+Code:wght@400;500&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Noto Sans KR', sans-serif;
            line-height: 1.6;
            color: #2c3e50;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            padding: 20px;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 60px 40px;
            text-align: center;
        }
        
        .header h1 {
            font-size: 2.5em;
            margin-bottom: 20px;
            font-weight: 700;
        }
        
        .header-info {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 30px;
            font-size: 0.95em;
        }
        
        .header-info div {
            background: rgba(255,255,255,0.1);
            padding: 10px;
            border-radius: 8px;
            backdrop-filter: blur(10px);
        }
        
        .content {
            padding: 50px;
        }
        
        .section {
            margin-bottom: 60px;
        }
        
        .section-title {
            font-size: 2em;
            color: #667eea;
            margin-bottom: 30px;
            padding-bottom: 15px;
            border-bottom: 3px solid #667eea;
            font-weight: 700;
        }
        
        .diagram-box {
            background: #f8f9fa;
            border: 3px solid #667eea;
            border-radius: 15px;
            padding: 30px;
            margin: 25px 0;
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.1);
        }
        
        .flow-diagram {
            display: flex;
            align-items: center;
            justify-content: space-around;
            flex-wrap: wrap;
            gap: 20px;
            padding: 20px;
        }
        
        .flow-box {
            background: white;
            border: 3px solid #667eea;
            border-radius: 12px;
            padding: 20px 30px;
            font-weight: 500;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        
        .flow-box:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.3);
        }
        
        .arrow {
            font-size: 2em;
            color: #667eea;
            font-weight: bold;
        }
        
        .stack-visual {
            display: flex;
            gap: 40px;
            justify-content: center;
            flex-wrap: wrap;
            margin: 30px 0;
        }
        
        .stack-container {
            flex: 1;
            min-width: 250px;
        }
        
        .stack-title {
            text-align: center;
            font-weight: 700;
            font-size: 1.3em;
            color: #764ba2;
            margin-bottom: 15px;
        }
        
        .stack-item {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 15px;
            margin: 5px 0;
            border-radius: 8px;
            text-align: center;
            font-family: 'Fira Code', monospace;
            box-shadow: 0 3px 10px rgba(0,0,0,0.2);
            animation: slideIn 0.5s ease-out;
        }
        
        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(-20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }
        
        .code-block {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 25px;
            border-radius: 10px;
            font-family: 'Fira Code', monospace;
            overflow-x: auto;
            margin: 20px 0;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        
        .code-line {
            margin: 5px 0;
        }
        
        .keyword { color: #ff79c6; font-weight: 500; }
        .variable { color: #50fa7b; }
        .number { color: #bd93f9; }
        .operator { color: #ff79c6; }
        .comment { color: #6272a4; font-style: italic; }
        
        .timeline {
            position: relative;
            padding: 30px 0;
        }
        
        .timeline::before {
            content: '';
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            width: 4px;
            height: 100%;
            background: linear-gradient(180deg, #667eea 0%, #764ba2 100%);
        }
        
        .timeline-item {
            display: flex;
            align-items: center;
            margin: 30px 0;
            position: relative;
        }
        
        .timeline-item:nth-child(odd) .timeline-content {
            margin-left: auto;
            text-align: left;
        }
        
        .timeline-item:nth-child(even) .timeline-content {
            margin-right: auto;
            text-align: right;
        }
        
        .timeline-content {
            background: white;
            border: 3px solid #667eea;
            border-radius: 12px;
            padding: 20px;
            width: 45%;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .timeline-marker {
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            width: 25px;
            height: 25px;
            background: #667eea;
            border: 4px solid white;
            border-radius: 50%;
            box-shadow: 0 0 0 4px #667eea;
        }
        
        .grid-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            margin: 30px 0;
        }
        
        .grid-3 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 25px;
            margin: 30px 0;
        }
        
        .card {
            background: white;
            border: 3px solid #667eea;
            border-radius: 12px;
            padding: 25px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            transition: transform 0.3s;
        }
        
        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(102, 126, 234, 0.2);
        }
        
        .card-title {
            font-size: 1.4em;
            font-weight: 700;
            color: #764ba2;
            margin-bottom: 15px;
        }
        
        .highlight-box {
            background: linear-gradient(135deg, #ffeaa7 0%, #fdcb6e 100%);
            border-left: 5px solid #e17055;
            padding: 20px;
            margin: 20px 0;
            border-radius: 8px;
            font-weight: 500;
        }
        
        .table-container {
            overflow-x: auto;
            margin: 25px 0;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            background: white;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            border-radius: 10px;
            overflow: hidden;
        }
        
        th {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 15px;
            font-weight: 600;
            text-align: left;
        }
        
        td {
            padding: 15px;
            border-bottom: 1px solid #eee;
        }
        
        tr:hover {
            background: #f8f9fa;
        }
        
        .conclusion {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 40px;
            border-radius: 15px;
            margin-top: 50px;
        }
        
        .conclusion h3 {
            font-size: 1.8em;
            margin-bottom: 20px;
        }
        
        ul {
            list-style: none;
            padding-left: 0;
        }
        
        li {
            padding: 8px 0;
            padding-left: 25px;
            position: relative;
        }
        
        li::before {
            content: "▹";
            position: absolute;
            left: 0;
            color: #667eea;
            font-weight: bold;
            font-size: 1.2em;
        }
        
        .conclusion li::before {
            color: #ffeaa7;
        }
        
        @media (max-width: 768px) {
            .content {
                padding: 30px 20px;
            }
            
            .grid-2 {
                grid-template-columns: 1fr;
            }
            
            .timeline::before {
                left: 30px;
            }
            
            .timeline-content {
                width: calc(100% - 80px);
                margin-left: 80px !important;
                text-align: left !important;
            }
            
            .timeline-marker {
                left: 30px;
            }
        }
        
        .process-flow {
            display: flex;
            flex-direction: column;
            gap: 15px;
            padding: 20px;
        }
        
        .process-step {
            display: flex;
            align-items: center;
            gap: 20px;
        }
        
        .step-number {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 1.2em;
            flex-shrink: 0;
        }
        
        .step-content {
            flex: 1;
            background: white;
            border: 2px solid #667eea;
            padding: 15px 20px;
            border-radius: 8px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📊 BASIC Interpreter 소스코드 분석</h1>
            <div class="header-info">
                <div><strong>과목명</strong><br>운영체제</div>
                <div><strong>학과</strong><br>컴퓨터공학과</div>
                <div><strong>성명</strong><br>조국현</div>
                <div><strong>담당교수</strong><br>강영명 교수님</div>
                <div><strong>과목코드</strong><br>001</div>
                <div><strong>제출일</strong><br>2025-10-29</div>
            </div>
        </div>

        <div class="content">
            <!-- 1. 프로그램 개요 -->
            <div class="section">
                <h2 class="section-title">1️⃣ 프로그램 개요</h2>
                
                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🔄 인터프리터 동작 원리</h3>
                    <div class="flow-diagram">
                        <div class="flow-box">📄 소스코드<br>(.spl)</div>
                        <div class="arrow">→</div>
                        <div class="flow-box">⚙️ 인터프리터<br>(실시간 해석)</div>
                        <div class="arrow">→</div>
                        <div class="flow-box">✅ 실행 결과<br>(Output)</div>
                    </div>
                    <div class="highlight-box">
                        💡 컴파일 과정 없이 한 줄씩 즉시 실행하는 방식
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🏗️ 프로그램 아키텍처</h3>
                    <div class="process-flow">
                        <div class="process-step">
                            <div class="step-number">1</div>
                            <div class="step-content"><strong>파일 읽기</strong> - fgets()로 한 줄씩 입력</div>
                        </div>
                        <div class="process-step">
                            <div class="step-number">2</div>
                            <div class="step-content"><strong>라인 파싱</strong> - strtok()으로 토큰 분리</div>
                        </div>
                        <div class="process-step">
                            <div class="step-number">3</div>
                            <div class="step-content"><strong>키워드 분석</strong> - begin/end/int/function 판별</div>
                        </div>
                        <div class="process-step">
                            <div class="step-number">4</div>
                            <div class="step-content"><strong>스택 관리</strong> - Push/Pop으로 데이터 관리</div>
                        </div>
                        <div class="process-step">
                            <div class="step-number">5</div>
                            <div class="step-content"><strong>수식 계산</strong> - Infix→Postfix→Calculate</div>
                        </div>
                    </div>
                </div>

                <div class="code-block">
                    <div class="code-line"><span class="comment">// 예제 입력 파일 (sample.spl)</span></div>
                    <div class="code-line"><span class="keyword">function</span> <span class="variable">main</span></div>
                    <div class="code-line"><span class="keyword">begin</span></div>
                    <div class="code-line">    <span class="keyword">int</span> <span class="variable">x</span> = <span class="number">3</span></div>
                    <div class="code-line">    <span class="keyword">int</span> <span class="variable">y</span> = <span class="number">5</span></div>
                    <div class="code-line">    (<span class="variable">x</span> <span class="operator">+</span> <span class="variable">y</span>)</div>
                    <div class="code-line"><span class="keyword">end</span></div>
                    <div class="code-line"><span class="comment">// 출력: Output=8</span></div>
                </div>
            </div>

            <!-- 2. 자료구조 분석 -->
            <div class="section">
                <h2 class="section-title">2️⃣ 자료구조 시각화</h2>
                
                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">📚 3개의 스택 구조</h3>
                    <div class="stack-visual">
                        <div class="stack-container">
                            <div class="stack-title">STACK<br>(변수/함수)</div>
                            <div class="stack-item">type=1, x=3</div>
                            <div class="stack-item">type=4 (begin)</div>
                            <div class="stack-item">type=2, main</div>
                        </div>
                        <div class="stack-container">
                            <div class="stack-title">OpStack<br>(연산자)</div>
                            <div class="stack-item">+</div>
                            <div class="stack-item">*</div>
                            <div class="stack-item">/</div>
                        </div>
                        <div class="stack-container">
                            <div class="stack-title">CalcStack<br>(계산)</div>
                            <div class="stack-item">8</div>
                            <div class="stack-item">5</div>
                            <div class="stack-item">3</div>
                        </div>
                    </div>
                </div>

                <div class="grid-3">
                    <div class="card">
                        <div class="card-title">📦 Node 구조체</div>
                        <ul>
                            <li><strong>type</strong>: 1=변수, 2=함수</li>
                            <li><strong>exp_data</strong>: 이름(1글자)</li>
                            <li><strong>val</strong>: 값/라인번호</li>
                            <li><strong>next</strong>: 다음 노드</li>
                        </ul>
                    </div>
                    <div class="card">
                        <div class="card-title">➕ opNode 구조체</div>
                        <ul>
                            <li><strong>op</strong>: 연산자 (+,-,*,/)</li>
                            <li><strong>next</strong>: 다음 노드</li>
                            <li>우선순위 관리</li>
                        </ul>
                    </div>
                    <div class="card">
                        <div class="card-title">🔢 Postfixnode</div>
                        <ul>
                            <li><strong>val</strong>: 계산 값</li>
                            <li><strong>next</strong>: 다음 노드</li>
                            <li>최종 결과 저장</li>
                        </ul>
                    </div>
                </div>
            </div>

            <!-- 3. 실행 흐름 -->
            <div class="section">
                <h2 class="section-title">3️⃣ 실행 흐름 타임라인</h2>
                
                <div class="timeline">
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>📝 Line 1: function main</h4>
                            <p><strong>동작:</strong> 함수 정의 등록</p>
                            <p><strong>스택:</strong> [type=2, 'm', line=1]</p>
                            <p><strong>플래그:</strong> foundMain = 1</p>
                        </div>
                    </div>
                    
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>🚪 Line 2: begin</h4>
                            <p><strong>동작:</strong> 블록 시작 마커 추가</p>
                            <p><strong>스택:</strong> [type=4] → [type=2, 'm']</p>
                        </div>
                    </div>
                    
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>🔤 Line 3: int x = 3</h4>
                            <p><strong>동작:</strong> 변수 x 선언 및 초기화</p>
                            <p><strong>스택:</strong> [x=3] → [begin] → [main]</p>
                            <p><strong>처리:</strong> strtok로 "int", "x", "=", "3" 분리</p>
                        </div>
                    </div>
                    
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>🔤 Line 4: int y = 5</h4>
                            <p><strong>동작:</strong> 변수 y 선언 및 초기화</p>
                            <p><strong>스택:</strong> [y=5] → [x=3] → [begin] → [main]</p>
                        </div>
                    </div>
                    
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>🧮 Line 5: (x + y)</h4>
                            <p><strong>동작:</strong> 수식 계산</p>
                            <p><strong>과정:</strong></p>
                            <p>① GetVal('x') → 3</p>
                            <p>② GetVal('y') → 5</p>
                            <p>③ Infix→Postfix: "35+"</p>
                            <p>④ 계산: 3+5=8</p>
                            <p><strong>결과:</strong> LastExpReturn = 8</p>
                        </div>
                    </div>
                    
                    <div class="timeline-item">
                        <div class="timeline-marker"></div>
                        <div class="timeline-content">
                            <h4>🏁 Line 6: end</h4>
                            <p><strong>동작:</strong> 블록 종료</p>
                            <p><strong>출력:</strong> printf("Output=8")</p>
                            <p><strong>메모리:</strong> FreeAll() 호출</p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 4. 수식 계산 과정 -->
            <div class="section">
                <h2 class="section-title">4️⃣ 수식 계산 상세 과정</h2>
                
                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🔄 Infix → Postfix 변환</h3>
                    <div class="table-container">
                        <table>
                            <thead>
                                <tr>
                                    <th>단계</th>
                                    <th>입력</th>
                                    <th>OpStack</th>
                                    <th>Postfix</th>
                                    <th>설명</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td>0</td>
                                    <td>(</td>
                                    <td>[]</td>
                                    <td>""</td>
                                    <td>괄호 무시</td>
                                </tr>
                                <tr>
                                    <td>1</td>
                                    <td>x</td>
                                    <td>[]</td>
                                    <td>"3"</td>
                                    <td>GetVal('x')→3</td>
                                </tr>
                                <tr>
                                    <td>2</td>
                                    <td>+</td>
                                    <td>[+]</td>
                                    <td>"3"</td>
                                    <td>연산자 Push</td>
                                </tr>
                                <tr>
                                    <td>3</td>
                                    <td>y</td>
                                    <td>[+]</td>
                                    <td>"35"</td>
                                    <td>GetVal('y')→5</td>
                                </tr>
                                <tr>
                                    <td>4</td>
                                    <td>)</td>
                                    <td>[]</td>
                                    <td>"35+"</td>
                                    <td>연산자 Pop</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🧮 Postfix 계산</h3>
                    <div class="table-container">
                        <table>
                            <thead>
                                <tr>
                                    <th>단계</th>
                                    <th>입력</th>
                                    <th>CalcStack</th>
                                    <th>동작</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td>1</td>
                                    <td>3</td>
                                    <td>[3]</td>
                                    <td>Push 3</td>
                                </tr>
                                <tr>
                                    <td>2</td>
                                    <td>5</td>
                                    <td>[5, 3]</td>
                                    <td>Push 5</td>
                                </tr>
                                <tr>
                                    <td>3</td>
                                    <td>+</td>
                                    <td>[8]</td>
                                    <td>Pop 5, Pop 3 → 3+5=8 → Push 8</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <div class="highlight-box">
                        ✨ 최종 결과: CalcStack.top.val = <strong>8</strong>
                    </div>
                </div>
            </div>

            <!-- 5. 주요 함수 분석 -->
            <div class="section">
                <h2 class="section-title">5️⃣ 주요 함수 분석</h2>
                
                <div class="grid-2">
                    <div class="card">
                        <div class="card-title">🔼 Push() 함수</div>
                        <div class="code-block" style="font-size: 0.85em;">
                            <div class="code-line"><span class="comment">// 스택에 노드 추가</span></div>
                            <div class="code-line">newnode = malloc()</div>
                            <div class="code-line">newnode->next = top</div>
                            <div class="code-line">top = newnode</div>
                        </div>
                        <p style="margin-top: 15px;"><strong>시간복잡도:</strong> O(1)</p>
                    </div>
                    
                    <div class="card">
                        <div class="card-title">🔽 Pop() 함수</div>
                        <div class="code-block" style="font-size: 0.85em;">
                            <div class="code-line"><span class="comment">// 스택에서 노드 제거</span></div>
                            <div class="code-line">temp = top</div>
                            <div class="code-line">top = top->next</div>
                            <div class="code-line">free(temp)</div>
                        </div>
                        <p style="margin-top: 15px;"><strong>시간복잡도:</strong> O(1)</p>
                    </div>
                    
                    <div class="card">
                        <div class="card-title">🔍 GetVal() 함수</div>
                        <div class="code-block" style="font-size: 0.85em;">
                            <div class="code-line"><span class="comment">// 변수 값 검색</span></div>
                            <div class="code-line">head = top</div>
                            <div class="code-line">while(head != NULL)</div>
                            <div class="code-line">  if(head->exp_data == name)</div>
                            <div class="code-line">    return head->val</div>
                        </div>
                        <p style="margin-top: 15px;"><strong>시간복잡도:</strong> O(n)</p>
                    </div>
                    
                    <div class="card">
                        <div class="card-title">⚖️ Priotry() 함수</div>
                        <div class="table-container" style="margin-top: 15px;">
                            <table>
                                <tr>
                                    <th>연산자</th>
                                    <th>우선순위</th>
                                </tr>
                                <tr>
                                    <td>+, -</td>
                                    <td>1 (낮음)</td>
                                </tr>
                                <tr>
                                    <td>*, /</td>
                                    <td>2 (높음)</td>
                                </tr>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 6. 알고리즘 심층 분석 -->
            <div class="section">
                <h2 class="section-title">6️⃣ 알고리즘 시각화</h2>
                
                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🎯 변수 검색 과정 (GetVal)</h3>
                    <div style="text-align: center; padding: 20px;">
                        <div style="display: inline-block; text-align: left;">
                            <div style="margin: 10px 0; padding: 15px; background: #e8f4f8; border-left: 5px solid #667eea; border-radius: 8px;">
                                <strong>검색 목표:</strong> 'x' 찾기
                            </div>
                            <div style="margin: 10px 0; padding: 15px; background: #ffe5e5; border-left: 5px solid #ff6b6b; border-radius: 8px;">
                                <strong>① top →</strong> [type=1, 'y', val=5] ❌ 'y' ≠ 'x'
                            </div>
                            <div style="margin: 10px 0; padding: 15px; background: #e5ffe5; border-left: 5px solid #51cf66; border-radius: 8px;">
                                <strong>② next →</strong> [type=1, 'x', val=3] ✅ 'x' = 'x' <strong>발견!</strong><br>
                                <strong>→ return 3</strong>
                            </div>
                            <div style="margin: 10px 0; padding: 15px; background: #f0f0f0; border-left: 5px solid #999; border-radius: 8px;">
                                <strong>③ next →</strong> [type=4, begin] (탐색 중단)
                            </div>
                        </div>
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">📐 수식 "3 + 5 * 2" 처리 예제</h3>
                    
                    <div style="background: white; padding: 20px; border-radius: 10px; margin: 20px 0;">
                        <h4 style="color: #667eea; margin-bottom: 15px;">Step 1: Infix → Postfix 변환</h4>
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>입력</th>
                                        <th>OpStack</th>
                                        <th>Postfix</th>
                                        <th>설명</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>3</td>
                                        <td>[]</td>
                                        <td>"3"</td>
                                        <td>숫자 → 출력</td>
                                    </tr>
                                    <tr>
                                        <td>+</td>
                                        <td>[+]</td>
                                        <td>"3"</td>
                                        <td>스택 비어있음 → Push</td>
                                    </tr>
                                    <tr>
                                        <td>5</td>
                                        <td>[+]</td>
                                        <td>"35"</td>
                                        <td>숫자 → 출력</td>
                                    </tr>
                                    <tr>
                                        <td>*</td>
                                        <td>[*, +]</td>
                                        <td>"35"</td>
                                        <td>* > + → Push</td>
                                    </tr>
                                    <tr>
                                        <td>2</td>
                                        <td>[*, +]</td>
                                        <td>"352"</td>
                                        <td>숫자 → 출력</td>
                                    </tr>
                                    <tr>
                                        <td>끝</td>
                                        <td>[]</td>
                                        <td>"352*+"</td>
                                        <td>스택 비우기</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <div style="background: white; padding: 20px; border-radius: 10px; margin: 20px 0;">
                        <h4 style="color: #764ba2; margin-bottom: 15px;">Step 2: Postfix 계산 "352*+"</h4>
                        <div class="process-flow">
                            <div class="process-step">
                                <div class="step-number">1</div>
                                <div class="step-content">'3' → Push 3 → Stack: [3]</div>
                            </div>
                            <div class="process-step">
                                <div class="step-number">2</div>
                                <div class="step-content">'5' → Push 5 → Stack: [5, 3]</div>
                            </div>
                            <div class="process-step">
                                <div class="step-number">3</div>
                                <div class="step-content">'2' → Push 2 → Stack: [2, 5, 3]</div>
                            </div>
                            <div class="process-step">
                                <div class="step-number">4</div>
                                <div class="step-content">'*' → Pop 2, Pop 5 → 5*2=10 → Push 10 → Stack: [10, 3]</div>
                            </div>
                            <div class="process-step">
                                <div class="step-number">5</div>
                                <div class="step-content">'+' → Pop 10, Pop 3 → 3+10=13 → Push 13 → Stack: [13]</div>
                            </div>
                        </div>
                        <div class="highlight-box">
                            🎉 최종 결과: <strong>13</strong>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 7. 메모리 관리 -->
            <div class="section">
                <h2 class="section-title">7️⃣ 메모리 관리</h2>
                
                <div class="grid-2">
                    <div class="card">
                        <div class="card-title">💾 동적 메모리 할당</div>
                        <div style="margin: 15px 0;">
                            <strong>할당 위치:</strong>
                            <ul style="margin-top: 10px;">
                                <li>Line 43: Node 할당</li>
                                <li>Line 56: opNode 할당</li>
                                <li>Line 80: Postfixnode 할당</li>
                                <li>Line 141-145: 3개 스택 할당</li>
                            </ul>
                        </div>
                        <div class="code-block" style="font-size: 0.85em; margin-top: 15px;">
                            <div class="code-line">Node* n = malloc(sizeof(Node));</div>
                            <div class="code-line">if (!n) return NULL;</div>
                        </div>
                    </div>
                    
                    <div class="card">
                        <div class="card-title">🗑️ 메모리 해제 (FreeAll)</div>
                        <div class="code-block" style="font-size: 0.85em; margin: 15px 0;">
                            <div class="code-line"><span class="keyword">while</span>(head != <span class="keyword">NULL</span>) {</div>
                            <div class="code-line">  temp = head;</div>
                            <div class="code-line">  head = head-><span class="variable">next</span>;</div>
                            <div class="code-line">  <span class="keyword">free</span>(temp);</div>
                            <div class="code-line">}</div>
                        </div>
                        <div style="background: #fff3cd; padding: 10px; border-radius: 5px; margin-top: 10px;">
                            ⚠️ <strong>문제점:</strong> OpStack, CalcStack 미해제
                        </div>
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🔄 메모리 해제 과정</h3>
                    <div style="display: flex; justify-content: space-around; align-items: center; flex-wrap: wrap; gap: 30px; padding: 20px;">
                        <div style="text-align: center;">
                            <h4 style="color: #667eea; margin-bottom: 15px;">Before FreeAll</h4>
                            <div style="background: #f8f9fa; padding: 20px; border-radius: 10px;">
                                <div style="padding: 10px; background: #667eea; color: white; margin: 5px; border-radius: 5px;">Node 5</div>
                                <div style="font-size: 1.5em; color: #667eea;">↓</div>
                                <div style="padding: 10px; background: #667eea; color: white; margin: 5px; border-radius: 5px;">Node 4</div>
                                <div style="font-size: 1.5em; color: #667eea;">↓</div>
                                <div style="padding: 10px; background: #667eea; color: white; margin: 5px; border-radius: 5px;">Node 3</div>
                                <div style="font-size: 1.5em; color: #667eea;">↓</div>
                                <div style="padding: 10px; background: #999; color: white; margin: 5px; border-radius: 5px;">NULL</div>
                            </div>
                        </div>
                        
                        <div style="font-size: 3em; color: #764ba2;">→</div>
                        
                        <div style="text-align: center;">
                            <h4 style="color: #764ba2; margin-bottom: 15px;">After FreeAll</h4>
                            <div style="background: #f8f9fa; padding: 40px 60px; border-radius: 10px;">
                                <div style="padding: 10px; background: #999; color: white; margin: 5px; border-radius: 5px;">NULL</div>
                                <div style="margin-top: 10px; color: #51cf66; font-weight: bold;">✅ 모두 해제됨</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 8. 프로그램 흐름 요약 -->
            <div class="section">
                <h2 class="section-title">8️⃣ 전체 실행 흐름 요약</h2>
                
                <div class="diagram-box">
                    <div class="flow-diagram" style="flex-direction: column; align-items: stretch;">
                        <div class="flow-box" style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white;">
                            🚀 <strong>프로그램 시작</strong>
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            📂 <strong>파일 열기</strong> - argc 체크, fopen()
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            📖 <strong>라인 읽기</strong> - fgets() 루프
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            🔍 <strong>키워드 분석</strong> - function/begin/int/end 판별
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            📚 <strong>스택 관리</strong> - Push/Pop 처리
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            🧮 <strong>수식 계산</strong> - Infix→Postfix→Result
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            📤 <strong>결과 출력</strong> - printf()
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box">
                            🗑️ <strong>메모리 해제</strong> - FreeAll(), fclose()
                        </div>
                        <div class="arrow" style="transform: rotate(90deg); margin: 10px 0;">→</div>
                        <div class="flow-box" style="background: linear-gradient(135deg, #51cf66 0%, #40c057 100%); color: white;">
                            ✅ <strong>프로그램 종료</strong>
                        </div>
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">⏱️ 실행 타임라인 요약</h3>
                    <div class="table-container">
                        <table>
                            <thead>
                                <tr>
                                    <th>시간</th>
                                    <th>라인</th>
                                    <th>동작</th>
                                    <th>스택 상태</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr>
                                    <td>T0</td>
                                    <td>-</td>
                                    <td>프로그램 시작</td>
                                    <td>[]</td>
                                </tr>
                                <tr>
                                    <td>T1</td>
                                    <td>1</td>
                                    <td>function main</td>
                                    <td>[type=2, 'm']</td>
                                </tr>
                                <tr>
                                    <td>T2</td>
                                    <td>2</td>
                                    <td>begin</td>
                                    <td>[type=4] → [type=2]</td>
                                </tr>
                                <tr>
                                    <td>T3</td>
                                    <td>3</td>
                                    <td>int x = 3</td>
                                    <td>[x=3] → [begin] → [main]</td>
                                </tr>
                                <tr>
                                    <td>T4</td>
                                    <td>4</td>
                                    <td>int y = 5</td>
                                    <td>[y=5] → [x=3] → [begin] → [main]</td>
                                </tr>
                                <tr>
                                    <td>T5</td>
                                    <td>5</td>
                                    <td>(x + y) 계산</td>
                                    <td>LastExpReturn = 8</td>
                                </tr>
                                <tr>
                                    <td>T6</td>
                                    <td>6</td>
                                    <td>end, 출력</td>
                                    <td>Output=8</td>
                                </tr>
                                <tr>
                                    <td>T7</td>
                                    <td>-</td>
                                    <td>종료</td>
                                    <td>[]</td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- 9. 함수 목록 -->
            <div class="section">
                <h2 class="section-title">9️⃣ 전체 함수 목록</h2>
                
                <div class="table-container">
                    <table>
                        <thead>
                            <tr>
                                <th>함수명</th>
                                <th>라인</th>
                                <th>기능</th>
                                <th>시간복잡도</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td><strong>Push()</strong></td>
                                <td>42-52</td>
                                <td>스택에 노드 추가</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>Pop()</strong></td>
                                <td>100-110</td>
                                <td>스택에서 노드 제거</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>PushOp()</strong></td>
                                <td>54-62</td>
                                <td>연산자 스택에 추가</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>PopOp()</strong></td>
                                <td>64-75</td>
                                <td>연산자 스택에서 제거</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>PushPostfix()</strong></td>
                                <td>77-85</td>
                                <td>계산 스택에 추가</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>PopPostfix()</strong></td>
                                <td>87-98</td>
                                <td>계산 스택에서 제거</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>GetVal()</strong></td>
                                <td>528-542</td>
                                <td>변수/함수 값 조회</td>
                                <td>O(n)</td>
                            </tr>
                            <tr>
                                <td><strong>Priotry()</strong></td>
                                <td>117-122</td>
                                <td>연산자 우선순위 반환</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>isStackEmpty()</strong></td>
                                <td>112-115</td>
                                <td>스택 비어있는지 확인</td>
                                <td>O(1)</td>
                            </tr>
                            <tr>
                                <td><strong>GetLastFunctionCall()</strong></td>
                                <td>519-526</td>
                                <td>마지막 함수 호출 위치</td>
                                <td>O(n)</td>
                            </tr>
                            <tr>
                                <td><strong>FreeAll()</strong></td>
                                <td>508-517</td>
                                <td>모든 노드 메모리 해제</td>
                                <td>O(n)</td>
                            </tr>
                            <tr>
                                <td><strong>my_stricmp()</strong></td>
                                <td>544-554</td>
                                <td>대소문자 무시 문자열 비교</td>
                                <td>O(n)</td>
                            </tr>
                            <tr>
                                <td><strong>rstrip()</strong></td>
                                <td>556-560</td>
                                <td>문자열 끝 공백 제거</td>
                                <td>O(n)</td>
                            </tr>
                            <tr>
                                <td><strong>main()</strong></td>
                                <td>124-408</td>
                                <td>메인 함수 (전체 제어)</td>
                                <td>O(n*m)</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- 10. 결론 및 개선 -->
            <div class="section">
                <h2 class="section-title">🔟 결론 및 개선 방안</h2>
                
                <div class="grid-2">
                    <div class="card">
                        <div class="card-title">✅ 프로그램 장점</div>
                        <ul>
                            <li><strong>명확한 구조</strong> - 스택 기반 단순 설계</li>
                            <li><strong>교육적 가치</strong> - 인터프리터 원리 학습에 최적</li>
                            <li><strong>효율적 계산</strong> - Postfix 방식 사용</li>
                            <li><strong>LIFO 활용</strong> - 스코프 관리 용이</li>
                            <li><strong>모듈화</strong> - 함수별 역할 분리</li>
                        </ul>
                    </div>
                    
                    <div class="card">
                        <div class="card-title">⚠️ 개선 필요 사항</div>
                        <ul>
                            <li><strong>메모리 누수</strong> - OpStack, CalcStack 미해제</li>
                            <li><strong>변수명 제한</strong> - 1글자만 가능</li>
                            <li><strong>에러 처리 부족</strong> - 0으로 나누기 등</li>
                            <li><strong>함수 인자</strong> - 1개로 제한</li>
                            <li><strong>성능 최적화</strong> - 해시테이블 미사용</li>
                        </ul>
                    </div>
                </div>

                <div class="diagram-box">
                    <h3 style="text-align: center; margin-bottom: 25px;">🔧 구체적 개선 방안</h3>
                    
                    <div class="grid-3">
                        <div class="card">
                            <div class="card-title">1️⃣ 메모리 누수 해결</div>
                            <div class="code-block" style="font-size: 0.8em;">
                                <div class="code-line"><span class="comment">// 추가 필요</span></div>
                                <div class="code-line">FreeOpStack(MathStack);</div>
                                <div class="code-line">FreeCalcStack(CalcStack);</div>
                            </div>
                        </div>
                        
                        <div class="card">
                            <div class="card-title">2️⃣ 에러 처리 강화</div>
                            <div class="code-block" style="font-size: 0.8em;">
                                <div class="code-line"><span class="keyword">if</span> (val1 == <span class="number">0</span>) {</div>
                                <div class="code-line">  printf(<span class="variable">"Error: Div by 0"</span>);</div>
                                <div class="code-line">  exit(<span class="number">1</span>);</div>
                                <div class="code-line">}</div>
                            </div>
                        </div>
                        
                        <div class="card">
                            <div class="card-title">3️⃣ 변수명 확장</div>
                            <div class="code-block" style="font-size: 0.8em;">
                                <div class="code-line"><span class="comment">// char → string</span></div>
                                <div class="code-line"><span class="keyword">char</span> exp_data[<span class="number">256</span>];</div>
                                <div class="code-line"><span class="comment">// "variable_name"</span></div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="conclusion">
                    <h3>📚 학습 내용 종합</h3>
                    <div class="grid-2" style="gap: 30px; margin-top: 20px;">
                        <div>
                            <h4 style="font-size: 1.3em; margin-bottom: 15px;">🎓 핵심 학습 사항</h4>
                            <ul>
                                <li>인터프리터 동작 원리 이해</li>
                                <li>스택 자료구조 실전 활용</li>
                                <li>Infix ↔ Postfix 변환 알고리즘</li>
                                <li>동적 메모리 관리 기법</li>
                                <li>파일 I/O 및 문자열 처리</li>
                                <li>토큰 파싱 및 구문 분석</li>
                            </ul>
                        </div>
                        <div>
                            <h4 style="font-size: 1.3em; margin-bottom: 15px;">💼 실무 응용 가능성</h4>
                            <ul>
                                <li>컴파일러/인터프리터 설계</li>
                                <li>DSL (Domain Specific Language) 개발</li>
                                <li>수식 계산기 구현</li>
                                <li>스크립트 엔진 개발</li>
                                <li>파서 및 렉서 구현</li>
                                <li>시스템 프로그래밍 기초</li>
                            </ul>
                        </div>
                    </div>
                    
                    <div style="margin-top: 40px; padding: 25px; background: rgba(255,255,255,0.1); border-radius: 10px; text-align: center;">
                        <p style="font-size: 1.2em; line-height: 1.8;">
                            본 프로젝트를 통해 <strong>인터프리터의 내부 동작 원리</strong>를 깊이 이해할 수 있었으며,<br>
                            <strong>스택 자료구조의 실전 활용법</strong>과 <strong>수식 처리 알고리즘</strong>을 학습하였습니다.<br>
                            향후 더 복잡한 언어 처리 시스템 개발의 <strong>기초 토대</strong>를 마련하였습니다.
                        </p>
                    </div>
                </div>
            </div>

            <!-- 부록 -->
            <div class="section">
                <h2 class="section-title">📎 부록: 실행 가이드</h2>
                
                <div class="diagram-box">
                    <h3 style="margin-bottom: 20px;">🖥️ 컴파일 및 실행 방법</h3>
                    <div class="code-block">
                        <div class="code-line"><span class="comment"># 1. 컴파일</span></div>
                        <div class="code-line">$ gcc basic_interpreter.c -o basic_interpreter.exe</div>
                        <div class="code-line"></div>
                        <div class="code-line"><span class="comment"># 2. 실행</span></div>
                        <div class="code-line">$ basic_interpreter.exe sample.spl</div>
                        <div class="code-line"></div>
                        <div class="code-line"><span class="comment"># 3. 결과</span></div>
                        <div class="code-line"><span class="variable">Output=8</span></div>
                        <div class="code-line">Press a key to exit...</div>
                    </div>
                </div>

                <div class="grid-2">
                    <div class="card">
                        <div class="card-title">✅ 실행 전 체크리스트</div>
                        <ul>
                            <li>MinGW 또는 GCC 설치 완료</li>
                            <li>.spl 테스트 파일 준비</li>
                            <li>컴파일 명령어 확인</li>
                            <li>실행 권한 확인</li>
                        </ul>
                    </div>
                    <div class="card">
                        <div class="card-title">🐛 디버깅 팁</div>
                        <ul>
                            <li>printf()로 변수 값 추적</li>
                            <li>스택 상태 출력 함수 추가</li>
                            <li>GDB 디버거 활용</li>
                            <li>단계별 실행 확인</li>
                        </ul>
                    </div>
                </div>

                <div class="highlight-box" style="margin-top: 30px; text-align: center;">
                    <strong>📅 작성 완료일:</strong> 2025년 10월 29일<br>
                    <strong>⏱️ 분석 소요 시간:</strong> 11시간<br>
                    <strong>📖 참고 자료:</strong> basic_interpreter.c 소스코드
                </div>
            </div>
        </div>
    </div>

    <script>
        // 페이지 로드 시 애니메이션
        document.addEventListener('DOMContentLoaded', function() {
            const sections = document.querySelectorAll('.section');
            
            const observer = new IntersectionObserver((entries) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        entry.target.style.opacity = '0';
                        entry.target.style.transform = 'translateY(20px)';
                        entry.target.style.transition = 'opacity 0.6s ease-out, transform 0.6s ease-out';
                        
                        setTimeout(() => {
                            entry.target.style.opacity = '1';
                            entry.target.style.transform = 'translateY(0)';
                        }, 100);
                    }
                });
            }, { threshold: 0.1 });

            sections.forEach(section => {
                observer.observe(section);
            });

            // 스택 아이템 호버 효과
            const stackItems = document.querySelectorAll('.stack-item');
            stackItems.forEach(item => {
                item.addEventListener('mouseenter', function() {
                    this.style.transform = 'scale(1.05)';
                    this.style.transition = 'transform 0.3s ease';
                });
                item.addEventListener('mouseleave', function() {
                    this.style.transform = 'scale(1)';
                });
            });

            // 카드 3D 효과
            const cards = document.querySelectorAll('.card');
            cards.forEach(card => {
                card.addEventListener('mousemove', function(e) {
                    const rect = this.getBoundingClientRect();
                    const x = e.clientX - rect.left;
                    const y = e.clientY - rect.top;
                    
                    const centerX = rect.width / 2;
                    const centerY = rect.height / 2;
                    
                    const rotateX = (y - centerY) / 10;
                    const rotateY = (centerX - x) / 10;
                    
                    this.style.transform = `perspective(1000px) rotateX(${rotateX}deg) rotateY(${rotateY}deg) translateY(-5px)`;
                });
                
                card.addEventListener('mouseleave', function() {
                    this.style.transform = 'perspective(1000px) rotateX(0) rotateY(0) translateY(0)';
                });
            });

            // 타임라인 애니메이션
            const timelineItems = document.querySelectorAll('.timeline-item');
            const timelineObserver = new IntersectionObserver((entries) => {
                entries.forEach((entry, index) => {
                    if (entry.isIntersecting) {
                        setTimeout(() => {
                            entry.target.style.opacity = '0';
                            entry.target.style.transform = 'translateX(-50px)';
                            entry.target.style.transition = 'opacity 0.5s ease-out, transform 0.5s ease-out';
                            
                            setTimeout(() => {
                                entry.target.style.opacity = '1';
                                entry.target.style.transform = 'translateX(0)';
                            }, 50);
                        }, index * 100);
                    }
                });
            }, { threshold: 0.5 });

            timelineItems.forEach(item => {
                timelineObserver.observe(item);
            });

            // 테이블 행 클릭 효과
            const tableRows = document.querySelectorAll('tbody tr');
            tableRows.forEach(row => {
                row.addEventListener('click', function() {
                    this.style.backgroundColor = '#e8f4f8';
                    setTimeout(() => {
                        this.style.backgroundColor = '';
                    }, 500);
                });
            });

            // 코드 블록 복사 기능
            const codeBlocks = document.querySelectorAll('.code-block');
            codeBlocks.forEach(block => {
                block.style.position = 'relative';
                
                const copyButton = document.createElement('button');
                copyButton.textContent = '📋';
                copyButton.style.cssText = `
                    position: absolute;
                    top: 10px;
                    right: 10px;
                    background: rgba(255,255,255,0.2);
                    border: none;
                    padding: 8px 12px;
                    border-radius: 5px;
                    cursor: pointer;
                    font-size: 1.2em;
                    transition: all 0.3s;
                `;
                
                copyButton.addEventListener('mouseenter', function() {
                    this.style.background = 'rgba(255,255,255,0.3)';
                    this.style.transform = 'scale(1.1)';
                });
                
                copyButton.addEventListener('mouseleave', function() {
                    this.style.background = 'rgba(255,255,255,0.2)';
                    this.style.transform = 'scale(1)';
                });
                
                copyButton.addEventListener('click', function() {
                    const text = block.innerText;
                    navigator.clipboard.writeText(text).then(() => {
                        this.textContent = '✅';
                        setTimeout(() => {
                            this.textContent = '📋';
                        }, 2000);
                    });
                });
                
                block.appendChild(copyButton);
            });

            // 스크롤 진행 표시
            const progressBar = document.createElement('div');
            progressBar.style.cssText = `
                position: fixed;
                top: 0;
                left: 0;
                width: 0%;
                height: 4px;
                background: linear-gradient(90deg, #667eea 0%, #764ba2 100%);
                z-index: 9999;
                transition: width 0.1s ease;
            `;
            document.body.appendChild(progressBar);

            window.addEventListener('scroll', function() {
                const scrollTop = window.pageYOffset || document.documentElement.scrollTop;
                const scrollHeight = document.documentElement.scrollHeight - document.documentElement.clientHeight;
                const scrollPercent = (scrollTop / scrollHeight) * 100;
                progressBar.style.width = scrollPercent + '%';
            });

            // 섹션 네비게이션 (선택사항)
            const nav = document.createElement('div');
            nav.style.cssText = `
                position: fixed;
                right: 30px;
                top: 50%;
                transform: translateY(-50%);
                z-index: 1000;
                background: white;
                padding: 15px;
                border-radius: 10px;
                box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            `;

            const sectionTitles = document.querySelectorAll('.section-title');
            sectionTitles.forEach((title, index) => {
                const dot = document.createElement('div');
                dot.style.cssText = `
                    width: 12px;
                    height: 12px;
                    background: #ddd;
                    border-radius: 50%;
                    margin: 10px 0;
                    cursor: pointer;
                    transition: all 0.3s;
                `;
                
                dot.addEventListener('mouseenter', function() {
                    this.style.background = '#667eea';
                    this.style.transform = 'scale(1.3)';
                });
                
                dot.addEventListener('mouseleave', function() {
                    this.style.background = '#ddd';
                    this.style.transform = 'scale(1)';
                });
                
                dot.addEventListener('click', function() {
                    title.scrollIntoView({ behavior: 'smooth', block: 'start' });
                });
                
                nav.appendChild(dot);
            });

            document.body.appendChild(nav);

            // Flow Box 애니메이션
            const flowBoxes = document.querySelectorAll('.flow-box');
            flowBoxes.forEach((box, index) => {
                setTimeout(() => {
                    box.style.opacity = '0';
                    box.style.transform = 'scale(0.8)';
                    box.style.transition = 'all 0.5s ease-out';
                    
                    setTimeout(() => {
                        box.style.opacity = '1';
                        box.style.transform = 'scale(1)';
                    }, 100);
                }, index * 200);
            });

            // Process Step 순차 애니메이션
            const processSteps = document.querySelectorAll('.process-step');
            const processObserver = new IntersectionObserver((entries) => {
                entries.forEach(entry => {
                    if (entry.isIntersecting) {
                        const steps = entry.target.parentElement.querySelectorAll('.process-step');
                        steps.forEach((step, index) => {
                            setTimeout(() => {
                                step.style.opacity = '0';
                                step.style.transform = 'translateX(-30px)';
                                step.style.transition = 'all 0.5s ease-out';
                                
                                setTimeout(() => {
                                    step.style.opacity = '1';
                                    step.style.transform = 'translateX(0)';
                                }, 50);
                            }, index * 150);
                        });
                    }
                });
            }, { threshold: 0.3 });

            const processFlows = document.querySelectorAll('.process-flow');
            processFlows.forEach(flow => {
                processObserver.observe(flow);
            });

            // 다크모드 토글 (보너스)
            const darkModeToggle = document.createElement('button');
            darkModeToggle.textContent = '🌙';
            darkModeToggle.style.cssText = `
                position: fixed;
                bottom: 30px;
                right: 30px;
                width: 60px;
                height: 60px;
                border-radius: 50%;
                border: none;
                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                color: white;
                font-size: 1.5em;
                cursor: pointer;
                box-shadow: 0 5px 15px rgba(0,0,0,0.3);
                transition: all 0.3s;
                z-index: 9999;
            `;

            darkModeToggle.addEventListener('mouseenter', function() {
                this.style.transform = 'scale(1.1) rotate(15deg)';
            });

            darkModeToggle.addEventListener('mouseleave', function() {
                this.style.transform = 'scale(1) rotate(0deg)';
            });

            let isDark = false;
            darkModeToggle.addEventListener('click', function() {
                isDark = !isDark;
                if (isDark) {
                    document.body.style.background = 'linear-gradient(135deg, #2c3e50 0%, #34495e 100%)';
                    document.querySelector('.container').style.background = '#2c3e50';
                    document.querySelector('.container').style.color = '#ecf0f1';
                    this.textContent = '☀️';
                    
                    document.querySelectorAll('.card, .diagram-box').forEach(el => {
                        el.style.background = '#34495e';
                        el.style.color = '#ecf0f1';
                    });
                } else {
                    document.body.style.background = 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)';
                    document.querySelector('.container').style.background = 'white';
                    document.querySelector('.container').style.color = '#2c3e50';
                    this.textContent = '🌙';
                    
                    document.querySelectorAll('.card, .diagram-box').forEach(el => {
                        el.style.background = '';
                        el.style.color = '';
                    });
                }
            });

            document.body.appendChild(darkModeToggle);

            // 인쇄 버튼
            const printButton = document.createElement('button');
            printButton.textContent = '🖨️';
            printButton.style.cssText = `
                position: fixed;
                bottom: 100px;
                right: 30px;
                width: 60px;
                height: 60px;
                border-radius: 50%;
                border: none;
                background: linear-gradient(135deg, #51cf66 0%, #40c057 100%);
                color: white;
                font-size: 1.5em;
                cursor: pointer;
                box-shadow: 0 5px 15px rgba(0,0,0,0.3);
                transition: all 0.3s;
                z-index: 9999;
            `;

            printButton.addEventListener('mouseenter', function() {
                this.style.transform = 'scale(1.1)';
            });

            printButton.addEventListener('mouseleave', function() {
                this.style.transform = 'scale(1)';
            });

            printButton.addEventListener('click', function() {
                window.print();
            });

            document.body.appendChild(printButton);

            console.log('%c📊 BASIC Interpreter 분석 보고서 ', 'background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 10px 20px; font-size: 16px; font-weight: bold;');
            console.log('%c제작: 조국현 | 과목: 운영체제 | 2025-10-29', 'color: #667eea; font-size: 12px;');
        });
    </script>
</body>
</html>
