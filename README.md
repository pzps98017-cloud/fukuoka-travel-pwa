<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8" />
<title>福岡旅遊手帳 v15 (精緻透明版)</title>
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover, user-scalable=no" />
<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/sortablejs@1.15.2/Sortable.min.js"></script>
<script>
  tailwind.config = {
    theme: {
      extend: {
        colors: {
          paper: '#Fdfbf6',
          wine: '#8C433E',
          indigo: '#2B3A55',
          ink: '#444444', // 稍微變淺一點的黑色，更柔和
          'cat-transport': '#5D7D9A',
          'cat-shrine': '#C0554F',
          'cat-food': '#D99559',
          'cat-spot': '#7D946F',
          'cat-hotel': '#83769C',
          'cat-shop': '#BA6E7E',
          'cat-flex': '#888888',
        },
        boxShadow: {
          card: '0 4px 30px rgba(0,0,0,0.1)', // 更柔和的陰影
          float: '0 10px 40px -10px rgba(43, 58, 85, 0.2)',
        },
        fontFamily: {
            // 強制設定楷體
            sans: ['"KaiTi"', '"BiauKai"', '"DFKai-SB"', '"STKaiti"', '"Noto Serif TC"', 'serif'],
            serif: ['"KaiTi"', '"BiauKai"', '"DFKai-SB"', '"STKaiti"', '"Noto Serif TC"', 'serif']
        }
      }
    }
  }
</script>
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+TC:wght@400;600&display=swap" rel="stylesheet">
<style>
  :root { --wine:#8C433E; --ink:#444444; }
  
  /* 全域楷體設定，預設字重變細 */
  body { 
    font-family: "KaiTi", "BiauKai", "DFKai-SB", "STKaiti", "Noto Serif TC", serif;
    color: var(--ink); 
    background: #fdfbf6; 
    min-height: 100vh;
    font-weight: 400; /* 預設細體 */
    -webkit-tap-highlight-color: transparent;
  }
  
  input, select, textarea, button { font-family: inherit; }

  .app-container { max-width: 600px; margin: 0 auto; min-height: 100vh; position: relative; background-color: transparent; }

  /* Cover */
  #cover-page {
    position: fixed; top: 0; left: 0; width: 100%; height: 100vh;
    z-index: 3000; background-color: #222;
    transition: transform 0.6s cubic-bezier(0.2, 0.8, 0.2, 1);
    display: flex; flex-direction: column; justify-content: flex-end; align-items: center;
  }
  #cover-page.hidden-cover { transform: translateY(-100%); }

  /* Modal */
  .overlay { position:fixed; inset:0; background:rgba(43,58,85,0.4); backdrop-filter: blur(5px); display:none; z-index:4000; transition: opacity 0.3s; }
  .overlay.show { display:block; }
  
  .modal { 
    position:fixed; left:50%; bottom:0; transform:translate(-50%, 100%); 
    width:100%; max-width:600px; height: auto; max-height: 85vh;
    background: #fff; border-radius: 28px 28px 0 0; 
    box-shadow: var(--float); z-index:4001; 
    transition: transform 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
    display: flex; flex-direction: column;
  }
  .modal.show { transform:translate(-50%, 0); }
  
  /* Sticky Header (Dynamic Transparency) */
  .sticky-header { 
    position: sticky; top: 0; z-index: 100; 
    transition: all 0.4s ease;
    background: transparent; 
    backdrop-filter: none;
    border-bottom: 1px solid transparent;
  }
  /* 滑動後變成高強度毛玻璃，保持透明感 */
  .sticky-header.scrolled {
    background: rgba(255, 255, 255, 0.5); /* 半透明白 */
    backdrop-filter: blur(20px) saturate(180%); /* 強力毛玻璃 */
    border-bottom: 1px solid rgba(255,255,255,0.2);
    box-shadow: 0 4px 30px rgba(0,0,0,0.1);
  }
  
  /* Timeline */
  .tl-col { width: 52px; display: flex; flex-direction: column; align-items: center; padding-top: 8px; flex-shrink: 0; }
  /* 字體變細：從 900 改為 600 */
  .tl-time { font-weight: 600; color: var(--wine); font-size: 15px; letter-spacing: 0.5px; }
  .tl-line { width: 0; border-left: 2px dashed rgba(140,67,62,0.3); flex: 1; margin: 8px 0; }
  .tl-dot { 
    width: 12px; height: 12px; border-radius: 50%; flex-shrink: 0;
    background: #fff; border: 3px solid var(--wine); box-shadow: 0 0 0 3px rgba(140,67,62,0.1);
  }

  /* Card (增加透明度與毛玻璃) */
  .event-card { 
    background: rgba(255,255,255,0.75); /* 卡片半透明 */
    backdrop-filter: blur(15px) saturate(150%); /* 卡片毛玻璃 */
    border-radius: 24px; padding: 20px; margin-bottom: 20px; 
    box-shadow: var(--card); border: 1px solid rgba(255,255,255,0.4);
    transition: transform 0.2s; user-select: none; position: relative;
  }
  .event-card:active { transform: scale(0.98); }
  .sortable-ghost { opacity: 0.5; background: rgba(255,255,255,0.4); border: 2px dashed var(--wine); }
  .sortable-drag { cursor: grabbing; transform: scale(1.03) rotate(1deg); box-shadow: 0 20px 40px rgba(0,0,0,0.15); z-index: 999; }

  /* UI Elements (字體微調) */
  .cat-tag { font-size: 11px; padding: 4px 10px; border-radius: 12px; color: #fff; font-weight: 600; display: inline-block; margin-right: 6px; letter-spacing: 1px; box-shadow: 0 2px 6px rgba(0,0,0,0.1); }
  
  .input-field { width: 100%; padding: 14px; background: #F8F7F4; border: 1px solid #EBE5DC; border-radius: 16px; font-size: 16px; outline: none; transition: all 0.2s; }
  .input-field:focus { border-color: var(--wine); background: #fff; box-shadow: 0 0 0 4px rgba(140,67,62,0.05); }
  
  .label-text { font-size: 14px; font-weight: 600; color: #666; margin-bottom: 6px; display: block; letter-spacing: 1px; }

  /* FAB */
  .fab { 
    position: fixed; right: 24px; bottom: max(30px, env(safe-area-inset-bottom) + 30px);
    width: 64px; height: 64px; 
    background: var(--wine); color: white; 
    border-radius: 26px; 
    display: flex; align-items: center; justify-content: center; 
    font-size: 32px; box-shadow: var(--float); 
    z-index: 200; 
    transition: transform 0.2s; border: 2px solid rgba(255,255,255,0.3); 
    cursor: pointer;
  }
  .fab:active { transform: scale(0.9); }

  /* Buttons (字體微調) */
  .btn-wine { background: var(--wine); color: white; font-weight: 600; border-radius: 16px; box-shadow: 0 4px 15px rgba(140,67,62,0.3); transition: all 0.2s; letter-spacing: 2px; }
  .btn-wine:active { transform: scale(0.97); }

  .preset-btn { 
    font-size: 13px; padding: 8px 12px; 
    background: #fff; border: 1px solid #e5e7eb; border-radius: 12px; 
    color: #555; transition: all 0.2s; font-weight: 500; letter-spacing: 1px;
  }
  .preset-btn:active, .preset-btn:hover { background: var(--wine); color: white; border-color: var(--wine); }

  .no-scrollbar::-webkit-scrollbar { display: none; }
  .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

</style>
</head>
<body class="bg-gray-100">

<div id="cover-page">
  <div class="absolute inset-0">
    <img id="coverImg" src="https://images.unsplash.com/photo-1549643276-fbc2cbd67406?q=80&w=1000&auto=format&fit=crop" class="w-full h-full object-cover transition-opacity duration-700 opacity-100">
    <div class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/10 to-black/40"></div>
  </div>
  
  <div class="relative z-10 text-center text-white px-8 w-full max-w-md pb-28 flex flex-col items-center">
    <div class="border-t border-b border-white/30 py-8 mb-12 w-full backdrop-blur-sm">
      <h1 class="text-6xl font-semibold mb-2 tracking-[0.3em] drop-shadow-2xl">福岡</h1>
      <p class="text-xl font-light tracking-[0.6em] opacity-90 uppercase mt-2">Travel Log</p>
    </div>
    
    <div class="flex items-baseline gap-2 mb-12 text-shadow-sm bg-black/20 px-6 py-2 rounded-full backdrop-blur-md border border-white/10">
       <span class="text-sm opacity-80 tracking-widest font-light">距離出發</span>
       <span id="coverCountdown" class="font-semibold text-4xl text-yellow-400">--</span> 
       <span class="text-sm opacity-80 tracking-widest font-light">天</span>
    </div>

    <button id="enterApp" class="w-full bg-white/10 backdrop-blur-md text-white font-semibold py-4 rounded-2xl shadow-2xl hover:bg-white/20 active:scale-95 transition-all text-xl tracking-[0.2em] border border-white/40 mb-8 flex items-center justify-center gap-2">
      開啟旅程 <span class="text-sm">➜</span>
    </button>

    <label class="inline-flex items-center text-xs text-white/70 hover:text-white cursor-pointer transition-colors px-4 py-2 rounded-full border border-white/10 bg-black/20 backdrop-blur tracking-wider font-light">
      <span class="mr-2 text-lg">📷</span> 更換封面照片
      <input type="file" id="coverUpload" accept="image/*" class="hidden">
    </label>
  </div>
</div>

<div id="hero-layer" class="fixed top-0 left-0 w-full h-full z-0 pointer-events-none">
    <img id="appHeroImg" src="https://images.unsplash.com/photo-1549643276-fbc2cbd67406?q=80&w=1000&auto=format&fit=crop" class="w-full h-full object-cover object-[center_bottom]">
    <div class="absolute inset-0 bg-gradient-to-b from-black/50 via-transparent to-white/30"></div>
</div>

<div class="app-container" id="app">
  
  <header id="main-header" class="sticky-header px-5 py-4 transition-colors duration-300">
    <div class="flex justify-between items-start mb-1">
      <div>
        <button onclick="showCover()" class="group mb-4 flex items-center gap-2 px-3 py-1.5 rounded-full bg-white/20 backdrop-blur-md border border-white/30 hover:bg-white/40 transition text-white" id="backBtn">
           <svg class="w-4 h-4" viewBox="0 0 24 24" fill="currentColor">
             <path d="M2 7h20v2h-2v11h-2V9h-5v5h2v6h-2v-6H9v6H7v-6h2V9H4v11H2V7zm5-4h10v2H7V3z"/>
           </svg>
           <span class="text-xs font-semibold tracking-widest">封面</span>
        </button>

        <div class="text-white drop-shadow-md transition-colors duration-300" id="headerTextContainer">
            <div class="flex items-center gap-2 cursor-pointer group" onclick="openSettings()">
                <h1 class="text-4xl font-semibold tracking-wider" id="headerTitle">Day 1</h1>
                <span class="opacity-50 text-sm group-hover:opacity-100 transition font-light">✎</span>
            </div>
            <div class="flex items-center text-sm opacity-90 mt-1 font-medium tracking-wider cursor-pointer" onclick="openSettings()">
            <span id="headerDate">--/--</span>
            <span class="mx-2 opacity-50">|</span>
            <span id="headerLoc" class="flex items-center gap-1">📍 設定地點</span>
            </div>
        </div>
      </div>
      
      <div class="text-right text-white drop-shadow-md">
        <div class="text-2xl font-medium tracking-widest font-mono" id="clock">00:00</div>
        <div class="flex items-center justify-end gap-2 mt-1 opacity-90">
             <span id="weatherIcon" class="text-xl">🌥️</span>
             <span id="weatherTemp" class="text-lg font-semibold">--</span><span class="text-xs font-light">°C</span>
        </div>
      </div>
    </div>
    
    <div class="mt-4 flex gap-2 overflow-x-auto no-scrollbar pb-2">
        <div class="bg-white/20 backdrop-blur-md border border-white/20 text-white text-xs px-3 py-1.5 rounded-full whitespace-nowrap shadow-sm font-medium tracking-wide" id="weatherAdviceBadge">
            💡 連線氣象局...
        </div>
    </div>

    <div class="flex overflow-x-auto gap-3 mt-2 pb-1 no-scrollbar" id="dayTabs"></div>
  </header>

  <main class="px-5 pt-[280px] pb-32 relative z-10" id="timelineList">
    </main>
  
  <div class="fixed bottom-0 left-0 right-0 bg-white/70 backdrop-blur-xl border-t border-white/30 p-4 z-30 max-w-[600px] mx-auto flex justify-between items-center shadow-[0_-5px_30px_rgba(0,0,0,0.05)] pb-[max(20px,env(safe-area-inset-bottom))]">
      <div class="flex flex-col">
        <span class="text-[10px] text-gray-500 tracking-widest uppercase font-semibold">Total Expense</span>
        <span class="text-xs text-gray-600 tracking-wide font-medium">本日消費總額</span>
      </div>
      <span class="text-2xl font-semibold text-wine">¥ <span id="dailyTotalDisplay">0</span></span>
  </div>

</div>

<button class="fab" onclick="openModal()">＋</button>

<div class="overlay" id="modalOverlay"></div>
<div class="modal" id="expenseModal">
  <div class="px-6 py-5 border-b border-gray-50 flex justify-between items-center bg-white rounded-t-[28px]">
    <h3 class="text-xl font-semibold text-wine flex items-center gap-2">記帳本</h3>
    <button onclick="closeModal()" class="w-8 h-8 rounded-full bg-gray-50 text-gray-400 hover:bg-gray-100 flex items-center justify-center transition">✕</button>
  </div>
  <div class="flex-1 overflow-y-auto p-6 space-y-5 bg-gray-50/50">
    <div class="bg-white rounded-2xl p-4 border border-gray-100 text-center shadow-sm">
        <div class="text-xs text-gray-500 mb-1 tracking-widest font-medium">目前行程</div>
        <div class="font-semibold text-wine text-lg" id="expenseTargetTitle">--</div>
    </div>
    <div id="expenseRows" class="space-y-3"></div>
    <button onclick="addExpenseRow()" class="w-full py-3 border-2 border-dashed border-gray-300 text-gray-500 text-sm rounded-xl hover:bg-white hover:border-wine hover:text-wine transition font-semibold tracking-wide">+ 新增項目</button>
    <div class="flex justify-between items-center pt-4 border-t border-gray-200 mt-2">
        <span class="font-semibold text-gray-600 tracking-widest">小計</span>
        <span class="font-semibold text-2xl text-wine">¥ <span id="expenseModalTotal">0</span></span>
    </div>
  </div>
  <div class="p-5 bg-white border-t border-gray-100 pb-[max(20px,env(safe-area-inset-bottom))]">
    <button onclick="saveExpense()" class="w-full btn-wine py-3.5 shadow-lg active:scale-95 transition">儲存帳目</button>
  </div>
</div>

<div class="modal" id="settingsModal">
  <div class="px-6 py-5 border-b border-gray-50 flex justify-between items-center bg-white rounded-t-[28px]">
    <h3 class="text-xl font-semibold text-wine">行程設定</h3>
    <button onclick="closeModal()" class="w-8 h-8 rounded-full bg-gray-50 text-gray-400 hover:bg-gray-100 flex items-center justify-center transition">✕</button>
  </div>
  <div class="flex-1 overflow-y-auto p-6 space-y-6 bg-gray-50/50">
    <div>
        <label class="label-text">📅 日期</label>
        <input type="date" id="setDate" class="input-field bg-white">
    </div>
    <div>
        <label class="label-text">✏️ 標題</label>
        <input type="text" id="setTitle" class="input-field bg-white" placeholder="例如：由布院一日遊">
    </div>
    
    <div class="bg-white p-5 rounded-2xl border border-gray-100 shadow-sm">
        <label class="label-text text-wine mb-3">🌍 天氣定位快速選單</label>
        <div class="grid grid-cols-3 gap-2 mb-4">
            <button onclick="fillLoc('博多', 33.59, 130.40)" class="preset-btn">🍜 博多</button>
            <button onclick="fillLoc('由布院', 33.26, 131.36)" class="preset-btn">♨️ 由布院</button>
            <button onclick="fillLoc('別府', 33.28, 131.50)" class="preset-btn">👹 別府</button>
            <button onclick="fillLoc('太宰府', 33.52, 130.53)" class="preset-btn">⛩️ 太宰府</button>
            <button onclick="fillLoc('門司港', 33.94, 130.96)" class="preset-btn">⚓ 門司港</button>
            <button onclick="fillLoc('小倉', 33.88, 130.87)" class="preset-btn">🏯 小倉</button>
        </div>
        <div class="grid grid-cols-2 gap-3 mb-2">
            <div><label class="text-[10px] text-gray-500">緯度</label><input type="number" id="setLat" class="input-field h-10 text-sm bg-gray-50"></div>
            <div><label class="text-[10px] text-gray-500">經度</label><input type="number" id="setLon" class="input-field h-10 text-sm bg-gray-50"></div>
        </div>
        <input type="hidden" id="setAreaName"> 
    </div>
  </div>
  <div class="p-5 bg-white border-t border-gray-100 pb-[max(20px,env(safe-area-inset-bottom))]">
    <button onclick="saveSettings()" class="w-full btn-wine py-3.5 shadow-lg active:scale-95 transition">更新設定</button>
  </div>
</div>

<div class="modal" id="editModal">
  <div class="px-6 py-5 border-b border-gray-50 flex justify-between items-center bg-white rounded-t-[28px]">
    <h3 class="text-xl font-semibold text-wine" id="modalTitle">新增行程</h3>
    <button onclick="closeModal()" class="w-8 h-8 rounded-full bg-white text-gray-400 shadow-sm flex items-center justify-center">✕</button>
  </div>

  <div class="flex-1 overflow-y-auto p-6 space-y-5 bg-gray-50/50">
    <input type="hidden" id="inpId">
    <div class="grid grid-cols-2 gap-4">
      <div><label class="label-text">時間</label><input type="time" id="inpTime" class="input-field bg-white"></div>
      <div>
        <label class="label-text">分類</label>
        <select id="inpCat" class="input-field bg-white">
          <option value="none">無</option>
          <option value="transport">🚅 交通 (藍)</option>
          <option value="food">🍜 吃飯 (橘)</option>
          <option value="spot">🌲 景點 (綠)</option>
          <option value="shop">🛍️ 逛街 (粉)</option>
          <option value="hotel">🏨 飯店 (紫)</option>
          <option value="shrine">⛩️ 神社 (紅)</option>
          <option value="flex">✨ 彈性 (灰)</option>
        </select>
      </div>
    </div>
    <div><label class="label-text">名稱</label><input type="text" id="inpTitle" class="input-field font-semibold bg-white" placeholder="行程名稱"></div>
    <div><label class="label-text">地圖連結</label><input type="url" id="inpMap" class="input-field text-sm bg-white" placeholder="Google Maps URL"></div>
    <div><label class="label-text">筆記</label><textarea id="inpNote" rows="3" class="input-field resize-none bg-white" placeholder="備註..."></textarea></div>
    <div>
      <label class="label-text">照片</label>
      <label class="block w-full h-28 border-2 border-dashed border-gray-300 rounded-2xl flex flex-col items-center justify-center text-gray-400 cursor-pointer hover:border-wine hover:text-wine hover:bg-wine/5 transition bg-white">
        <span class="text-xl mb-1">📷</span><span class="text-xs tracking-wide font-medium">點擊上傳</span>
        <input type="file" id="inpImg" accept="image/*" class="hidden">
      </label>
      <div id="imgPreviewArea" class="hidden mt-3 relative group">
        <img id="imgPreview" class="w-full h-40 object-cover rounded-2xl shadow-md border border-white/50">
        <button onclick="clearImage()" class="absolute top-2 right-2 w-7 h-7 bg-black/60 text-white rounded-full text-xs flex items-center justify-center backdrop-blur">✕</button>
      </div>
    </div>
  </div>
  <div class="p-5 border-t border-gray-100 bg-white flex gap-3 pb-[max(20px,env(safe-area-inset-bottom))]">
    <button onclick="deleteEvent()" id="btnDelete" class="px-5 py-3.5 rounded-xl border border-red-200 text-red-500 font-bold hover:bg-red-50 transition hidden">刪除</button>
    <button onclick="saveEvent()" class="flex-1 btn-wine py-3.5 shadow-lg active:scale-95 transition">儲存</button>
  </div>
</div>

<script>
// ==================== 1. Init Data ====================
const LS_KEY = 'fukuoka_trip_v15'; 
const COVER_KEY = 'fukuoka_cover_v15';

const INITIAL_DATA = {
  days: [
    { key: 'd1', date: '2025-01-04', title: 'Day 1: 抵達福岡', area: '博多', lat: 33.59, lon: 130.40, events: [
        { id: 'e1', time: '10:10', title: '福岡機場國際線', cat: 'transport', note: '搭巴士進市區', expenses:[{n:'車票',v:500}] },
        { id: 'e2', time: '12:00', title: '博多拉麵', cat: 'food', note: '一蘭本店', expenses:[{n:'拉麵',v:980}] }
    ]},
    { key: 'd2', date: '2025-01-05', title: 'Day 2: 由布院散策', area: '由布院', lat: 33.26, lon: 131.36, events: [] },
    { key: 'd3', date: '2025-01-06', title: 'Day 3: 太宰府祈福', area: '太宰府', lat: 33.52, lon: 130.53, events: [] }
  ]
};

let data = JSON.parse(localStorage.getItem(LS_KEY)) || INITIAL_DATA;
let curDayIdx = 0;
let editId = null;
let expenseId = null;
let tempImgBase64 = "";

function saveData() { localStorage.setItem(LS_KEY, JSON.stringify(data)); }
function getCurDay() { return data.days[curDayIdx]; }

// ==================== 2. 封面與主視覺 ====================
const savedCover = localStorage.getItem(COVER_KEY);
if(savedCover) applyCoverImage(savedCover);

function applyCoverImage(src) {
    document.getElementById('coverImg').src = src;
    document.getElementById('appHeroImg').src = src;
}

document.getElementById('coverUpload').onchange = (e) => {
    const f = e.target.files[0];
    if(!f) return;
    const r = new FileReader();
    r.onload = (ev) => {
        const res = ev.target.result;
        applyCoverImage(res);
        localStorage.setItem(COVER_KEY, res);
    };
    r.readAsDataURL(f);
};

document.getElementById('enterApp').onclick = () => {
  document.getElementById('cover-page').classList.add('hidden-cover');
  updateWeather();
};

function showCover() {
  document.getElementById('cover-page').classList.remove('hidden-cover');
}

function initCover() {
  const start = new Date(data.days[0].date);
  const now = new Date();
  const diff = Math.ceil((start - now) / (1000 * 60 * 60 * 24));
  document.getElementById('coverCountdown').innerText = diff > 0 ? diff : 0;
  setInterval(() => {
    const d = new Date();
    document.getElementById('clock').innerText = `${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`;
  }, 1000);
}

// ==================== 3. Scroll Effect (透明度不再變化，只變顏色) ====================
window.addEventListener('scroll', () => {
    const scrollY = window.scrollY;
    // 移除 hero-layer 的透明度變化，保持背景圖顯示
    // const heroLayer = document.getElementById('hero-layer');
    // let opacity = 1 - (scrollY / 350); 
    // if(opacity < 0) opacity = 0;
    // heroLayer.style.opacity = opacity;
    
    const header = document.getElementById('main-header');
    const headerText = document.getElementById('headerTextContainer');
    const backBtn = document.getElementById('backBtn');
    const clock = document.getElementById('clock');
    
    if (scrollY > 50) {
        header.classList.add('scrolled');
        headerText.classList.remove('text-white');
        headerText.classList.add('text-wine');
        backBtn.classList.remove('bg-white/20', 'border-white/30', 'text-white');
        backBtn.classList.add('bg-wine/10', 'border-wine/20', 'text-wine');
        clock.classList.remove('text-white');
        clock.classList.add('text-wine');
    } else {
        header.classList.remove('scrolled');
        headerText.classList.add('text-white');
        headerText.classList.remove('text-wine');
        backBtn.classList.add('bg-white/20', 'border-white/30', 'text-white');
        backBtn.classList.remove('bg-wine/10', 'border-wine/20', 'text-wine');
        clock.classList.add('text-white');
        clock.classList.remove('text-wine');
    }
});

// ==================== 4. 天氣 API ====================
async function updateWeather() {
  const day = getCurDay();
  const uiIcon = document.getElementById('weatherIcon');
  const uiTemp = document.getElementById('weatherTemp');
  const uiAdvice = document.getElementById('weatherAdviceBadge');
  uiAdvice.innerText = "💡 連線氣象局...";
  try {
    const url = `https://api.open-meteo.com/v1/forecast?latitude=${day.lat}&longitude=${day.lon}&daily=weathercode,temperature_2m_max,temperature_2m_min&current_weather=true&timezone=Asia%2FTokyo&start_date=${day.date}&end_date=${day.date}`;
    const res = await fetch(url);
    const json = await res.json();
    let code, temp;
    
    if(json.daily && json.daily.weathercode && json.daily.weathercode[0] !== undefined) {
        code = json.daily.weathercode[0];
        temp = Math.round((json.daily.temperature_2m_min[0]+json.daily.temperature_2m_max[0])/2);
    } else {
        code = json.current_weather.weathercode;
        temp = json.current_weather.temperature;
    }
    uiTemp.innerText = temp;
    
    let icon="🌤️", advice="天氣不錯。";
    if(code===0) { icon="☀️"; advice="陽光普照，適合拍照！"; }
    else if(code<=3) { icon="☁️"; advice="舒適多雲，適合散步。"; }
    else if(code<=48) { icon="🌫️"; advice="有霧，注意路況。"; }
    else if(code>=51) { icon="🌧️"; advice="記得帶傘，安排室內行程。"; }
    
    uiIcon.innerText = icon;
    uiAdvice.innerText = `💡 ${advice}`;

  } catch(e) { uiAdvice.innerText = "💡 無法取得天氣"; }
}

// ==================== 5. 渲染 ====================
function render() {
  const day = getCurDay();
  document.getElementById('headerTitle').innerText = day.title;
  document.getElementById('headerDate').innerText = day.date;
  document.getElementById('headerLoc').innerText = `📍 ${day.area || '設定地點'}`;
  
  const tabs = document.getElementById('dayTabs');
  tabs.innerHTML = '';
  data.days.forEach((d, i) => {
    const btn = document.createElement('button');
    btn.className = `flex-shrink-0 px-4 py-1.5 rounded-full text-xs font-semibold transition-all border shadow-sm ${i===curDayIdx ? 'bg-wine text-white border-wine' : 'bg-white/60 text-gray-600 border-white/30 backdrop-blur-md'}`;
    btn.innerText = `Day ${i+1}`;
    btn.onclick = () => { curDayIdx = i; render(); updateWeather(); };
    tabs.appendChild(btn);
  });
  
  const addBtn = document.createElement('button');
  addBtn.className = "flex-shrink-0 w-8 h-8 rounded-full border border-dashed border-white/50 bg-white/30 text-white flex items-center justify-center font-semibold backdrop-blur-md";
  addBtn.innerText = "+";
  addBtn.onclick = () => {
    const last = new Date(data.days[data.days.length-1].date);
    last.setDate(last.getDate()+1);
    data.days.push({ key: 'd'+Date.now(), date: last.toISOString().split('T')[0], title: `Day ${data.days.length+1}`, area:'博多', lat: 33.59, lon: 130.40, events: [] });
    saveData(); render();
  };
  tabs.appendChild(addBtn);

  const list = document.getElementById('timelineList');
  list.innerHTML = '';
  let dayTotal = 0;
  
  if(day.events.length === 0) list.innerHTML = `<div class="text-center text-gray-500 py-20 backdrop-blur-sm rounded-2xl bg-white/30 mx-5 mt-10"><p class="font-medium tracking-wider">點擊右下角 + 開始規劃</p></div>`;

  day.events.forEach(evt => {
    const el = document.createElement('div');
    el.className = "event-card flex gap-3";
    el.dataset.id = evt.id;
    
    el.onclick = (e) => {
        if(e.target.closest('.expense-btn')) return;
        openModal(evt.id);
    };

    const totalCost = (evt.expenses||[]).reduce((sum, i) => sum + Number(i.v), 0);
    dayTotal += totalCost;
    
    const colorMap = { 'transport': 'bg-[#5D7D9A]', 'shrine': 'bg-[#C0554F]', 'food': 'bg-[#D99559]', 'spot': 'bg-[#7D946F]', 'hotel': 'bg-[#83769C]', 'shop': 'bg-[#BA6E7E]', 'flex': 'bg-[#888888]' };
    const catNameMap = { 'transport':'交通', 'shrine':'神社', 'food':'吃飯', 'spot':'景點', 'shop':'逛街', 'hotel':'飯店', 'flex':'彈性', 'none':'' };
    const badge = catNameMap[evt.cat] ? `<span class="cat-tag ${colorMap[evt.cat]}">${catNameMap[evt.cat]}</span>` : '';
    const imgHtml = evt.img ? `<img src="${evt.img}" class="w-full h-32 object-cover rounded-xl mt-3 border border-black/10">` : '';
    const mapHtml = evt.map ? `<a href="${evt.map}" target="_blank" onclick="event.stopPropagation()" class="text-[10px] text-gray-500 underline ml-1 font-medium">地圖</a>` : '';

    // 字體變細：標題用 font-semibold
    el.innerHTML = `
      <div class="tl-col"><div class="tl-time">${evt.time}</div><div class="tl-line"></div><div class="tl-dot"></div></div>
      <div class="flex-1">
        <div class="flex justify-between items-start"><h3 class="font-semibold text-lg text-ink leading-tight">${evt.title}</h3></div>
        <div class="mt-1">${badge} ${mapHtml}</div>
        <p class="text-sm text-gray-600 mt-2 whitespace-pre-wrap leading-relaxed font-medium">${evt.note || ''}</p>
        <button class="expense-btn mt-3 px-3 py-1.5 rounded-lg text-xs font-semibold ${totalCost>0?'bg-wine/10 text-wine':'bg-gray-100 text-gray-500'} flex items-center gap-1 transition-colors" onclick="openExpense('${evt.id}')">¥ ${totalCost>0?totalCost:'記帳'}</button>
        ${imgHtml}
      </div>
    `;
    list.appendChild(el);
  });
  document.getElementById('dailyTotalDisplay').innerText = dayTotal;

  new Sortable(list, { animation: 200, delay: 200, delayOnTouchOnly: true, ghostClass: 'sortable-ghost', dragClass: 'sortable-drag', onEnd: (evt) => {
      const item = day.events.splice(evt.oldIndex, 1)[0];
      day.events.splice(evt.newIndex, 0, item);
      saveData();
  }});
}

// ==================== 6. Modals ====================
const overlay = document.getElementById('modalOverlay');
const editModal = document.getElementById('editModal');
const expenseModal = document.getElementById('expenseModal');
const settingsModal = document.getElementById('settingsModal');

function closeAll() { overlay.classList.remove('show'); editModal.classList.remove('show'); expenseModal.classList.remove('show'); settingsModal.classList.remove('show'); }
function closeModal() { closeAll(); }

// Settings
function openSettings() {
    const day = getCurDay();
    document.getElementById('setDate').value = day.date;
    document.getElementById('setTitle').value = day.title;
    document.getElementById('setLat').value = day.lat;
    document.getElementById('setLon').value = day.lon;
    document.getElementById('setAreaName').value = day.area || '';
    overlay.classList.add('show'); settingsModal.classList.add('show');
}
function fillLoc(name, lat, lon) { 
    document.getElementById('setLat').value = lat; 
    document.getElementById('setLon').value = lon; 
    document.getElementById('setAreaName').value = name;
}
function saveSettings() {
    const day = getCurDay();
    day.date = document.getElementById('setDate').value;
    day.title = document.getElementById('setTitle').value;
    day.lat = Number(document.getElementById('setLat').value);
    day.lon = Number(document.getElementById('setLon').value);
    day.area = document.getElementById('setAreaName').value || '福岡';
    saveData(); render(); updateWeather(); closeAll();
}

// Expense
function openExpense(id) {
    expenseId = id;
    const evt = getCurDay().events.find(e => e.id === id);
    document.getElementById('expenseTargetTitle').innerText = evt.title;
    document.getElementById('expenseRows').innerHTML = '';
    (evt.expenses || []).forEach(ex => addExpenseRow(ex.n, ex.v));
    if(!evt.expenses || evt.expenses.length === 0) addExpenseRow();
    updateExpenseTotal();
    overlay.classList.add('show'); expenseModal.classList.add('show');
}
function addExpenseRow(n='', v='') {
  const div = document.createElement('div');
  div.className = "flex gap-2 items-center";
  div.innerHTML = `<input type="text" class="ex-name w-1/2 p-3 border border-gray-200 rounded-xl text-sm bg-white" placeholder="項目" value="${n}"><input type="number" class="ex-val w-1/3 p-3 border border-gray-200 rounded-xl text-sm text-right font-mono" placeholder="¥" value="${v}" oninput="updateExpenseTotal()"><button onclick="this.parentElement.remove();updateExpenseTotal()" class="w-8 h-8 rounded-full bg-red-50 text-red-500">✕</button>`;
  document.getElementById('expenseRows').appendChild(div);
}
function updateExpenseTotal() {
  let total = 0;
  document.querySelectorAll('.ex-val').forEach(el => total += Number(el.value));
  document.getElementById('expenseModalTotal').innerText = total;
}
function saveExpense() {
    const evt = getCurDay().events.find(e => e.id === expenseId);
    const exps = [];
    document.querySelectorAll('#expenseRows > div').forEach(r => {
        const n = r.querySelector('.ex-name').value;
        const v = r.querySelector('.ex-val').value;
        if(n||v) exps.push({n,v});
    });
    evt.expenses = exps;
    saveData(); render(); closeAll();
}

// Edit Event
function openModal(id = null) {
  editId = id;
  document.getElementById('imgPreviewArea').classList.add('hidden');
  document.getElementById('inpImg').value = "";
  if(id) {
    const evt = getCurDay().events.find(e => e.id === id);
    document.getElementById('modalTitle').innerText = "編輯行程";
    document.getElementById('inpTime').value = evt.time;
    document.getElementById('inpTitle').value = evt.title;
    document.getElementById('inpCat').value = evt.cat || 'none';
    document.getElementById('inpMap').value = evt.map || '';
    document.getElementById('inpNote').value = evt.note || '';
    document.getElementById('btnDelete').classList.remove('hidden');
    tempImgBase64 = evt.img || '';
    if(tempImgBase64) { document.getElementById('imgPreview').src = tempImgBase64; document.getElementById('imgPreviewArea').classList.remove('hidden'); }
  } else {
    document.getElementById('modalTitle').innerText = "新增行程";
    document.getElementById('inpTime').value = "09:00";
    document.getElementById('inpTitle').value = "";
    document.getElementById('inpCat').value = "none";
    document.getElementById('inpMap').value = "";
    document.getElementById('inpNote').value = "";
    document.getElementById('btnDelete').classList.add('hidden');
    tempImgBase64 = "";
  }
  overlay.classList.add('show'); editModal.classList.add('show');
}

document.getElementById('inpImg').onchange = (e) => {
  const f = e.target.files[0];
  if(f) {
    const r = new FileReader();
    r.onload = (ev) => { tempImgBase64 = ev.target.result; document.getElementById('imgPreview').src = tempImgBase64; document.getElementById('imgPreviewArea').classList.remove('hidden'); };
    r.readAsDataURL(f);
  }
};
function clearImage() { tempImgBase64 = ""; document.getElementById('inpImg').value = ""; document.getElementById('imgPreviewArea').classList.add('hidden'); }

function saveEvent() {
  const time = document.getElementById('inpTime').value;
  const title = document.getElementById('inpTitle').value;
  if(!title) { alert("請輸入名稱"); return; }
  const day = getCurDay();
  if(editId) {
    const evt = day.events.find(e => e.id === editId);
    evt.time = time; evt.title = title; evt.cat = document.getElementById('inpCat').value;
    evt.map = document.getElementById('inpMap').value; evt.note = document.getElementById('inpNote').value;
    evt.img = tempImgBase64;
  } else {
    day.events.push({ id: 'e'+Date.now(), time, title, cat: document.getElementById('inpCat').value, map: document.getElementById('inpMap').value, note: document.getElementById('inpNote').value, img: tempImgBase64, expenses: [] });
    day.events.sort((a,b) => a.time.localeCompare(b.time));
  }
  saveData(); render(); closeAll();
}

function deleteEvent() {
  if(!confirm("確定刪除？")) return;
  const day = getCurDay();
  day.events = day.events.filter(e => e.id !== editId);
  saveData(); render(); closeAll();
}

initCover();
render();
</script>
</body>
</html>
