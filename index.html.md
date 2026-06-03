<!DOCTYPE html>  
<html lang="zh-TW">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>Minimal App</title>  
    <script src="https://cdn.tailwindcss.com"></script>  
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>  
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">  
    <style>  
        /* 隱藏滾動條但保持功能 */  
        .no-scrollbar::-webkit-scrollbar { display: none; }  
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }  
    </style>  
</head>  
<body class="bg-zinc-100 font-sans antialiased text-zinc-900 flex justify-center min-h-screen">  
  
    <div id="app" class="w-full max-w-md bg-white min-h-screen flex flex-col shadow-2xl relative border-x border-zinc-200">  
          
        <header class="bg-white border-b border-zinc-200 px-6 py-4 sticky top-0 z-50 flex justify-between items-center">  
            <h1 class="text-xl font-black tracking-widest uppercase">{{ currentTabTitle }}</h1>  
            <span class="text-xs font-bold bg-zinc-900 text-white px-2 py-1 rounded">{{ todayDate }}</span>  
        </header>  
  
        <main class="flex-1 overflow-y-auto p-5 pb-24 no-scrollbar">  
              
            <div v-if="activeTab === 'schedule'" class="space-y-4">  
                <div class="bg-black text-white p-4 rounded-2xl shadow-sm mb-6">  
                    <p class="text-xs uppercase tracking-wider opacity-60">今日進度</p>  
                    <p class="text-lg font-bold mt-1">你有 {{ schedules.length }} 個待辦行程</p>  
                </div>  
                  
                <div class="bg-zinc-50 p-4 rounded-2xl border border-zinc-200 space-y-2">  
                    <input v-model="newSchedule.time" type="time" class="w-full bg-white border border-zinc-300 rounded-xl px-3 py-2 text-sm focus:outline-none focus:border-black">  
                    <input v-model="newSchedule.content" type="text" placeholder="輸入行程內容..." class="w-full bg-white border border-zinc-300 rounded-xl px-3 py-2 text-sm focus:outline-none focus:border-black">  
                    <button @click="addSchedule" class="w-full bg-zinc-900 hover:bg-black text-white rounded-xl py-2 text-sm font-medium transition-all">新增行程</button>  
                </div>  
  
                <div class="space-y-3">  
                    <div v-for="(item, index) in sortedSchedules" :key="index" class="bg-white border border-zinc-200 p-4 rounded-2xl flex justify-between items-center shadow-sm">  
                        <div class="flex items-center space-x-3">  
                            <span class="text-xs font-mono font-bold bg-zinc-100 px-2 py-1 rounded border border-zinc-300">{{ item.time }}</span>  
                            <p class="text-sm font-medium text-zinc-800">{{ item.content }}</p>  
                        </div>  
                        <button @click="deleteSchedule(index)" class="text-zinc-400 hover:text-black transition-colors"><i class="fa-regular fa-trash-can"></i></button>  
                    </div>  
                    <p v-if="schedules.length === 0" class="text-center text-zinc-400 text-xs py-8">目前沒有安排行程</p>  
                </div>  
            </div>  
  
            <div v-if="activeTab === 'map'" class="space-y-4">  
                <div class="bg-white border border-zinc-200 rounded-2xl p-3 shadow-sm overflow-hidden">  
                    <iframe   
                        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m12!1m3!1d3615.007963297052!2d121.56228357632617!3d25.033664038301777!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x3442abb6da9c9e17%3A0x84a268a8b6940350!2z6Ie6 thoseIDEwMQ!5e0!3m2!1szh-TW!2stw!4v1700000000000!5m2!1szh-TW!2stw"   
                        class="w-full h-80 rounded-xl border-0"   
                        allowfullscreen=""   
                        loading="lazy"   
                        referrerpolicy="no-referrer-when-downgrade">  
                    </iframe>  
                </div>  
                <div class="bg-zinc-900 text-white p-4 rounded-2xl shadow-sm">  
                    <h3 class="font-bold text-sm mb-1"><i class="fa-solid fa-location-dot mr-1"></i> 定位導航</h3>  
                    <p class="text-xs text-zinc-400 leading-relaxed">單頁式應用可透過內嵌地圖提供即時位置瀏覽，點擊地圖放大即可進行路線規劃。</p>  
                </div>  
            </div>  
  
            <div v-if="activeTab === 'accounting'" class="space-y-4">  
                <div class="bg-black text-white p-5 rounded-2xl shadow-sm flex justify-between items-center">  
                    <div>  
                        <p class="text-xs uppercase tracking-wider opacity-60">目前總花費</p>  
                        <p class="text-3xl font-black mt-1">$ {{ totalExpense }}</p>  
                    </div>  
                    <i class="fa-solid fa-wallet text-3xl opacity-20"></i>  
                </div>  
  
                <div class="bg-zinc-50 p-4 rounded-2xl border border-zinc-200 space-y-2">  
                    <input v-model="newExpense.title" type="text" placeholder="品項名稱 (如：午餐)" class="w-full bg-white border border-zinc-300 rounded-xl px-3 py-2 text-sm focus:outline-none focus:border-black">  
                    <input v-model.number="newExpense.amount" type="number" placeholder="金額" class="w-full bg-white border border-zinc-300 rounded-xl px-3 py-2 text-sm focus:outline-none focus:border-black">  
                    <button @click="addExpense" class="w-full bg-zinc-900 hover:bg-black text-white rounded-xl py-2 text-sm font-medium transition-all">記下一筆</button>  
                </div>  
  
                <div class="space-y-2">  
                    <div v-for="(item, index) in expenses" :key="index" class="bg-white border border-zinc-200 p-4 rounded-2xl flex justify-between items-center shadow-sm">  
                        <div>  
                            <p class="text-sm font-bold text-zinc-800">{{ item.title }}</p>  
                            <p class="text-xs text-zinc-400">{{ item.date }}</p>  
                        </div>  
                        <div class="flex items-center space-x-3">  
                            <span class="text-sm font-mono font-black">-${{ item.amount }}</span>  
                            <button @click="deleteExpense(index)" class="text-zinc-400 hover:text-black transition-colors"><i class="fa-regular fa-trash-can"></i></button>  
                        </div>  
                    </div>  
                    <p v-if="expenses.length === 0" class="text-center text-zinc-400 text-xs py-8">尚無記帳紀錄</p>  
                </div>  
            </div>  
  
            <div v-if="activeTab === 'shopping'" class="space-y-4">  
                <div class="flex space-x-2">  
                    <input v-model="newShopItem" @keyup.enter="addShopItem" type="text" placeholder="想買什麼...？" class="flex-1 bg-white border border-zinc-300 rounded-xl px-4 py-2 text-sm focus:outline-none focus:border-black">  
                    <button @click="addShopItem" class="bg-zinc-900 hover:bg-black text-white px-4 rounded-xl text-sm font-medium transition-all"><i class="fa-solid fa-plus"></i></button>  
                </div>  
  
                <div class="bg-white border border-zinc-200 rounded-2xl divide-y divide-zinc-100 overflow-hidden shadow-sm">  
                    <div v-for="(item, index) in shoppingList" :key="index" class="p-4 flex justify-between items-center transition-all" :class="{'bg-zinc-50 opacity-60': item.done}">  
                        <div class="flex items-center space-x-3 cursor-pointer" @click="item.done = !item.done">  
                            <i class="fa-regular text-lg" :class="item.done ? 'fa-square-check text-black' : 'fa-square text-zinc-300'"></i>  
                            <span class="text-sm font-medium" :class="{'line-through text-zinc-400': item.done}">{{ item.name }}</span>  
                        </div>  
                        <button @click="deleteShopItem(index)" class="text-zinc-400 hover:text-black transition-colors"><i class="fa-regular fa-trash-can"></i></button>  
                    </div>  
                    <p v-if="shoppingList.length === 0" class="text-center text-zinc-400 text-xs py-8">購物清單空空如也</p>  
                </div>  
            </div>  
  
        </main>  
  
        <nav class="absolute bottom-0 left-0 right-0 bg-white border-t border-zinc-200 h-20 flex justify-around items-center px-2 z-50">  
            <button @click="activeTab = 'schedule'" :class="activeTab === 'schedule' ? 'text-black font-bold' : 'text-zinc-400'" class="flex flex-col items-center justify-center w-16 transition-all">  
                <i class="fa-regular fa-calendar-check text-lg mb-1"></i>  
                <span class="text-[10px] tracking-wider">行程</span>  
            </button>  
              
            <button @click="activeTab = 'map'" :class="activeTab === 'map' ? 'text-black font-bold' : 'text-zinc-400'" class="flex flex-col items-center justify-center w-16 transition-all">  
                <i class="fa-regular fa-map text-lg mb-1"></i>  
                <span class="text-[10px] tracking-wider">地圖</span>  
            </button>  
              
            <button @click="activeTab = 'accounting'" :class="activeTab === 'accounting' ? 'text-black font-bold' : 'text-zinc-400'" class="flex flex-col items-center justify-center w-16 transition-all">  
                <i class="fa-solid fa-coins text-lg mb-1"></i>  
                <span class="text-[10px] tracking-wider">記帳</span>  
            </button>  
              
            <button @click="activeTab = 'shopping'" :class="activeTab === 'shopping' ? 'text-black font-bold' : 'text-zinc-400'" class="flex flex-col items-center justify-center w-16 transition-all">  
                <i class="fa-solid fa-bag-shopping text-lg mb-1"></i>  
                <span class="text-[10px] tracking-wider">購物</span>  
            </button>  
        </nav>  
  
    </div>  
  
    <script>  
        const { createApp, ref, computed } = Vue;  
  
        createApp({  
            setup() {  
                // 狀態管理  
                const activeTab = ref('schedule');  
                  
                // 1. 行程表資料  
                const schedules = ref([  
                    { time: '09:00', content: '早晨咖啡與信件回覆' },  
                    { time: '14:30', content: '專案技術會議' }  
                ]);  
                const newSchedule = ref({ time: '', content: '' });  
  
                // 2. 記帳資料  
                const expenses = ref([  
                    { title: '美式咖啡', amount: 120, date: '今日' },  
                    { title: '日式拉麵', amount: 280, date: '今日' }  
                ]);  
                const newExpense = ref({ title: '', amount: null });  
  
                // 3. 購物清單資料  
                const shoppingList = ref([  
                    { name: 'iPad Pro 螢幕保護貼', done: false },  
                    { name: '黑色簡約馬克杯', done: true }  
                ]);  
                const newShopItem = ref('');  
  
                // 計算屬性：依時間排序行程  
                const sortedSchedules = computed(() => {  
                    return [...schedules.value].sort((a, b) => a.time.localeCompare(b.time));  
                });  
  
                // 計算屬性：計算總花費  
                const totalExpense = computed(() => {  
                    return expenses.value.reduce((sum, item) => sum + (Number(item.amount) || 0), 0);  
                });  
  
                // 計算屬性：根據 Tab 顯示標題  
                const currentTabTitle = computed(() => {  
                    const titles = { schedule: 'Schedule', map: 'Explore Map', accounting: 'Wallet', shopping: 'Cart List' };  
                    return titles[activeTab.value];  
                });  
  
                // 取得今日日期簡寫  
                const todayDate = computed(() => {  
                    const date = new Date();  
                    return `${date.getMonth() + 1}/${date.getDate()}`;  
                });  
  
                // --- 方法功能 ---  
                // 行程操作  
                const addSchedule = () => {  
                    if (newSchedule.value.time && newSchedule.value.content) {  
                        schedules.value.push({ ...newSchedule.value });  
                        newSchedule.value.time = '';  
                        newSchedule.value.content = '';  
                    }  
                };  
                const deleteSchedule = (index) => schedules.value.splice(index, 1);  
  
                // 記帳操作  
                const addExpense = () => {  
                    if (newExpense.value.title && newExpense.value.amount) {  
                        expenses.value.push({  
                            title: newExpense.value.title,  
                            amount: newExpense.value.amount,  
                            date: '今日'  
                        });  
                        newExpense.value.title = '';  
                        newExpense.value.amount = null;  
                    }  
                };  
                const deleteExpense = (index) => expenses.value.splice(index, 1);  
  
                // 購物清單操作  
                const addShopItem = () => {  
                    if (newShopItem.value.trim()) {  
                        shoppingList.value.push({ name: newShopItem.value.trim(), done: false });  
                        newShopItem.value = '';  
                    }  
                };  
                const deleteShopItem = (index) => shoppingList.value.splice(index, 1);  
  
                return {  
                    activeTab, currentTabTitle, todayDate,  
                    schedules, newSchedule, sortedSchedules, addSchedule, deleteSchedule,  
                    expenses, newExpense, totalExpense, addExpense, deleteExpense,  
                    shoppingList, newShopItem, addShopItem, deleteShopItem  
                };  
            }  
        }).mount('#app');  
    </script>  
</body>  
</html>  
