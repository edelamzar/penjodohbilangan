<!DOCTYPE html>
<html lang="ms">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IQ-BI: Kuiz Interaktif Penjodoh Bilangan</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.7.77/Tone.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');
        
        /* Tema Angkasa Dinamik */
        @keyframes move-stars {
            from { background-position: 0 0; }
            to { background-position: -10000px 5000px; }
        }

        body {
            font-family: 'Poppins', sans-serif;
            touch-action: manipulation;
            color: #f1f5f9;
            background-color: #000;
            background-image: 
                /* Lapisan Nebula */
                radial-gradient(at 40% 20%, hsla(255,100%,70%,0.2) 0px, transparent 50%),
                radial-gradient(at 80% 0%, hsla(200,100%,50%,0.2) 0px, transparent 50%),
                radial-gradient(at 0% 50%, hsla(340,100%,70%,0.2) 0px, transparent 50%),
                radial-gradient(at 80% 50%, hsla(170,100%,70%,0.2) 0px, transparent 50%),
                radial-gradient(at 0% 100%, hsla(220,100%,70%,0.3) 0px, transparent 50%),
                radial-gradient(at 80% 100%, hsla(300,100%,70%,0.2) 0px, transparent 50%),
                radial-gradient(at 0% 0%, hsla(340,100%,70%,0.2) 0px, transparent 50%),
                /* Lapisan Bintang */
                url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='100%25' height='100%25'%3E%3Cdefs%3E%3Cstyle%3E .star%7B fill:white; animation: twinkle 2s ease-in-out infinite alternate; %7D @keyframes twinkle %7B 0%25 %7B opacity: 0; %7D 100%25 %7B opacity: 1; %7D %7D %3C/style%3E%3C/defs%3E%3Crect width='100%25' height='100%25' fill='black'/%3E%3Ccircle class='star' cx='10%25' cy='10%25' r='1'/%3E%3Ccircle class='star' style='animation-delay:.1s' cx='20%25' cy='50%25' r='1'/%3E%3Ccircle class='star' style='animation-delay:.2s' cx='80%25' cy='30%25' r='1'/%3E%3Ccircle class='star' style='animation-delay:.3s' cx='50%25' cy='70%25' r='2'/%3E%3Ccircle class='star' style='animation-delay:.4s' cx='90%25' cy='90%25' r='1'/%3E%3Ccircle class='star' style='animation-delay:.5s' cx='30%25' cy='90%25' r='1'/%3E%3Ccircle class='star' style='animation-delay:.6s' cx='70%25' cy='10%25' r='2'/%3E%3C/svg%3E");
            background-attachment: fixed;
            background-size: 100% 100%;
            animation: move-stars 200s linear infinite;
        }

        .card {
            width: 80px;
            height: 120px;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            font-weight: 700;
            color: white;
            position: relative;
            cursor: pointer;
            transition: all 0.2s ease-in-out;
            box-shadow: 0 4px 6px rgba(0,0,0,0.2);
            border: 2px solid rgba(255, 255, 255, 0.3);
        }
        .card.player-card:hover {
            transform: translateY(-10px) scale(1.05);
            box-shadow: 0 8px 12px rgba(0,0,0,0.3);
        }
        .card .card-value { text-shadow: 1px 1px 3px rgba(0,0,0,0.4); }
        .card-corner { position: absolute; font-size: 1rem; font-weight: 600; }
        .card-corner.top { top: 5px; left: 8px; }
        .card-corner.bottom { bottom: 5px; right: 8px; transform: rotate(180deg); }
        .card-back {
            background-color: #1a1a2e;
            background-image: radial-gradient(circle at center, #7f5af0 0%, transparent 60%), linear-gradient(315deg, #16213e 0%, #0f3460 74%);
            border: 2px solid #94a3b8;
        }
        .card-back .logo { 
            font-size: 1.5rem; 
            font-weight: bold; 
            color: #e2e8f0; 
            transform: rotate(-15deg);
            text-shadow: 0 0 10px #a78bfa; /* Lavender glow */
        }
        .bg-merah { background-color: #ef4444; }
        .bg-hijau { background-color: #22c55e; }
        .bg-biru { background-color: #3b82f6; }
        .bg-kuning { background-color: #eab308; }
        .bg-liar { background: linear-gradient(135deg, #ef4444, #eab308, #22c55e, #3b82f6); }
        #player-hand .card, #ai1-hand .card, #ai2-hand .card { margin: 0 -20px; }
        .modal { transition: opacity 0.3s ease; }
        .modal-content {
            animation: slide-up 0.4s ease-out;
            background-color: rgba(30, 41, 59, 0.8); /* slate-800 with 80% opacity */
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 10px 25px rgba(0,0,0,0.3);
            position: relative;
            overflow: hidden;
        }
        @keyframes slide-up {
            from { transform: translateY(30px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        .spinner {
            border: 4px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: #fff;
            width: 40px;
            height: 40px;
            animation: spin 1s ease-in-out infinite;
            margin: 20px auto;
        }
        @keyframes spin { to { transform: rotate(360deg); } }
        /* Styling untuk Soalan Padanan */
        .matching-item.selected { background-color: #3b82f6 !important; color: white; }
        .matching-item.matched { background-color: #16a34a !important; color: white; cursor: not-allowed; }
        .matching-item.wrong { background-color: #dc2626 !important; color: white; }
        /* Styling untuk Soalan Tick */
        .checkbox-label {
            display: flex;
            align-items: center;
            padding: 0.75rem;
            background-color: #334155;
            border-radius: 0.5rem;
            cursor: pointer;
            transition: background-color 0.2s;
            border: 1px solid #475569;
        }
        .checkbox-label:hover { background-color: #475569; }
        .checkbox-label input { display: none; }
        .checkbox-label .custom-checkbox {
            width: 20px;
            height: 20px;
            border: 2px solid #94a3b8;
            border-radius: 4px;
            margin-right: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background-color 0.2s, border-color 0.2s;
        }
        .checkbox-label input:checked + .custom-checkbox {
            background-color: #3b82f6;
            border-color: #3b82f6;
        }
        .checkbox-label input:checked + .custom-checkbox::after {
            content: '✔';
            color: white;
            font-size: 14px;
        }

        /* Animasi Roket */
        @keyframes rocket-launch {
            0% { transform: translate(-50%, 100px) scale(0.8); opacity: 0; }
            20%, 80% { opacity: 1; }
            100% { transform: translate(-50%, -500px) scale(1.2); opacity: 0; }
        }

        #quiz-modal.show .modal-content::before {
            content: '';
            position: absolute;
            bottom: -20px;
            left: 50%;
            width: 80px;
            height: 120px;
            background-image: url("data:image/svg+xml,%3Csvg width='80' height='120' viewBox='0 0 80 120' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M40 0L70 30H10L40 0Z' fill='%23F87171'/%3E%3Crect x='20' y='30' width='40' height='60' rx='5' fill='%23E0E7FF'/%3E%3Cpath d='M20 90L0 120H20V90Z' fill='%234F46E5'/%3E%3Cpath d='M60 90L80 120H60V90Z' fill='%234F46E5'/%3E%3Ccircle cx='40' cy='60' r='12' fill='%233B82F6' stroke='white' stroke-width='2'/%3E%3C/svg%3E");
            background-size: contain;
            background-repeat: no-repeat;
            animation: rocket-launch 1.2s ease-out forwards;
            z-index: 1;
        }

        /* Animasi untuk teks soalan dan jawapan */
        @keyframes fade-in-up {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        #quiz-modal .animated-content {
            position: relative;
            z-index: 2;
            animation: fade-in-up 0.5s 0.2s ease-out backwards;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center min-h-screen p-4 overflow-hidden">

    <!-- Main Game Area -->
    <div id="game-container" class="w-full max-w-5xl mx-auto flex flex-col h-full hidden">
        <div class="h-1/4 flex items-center justify-center space-x-16">
            <div class="flex flex-col items-center">
                <h2 id="p2-info" class="text-lg font-semibold mb-2 text-slate-300">Pemain 2 (0 Kad)</h2>
                <div id="p2-hand" class="flex justify-center"></div>
            </div>
            <div class="flex flex-col items-center">
                <h2 id="p3-info" class="text-lg font-semibold mb-2 text-slate-300">Pemain 3 (0 Kad)</h2>
                <div id="p3-hand" class="flex justify-center"></div>
            </div>
        </div>
        <div class="h-2/4 flex items-center justify-around my-4">
            <div id="draw-pile" class="card card-back">
                <div class="text-center">
                    <span class="logo">IQ-BI</span>
                    <p class="text-xs font-semibold mt-2 leading-none text-white">Kad Tambahan</p>
                </div>
            </div>
            <div id="discard-pile"></div>
            <div id="game-message" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-black/50 text-white px-6 py-3 rounded-lg text-xl font-bold backdrop-blur-sm hidden"> Giliran Anda </div>
        </div>
        <div class="h-1/4 flex flex-col items-center justify-center">
            <div id="player-hand" class="flex justify-center mb-2"></div>
            <div class="flex items-center space-x-4">
                <h2 id="p1-info" class="text-lg font-semibold text-slate-200">Anda (0 Kad)</h2>
                <button id="pass-turn-button" class="hidden mt-2 bg-amber-500 hover:bg-amber-600 text-slate-900 font-bold py-3 px-8 rounded-lg text-lg transition-transform transform hover:scale-105 shadow-lg"> Tamat Giliran </button>
            </div>
        </div>
    </div>
    
    <!-- Start Menu -->
    <div id="start-menu" class="text-center">
        <h1 class="text-6xl font-bold mb-2 text-white">IQ-BI</h1>
        <p class="text-xl mb-6 text-slate-300">Kuiz Interaktif Penjodoh Bilangan</p>
        <div class="space-y-4">
            <button data-mode="ai" class="start-button bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-8 rounded-lg text-2xl transition-transform transform hover:scale-105 w-full md:w-auto"> Lawan Komputer </button>
            <button data-mode="multiplayer" class="start-button bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-8 rounded-lg text-2xl transition-transform transform hover:scale-105 w-full md:w-auto"> Multiplayer (3 Pemain) </button>
        </div>
    </div>

    <!-- Registration Modal -->
    <div id="registration-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-8 w-full max-w-md text-center text-white">
            <h2 class="text-3xl font-bold mb-6">Daftar Pemain</h2>
            <div class="space-y-4 text-left">
                <div>
                    <label for="p1-name" class="block mb-1 font-semibold text-slate-300">Nama Pemain 1:</label>
                    <input type="text" id="p1-name" class="w-full bg-slate-700 text-white p-2 rounded-md border border-slate-600 focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Pemain 1">
                </div>
                <div>
                    <label for="p2-name" class="block mb-1 font-semibold text-slate-300">Nama Pemain 2:</label>
                    <input type="text" id="p2-name" class="w-full bg-slate-700 text-white p-2 rounded-md border border-slate-600 focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Pemain 2">
                </div>
                <div>
                    <label for="p3-name" class="block mb-1 font-semibold text-slate-300">Nama Pemain 3:</label>
                    <input type="text" id="p3-name" class="w-full bg-slate-700 text-white p-2 rounded-md border border-slate-600 focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Pemain 3">
                </div>
            </div>
            <button id="start-multiplayer-game" class="mt-8 bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-8 rounded-lg text-xl"> Mula Permainan </button>
        </div>
    </div>
    
    <!-- Modals -->
    <div id="quiz-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-6 w-full max-w-lg text-center text-white">
            <div class="animated-content">
                <h2 id="quiz-category" class="text-2xl font-bold mb-4">Soalan</h2>
                <div id="quiz-question-container" class="text-lg mb-6 min-h-[150px] flex flex-col justify-center items-center"></div>
                <div id="quiz-options"></div>
            </div>
        </div>
    </div>

    <div id="color-picker-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-6 w-full max-w-sm text-center text-white">
            <h2 class="text-2xl font-bold mb-6">Pilih Warna</h2>
            <div class="flex justify-around">
                <button data-color="merah" class="color-choice w-20 h-20 rounded-full bg-merah transform hover:scale-110 transition-transform"></button>
                <button data-color="hijau" class="color-choice w-20 h-20 rounded-full bg-hijau transform hover:scale-110 transition-transform"></button>
                <button data-color="biru" class="color-choice w-20 h-20 rounded-full bg-biru transform hover:scale-110 transition-transform"></button>
                <button data-color="kuning" class="color-choice w-20 h-20 rounded-full bg-kuning transform hover:scale-110 transition-transform"></button>
            </div>
        </div>
    </div>
    
    <div id="turn-modal" class="modal fixed inset-0 bg-black bg-opacity-80 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-8 w-full max-w-md text-center text-white">
            <h2 id="turn-message" class="text-3xl font-bold mb-6">Giliran Pemain 2</h2>
            <p class="mb-6">Sila serahkan peranti.</p>
            <button id="turn-ok-button" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-8 rounded-lg text-xl"> Mula Giliran </button>
        </div>
    </div>

    <div id="game-over-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-8 w-full max-w-md text-center text-white">
            <div id="game-over-message" class="text-center w-full mb-6"></div>
            <button id="restart-button" class="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-8 rounded-lg text-xl transition-transform transform hover:scale-105"> Main Lagi </button>
        </div>
    </div>
    
    <div id="wrong-answer-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 rounded-lg shadow-xl p-6 w-full max-w-lg text-center text-white">
            <h2 class="text-3xl font-bold mb-4 text-red-500">Jawapan Salah!</h2>
            <p id="wrong-answer-feedback" class="text-lg mb-2">Jawapan yang betul ialah: <strong id="correct-answer-text" class="text-green-400"></strong></p>
            <p class="text-md mb-6">Anda perlu mengambil 2 kad.</p>
            <div class="flex justify-center space-x-4">
                <button id="gemini-explain-button" class="bg-gradient-to-r from-purple-500 to-pink-500 hover:from-purple-600 hover:to-pink-600 text-white font-bold py-2 px-4 rounded-lg"> ✨ Minta Penjelasan </button>
                <button id="close-wrong-answer-button" class="bg-slate-600 hover:bg-slate-700 text-white font-bold py-2 px-4 rounded-lg"> Tutup </button>
            </div>
        </div>
    </div>

    <div id="gemini-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 z-50 hidden">
        <div class="modal-content bg-slate-800 border border-slate-600 rounded-lg shadow-xl p-6 w-full max-w-2xl text-left text-white">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-pink-500">✨ Penjelasan Pintar</h2>
                <button id="close-gemini-button" class="text-slate-400 hover:text-white text-3xl">&times;</button>
            </div>
            <div id="gemini-explanation-content" class="text-slate-300 max-h-[60vh] overflow-y-auto pr-2">
                <div id="gemini-loader" class="text-center p-8">
                    <div class="spinner"></div>
                    <p class="mt-4">AI sedang menjana penjelasan...</p>
                </div>
                <div id="gemini-explanation-text"></div>
            </div>
        </div>
    </div>

    <script>
        const questions = {
            isiTempatKosong: {
                easy: [{ q: "Ibu membeli dua ______ baju.", o: ["helai", "pasang", "keping"], a: "helai" }, { q: "Ayah menangkap se______ ikan.", o: ["ekor", "kawan", "orang"], a: "ekor" }, { q: "Kami membaca se______ buku cerita.", o: ["buah", "naskhah", "batang"], a: "buah" }, { q: "Mereka melihat se______ burung.", o: ["kawan", "ekor", "kelompok"], a: "ekor" }, { q: "Ali menulis se______ surat.", o: ["pucuk", "helai", "keping"], a: "pucuk" }, { q: "Pak Mat membawa dua ______ ayam ke pasar.", o: ["ekor", "kelompok", "kawan"], a: "ekor" }, { q: "Kami duduk di bawah se______ pokok besar.", o: ["batang", "pohon", "rumpun"], a: "batang" }, { q: "Saya membeli se______ kereta mainan.", o: ["buah", "biji", "unit"], a: "buah" }, { q: "Ayah menebang dua ______ pokok pisang.", o: ["batang", "pohon", "tandan"], a: "batang" }, { q: "Siti memakai se______ tudung baharu.", o: ["helai", "keping", "bidang"], a: "helai" }, { q: "Datuk membeli se______ kangkung.", o: ["ikat", "berkas", "cekak"], a: "ikat" }, { q: "Nenek mengupas se______ epal.", o: ["biji", "buah", "ulas"], a: "biji" }, { q: "Kami menaiki se______ bas sekolah.", o: ["buah", "unit", "gerabak"], a: "buah" }, { q: "Saya menampal se______ setem pada sampul surat.", o: ["keping", "helai", "pucuk"], a: "keping" }, { q: "Ibu memasak dua ______ ikan kembung.", o: ["ekor", "tangkai", "potong"], a: "ekor" }, { q: "Amir membawa se______ pensel.", o: ["batang", "bilah", "utas"], a: "batang" }],
                medium: [{ q: "Farah memakai se______ cincin emas.", o: ["utas", "bentuk", "urat"], a: "bentuk" }, { q: "Pihak polis menjumpai beberapa ______ pistol di rumah itu.", o: ["pucuk", "das", "laras"], a: "pucuk" }, { q: "Sultan menghadiahkan se______ kalung berlian kepada permaisuri.", o: ["utas", "urat", "untai"], a: "utas" }, { q: "Perompak itu melepaskan beberapa ______ tembakan.", o: ["das", "butir", "laras"], a: "das" }, { q: "Petani itu meletakkan se______ garam pada umpan pancingnya.", o: ["cubit", "genggam", "butir"], a: "cubit" }],
                hard: [{ q: "Hanya tinggal se______ sahaja lagi sajak yang belum dideklamasikan.", o: ["rangkap", "baris", "bait"], a: "rangkap" }, { q: "Setiap murid dikehendaki membawa se______ rotan untuk aktiviti seni.", o: ["berkas", "ruas", "rumpun"], a: "berkas" }, { q: "Beliau memulakan ucapannya dengan se______ pantun.", o: ["patah", "rangkap", "baris"], a: "patah" }, { q: "Puan Rozi memasukkan se______ asam gelugur ke dalam masakannya.", o: ["hiris", "keping", "potong"], a: "keping" }]
            },
            anekaPilihan: {
                easy: [{ q: "Ibu menggoreng beberapa ______ karipap.", o: ["biji", "ketul", "potong", "ulas"], a: "biji" }, { q: "Siti menulis se______ kad ucapan Hari Guru.", o: ["keping", "helai", "pucuk", "naskhah"], a: "keping" }, { q: "Kami memancing beberapa ______ ikan keli.", o: ["ekor", "kawan", "kelompok", "pasukan"], a: "ekor" }, { q: "Saya membeli se______ komputer riba.", o: ["biji", "buah", "unit", "batang"], a: "buah" }],
                medium: [{ q: "Farah memakai se______ rantai mutiara.", o: ["utas", "urat", "untai", "lingkar"], a: "utas" }, { q: "Datuk memberi se______ emas kepada cucunya.", o: ["buku", "ketul", "keping", "jongkong"], a: "buku" }, { q: "Kami melihat beberapa ______ daun gugur.", o: ["helai", "keping", "rawan", "bidang"], a: "helai" }, { q: "Paman membeli se______ parang panjang.", o: ["bilah", "batang", "laras", "pucuk"], a: "bilah" }, { q: "Saya makan se______ kek coklat.", o: ["potong", "ketul", "buku", "ulas"], a: "potong" }],
                hard: [{ q: "Amir membeli se______ jam tangan.", o: ["biji", "buah", "utas", "bentuk"], a: "utas" }, { q: "Ibu membawa se______ air sirap.", o: ["bekas", "gelas", "botol", "jag"], a: "bekas" }, { q: "Kami membaca se______ risalah.", o: ["keping", "naskhah", "helai", "surat"], a: "naskhah" }, { q: "Siti membeli se______ penyapu lidi.", o: ["batang", "berkas", "ikat", "rumpun"], a: "batang" }, { q: "Ayah mengangkat se______ meja.", o: ["buah", "kaki", "bidang", "papan"], a: "buah" }, { q: "Saya duduk di atas se______ kerusi kayu.", o: ["buah", "papan", "kaki", "batang"], a: "buah" }, { q: "Datuk membeli se______ serai.", o: ["ikat", "rumpun", "tangkai", "batang"], a: "ikat" }, { q: "Kami melihat se______ kelapa.", o: ["pohon", "tandan", "biji", "tangkai"], a: "pohon" }, { q: "Adik membeli se______ coklat.", o: ["papan", "keping", "potong", "biji"], a: "papan" }]
            },
            betulSalah: {
                easy: [{ q: "Ayat ini betul atau salah? 'Ibu membeli sebatang sayur.'", o: ["Betul", "Salah"], a: "Salah" }, { q: "Ayat ini betul atau salah? 'Adik makan dua papan roti.'", o: ["Betul", "Salah"], a: "Salah" }, { q: "Ayat ini betul atau salah? 'Dia mengirimkan sepucuk surat kepada rakannya.'", o: ["Betul", "Salah"], a: "Betul" }, { q: "Ayat ini betul atau salah? 'Nelayan itu membawa pulang seekor ikan.'", o: ["Betul", "Salah"], a: "Betul" }],
                medium: [{ q: "Ayat ini betul atau salah? 'Di langit kelihatan segugus bintang.'", o: ["Betul", "Salah"], a: "Betul" }, { q: "Ayat ini betul atau salah? 'Pemuda itu membeli senaskhah akhbar Utusan Malaysia.'", o: ["Betul", "Salah"], a: "Betul" }, { q: "Ayat ini betul atau salah? 'Ayah memakai seutas cermin mata yang baru.'", o: ["Betul", "Salah"], a: "Salah" }, { q: "Ayat ini betul atau salah? 'Terdapat serumpun serai di belakang rumahnya.'", o: ["Betul", "Salah"], a: "Betul" }],
                hard: [{ q: "Ayat ini betul atau salah? 'Perompak bank itu melepaskan beberapa laras tembakan.'", o: ["Betul", "Salah"], a: "Salah" }, { q: "Ayat ini betul atau salah? 'Ayah membeli beberapa jelung dawai untuk membuat pagar.'", o: ["Betul", "Salah"], a: "Salah" }, { q: "Ayat ini betul atau salah? 'Dua puntung rokok bertaburan di atas lantai.'", o: ["Betul", "Salah"], a: "Betul" }, { q: "Ayat ini betul atau salah? 'Puisi itu terdiri daripada empat rangkap.'", o: ["Betul", "Salah"], a: "Betul" }]
            },
            padananBergambar: { easy: [], medium: [], hard: [] },
            isiJawapan: {
                easy: [{ q: "Adik minum se______ susu.", a: ["gelas", "cawan"] }, { q: "Nenek menjahit menggunakan se______ jarum.", a: ["batang"] }],
                medium: [{ q: "Kami makan se______ nasi lemak.", a: ["pinggan"] }, { q: "Dia membawa se______ rotan.", a: ["berkas"] }],
                hard: [{ q: "Sajak itu terdiri daripada empat ______.", a: ["rangkap"] }]
            },
            padananTeks: {
                easy: [{ q: "Padankan objek dengan penjodoh bilangannya.", items: ["Pisang", "Rumah", "Kasut"], matches: ["Sebuah", "Sepasang", "Sesikat"], a: { "Pisang": "Sesikat", "Rumah": "Sebuah", "Kasut": "Sepasang" } }, { q: "Padankan objek dengan penjodoh bilangannya.", items: ["Surat", "Roti", "Jarum"], matches: ["Sekeping", "Sepucuk", "Bilah"], a: { "Surat": "Sepucuk", "Roti": "Sekeping", "Jarum": "Bilah" } }, { q: "Padankan objek dengan penjodoh bilangannya.", items: ["Bola", "Ikan", "Pokok"], matches: ["Pohon", "Ekor", "Biji"], a: { "Bola": "Biji", "Ikan": "Ekor", "Pokok": "Pohon" } }],
                medium: [{ q: "Padankan objek dengan penjodoh bilangannya.", items: ["Cincin", "Tanah", "Sungai"], matches: ["Sebidang", "Sebatang", "Sebentuk"], a: { "Cincin": "Sebentuk", "Tanah": "Sebidang", "Sungai": "Sebatang" } }, { q: "Padankan objek dengan penjodoh bilangannya.", items: ["Pantun", "Askar", "Pulau"], matches: ["Sepasukan", "Sebuah", "Serangkap"], a: { "Pantun": "Serangkap", "Askar": "Sepasukan", "Pulau": "Sebuah" } }, { q: "Padankan objek dengan penjodoh bilangannya.", items: ["Kertas", "Kerusi", "Tali"], matches: ["Buah", "Utas", "Helai"], a: { "Kertas": "Helai", "Kerusi": "Buah", "Tali": "Utas" } }],
                hard: [{ q: "Padankan objek dengan penjodoh bilangannya.", items: ["Meriam", "Jala", "Rotan"], matches: ["Serawan", "Seberkas", "Sedas"], a: { "Meriam": "Sedas", "Jala": "Serawan", "Rotan": "Seberkas" } }, { q: "Padankan objek dengan penjodoh bilangannya.", items: ["Mutiara", "Dakwat", "Gajah"], matches: ["Setitik", "Butir", "Ekor"], a: { "Mutiara": "Butir", "Dakwat": "Setitik", "Gajah": "Ekor" } }]
            },
            pilihBeberapa: {
                easy: [{ q: "Pilih semua penjodoh bilangan yang sesuai untuk 'bunga'.", o: ["Kuntum", "Jambak", "Batang", "Tangkai"], a: ["Kuntum", "Jambak", "Tangkai"] }],
                medium: [{ q: "Pilih semua objek yang menggunakan penjodoh bilangan 'helai'.", o: ["Daun", "Baju", "Kertas", "Buku"], a: ["Daun", "Baju", "Kertas"] }, { q: "Pilih semua objek yang boleh menggunakan penjodoh bilangan 'keping'.", o: ["Papan", "Gambar", "Biskut", "Guli"], a: ["Papan", "Gambar", "Biskut"] }]
            }
        };

        const startMenu = document.getElementById('start-menu');
        const gameContainer = document.getElementById('game-container');
        const startButtons = document.querySelectorAll('.start-button');
        const restartButton = document.getElementById('restart-button');
        const registrationModal = document.getElementById('registration-modal');
        const startMultiplayerGameButton = document.getElementById('start-multiplayer-game');
        
        const playerHandEl = document.getElementById('player-hand');
        const p2HandEl = document.getElementById('p2-hand');
        const p3HandEl = document.getElementById('p3-hand');
        const discardPileEl = document.getElementById('discard-pile');
        const drawPileEl = document.getElementById('draw-pile');
        const gameMessageEl = document.getElementById('game-message');
        const passTurnButton = document.getElementById('pass-turn-button');
        
        const p1InfoEl = document.getElementById('p1-info');
        const p2InfoEl = document.getElementById('p2-info');
        const p3InfoEl = document.getElementById('p3-info');
        
        const quizModal = document.getElementById('quiz-modal');
        const quizCategoryEl = document.getElementById('quiz-category');
        const quizQuestionContainerEl = document.getElementById('quiz-question-container');
        const quizOptionsEl = document.getElementById('quiz-options');

        const colorPickerModal = document.getElementById('color-picker-modal');
        const gameOverModal = document.getElementById('game-over-modal');
        const gameOverMessageEl = document.getElementById('game-over-message');
        const turnModal = document.getElementById('turn-modal');
        const turnMessageEl = document.getElementById('turn-message');
        const turnOkButton = document.getElementById('turn-ok-button');
        
        const wrongAnswerModal = document.getElementById('wrong-answer-modal');
        const correctAnswerText = document.getElementById('correct-answer-text');
        const geminiExplainButton = document.getElementById('gemini-explain-button');
        const closeWrongAnswerButton = document.getElementById('close-wrong-answer-button');

        const geminiModal = document.getElementById('gemini-modal');
        const geminiLoader = document.getElementById('gemini-loader');
        const geminiExplanationText = document.getElementById('gemini-explanation-text');
        const closeGeminiButton = document.getElementById('close-gemini-button');
        
        let deck = [];
        let players = [];
        let discardPile = [];
        let currentPlayerIndex = 0;
        let gameDirection = 1;
        let currentQuestion = {};
        let canPlayCard = false;
        let gameMode = 'ai';
        let turnEffects = {};
        let lastWrongAnswer = {};
        let nextRank = 1;
        let audioInitialized = false;
        let userMatchingPairs = {};
        
        let fullQuestionPools = {};
        let sessionQuestionPools = {};
        let questionTypeCycle = [];
        let playsThisTurn = 0;

        // --- Audio Setup ---
        let synth, CorrectSynth, WrongSynth, bgMusic, musicSynth, kick, snare;

        function setupAudio() {
            CorrectSynth = new Tone.PolySynth(Tone.Synth, { oscillator: { type: 'triangle' }, envelope: { attack: 0.005, decay: 0.1, sustain: 0.3, release: 0.5 } }).toDestination();
            CorrectSynth.volume.value = -6;

            WrongSynth = new Tone.PolySynth(Tone.FMSynth, { harmonicity: 2, modulationIndex: 10, detune: 0, oscillator: { type: "sine" }, envelope: { attack: 0.01, decay: 0.4, sustain: 0.01, release: 0.4 }, modulation: { type: "square" }, modulationEnvelope: { attack: 0.4, decay: 0, sustain: 1, release: 0.5 } }).toDestination();
            WrongSynth.volume.value = -6;
            
            synth = new Tone.Synth({ oscillator: { type: 'sine' }, envelope: { attack: 0.001, decay: 0.1, sustain: 0.1, release: 0.1 } }).toDestination();
            synth.volume.value = -10;

            kick = new Tone.MembraneSynth({ pitchDecay: 0.05, octaves: 10, oscillator: { type: 'sine' }, envelope: { attack: 0.001, decay: 0.4, sustain: 0.01, release: 1.4, attackCurve: 'exponential' } }).toDestination();
            kick.volume.value = -6;
            
            snare = new Tone.NoiseSynth({ noise: { type: 'white' }, envelope: { attack: 0.005, decay: 0.1, sustain: 0 } }).toDestination();
            snare.volume.value = -12;
            
            musicSynth = new Tone.PolySynth(Tone.AMSynth, { harmonicity: 1.5, detune: 0, oscillator: { type: "sine" }, envelope: { attack: 0.01, decay: 0.1, sustain: 0.2, release: 0.8 }, modulation: { type: "square" }, modulationEnvelope: { attack: 0.5, decay: 0, sustain: 1, release: 0.5 } }).toDestination();
            musicSynth.volume.value = -18;

            const melody = new Tone.Sequence((time, note) => {
                musicSynth.triggerAttackRelease(note, '16n', time);
            }, ["C4", ["E4", "G4"], "B4", ["G4", "E4"]], "4n").start(0);

            const kickLoop = new Tone.Loop(time => {
                kick.triggerAttackRelease("C2", "8n", time);
            }, "4n").start(0);

            const snareLoop = new Tone.Loop(time => {
                snare.triggerAttackRelease("8n", time);
            }, "2n").start("4n");

            Tone.Transport.bpm.value = 100;
            audioInitialized = true;
        }

        function playCardSound() { if(audioInitialized) synth.triggerAttackRelease("G4", "16n"); }
        function drawCardSound() { if(audioInitialized) synth.triggerAttackRelease("C3", "16n"); }
        function correctAnswerSound() { if(audioInitialized) CorrectSynth.triggerAttackRelease(["C5", "E5", "G5"], "16n", Tone.now()); }
        function wrongAnswerSound() { if(audioInitialized) WrongSynth.triggerAttackRelease(["C3", "D#3"], "4n"); }
        function winSound() {
            if (audioInitialized) {
                const now = Tone.now();
                CorrectSynth.triggerAttackRelease("C5", "8n", now);
                CorrectSynth.triggerAttackRelease("E5", "8n", now + 0.12);
                CorrectSynth.triggerAttackRelease("G5", "8n", now + 0.24);
                CorrectSynth.triggerAttackRelease("C6", "4n", now + 0.36);
            }
        }
        // --- End Audio Setup ---

        function initializeQuestionPool() {
            fullQuestionPools = {};
            for (const type in questions) {
                if (type === 'padananBergambar') continue; // Skip image questions
                fullQuestionPools[type] = [];
                for (const difficulty in questions[type]) {
                    questions[type][difficulty].forEach(q => {
                        fullQuestionPools[type].push({ ...q, type: type });
                    });
                }
            }
        }

        function startNewQuestionCycle() {
            sessionQuestionPools = JSON.parse(JSON.stringify(fullQuestionPools));
            for(const type in sessionQuestionPools){
                shuffleDeck(sessionQuestionPools[type]);
            }
            questionTypeCycle = Object.keys(sessionQuestionPools).filter(type => sessionQuestionPools[type].length > 0);
            shuffleDeck(questionTypeCycle);
        }
        
        function createDeck() {
            const colors = ['merah', 'hijau', 'biru', 'kuning'];
            const values = ['1', '2', '3', '4', '5', '6', '7', '8', '9', '+2', 'skip', 'reverse'];
            let newDeck = [];
            for (let i = 0; i < 2; i++) {
                for (const color of colors) {
                    for (const value of values) {
                        newDeck.push({ color, value });
                    }
                }
            }
            for (let i = 0; i < 4; i++) {
                newDeck.push({ color: 'liar', value: 'wild' });
                newDeck.push({ color: 'liar', value: '+4' });
            }
            return newDeck;
        }
        
        function shuffleDeck(deck) {
            for (let i = deck.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [deck[i], deck[j]] = [deck[j], deck[i]];
            }
        }

        function createCardElement(card, isClickable = false, isFaceDown = false) {
            const cardEl = document.createElement('div');
            if (isFaceDown) {
                cardEl.className = 'card card-back';
                cardEl.innerHTML = `<span class="logo">IQ-BI</span>`;
                return cardEl;
            }
            cardEl.className = `card ${isClickable ? 'player-card' : ''} bg-${card.color}`;
            cardEl.dataset.color = card.color;
            cardEl.dataset.value = card.value;
            let displayValue = card.value;
            if (card.value === 'skip') displayValue = '🚫';
            if (card.value === 'reverse') displayValue = '🔄';
            if (card.value === 'wild') displayValue = '🎨';
            if (card.value === '+4') displayValue = '＋4';
            cardEl.innerHTML = `<span class="card-corner top">${card.value.replace('reverse', 'REV').replace('skip', 'SKIP')}</span><span class="card-value">${displayValue}</span><span class="card-corner bottom">${card.value.replace('reverse', 'REV').replace('skip', 'SKIP')}</span>`;
            return cardEl;
        }

        function renderHands() {
            players.forEach((player, index) => {
                player.handEl.innerHTML = '';
                if (player.isFinished) return;
                player.hand.forEach((card, cardIndex) => {
                    const isFaceDown = player.isAI || (gameMode === 'multiplayer' && currentPlayerIndex !== index);
                    const isClickable = !player.isAI && currentPlayerIndex === index;
                    const cardEl = createCardElement(card, isClickable, isFaceDown);
                    if (isClickable) {
                        cardEl.addEventListener('click', () => playCard(cardIndex));
                    }
                    player.handEl.appendChild(cardEl);
                });
                player.infoEl.textContent = `${player.name} (${player.hand.length} Kad)`;
            });
        }

        function renderDiscardPile() {
            discardPileEl.innerHTML = '';
            const topCard = discardPile[discardPile.length - 1];
            if (topCard) {
                const cardEl = createCardElement(topCard);
                discardPileEl.appendChild(cardEl);
            }
        }

        function startGame(playerNames = {}) {
            if (audioInitialized) Tone.Transport.start();
            deck = createDeck();
            shuffleDeck(deck);
            startNewQuestionCycle();
            nextRank = 1;
            if (gameMode === 'ai') {
                players = [
                    { id: 'player', hand: [], isAI: false, name: 'Anda', handEl: playerHandEl, infoEl: p1InfoEl, isFinished: false, rank: null, score: 0 },
                    { id: 'ai1', hand: [], isAI: true, name: 'Komputer 1', handEl: p2HandEl, infoEl: p2InfoEl, isFinished: false, rank: null, score: 0 },
                    { id: 'ai2', hand: [], isAI: true, name: 'Komputer 2', handEl: p3HandEl, infoEl: p3InfoEl, isFinished: false, rank: null, score: 0 }
                ];
            } else {
                players = [
                    { id: 'p1', hand: [], isAI: false, name: playerNames.p1 || 'Pemain 1', handEl: playerHandEl, infoEl: p1InfoEl, isFinished: false, rank: null, score: 0 },
                    { id: 'p2', hand: [], isAI: false, name: playerNames.p2 || 'Pemain 2', handEl: p2HandEl, infoEl: p2InfoEl, isFinished: false, rank: null, score: 0 },
                    { id: 'p3', hand: [], isAI: false, name: playerNames.p3 || 'Pemain 3', handEl: p3HandEl, infoEl: p3InfoEl, isFinished: false, rank: null, score: 0 }
                ];
            }
            discardPile = [];
            for (let i = 0; i < 5; i++) {
                players.forEach(p => { if(deck.length > 0) p.hand.push(deck.pop()); });
            }
            let firstCard = deck.pop();
            while (firstCard.color === 'liar') {
                deck.push(firstCard);
                shuffleDeck(deck);
                firstCard = deck.pop();
            }
            discardPile.push(firstCard);
            startMenu.classList.add('hidden');
            gameContainer.classList.remove('hidden');
            gameOverModal.classList.add('hidden');
            currentPlayerIndex = 0;
            gameDirection = 1;
            renderHands();
            renderDiscardPile();
            setTimeout(startTurn, 500);
        }

        function startTurn(effects = {}) {
            renderHands(); 
            const player = players[currentPlayerIndex];
            if (player.isFinished) {
                endTurn();
                return;
            }
            if (gameMode === 'multiplayer' && player.id !== 'p1') {
                 turnMessageEl.textContent = `Giliran ${player.name}`;
                 turnModal.classList.remove('hidden');
            } else {
                beginTurn(effects);
            }
        }
        
        turnOkButton.addEventListener('click', () => {
            turnModal.classList.add('hidden');
            beginTurn({});
        });
        
        function beginTurn(effects = {}){
            renderHands();
            const player = players[currentPlayerIndex];
            const topCard = discardPile[discardPile.length - 1];
            
            playsThisTurn = 0; // Reset play counter for the turn

            if (effects.skip > 0) {
                canPlayCard = false; 
                showMessage(`${player.name}, giliran anda dilangkau! Jawab soalan.`);
            } else {
                canPlayCard = true; 
                showMessage(`Giliran ${player.name}`);
            }

            if (!player.isAI) {
                triggerQuiz(topCard.color);
            } else {
                setTimeout(() => aiTurn(effects), 1500);
            }
        }

        function triggerQuiz(color) {
            if (questionTypeCycle.length === 0) {
                const availableTypes = Object.keys(sessionQuestionPools).filter(type => sessionQuestionPools[type].length > 0);
                if (availableTypes.length === 0) {
                    showMessage("Semua soalan telah digunakan! Kitaran soalan bermula semula.");
                    startNewQuestionCycle();
                } 
                questionTypeCycle = Object.keys(sessionQuestionPools).filter(type => sessionQuestionPools[type].length > 0);
                shuffleDeck(questionTypeCycle);
            }
            
            const randomType = questionTypeCycle.pop();
            currentQuestion = sessionQuestionPools[randomType].pop();

            const quizTypeNames = {
                isiTempatKosong: "Isi Tempat Kosong", anekaPilihan: "Aneka Pilihan", betulSalah: "Betul atau Salah",
                isiJawapan: "Isi Jawapan Tepat", padananTeks: "Padankan Jawapan", pilihBeberapa: "Pilih Beberapa Jawapan"
            };

            quizCategoryEl.textContent = `Soalan ${quizTypeNames[currentQuestion.type]}`;
            quizCategoryEl.className = `text-2xl font-bold mb-4 text-blue-400`;
            quizQuestionContainerEl.innerHTML = '';
            quizOptionsEl.innerHTML = ''; 
            const questionText = document.createElement('p');
            questionText.className = 'mb-4';
            questionText.textContent = currentQuestion.q;
            quizQuestionContainerEl.appendChild(questionText);

            switch (currentQuestion.type) {
                case 'isiJawapan':
                    quizOptionsEl.innerHTML = `<input type="text" id="typed-answer" class="w-full bg-slate-700 text-white p-2 rounded-md border border-slate-600 focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Taip jawapan anda di sini..."><button id="submit-typed-answer" class="mt-4 bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg w-full">Hantar</button>`;
                    document.getElementById('submit-typed-answer').addEventListener('click', () => {
                        const answer = document.getElementById('typed-answer').value;
                        checkAnswer(answer);
                    });
                    break;
                case 'padananTeks':
                    userMatchingPairs = {};
                    let selectedLeft = null;
                    const matchingContainer = document.createElement('div');
                    matchingContainer.className = 'flex justify-between gap-4';
                    const leftCol = document.createElement('div');
                    leftCol.className = 'flex flex-col space-y-2 w-1/2';
                    const rightCol = document.createElement('div');
                    rightCol.className = 'flex flex-col space-y-2 w-1/2';
                    const shuffledMatches = [...currentQuestion.matches].sort(() => Math.random() - 0.5);
                    currentQuestion.items.forEach(item => {
                        const btn = document.createElement('button');
                        btn.textContent = item;
                        btn.className = 'matching-item p-3 bg-slate-600 rounded';
                        btn.onclick = () => {
                            if (selectedLeft) selectedLeft.classList.remove('selected');
                            selectedLeft = btn;
                            btn.classList.add('selected');
                        };
                        leftCol.appendChild(btn);
                    });
                    shuffledMatches.forEach(match => {
                        const btn = document.createElement('button');
                        btn.textContent = match;
                        btn.className = 'matching-item p-3 bg-slate-600 rounded';
                        btn.onclick = () => {
                            if (selectedLeft && !selectedLeft.disabled) {
                                userMatchingPairs[selectedLeft.textContent] = btn.textContent;
                                selectedLeft.classList.remove('selected');
                                selectedLeft.classList.add('matched');
                                btn.classList.add('matched');
                                selectedLeft.disabled = true;
                                btn.disabled = true;
                                selectedLeft = null;
                            }
                        };
                        rightCol.appendChild(btn);
                    });
                    matchingContainer.appendChild(leftCol);
                    matchingContainer.appendChild(rightCol);
                    quizQuestionContainerEl.appendChild(matchingContainer);
                    quizOptionsEl.innerHTML = `<button id="submit-matching-answer" class="mt-4 bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg w-full">Hantar Padanan</button>`;
                    document.getElementById('submit-matching-answer').addEventListener('click', () => checkAnswer(userMatchingPairs));
                    break;
                case 'pilihBeberapa':
                    quizOptionsEl.className = "grid grid-cols-1 md:grid-cols-2 gap-4";
                    currentQuestion.o.forEach(option => {
                        const label = document.createElement('label');
                        label.className = 'checkbox-label';
                        label.innerHTML = `<input type="checkbox" value="${option}" name="multi-choice"><span class="custom-checkbox"></span><span>${option}</span>`;
                        quizOptionsEl.appendChild(label);
                    });
                    quizOptionsEl.innerHTML += `<button id="submit-multi-choice" class="mt-4 bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg w-full md:col-span-2">Hantar Pilihan</button>`;
                    document.getElementById('submit-multi-choice').addEventListener('click', () => {
                        const selected = Array.from(document.querySelectorAll('input[name="multi-choice"]:checked')).map(cb => cb.value);
                        checkAnswer(selected);
                    });
                    break;
                default:
                    quizOptionsEl.className = "grid grid-cols-1 md:grid-cols-2 gap-4";
                    currentQuestion.o.forEach(option => {
                        const button = document.createElement('button');
                        button.className = 'bg-slate-600 hover:bg-slate-700 font-bold py-3 px-4 rounded-lg transition';
                        button.textContent = option;
                        button.onclick = () => checkAnswer(option);
                        quizOptionsEl.appendChild(button);
                    });
            }
            quizModal.classList.add('show');
            quizModal.classList.remove('hidden');
        }

        function checkAnswer(selectedAnswer) {
            quizModal.classList.remove('show');
            quizModal.classList.add('hidden');
            let isCorrect = false;
            switch (currentQuestion.type) {
                case 'isiJawapan':
                    isCorrect = currentQuestion.a.includes(selectedAnswer.trim().toLowerCase());
                    break;
                case 'padananTeks':
                    isCorrect = Object.keys(currentQuestion.a).length === Object.keys(selectedAnswer).length && 
                                Object.keys(currentQuestion.a).every(key => currentQuestion.a[key] === selectedAnswer[key]);
                    break;
                case 'pilihBeberapa':
                    isCorrect = selectedAnswer.length === currentQuestion.a.length &&
                                selectedAnswer.every(val => currentQuestion.a.includes(val)) &&
                                currentQuestion.a.every(val => selectedAnswer.includes(val));
                    break;
                default:
                    isCorrect = selectedAnswer === currentQuestion.a;
            }
            if (isCorrect) {
                correctAnswerSound();
                if(canPlayCard){
                    showMessage('Jawapan Betul! Sila mainkan kad.');
                    if (!players[currentPlayerIndex].isAI) {
                        passTurnButton.classList.remove('hidden');
                    }
                } else {
                    showMessage('Jawapan Betul! Giliran diteruskan.');
                    setTimeout(endTurn, 1500);
                }
            } else {
                wrongAnswerSound();
                lastWrongAnswer = { question: currentQuestion.q, options: currentQuestion.o, correctAnswer: currentQuestion.a, playerAnswer: selectedAnswer, image: null, type: currentQuestion.type };
                let correctAnswerDisplay = Array.isArray(currentQuestion.a) ? currentQuestion.a.join(', ') : currentQuestion.a;
                if (typeof currentQuestion.a === 'object' && !Array.isArray(currentQuestion.a)) {
                     correctAnswerDisplay = Object.entries(currentQuestion.a).map(([k, v]) => `${k} -> ${v}`).join(', ');
                }
                correctAnswerText.textContent = correctAnswerDisplay;
                wrongAnswerModal.classList.remove('hidden');
            }
        }
        
        function applyPenaltyAndEndTurn() {
            wrongAnswerModal.classList.add('hidden');
            if(canPlayCard){
                showMessage('Jawapan Salah! Ambil 2 kad.');
                drawCards(currentPlayerIndex, 2);
                setTimeout(endTurn, 1500);
            } else {
                showMessage('Jawapan Salah! Anda dilangkau dan ambil 2 kad.');
                drawCards(currentPlayerIndex, 2);
                setTimeout(endTurn, 1500);
            }
            canPlayCard = false; // Reset for next turn
        }

        closeWrongAnswerButton.addEventListener('click', applyPenaltyAndEndTurn);
        closeGeminiButton.addEventListener('click', () => {
            geminiModal.classList.add('hidden');
            applyPenaltyAndEndTurn();
        });

        geminiExplainButton.addEventListener('click', () => {
            wrongAnswerModal.classList.add('hidden');
            getGeminiExplanation();
        });

        async function getGeminiExplanation() {
            geminiExplanationText.innerHTML = '';
            geminiLoader.style.display = 'block';
            geminiModal.classList.remove('hidden');
            const systemPrompt = "Anda seorang guru Bahasa Melayu yang pakar, peramah dan suka membantu. Berikan penjelasan yang ringkas, padat, dan mudah difahami dalam format Markdown. Gunakan senarai berbutir (bullet points) untuk menerangkan peraturan tatabahasa yang berkaitan.";
            let userQuery = `Seorang murid salah menjawab soalan kuiz penjodoh bilangan.\n\n`;
            userQuery += `Soalan: "${lastWrongAnswer.question}"\n`;
            if (lastWrongAnswer.options) userQuery += `Pilihan Jawapan: ${lastWrongAnswer.options.join(', ')}\n`;
            userQuery += `Jawapan Murid: "${lastWrongAnswer.playerAnswer}"\n`;
            userQuery += `Jawapan Betul: "${lastWrongAnswer.correctAnswer}"\n\n`;
            userQuery += `Tolong jelaskan dalam Bahasa Melayu:\n1. Mengapa jawapan murid itu salah.\n2. Mengapa jawapan yang betul itu adalah tepat.\n3. Berikan satu atau dua contoh ayat lain menggunakan penjodoh bilangan yang betul itu untuk mengukuhkan pemahaman.`;
            const apiKey = ""; 
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: userQuery }] }],
                systemInstruction: { parts: [{ text: systemPrompt }] },
            };
            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                const result = await response.json();
                const candidate = result.candidates?.[0];
                if (candidate && candidate.content?.parts?.[0]?.text) {
                    let explanation = candidate.content.parts[0].text;
                    explanation = explanation
                        .replace(/\*\*(.*?)\*\*/g, '<strong class="text-amber-500">$1</strong>')
                        .replace(/\*(.*?)\*/g, '<em>$1</em>')
                        .replace(/^- (.*$)/gm, '<ul class="list-disc list-inside ml-4"><li>$1</li></ul>')
                        .replace(/<\/ul>\n<ul class="list-disc list-inside ml-4">/g, '');
                    geminiExplanationText.innerHTML = explanation;
                } else {
                    geminiExplanationText.textContent = "Maaf, AI tidak dapat memberikan penjelasan pada masa ini. Sila cuba lagi nanti.";
                }
            } catch (error) {
                console.error('Error calling Gemini API:', error);
                geminiExplanationText.textContent = "Maaf, berlaku ralat semasa menghubungi AI. Sila semak sambungan internet anda.";
            } finally {
                geminiLoader.style.display = 'none';
            }
        }
        
        function playCard(cardIndex) {
            const player = players[currentPlayerIndex];
            if (player.isAI || !canPlayCard) return;
            playCardSound();
            const cardToPlay = player.hand[cardIndex];
            const topCard = discardPile[discardPile.length - 1];
            if (cardToPlay.color === 'liar' || cardToPlay.color === topCard.color || cardToPlay.value === topCard.value) {
                
                player.hand.splice(cardIndex, 1);
                
                discardPile.push(cardToPlay);
                if (cardToPlay.value === '+2') {
                    turnEffects.draw += 2;
                    turnEffects.forceSkip += 1;
                }
                if (cardToPlay.value === 'skip') turnEffects.skip += 1;
                if (cardToPlay.value === 'reverse') turnEffects.reverseCount += 1;
                if (cardToPlay.value === '+4') {
                    turnEffects.draw += 4;
                    turnEffects.forceSkip += 1;
                }
                
                renderHands();
                renderDiscardPile();
                playsThisTurn++;

                if (checkGameState(currentPlayerIndex)) return;

                const lastCardPlayed = discardPile[discardPile.length - 1];
                if (lastCardPlayed.color === 'liar' && lastCardPlayed.value !== '+4') {
                    chooseColor();
                } else if (lastCardPlayed.value === '+4') {
                    chooseColor();
                }

                if (playsThisTurn >= 2) {
                    showMessage('Had 2 kad dicapai. Giliran tamat.');
                    passTurnButton.classList.add('hidden');
                    canPlayCard = false;
                    setTimeout(passTurn, 1500);
                    return;
                }

                 const newTopCard = discardPile[discardPile.length - 1];
                 const hasMoreMoves = player.hand.some(c => c.color === 'liar' || c.color === newTopCard.color || c.value === newTopCard.value);
                 if (!hasMoreMoves) showMessage('Tiada kad lain untuk dimainkan. Sila serah giliran.');
            } else {
                showMessage('Kad tidak sepadan!');
            }
        }

        function drawCards(playerIndex, count) {
             drawCardSound();
             for (let i = 0; i < count; i++) {
                if (deck.length === 0) {
                    const top = discardPile.pop();
                    deck = [...discardPile];
                    shuffleDeck(deck);
                    discardPile = [top];
                    if(deck.length === 0) {
                        showMessage("Tiada kad lagi untuk diambil!");
                        return;
                    }
                }
                players[playerIndex].hand.push(deck.pop());
            }
            renderHands();
        }

        drawPileEl.addEventListener('click', () => {
            const player = players[currentPlayerIndex];
            if (!player.isAI && canPlayCard) {
                showMessage("Anda ambil 1 kad.");
                drawCards(currentPlayerIndex, 1);
            }
        });
        
        function passTurn() {
            canPlayCard = false;
            passTurnButton.classList.add('hidden');
            
            if (turnEffects.reverseCount % 2 !== 0) {
                gameDirection *= -1;
                showMessage('Arah pusingan diterbalikkan!');
            }

            let targetPlayerIndex = findNextActivePlayer(currentPlayerIndex, gameDirection);
            const effectsForNextTurn = { skip: turnEffects.skip };
            
            if (turnEffects.forceSkip > 0 || turnEffects.draw > 0) {
                showMessage(`${players[targetPlayerIndex].name} ambil ${turnEffects.draw} kad.`);
                drawCards(targetPlayerIndex, turnEffects.draw);
                showMessage(`${players[targetPlayerIndex].name} dilangkau.`);
                targetPlayerIndex = findNextActivePlayer(targetPlayerIndex, gameDirection);
            }
            
            currentPlayerIndex = targetPlayerIndex;
            startTurn(effectsForNextTurn);
        }
        
        passTurnButton.addEventListener('click', passTurn);

        function endTurn() {
            const nextPlayer = findNextActivePlayer(currentPlayerIndex, gameDirection);
            currentPlayerIndex = nextPlayer;
            startTurn({});
        }

        function chooseColor() {
            const player = players[currentPlayerIndex];
            if (!player.isAI) {
                colorPickerModal.classList.remove('hidden');
                document.querySelectorAll('.color-choice').forEach(btn => {
                    btn.onclick = () => {
                        const color = btn.dataset.color;
                        colorPickerModal.classList.add('hidden');
                        const wildCard = discardPile[discardPile.length - 1];
                        wildCard.color = color;
                        renderDiscardPile();
                        showMessage(`Warna ditukar ke ${color}.`);
                    }
                });
            } else {
                const bestColor = getAIBestColor(currentPlayerIndex);
                const wildCard = discardPile[discardPile.length - 1];
                wildCard.color = bestColor;
                renderDiscardPile();
                showMessage(`${player.name} tukar warna ke ${bestColor}.`);
            }
        }
        
        function getAIBestColor(playerIndex) {
            const colorCounts = { merah: 0, hijau: 0, biru: 0, kuning: 0 };
            players[playerIndex].hand.forEach(card => {
                if (card.color !== 'liar') colorCounts[card.color]++;
            });
            let bestColor = 'merah';
            let maxCount = 0;
            for(const color in colorCounts){
                if(colorCounts[color] > maxCount){
                    maxCount = colorCounts[color];
                    bestColor = color;
                }
            }
            return bestColor;
        }

        function findNextActivePlayer(startIndex, direction) {
            let nextIndex = (startIndex + direction + players.length) % players.length;
            let safety = 0;
            while (players[nextIndex].isFinished && safety < players.length) {
                nextIndex = (nextIndex + direction + players.length) % players.length;
                safety++;
            }
            return nextIndex;
        }

        function checkGameState(playerIndex) {
            const player = players[playerIndex];
            if (player.hand.length === 0 && !player.isFinished) {
                winSound();
                player.isFinished = true;
                player.rank = nextRank++;
                const rankText = player.rank === 1 ? "Pertama" : (player.rank === 2 ? "Kedua" : "Ketiga");
                showMessage(`${player.name} selesai di tempat ke-${rankText}!`);
                player.infoEl.textContent = `${player.name} (Selesai - #${player.rank})`;
                player.handEl.innerHTML = '';
            }
            const activePlayers = players.filter(p => !p.isFinished);
            if (activePlayers.length <= 1) {
                if (activePlayers.length === 1 && !activePlayers[0].isFinished) {
                    activePlayers[0].isFinished = true;
                    activePlayers[0].rank = nextRank;
                    activePlayers[0].infoEl.textContent = `${activePlayers[0].name} (Selesai - #${activePlayers[0].rank})`;
                }
                endGame();
                return true;
            }
            return false;
        }

        function endGame() {
            if(audioInitialized) Tone.Transport.stop();
            gameContainer.classList.add('hidden');
            gameOverModal.classList.remove('hidden');
            
            const rankScores = { 1: 100, 2: 50, 3: 25 };
            players.forEach(p => { p.score = rankScores[p.rank] || 0; });
            
            sendScoresToGoogleSheet();

            let rankingHTML = '<h2 class="text-4xl font-bold mb-6">Papan Markah</h2>';
            rankingHTML += '<table class="w-full text-left text-xl"><thead><tr class="border-b border-slate-600"><th>Ked.</th><th>Nama</th><th>Skor</th></tr></thead><tbody>';
            
            const sortedPlayers = [...players].sort((a, b) => (a.rank || 99) - (b.rank || 99));
            sortedPlayers.forEach(player => {
                const rankText = player.rank === 1 ? "🥇" : (player.rank === 2 ? "🥈" : "🥉");
                rankingHTML += `<tr class="border-b border-slate-700"><td class="p-2">${rankText} ${player.rank}</td><td>${player.name}</td><td>${player.score}</td></tr>`;
            });
            rankingHTML += '</tbody></table>';
            gameOverMessageEl.innerHTML = rankingHTML;
        }

        function sendScoresToGoogleSheet() {
            const scriptURL = 'https://script.google.com/macros/s/AKfycbySM_wlMAfMVtBGYvq26qFkVA31m90HZCElolGk-PC7Jit3boR20YAFKJLUwDoJPVg9QA/exec';
            const playersData = players.map(p => ({ name: p.name, score: p.score }));

            fetch(scriptURL, {
                method: 'POST',
                mode: 'no-cors', // Penting untuk mengelakkan ralat CORS
                cache: 'no-cache',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ players: playersData })
            })
            .then(response => console.log('Success! Scores sent to Google Sheet.'))
            .catch(error => console.error('Error!', error.message));
        }

        async function aiTurn(effects = {}) {
            const aiPlayer = players[currentPlayerIndex];
            playsThisTurn = 0;
            const answersCorrectly = Math.random() < 0.85;
            if (!answersCorrectly) {
                wrongAnswerSound();
                showMessage(`${aiPlayer.name} salah jawab! Ambil 2 kad.`);
                drawCards(currentPlayerIndex, 2);
                setTimeout(endTurn, 1500);
                return;
            }
            correctAnswerSound();

            if (effects.skip > 0) {
                 showMessage(`${aiPlayer.name} jawab betul. Giliran diteruskan.`);
                 setTimeout(endTurn, 1500);
                 return;
            }
            
            showMessage(`${aiPlayer.name} jawab betul. Memainkan kad...`);
            await new Promise(resolve => setTimeout(resolve, 1000));
            let hasPlayed = true;
            while(hasPlayed && !aiPlayer.isFinished && playsThisTurn < 2) {
                const topCard = discardPile[discardPile.length - 1];
                let cardToPlay = null;
                const playableCards = aiPlayer.hand.filter(c => c.color === 'liar' || c.color === topCard.color || c.value === topCard.value);
                if (playableCards.length > 0) {
                    playCardSound();
                    cardToPlay = playableCards[0]; 
                    
                    const cardIndex = aiPlayer.hand.findIndex(c => c === cardToPlay);
                    if (cardIndex > -1) {
                        aiPlayer.hand.splice(cardIndex, 1);
                    }

                    discardPile.push(cardToPlay);
                    if (cardToPlay.value === '+2') {
                        turnEffects.draw += 2;
                        turnEffects.forceSkip += 1;
                    }
                    if (cardToPlay.value === 'skip') turnEffects.skip += 1;
                    if (cardToPlay.value === 'reverse') turnEffects.reverseCount += 1;
                    if (cardToPlay.value === '+4') {
                        turnEffects.draw += 4;
                        turnEffects.forceSkip += 1;
                    }
                    
                    renderHands();
                    renderDiscardPile();
                    playsThisTurn++;
                    if (checkGameState(currentPlayerIndex)) return;
                    await new Promise(resolve => setTimeout(resolve, 1000));
                    const lastCardPlayed = discardPile[discardPile.length - 1];
                    if (lastCardPlayed.color === 'liar') {
                        chooseColor();
                        await new Promise(resolve => setTimeout(resolve, 500));
                    }
                } else {
                    showMessage(`${aiPlayer.name} ambil 1 kad.`);
                    drawCards(currentPlayerIndex, 1);
                    hasPlayed = false;
                }
            }
            
            if (players[currentPlayerIndex].isFinished) {
                endTurn();
            } else {
                passTurn();
            }
        }

        function showMessage(msg) {
            gameMessageEl.textContent = msg;
            gameMessageEl.classList.remove('hidden');
            setTimeout(() => { gameMessageEl.classList.add('hidden'); }, 2000);
        }
        
        startButtons.forEach(button => {
            button.addEventListener('click', async () => {
                if (!audioInitialized) {
                    await Tone.start();
                    setupAudio();
                }
                gameMode = button.dataset.mode;
                if (gameMode === 'multiplayer') {
                    registrationModal.classList.remove('hidden');
                } else {
                    startGame();
                }
            });
        });

        startMultiplayerGameButton.addEventListener('click', () => {
            const playerNames = {
                p1: document.getElementById('p1-name').value,
                p2: document.getElementById('p2-name').value,
                p3: document.getElementById('p3-name').value,
            };
            registrationModal.classList.add('hidden');
            startGame(playerNames);
        });

        restartButton.addEventListener('click', () => {
             gameOverModal.classList.add('hidden');
             startMenu.classList.remove('hidden');
        });

        // Initialize question pool on load
        initializeQuestionPool();
    </script>
</body>
</html>

