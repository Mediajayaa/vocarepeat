<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VocaRepeat - Belajar Kosakata</title>
    <style>
        * {margin:0;padding:0;box-sizing:border-box;}
        body {
            font-family:'Segoe UI',Tahoma,Geneva,Verdana,sans-serif;
            background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);
            min-height:100vh; padding:20px;
        }
        .container {
            max-width:500px; margin:0 auto; background:white; border-radius:20px;
            padding:30px; box-shadow:0 10px 40px rgba(0,0,0,0.3);
        }
        h1 {text-align:center;color:#667eea;margin-bottom:10px;font-size:36px;}
        .subtitle {text-align:center;color:#666;margin-bottom:30px;font-size:14px;}

        .menu-btn {
            width:100%; padding:20px; margin:15px 0; font-size:20px; border:none;
            border-radius:15px; cursor:pointer; transition:.3s; font-weight:bold;
            box-shadow:0 4px 15px rgba(0,0,0,0.1);
        }
        .btn-flashcard {background:linear-gradient(135deg,#667eea,#764ba2);color:white;}
        .btn-matching  {background:linear-gradient(135deg,#f093fb,#f5576c);color:white;}
        .btn-quiz      {background:linear-gradient(135deg,#4facfe,#00f2fe);color:white;}

        .menu-btn:hover {transform:translateY(-3px);box-shadow:0 6px 20px rgba(0,0,0,0.2);}
        .hidden {display:none;}

        .card {
            background:#f8f9fa;border-radius:15px;padding:30px;margin:20px 0;
            text-align:center; min-height:250px; display:flex; flex-direction:column; justify-content:center;
        }
        .word {font-size:48px;margin:20px 0;font-weight:bold; display:flex; align-items:center; justify-content:center; gap: 10px;}
        .meaning {font-size:24px;color:#333;margin:15px 0;}
        .example {font-size:18px;color:#666;font-style:italic;margin-top:15px;line-height:1.6;}

        .btn {
            padding:15px 30px;margin:10px 5px;font-size:18px;border:none;
            border-radius:10px;cursor:pointer;font-weight:bold;transition:.3s;
        }
        .btn-primary {background:#667eea;color:white;}
        .btn-success {background:#28a745;color:white;}
        .btn-secondary {background:#6c757d;color:white;}
        .btn:hover {transform:scale(1.05);box-shadow:0 4px 15px rgba(0,0,0,0.2);}

        .progress {text-align:center;color:#666;margin-bottom:15px;font-size:16px;}

        .grid {display:grid;grid-template-columns:1fr 1fr;gap:10px;margin:20px 0;}
        .grid-btn {
            padding:20px;font-size:16px;border:2px solid #ddd;border-radius:10px;
            background:white;cursor:pointer;transition:.3s;min-height:80px;
        }
        .grid-btn:hover {background:#f8f9fa;}
        .grid-btn.selected {background:#ffc107;border-color:#ffc107;}
        .grid-btn.matched {background:#28a745;color:white;border-color:#28a745;}

        .quiz-options {margin:20px 0;}
        .quiz-option {
            width:100%;padding:15px;margin:10px 0;font-size:18px;border:2px solid #ddd;
            border-radius:10px;background:white;cursor:pointer;text-align:left;transition:.3s;
        }
        .quiz-option:hover {background:#f8f9fa;border-color:#667eea;}
        .quiz-option.selected {background:#667eea;color:white;border-color:#667eea;}

        .wrong-words {
            background:#ffebee;border-left:4px solid #f44336;
            padding:15px;margin:15px 0;border-radius:10px;
        }
        .score {font-size:32px;font-weight:bold;color:#667eea;margin:20px 0;}
    </style>
</head>
<body>

<div class="container">
    <div id="menuPage">
        <h1>🍎 VocaRepeat 📚</h1>
        <p class="subtitle">Belajar Kosakata Bahasa Inggris dengan Mudah!</p>

        <button class="menu-btn btn-flashcard" onclick="startFlashcard()">📇 Flashcards</button>
        <button class="menu-btn btn-matching" onclick="startMatching()">🎯 Matching Game</button>
        <button class="menu-btn btn-quiz" onclick="startQuiz()">✅ Quiz</button>
    </div>

    <div id="flashcardPage" class="hidden">
        <div class="progress" id="flashProgress"></div>
        <div class="card">
            <div class="word" id="flashWord"></div>
            <div id="flashContent"></div>
        </div>
        <button class="btn btn-primary" onclick="flipCard()">Lihat Arti</button>
        <button class="btn btn-success" onclick="nextCard()">Kartu Selanjutnya →</button>
        <button class="btn btn-secondary" onclick="backToMenu()">Kembali ke Menu</button>
    </div>

    <div id="matchingPage" class="hidden">
        <h2 style="text-align:center;margin-bottom:20px;">🎯 Cocokkan Kata!</h2>
        <div class="grid" id="matchingGrid"></div>
        <button class="btn btn-primary" onclick="setupMatching()" style="width:100%">🔄 Ulangi Game</button>
        <button class="btn btn-secondary" onclick="backToMenu()" style="width:100%;margin-top:10px;">Kembali ke Menu</button>
    </div>

    <div id="quizPage" class="hidden">
        <div class="progress" id="quizProgress"></div>
        <div class="card">
            <div class="word" id="quizWord"></div>
            <p style="margin-top:20px;font-size:18px;color:#666;">Pilih arti yang benar:</p>
        </div>
        <div class="quiz-options" id="quizOptions"></div>
        <button class="btn btn-success" onclick="submitAnswer()" style="width:100%">Submit Jawaban</button>
    </div>

    <div id="resultPage" class="hidden">
        <h1 style="text-align:center;margin-bottom:30px;">🎉 Hasil Quiz 🎉</h1>
        <div class="score" id="scoreDisplay"></div>
        <div id="wrongWordsDisplay"></div>
        <button class="btn btn-primary" onclick="backToMenu()" style="width:100%;margin-top:20px;">Kembali ke Menu</button>
    </div>
</div>

<script>
const vocabulary = [
    {word:"apple",emoji:"🍎",meaning:"apel",example:"I eat an apple every morning."},
    {word:"banana",emoji:"🍌",meaning:"pisang",example:"She likes yellow bananas."},
    {word:"rice",emoji:"🍚",meaning:"nasi",example:"We eat rice for lunch."},
    {word:"chicken",emoji:"🍗",meaning:"ayam",example:"He cooks chicken for dinner."},
    {word:"fish",emoji:"🐟",meaning:"ikan",example:"My father catches fish."},
    {word:"bread",emoji:"🍞",meaning:"roti",example:"She buys fresh bread."},
    {word:"milk",emoji:"🥛",meaning:"susu",example:"Children drink milk every day."},
    {word:"water",emoji:"💧",meaning:"air",example:"I need a glass of water."},
    {word:"egg",emoji:"🥚",meaning:"telur",example:"She boils an egg."},
    {word:"soup",emoji:"🍲",meaning:"sup",example:"This soup is very hot."},
    {word:"noodles",emoji:"🍜",meaning:"mie",example:"I love eating noodles."},
    {word:"coffee",emoji:"☕",meaning:"kopi",example:"He drinks coffee in the morning."},
    {word:"sugar",emoji:"🧂",meaning:"gula",example:"Add some sugar to your tea."},
    {word:"salt",emoji:"🧂",meaning:"garam",example:"The soup needs more salt."},
    {word:"cheese",emoji:"🧀",meaning:"keju",example:"I put cheese on my sandwich."},
    {word:"cake",emoji:"🍰",meaning:"kue",example:"She bakes a birthday cake."},
    {word:"juice",emoji:"🧃",meaning:"jus",example:"Orange juice is healthy."}
];

// Fungsi Text-to-Speech (TTS)
function speak(text) {
    if ('speechSynthesis' in window) {
        const utterance = new SpeechSynthesisUtterance(text);
        utterance.lang = 'en-US'; 
        utterance.rate = 0.9;
        window.speechSynthesis.speak(utterance);
    } 
    // Tidak ada alert jika browser tidak mendukung karena ini untuk aplikasi Android
}

let currentIndex=0,showMeaning=false;
let flashcardData=[],quizData=[],currentQuestion=0,correctAnswers=0,wrongWords=[],selectedAnswer=null;

function showPage(id){
    document.querySelectorAll('.container > div').forEach(x=>x.classList.add('hidden'));
    document.getElementById(id).classList.remove('hidden');
}
function backToMenu(){showPage('menuPage');resetStates();}
function resetStates(){
    currentIndex=0;showMeaning=false;
    currentQuestion=0;correctAnswers=0;wrongWords=[];
    selectedAnswer=null;
}

// FLASHCARD
function startFlashcard(){
    // Mengacak data agar kartu tidak berurutan
    flashcardData = [...vocabulary].sort(() => Math.random() - 0.5); 
    showPage('flashcardPage');
    currentIndex=0;showMeaning=false;
    updateFlashcard();
}
function updateFlashcard(){
    const v = flashcardData[currentIndex]; // Menggunakan flashcardData
    document.getElementById('flashProgress').textContent=`Kartu ${currentIndex+1}/${flashcardData.length}`;
    
    // Menampilkan ikon suara 🔊 (TTS)
    document.getElementById('flashWord').innerHTML = `
        ${v.emoji} ${v.word} 
        <span onclick="speak('${v.word}')" style="cursor:pointer; font-size:30px;">🔊</span>
    `;
    
    document.getElementById('flashContent').innerHTML = showMeaning
        ? `<div class="meaning">${v.meaning}</div><div class="example">"${v.example}"</div>`
        : `<p style="color:#999;font-size:18px;">Tekan "Lihat Arti"</p>`;
}
function flipCard(){showMeaning=!showMeaning;updateFlashcard();}
function nextCard(){currentIndex=(currentIndex+1)%flashcardData.length;showMeaning=false;updateFlashcard();}

// MATCHING
let matchingCards=[],selectedCards=[],matchedPairs=0;
function startMatching(){showPage('matchingPage');setupMatching();}
function setupMatching(){
    const grid=document.getElementById('matchingGrid');
    grid.innerHTML='';matchingCards=[];selectedCards=[];matchedPairs=0;
    const chosen=[...vocabulary].sort(()=>Math.random()-0.5).slice(0,6);
    chosen.forEach(v=>{
        matchingCards.push({pair:v.word,text:`${v.emoji} ${v.word}`});
        matchingCards.push({pair:v.word,text:v.meaning});
    });
    matchingCards.sort(()=>Math.random()-0.5);
    matchingCards.forEach((c,i)=>{
        const btn=document.createElement('button');
        btn.className='grid-btn';btn.textContent=c.text;
        btn.onclick=()=>selectMatch(i);
        grid.appendChild(btn);
    });
}
function selectMatch(i){
    const btns=document.querySelectorAll('.grid-btn');
    const btn=btns[i];
    if(btn.classList.contains('matched')||selectedCards.includes(i))return;
    btn.classList.add('selected');selectedCards.push(i);
    if(selectedCards.length===2){
        const c1=matchingCards[selectedCards[0]];
        const c2=matchingCards[selectedCards[1]];
        setTimeout(()=>{
            if(c1.pair===c2.pair){
                btns[selectedCards[0]].classList.add('matched');
                btns[selectedCards[1]].classList.add('matched');
                matchedPairs++;
                if(matchedPairs===6) alert('🎉 Semua pasangan selesai!');
            }
            btns[selectedCards[0]].classList.remove('selected');
            btns[selectedCards[1]].classList.remove('selected');
            selectedCards=[];
        },700);
    }
}

// QUIZ
function startQuiz(){
    showPage('quizPage');
    quizData=[...vocabulary].sort(()=>Math.random()-0.5).slice(0,10);
    currentQuestion=0;correctAnswers=0;wrongWords=[];
    loadQuestion();
}
function loadQuestion(){
    if(currentQuestion>=quizData.length){return showResult();}
    const v=quizData[currentQuestion];
    document.getElementById('quizProgress').textContent=`Pertanyaan ${currentQuestion+1}/10`;
    document.getElementById('quizWord').textContent=`${v.emoji} ${v.word}`;
    
    const opts=[v.meaning,...[...vocabulary].filter(x=>x.word!==v.word).sort(()=>Math.random()-0.5).slice(0,2).map(x=>x.meaning)];
    opts.sort(()=>Math.random()-0.5);
    const wrap=document.getElementById('quizOptions');wrap.innerHTML='';
    opts.forEach((o,i)=>{
        const btn=document.createElement('button');
        btn.className='quiz-option';btn.textContent=o;
        btn.onclick=()=>selectOption(i,o);
        wrap.appendChild(btn);
    });
    selectedAnswer=null;
}
function selectOption(i,val){
    document.querySelectorAll('.quiz-option').forEach(b=>b.classList.remove('selected'));
    document.querySelectorAll('.quiz-option')[i].classList.add('selected');
    selectedAnswer=val;
}
function submitAnswer(){
    if(selectedAnswer===null){alert("Pilih dulu jawabannya!");return;}
    const v=quizData[currentQuestion];
    if(selectedAnswer===v.meaning) correctAnswers++;
    else wrongWords.push(v);
    currentQuestion++; loadQuestion();
}

// RESULT
function showResult(){
    showPage('resultPage');
    const percent=Math.round((correctAnswers/quizData.length)*100);
    document.getElementById('scoreDisplay').textContent=`Skor: ${correctAnswers}/10 (${percent}%)`;

    const box=document.getElementById('wrongWordsDisplay');
    if(wrongWords.length===0){
        box.innerHTML=`<p style="color:green;font-size:18px;text-align:center;">🎉 Semua benar!</p>`;
    } else {
        let html=`<div class="wrong-words"><h3 style="color:#c62828;">❌ Kata Salah:</h3><ul>`;
        wrongWords.forEach(w=>{
            html+=`<li><b>${w.emoji} ${w.word}</b> = ${w.meaning}</li>`;
        });
        html+=`</ul></div>`;
        box.innerHTML=html;
    }
}
</script>

</body>
</html>
