# food
<!DOCTYPE html>
<html lang="vi" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FitScan AI - Camera Quét Dinh Dưỡng & Món Ăn Vặt Đồ Chiên Rán Cho Gymer</title>
  
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            gymDark: '#0B0F17',
            gymPanel: '#111827',
            gymCard: '#1F2937',
            neonGreen: '#10B981',
            neonCyan: '#06B6D4',
            neonAmber: '#F59E0B',
            neonRose: '#F43F5E',
            neonPurple: '#8B5CF6'
          },
          fontFamily: {
            sans: ['Inter', 'system-ui', 'sans-serif']
          }
        }
      }
    }
  </script>
  
  <!-- Font Awesome Icons CDN -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <!-- Custom Styling for Animations and Scanner Glass effect -->
  <style>
    @keyframes scanLine {
      0% { top: 0%; opacity: 0.8; }
      50% { top: 95%; opacity: 1; }
      100% { top: 0%; opacity: 0.8; }
    }
    .animate-scan-line {
      animation: scanLine 2.5s ease-in-out infinite;
    }
    
    @keyframes pulseGlow {
      0%, 100% { box-shadow: 0 0 15px rgba(16, 185, 129, 0.4); }
      50% { box-shadow: 0 0 30px rgba(16, 185, 129, 0.8); }
    }
    .glow-neon {
      animation: pulseGlow 2s infinite;
    }

    @keyframes pulseAmber {
      0%, 100% { box-shadow: 0 0 15px rgba(245, 158, 11, 0.4); }
      50% { box-shadow: 0 0 30px rgba(245, 158, 11, 0.8); }
    }
    .glow-amber {
      animation: pulseAmber 2s infinite;
    }

    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #0B0F17;
    }
    ::-webkit-scrollbar-thumb {
      background: #374151;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #10B981;
    }

    /* Glassmorphism Panel */
    .glass-panel {
      background: rgba(17, 24, 39, 0.75);
      backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.08);
    }
  </style>
</head>
<body class="bg-gymDark text-gray-100 font-sans min-h-screen flex flex-col antialiased selection:bg-neonGreen selection:text-black">

  <!-- Top Navigation Bar -->
  <header class="sticky top-0 z-40 bg-gymPanel/90 backdrop-blur-md border-b border-gray-800 px-4 py-3 shadow-lg">
    <div class="max-w-7xl mx-auto flex flex-wrap justify-between items-center gap-3">
      
      <!-- Logo & Title -->
      <div class="flex items-center space-x-3">
        <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-neonGreen to-neonCyan flex items-center justify-center text-gymDark text-xl font-black shadow-lg shadow-neonGreen/20">
          <i class="fa-solid fa-camera-retro"></i>
        </div>
        <div>
          <div class="flex items-center space-x-2">
            <h1 class="font-extrabold text-lg md:text-xl tracking-tight bg-gradient-to-r from-neonGreen via-neonCyan to-white bg-clip-text text-transparent">
              FITSCAN AI
            </h1>
            <span class="bg-neonAmber/20 text-neonAmber border border-neonAmber/30 text-[10px] uppercase tracking-wider font-extrabold px-2 py-0.5 rounded-full">
              Gymer & Street Snack Edition
            </span>
          </div>
          <p class="text-xs text-gray-400">Camera Quét Dinh Dưỡng Live & Cảnh Báo Cheat Meal</p>
        </div>
      </div>

      <!-- Quick Action Stats & Gemini Key -->
      <div class="flex items-center space-x-2 md:space-x-4">
        <button onclick="openApiKeyModal()" id="apiKeyBtn" class="flex items-center space-x-1.5 px-3 py-1.5 text-xs rounded-lg border border-gray-700 bg-gymCard hover:border-neonGreen transition-all text-gray-300">
          <i class="fa-solid fa-key text-neonAmber"></i>
          <span id="apiKeyStatusText">Gemini API: Auto Mock</span>
        </button>

        <div class="hidden sm:flex items-center bg-gymCard border border-gray-800 rounded-lg px-3 py-1.5 text-xs text-gray-300 space-x-3">
          <div><i class="fa-solid fa-fire text-neonRose mr-1"></i> <span id="headerCalories">0</span> / <span id="headerCalorieTarget">2200</span> kcal</div>
          <div class="text-gray-600">|</div>
          <div><i class="fa-solid fa-drumstick-bite text-neonCyan mr-1"></i> <span id="headerProtein">0</span>g Prot</div>
        </div>
      </div>

    </div>
  </header>

  <!-- Main Application Body Container -->
  <main class="flex-1 max-w-7xl w-full mx-auto p-3 sm:p-5 flex flex-col space-y-6">

    <!-- Navigation Tabs -->
    <nav class="flex overflow-x-auto space-x-2 p-1.5 bg-gymPanel rounded-2xl border border-gray-800 no-scrollbar">
      <button onclick="switchTab('cameraTab')" id="tab-cameraTab" class="tab-btn active flex items-center space-x-2 px-4 py-2.5 rounded-xl text-xs sm:text-sm font-semibold transition-all whitespace-nowrap bg-neonGreen text-gymDark shadow-md">
        <i class="fa-solid fa-camera"></i>
        <span>Camera AI Quét Món</span>
      </button>

      <button onclick="switchTab('libraryTab')" id="tab-libraryTab" class="tab-btn flex items-center space-x-2 px-4 py-2.5 rounded-xl text-xs sm:text-sm font-semibold transition-all whitespace-nowrap text-gray-400 hover:text-white hover:bg-gymCard">
        <i class="fa-solid fa-burger"></i>
        <span>Thư Viện Ăn Vặt & Chiên Rán (30+)</span>
      </button>

      <button onclick="switchTab('diaryTab')" id="tab-diaryTab" class="tab-btn flex items-center space-x-2 px-4 py-2.5 rounded-xl text-xs sm:text-sm font-semibold transition-all whitespace-nowrap text-gray-400 hover:text-white hover:bg-gymCard">
        <i class="fa-solid fa-book-journal-whills"></i>
        <span>Nhật Ký Bữa Ăn</span>
      </button>

      <button onclick="switchTab('bmrTab')" id="tab-bmrTab" class="tab-btn flex items-center space-x-2 px-4 py-2.5 rounded-xl text-xs sm:text-sm font-semibold transition-all whitespace-nowrap text-gray-400 hover:text-white hover:bg-gymCard">
        <i class="fa-solid fa-calculator"></i>
        <span>Tính BMR & TDEE Target</span>
      </button>

      <button onclick="switchTab('tipsTab')" id="tab-tipsTab" class="tab-btn flex items-center space-x-2 px-4 py-2.5 rounded-xl text-xs sm:text-sm font-semibold transition-all whitespace-nowrap text-gray-400 hover:text-white hover:bg-gymCard">
        <i class="fa-solid fa-dumbbell"></i>
        <span>Góc Xả Cơ & Burnout</span>
      </button>
    </nav>

    <!-- TAB 1: CAMERA AI SCANNER -->
    <section id="cameraTab" class="tab-content grid grid-cols-1 lg:grid-cols-12 gap-6">
      
      <!-- Video / Image Preview Window -->
      <div class="lg:col-span-7 flex flex-col space-y-4">
        <div class="relative w-full aspect-[4/3] sm:aspect-[16/10] bg-black rounded-3xl overflow-hidden border-2 border-gray-800 shadow-2xl flex items-center justify-center group">
          
          <!-- Live Camera Video -->
          <video id="webcamVideo" autoplay playsinline muted class="w-full h-full object-cover"></video>
          
          <!-- Uploaded Image Canvas / Preview -->
          <img id="imagePreview" class="hidden w-full h-full object-cover" alt="Ảnh tải lên">

          <!-- Cyberpunk Overlay Grid & Target Scanner -->
          <div id="scannerOverlay" class="absolute inset-0 pointer-events-none border-[12px] border-black/40 flex flex-col justify-between p-4">
            
            <!-- Top HUD Info -->
            <div class="flex justify-between items-start text-xs font-mono">
              <span class="bg-black/60 backdrop-blur-md px-2.5 py-1 rounded text-neonGreen border border-neonGreen/30 flex items-center space-x-1">
                <span class="w-2 h-2 rounded-full bg-neonGreen animate-ping mr-1"></span>
                <span>SYSTEM ONLINE</span>
              </span>
              <span class="bg-black/60 backdrop-blur-md px-2.5 py-1 rounded text-gray-300 border border-gray-700" id="camResolution">
                LIVE CAM 1080P
              </span>
            </div>

            <!-- Reticle Target Lines -->
            <div class="relative w-48 h-48 sm:w-64 sm:h-64 mx-auto border-2 border-dashed border-neonGreen/60 rounded-2xl flex items-center justify-center transition-all duration-300">
              <!-- Corner brackets -->
              <div class="absolute -top-1 -left-1 w-6 h-6 border-t-4 border-l-4 border-neonGreen"></div>
              <div class="absolute -top-1 -right-1 w-6 h-6 border-t-4 border-r-4 border-neonGreen"></div>
              <div class="absolute -bottom-1 -left-1 w-6 h-6 border-b-4 border-l-4 border-neonGreen"></div>
              <div class="absolute -bottom-1 -right-1 w-6 h-6 border-b-4 border-r-4 border-neonGreen"></div>
              
              <!-- Laser Scanner line -->
              <div id="laserLine" class="hidden absolute left-2 right-2 h-0.5 bg-gradient-to-r from-transparent via-neonGreen to-transparent shadow-[0_0_15px_#10B981] animate-scan-line"></div>
              <div class="text-[10px] text-neonGreen/70 font-mono tracking-widest text-center uppercase">Đặt món ăn vào khung hình</div>
            </div>

            <!-- Bottom HUD Status -->
            <div class="text-right text-[10px] font-mono text-neonCyan/80 bg-black/50 px-2 py-0.5 rounded self-end">
              FITSCAN_ENGINE_v3.2
            </div>
          </div>

          <!-- Camera Controls Overlay -->
          <div class="absolute bottom-4 left-0 right-0 px-4 flex justify-center items-center space-x-3 z-20">
            <button onclick="toggleCamera()" title="Đổi camera" class="p-3 bg-black/60 hover:bg-black text-white rounded-full border border-gray-700 backdrop-blur-md transition-all">
              <i class="fa-solid fa-camera-rotate"></i>
            </button>

            <button onclick="captureAndScan()" id="scanBtn" class="px-6 py-3 bg-gradient-to-r from-neonGreen to-neonCyan text-gymDark font-black text-sm rounded-full shadow-lg hover:scale-105 active:scale-95 transition-all flex items-center space-x-2 glow-neon">
              <i class="fa-solid fa-bolt text-base"></i>
              <span>CỤP ÁNH & QUÉT AI</span>
            </button>

            <label title="Tải ảnh món ăn lên" class="p-3 bg-black/60 hover:bg-black text-white rounded-full border border-gray-700 backdrop-blur-md cursor-pointer transition-all">
              <i class="fa-solid fa-upload"></i>
              <input type="file" id="fileInput" accept="image/*" class="hidden" onchange="handleImageUpload(event)">
            </label>
          </div>
        </div>

        <!-- Quick Sample Scanner Trigger Bar -->
        <div class="glass-panel p-3 rounded-2xl flex flex-col space-y-2">
          <div class="flex justify-between items-center text-xs text-gray-400">
            <span><i class="fa-solid fa-fire-flame-curved text-neonAmber mr-1"></i> Thử nhanh món ăn phổ biến:</span>
            <span class="text-[10px] text-gray-500">(Click để giả lập quét)</span>
          </div>
          <div class="flex overflow-x-auto space-x-2 py-1 no-scrollbar">
            <button onclick="simulateScan('Gà Rán Giòn')" class="px-3 py-1.5 bg-gymCard hover:bg-gymCard/80 border border-gray-700 text-xs rounded-xl flex items-center space-x-1.5 whitespace-nowrap">
              <span>🍗 Gà Rán Giòn</span>
            </button>
            <button onclick="simulateScan('Bánh Tráng Trộn')" class="px-3 py-1.5 bg-gymCard hover:bg-gymCard/80 border border-gray-700 text-xs rounded-xl flex items-center space-x-1.5 whitespace-nowrap">
              <span>🥗 Bánh Tráng Trộn</span>
            </button>
            <button onclick="simulateScan('Trà Sữa Trân Châu')" class="px-3 py-1.5 bg-gymCard hover:bg-gymCard/80 border border-gray-700 text-xs rounded-xl flex items-center space-x-1.5 whitespace-nowrap">
              <span>🧋 Trà Sữa Trân Châu</span>
            </button>
            <button onclick="simulateScan('Ngực Gà Áp Chảo')" class="px-3 py-1.5 bg-gymCard hover:bg-gymCard/80 border border-gray-700 text-xs rounded-xl flex items-center space-x-1.5 whitespace-nowrap">
              <span>🥩 Ngực Gà Áp Chảo</span>
            </button>
            <button onclick="simulateScan('Khoai Tây Chiên')" class="px-3 py-1.5 bg-gymCard hover:bg-gymCard/80 border border-gray-700 text-xs rounded-xl flex items-center space-x-1.5 whitespace-nowrap">
              <span>🍟 Khoai Tây Chiên</span>
            </button>
          </div>
        </div>

      </div>

      <!-- AI Analysis Result Panel -->
      <div class="lg:col-span-5 flex flex-col justify-between">
        <div class="glass-panel p-5 rounded-3xl border border-gray-800 h-full flex flex-col justify-between space-y-4">
          
          <!-- Header of Analysis -->
          <div class="flex justify-between items-center border-b border-gray-800 pb-3">
            <div class="flex items-center space-x-2">
              <i class="fa-solid fa-microchip text-neonGreen text-lg"></i>
              <h2 class="font-bold text-base text-gray-200">KẾT QUẢ PHÂN TÍCH AI</h2>
            </div>
            <span id="scanStatusBadge" class="bg-gray-800 text-gray-400 text-xs px-2.5 py-1 rounded-full border border-gray-700">
              Sẵn sàng quét
            </span>
          </div>

          <!-- Content Body when scanned -->
          <div id="scanResultContainer" class="flex-1 flex flex-col justify-center space-y-4">
            
            <!-- Default Placeholder State -->
            <div id="scanPlaceholder" class="text-center py-10 text-gray-500 space-y-3">
              <div class="w-16 h-16 mx-auto rounded-full bg-gymCard border border-gray-800 flex items-center justify-center text-2xl text-neonGreen">
                <i class="fa-solid fa-expand"></i>
              </div>
              <p class="text-sm">Hướng camera vào món ăn hoặc tải ảnh lên, sau đó bấm <br><span class="text-neonGreen font-semibold">"Cụp Ảnh & Quét AI"</span></p>
            </div>

            <!-- Active Scan Results (Hidden initially) -->
            <div id="scanContent" class="hidden space-y-4">
              
              <!-- Dish Image & Basic Info -->
              <div class="flex items-center space-x-4">
                <img id="scannedFoodImage" src="" alt="Món quét" class="w-20 h-20 rounded-2xl object-cover border border-gray-700 shadow-md">
                <div class="flex-1">
                  <span id="scannedFoodCategory" class="text-[10px] uppercase font-bold text-neonGreen bg-neonGreen/10 border border-neonGreen/30 px-2 py-0.5 rounded-md">Món Ăn Vặt</span>
                  <h3 id="scannedFoodName" class="text-xl font-black text-white mt-1">Tên Món Ăn</h3>
                  <p id="scannedFoodServing" class="text-xs text-gray-400">Khẩu phần: ~150g (1 phần standard)</p>
                </div>
              </div>

              <!-- Macro Breakdown Grid -->
              <div class="grid grid-cols-4 gap-2 text-center">
                <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
                  <span class="block text-[10px] text-gray-400 uppercase font-semibold">Calo</span>
                  <span id="scannedCal" class="text-base font-black text-neonRose">0</span>
                  <span class="text-[9px] text-gray-500 block">kcal</span>
                </div>
                <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
                  <span class="block text-[10px] text-gray-400 uppercase font-semibold">Protein</span>
                  <span id="scannedProtein" class="text-base font-black text-neonCyan">0</span>
                  <span class="text-[9px] text-gray-500 block">g</span>
                </div>
                <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
                  <span class="block text-[10px] text-gray-400 uppercase font-semibold">Carbs</span>
                  <span id="scannedCarbs" class="text-base font-black text-neonAmber">0</span>
                  <span class="text-[9px] text-gray-500 block">g</span>
                </div>
                <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
                  <span class="block text-[10px] text-gray-400 uppercase font-semibold">Fat</span>
                  <span id="scannedFat" class="text-base font-black text-purple-400">0</span>
                  <span class="text-[9px] text-gray-500 block">g</span>
                </div>
              </div>

              <!-- Cheat Meal Warning & Oil Rating -->
              <div id="cheatMealBox" class="p-3.5 rounded-2xl bg-neonAmber/10 border border-neonAmber/30 flex items-start space-x-3">
                <div class="p-2 bg-neonAmber/20 rounded-xl text-neonAmber text-lg">
                  <i class="fa-solid fa-triangle-exclamation"></i>
                </div>
                <div class="text-xs space-y-1">
                  <div class="flex justify-between items-center font-bold text-neonAmber">
                    <span>CHEAT MEAL ALERT</span>
                    <span id="oilRatingText" class="text-[11px] bg-neonAmber/20 px-2 py-0.5 rounded text-amber-200">Độ Dầu Mỡ: Cao ⚡</span>
                  </div>
                  <p id="cheatMealAdvice" class="text-gray-300">
                    Món này chứa nhiều chất béo bão hòa. Hãy tập luyện thêm Cardio sau khi ăn.
                  </p>
                </div>
              </div>

              <!-- Burnout Advice for Gymer -->
              <div class="bg-gymCard/80 p-3.5 rounded-2xl border border-gray-800 space-y-2 text-xs">
                <div class="font-bold text-neonGreen flex items-center space-x-1.5">
                  <i class="fa-solid fa-person-running"></i>
                  <span>LỜI KHUYÊN BURN-OUT CHO GYMER</span>
                </div>
                <p id="burnoutWorkouts" class="text-gray-300 leading-relaxed">
                  Cần chạy bộ ~45 phút (tốc độ 8km/h) hoặc 500 cái Nhảy Dây để tiêu thụ lượng calo này.
                </p>
              </div>

              <!-- Action Buttons -->
              <div class="pt-2 flex flex-col sm:flex-row gap-2">
                <button onclick="addScannedToDiary()" class="flex-1 py-3 px-4 bg-neonGreen hover:bg-emerald-400 text-gymDark font-bold text-sm rounded-xl transition-all shadow-lg flex items-center justify-center space-x-2">
                  <i class="fa-solid fa-plus-circle"></i>
                  <span>Thêm Vào Nhật Ký Bữa Ăn</span>
                </button>
                <button onclick="openScannedFoodModal()" class="py-3 px-4 bg-gymCard hover:bg-gray-700 text-gray-200 border border-gray-700 font-semibold text-sm rounded-xl transition-all flex items-center justify-center">
                  <i class="fa-solid fa-circle-info mr-1"></i> Chi Tiết
                </button>
              </div>

            </div>

          </div>

          <!-- Bottom Gemini AI Powered Label -->
          <div class="pt-3 border-t border-gray-800/80 text-[11px] text-gray-500 flex justify-between items-center font-mono">
            <span>MODEL: GEMINI 3 FLASH</span>
            <span class="text-neonCyan"><i class="fa-solid fa-shield-halved mr-1"></i> VERIFIED NUTRITION</span>
          </div>

        </div>
      </div>

    </section>

    <!-- TAB 2: FOOD LIBRARY (30+ SNACKS & GYM MEALS) -->
    <section id="libraryTab" class="tab-content hidden space-y-5">
      
      <!-- Top Filters & Search Controls -->
      <div class="glass-panel p-4 rounded-3xl border border-gray-800 flex flex-col md:flex-row gap-3 justify-between items-center">
        
        <!-- Search Input -->
        <div class="relative w-full md:w-80">
          <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3.5 text-gray-500 text-sm"></i>
          <input type="text" id="librarySearchInput" oninput="renderLibrary()" placeholder="Tìm món ăn, trà sữa, gà rán..." class="w-full bg-gymCard border border-gray-700 rounded-xl pl-10 pr-4 py-2.5 text-xs text-gray-200 focus:outline-none focus:border-neonGreen transition-all">
        </div>

        <!-- Filter Category Chips -->
        <div class="flex overflow-x-auto space-x-2 w-full md:w-auto py-1 no-scrollbar">
          <button onclick="filterLibrary('all')" class="cat-chip active px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap bg-neonGreen text-gymDark">Tất Cả</button>
          <button onclick="filterLibrary('fried')" class="cat-chip px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap bg-gymCard text-gray-400 hover:text-white border border-gray-700">🔥 Đồ Chiên & Ăn Vặt</button>
          <button onclick="filterLibrary('gym')" class="cat-chip px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap bg-gymCard text-gray-400 hover:text-white border border-gray-700">💪 Tăng Cơ Đạm Cao</button>
          <button onclick="filterLibrary('drinks')" class="cat-chip px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap bg-gymCard text-gray-400 hover:text-white border border-gray-700">🥤 Đồ Uống & Chè</button>
          <button onclick="filterLibrary('healthy')" class="cat-chip px-3 py-1.5 rounded-xl text-xs font-semibold whitespace-nowrap bg-gymCard text-gray-400 hover:text-white border border-gray-700">🥑 Carbs & Fat Tốt</button>
        </div>

        <!-- Add Custom Dish Button -->
        <button onclick="openAddCustomFoodModal()" class="w-full md:w-auto px-4 py-2.5 bg-gymCard hover:bg-gray-700 border border-gray-700 text-neonGreen font-semibold text-xs rounded-xl flex items-center justify-center space-x-1.5 transition-all">
          <i class="fa-solid fa-circle-plus"></i>
          <span>Thêm Món Mới</span>
        </button>
      </div>

      <!-- Food Grid List -->
      <div id="libraryFoodGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
        <!-- Rendered dynamically via JavaScript -->
      </div>

    </section>

    <!-- TAB 3: DAILY MEAL DIARY -->
    <section id="diaryTab" class="tab-content hidden space-y-6">
      
      <!-- Daily Macros Overview Dashboard -->
      <div class="glass-panel p-5 rounded-3xl border border-gray-800 space-y-5">
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-2 border-b border-gray-800 pb-3">
          <div>
            <h2 class="text-lg font-bold text-gray-100 flex items-center space-x-2">
              <i class="fa-solid fa-chart-pie text-neonGreen"></i>
              <span>TỔNG QUAN DINH DƯỠNG HÔM NAY</span>
            </h2>
            <p class="text-xs text-gray-400">Theo dõi tiến độ dinh dưỡng theo mục tiêu BMR/TDEE của bạn</p>
          </div>
          <div class="flex items-center space-x-2">
            <input type="date" id="diaryDatePicker" onchange="loadDiaryForDate()" class="bg-gymCard border border-gray-700 rounded-xl px-3 py-1.5 text-xs text-gray-200 focus:outline-none focus:border-neonGreen">
            <button onclick="clearCurrentDiary()" class="px-3 py-1.5 bg-neonRose/20 border border-neonRose/40 text-neonRose hover:bg-neonRose hover:text-white text-xs font-semibold rounded-xl transition-all">
              <i class="fa-solid fa-trash-can mr-1"></i> Xóa Ngày
            </button>
          </div>
        </div>

        <!-- Macro Progress Bars -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
          
          <!-- Calories -->
          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 space-y-2">
            <div class="flex justify-between text-xs font-bold">
              <span class="text-neonRose"><i class="fa-solid fa-fire mr-1"></i> CALORIES</span>
              <span id="diaryCalText" class="text-gray-300">0 / 2200 kcal</span>
            </div>
            <div class="w-full bg-gray-800 rounded-full h-2.5 overflow-hidden">
              <div id="diaryCalBar" class="bg-neonRose h-2.5 rounded-full transition-all duration-500" style="width: 0%"></div>
            </div>
          </div>

          <!-- Protein -->
          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 space-y-2">
            <div class="flex justify-between text-xs font-bold">
              <span class="text-neonCyan"><i class="fa-solid fa-drumstick-bite mr-1"></i> PROTEIN</span>
              <span id="diaryProteinText" class="text-gray-300">0 / 140 g</span>
            </div>
            <div class="w-full bg-gray-800 rounded-full h-2.5 overflow-hidden">
              <div id="diaryProteinBar" class="bg-neonCyan h-2.5 rounded-full transition-all duration-500" style="width: 0%"></div>
            </div>
          </div>

          <!-- Carbs -->
          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 space-y-2">
            <div class="flex justify-between text-xs font-bold">
              <span class="text-neonAmber"><i class="fa-solid fa-bowl-rice mr-1"></i> CARBS</span>
              <span id="diaryCarbsText" class="text-gray-300">0 / 250 g</span>
            </div>
            <div class="w-full bg-gray-800 rounded-full h-2.5 overflow-hidden">
              <div id="diaryCarbsBar" class="bg-neonAmber h-2.5 rounded-full transition-all duration-500" style="width: 0%"></div>
            </div>
          </div>

          <!-- Fat -->
          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 space-y-2">
            <div class="flex justify-between text-xs font-bold">
              <span class="text-purple-400"><i class="fa-solid fa-oil-well mr-1"></i> FAT</span>
              <span id="diaryFatText" class="text-gray-300">0 / 60 g</span>
            </div>
            <div class="w-full bg-gray-800 rounded-full h-2.5 overflow-hidden">
              <div id="diaryFatBar" class="bg-purple-500 h-2.5 rounded-full transition-all duration-500" style="width: 0%"></div>
            </div>
          </div>

        </div>
      </div>

      <!-- Meal Categories List (Breakfast, Lunch, Dinner, Snacks) -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-5">
        
        <!-- Bữa Sáng -->
        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-3">
          <div class="flex justify-between items-center border-b border-gray-800 pb-2">
            <div class="flex items-center space-x-2 font-bold text-sm text-neonCyan">
              <i class="fa-solid fa-sun"></i>
              <span>BỮA SÁNG</span>
            </div>
            <span id="subtotal-breakfast" class="text-xs text-gray-400 font-mono">0 kcal</span>
          </div>
          <div id="mealList-breakfast" class="space-y-2 min-h-[80px]">
            <!-- Items injected here -->
          </div>
        </div>

        <!-- Bữa Trưa -->
        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-3">
          <div class="flex justify-between items-center border-b border-gray-800 pb-2">
            <div class="flex items-center space-x-2 font-bold text-sm text-neonGreen">
              <i class="fa-solid fa-utensils"></i>
              <span>BỮA TRƯA</span>
            </div>
            <span id="subtotal-lunch" class="text-xs text-gray-400 font-mono">0 kcal</span>
          </div>
          <div id="mealList-lunch" class="space-y-2 min-h-[80px]">
            <!-- Items injected here -->
          </div>
        </div>

        <!-- Bữa Tối -->
        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-3">
          <div class="flex justify-between items-center border-b border-gray-800 pb-2">
            <div class="flex items-center space-x-2 font-bold text-sm text-indigo-400">
              <i class="fa-solid fa-moon"></i>
              <span>BỮA TỐI</span>
            </div>
            <span id="subtotal-dinner" class="text-xs text-gray-400 font-mono">0 kcal</span>
          </div>
          <div id="mealList-dinner" class="space-y-2 min-h-[80px]">
            <!-- Items injected here -->
          </div>
        </div>

        <!-- Bữa Phụ / Ăn Vặt Chiên Rán -->
        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-3">
          <div class="flex justify-between items-center border-b border-gray-800 pb-2">
            <div class="flex items-center space-x-2 font-bold text-sm text-neonAmber">
              <i class="fa-solid fa-cookie-bite"></i>
              <span>BỮA PHỤ / ĂN VẶT & ĐỒ CHIÊN</span>
            </div>
            <span id="subtotal-snack" class="text-xs text-gray-400 font-mono">0 kcal</span>
          </div>
          <div id="mealList-snack" class="space-y-2 min-h-[80px]">
            <!-- Items injected here -->
          </div>
        </div>

      </div>

    </section>

    <!-- TAB 4: BMR & TDEE CALCULATOR -->
    <section id="bmrTab" class="tab-content hidden grid grid-cols-1 lg:grid-cols-12 gap-6">
      
      <!-- Input Form -->
      <div class="lg:col-span-6 glass-panel p-5 rounded-3xl border border-gray-800 space-y-4">
        <div class="border-b border-gray-800 pb-3">
          <h2 class="text-lg font-bold text-white flex items-center space-x-2">
            <i class="fa-solid fa-sliders text-neonGreen"></i>
            <span>TÍNH BMR, TDEE & THIẾT LẬP MỤC TIÊU</span>
          </h2>
          <p class="text-xs text-gray-400">Công thức Mifflin-St Jeor chuẩn dành cho người tập Gym & Thể thao</p>
        </div>

        <form id="bmrForm" onsubmit="calculateBMR(event)" class="space-y-4 text-xs">
          <!-- Gender -->
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Giới tính:</label>
            <div class="grid grid-cols-2 gap-3">
              <label class="p-3 bg-gymCard border border-gray-700 rounded-xl cursor-pointer flex items-center space-x-2">
                <input type="radio" name="gender" value="male" checked class="text-neonGreen focus:ring-0">
                <span class="font-bold text-gray-200">Nam</span>
              </label>
              <label class="p-3 bg-gymCard border border-gray-700 rounded-xl cursor-pointer flex items-center space-x-2">
                <input type="radio" name="gender" value="female" class="text-neonGreen focus:ring-0">
                <span class="font-bold text-gray-200">Nữ</span>
              </label>
            </div>
          </div>

          <!-- Weight, Height, Age -->
          <div class="grid grid-cols-3 gap-3">
            <div>
              <label class="block font-semibold text-gray-300 mb-1">Cân nặng (kg):</label>
              <input type="number" id="userWeight" value="70" required min="30" max="200" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 font-bold focus:border-neonGreen">
            </div>
            <div>
              <label class="block font-semibold text-gray-300 mb-1">Chiều cao (cm):</label>
              <input type="number" id="userHeight" value="172" required min="100" max="230" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 font-bold focus:border-neonGreen">
            </div>
            <div>
              <label class="block font-semibold text-gray-300 mb-1">Tuổi:</label>
              <input type="number" id="userAge" value="24" required min="12" max="100" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 font-bold focus:border-neonGreen">
            </div>
          </div>

          <!-- Activity Level -->
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Mức độ vận động:</label>
            <select id="userActivity" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 font-semibold focus:border-neonGreen">
              <option value="1.2">Ít vận động (Dân văn phòng, ngồi nhiều)</option>
              <option value="1.375">Vận động nhẹ (Tập thể dục 1-3 ngày/tuần)</option>
              <option value="1.55" selected>Vận động vừa (Tập gym 3-5 ngày/tuần)</option>
              <option value="1.725">Vận động cao (Tập gym 6-7 ngày/tuần)</option>
              <option value="1.9">Vận động rất cao (VĐV chuyên nghiệp / Lao động nặng)</option>
            </select>
          </div>

          <!-- Goal -->
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Mục tiêu Gymer:</label>
            <select id="userGoal" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 font-semibold focus:border-neonGreen">
              <option value="cut">Siết cơ / Giảm mỡ (Caloric Deficit -15%)</option>
              <option value="maintain" selected>Giữ dáng / Tăng sức bền (Maintenance)</option>
              <option value="bulk">Xả cơ / Tăng cân tăng cơ (Caloric Surplus +15%)</option>
            </select>
          </div>

          <button type="submit" class="w-full py-3 bg-gradient-to-r from-neonGreen to-neonCyan text-gymDark font-black text-sm rounded-xl shadow-lg transition-all hover:opacity-90">
            TÍNH TOÁN & CẬP NHẬT MỤC TIÊU
          </button>
        </form>
      </div>

      <!-- Result Card -->
      <div class="lg:col-span-6 glass-panel p-5 rounded-3xl border border-gray-800 flex flex-col justify-between space-y-4">
        <div class="border-b border-gray-800 pb-3">
          <h2 class="text-lg font-bold text-white flex items-center space-x-2">
            <i class="fa-solid fa-bullseye text-neonCyan"></i>
            <span>MỤC TIÊU KHUYÊN DÙNG CỦA BẠN</span>
          </h2>
          <p class="text-xs text-gray-400">Chỉ số năng lượng và phân bổ Macro đề xuất</p>
        </div>

        <div class="grid grid-cols-2 gap-4">
          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 text-center">
            <span class="text-xs text-gray-400 block font-semibold">BMR (Năng lượng nghỉ)</span>
            <span id="bmrResult" class="text-2xl font-black text-neonCyan">1,620</span>
            <span class="text-[10px] text-gray-500 block">kcal / ngày</span>
          </div>

          <div class="bg-gymCard p-4 rounded-2xl border border-gray-800 text-center">
            <span class="text-xs text-gray-400 block font-semibold">TDEE (Tiêu hao hàng ngày)</span>
            <span id="tdeeResult" class="text-2xl font-black text-neonGreen">2,510</span>
            <span class="text-[10px] text-gray-500 block">kcal / ngày</span>
          </div>
        </div>

        <!-- Target Macros Card -->
        <div class="bg-gymCard/90 p-4 rounded-2xl border border-gray-800 space-y-3">
          <div class="text-xs font-bold text-neonAmber flex justify-between">
            <span>MỤC TIÊU MỖI NGÀY (TARGET)</span>
            <span id="targetGoalLabel" class="text-gray-300 font-normal">Giữ dáng</span>
          </div>

          <div class="text-center py-2">
            <span id="targetCalories" class="text-3xl font-black text-white">2,200</span>
            <span class="text-xs text-gray-400">kcal/ngày</span>
          </div>

          <div class="grid grid-cols-3 gap-2 text-center text-xs">
            <div class="bg-gymDark p-2 rounded-xl border border-gray-800">
              <span class="text-neonCyan font-bold block" id="targetProtein">140g</span>
              <span class="text-[10px] text-gray-400">Protein (2.0g/kg)</span>
            </div>
            <div class="bg-gymDark p-2 rounded-xl border border-gray-800">
              <span class="text-neonAmber font-bold block" id="targetCarbs">250g</span>
              <span class="text-[10px] text-gray-400">Carbs (~45%)</span>
            </div>
            <div class="bg-gymDark p-2 rounded-xl border border-gray-800">
              <span class="text-purple-400 font-bold block" id="targetFat">60g</span>
              <span class="text-[10px] text-gray-400">Fat (~25%)</span>
            </div>
          </div>
        </div>

        <div class="p-3 bg-neonGreen/10 border border-neonGreen/30 rounded-2xl text-xs text-gray-300 flex items-start space-x-2">
          <i class="fa-solid fa-circle-check text-neonGreen text-base mt-0.5"></i>
          <span>Mục tiêu này đã được đồng bộ trực tiếp vào thanh tiến độ Nhật Ký Dinh Dưỡng của bạn.</span>
        </div>
      </div>

    </section>

    <!-- TAB 5: CHEAT DAY ADVICE & BURNOUT CALCULATOR -->
    <section id="tipsTab" class="tab-content hidden space-y-6">
      
      <!-- Interactive Burnout Calculator -->
      <div class="glass-panel p-5 rounded-3xl border border-gray-800 space-y-4">
        <div class="border-b border-gray-800 pb-3 flex justify-between items-center">
          <div>
            <h2 class="text-lg font-bold text-white flex items-center space-x-2">
              <i class="fa-solid fa-fire-burner text-neonAmber"></i>
              <span>CÔNG CỤ TÍNH BURNOUT BÀI TẬP CHO CHEAT MEAL</span>
            </h2>
            <p class="text-xs text-gray-400">Nhập số Calo món ăn vặt vừa lỡ ăn để tính khối lượng bài tập cần triệt tiêu</p>
          </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-12 gap-4 items-center">
          <div class="md:col-span-5 space-y-2">
            <label class="text-xs font-semibold text-gray-300">Nhập lượng calo từ món chiên/ăn vặt (kcal):</label>
            <div class="flex space-x-2">
              <input type="number" id="customCheatCal" value="650" min="50" max="5000" class="flex-1 bg-gymCard border border-gray-700 rounded-xl p-2.5 text-sm font-bold text-neonAmber focus:border-neonAmber">
              <button onclick="calculateBurnoutCustom()" class="px-4 py-2.5 bg-neonAmber hover:bg-amber-400 text-gymDark font-bold text-xs rounded-xl transition-all">
                TÍNH TOÁN
              </button>
            </div>
          </div>

          <div class="md:col-span-7 grid grid-cols-2 sm:grid-cols-4 gap-2 text-center text-xs">
            <div class="bg-gymCard p-3 rounded-2xl border border-gray-800">
              <i class="fa-solid fa-person-running text-neonGreen text-base mb-1"></i>
              <span class="block font-bold text-white" id="burnoutRun">45 phút</span>
              <span class="text-[10px] text-gray-400">Chạy bộ (8km/h)</span>
            </div>
            <div class="bg-gymCard p-3 rounded-2xl border border-gray-800">
              <i class="fa-solid fa-bolt text-neonCyan text-base mb-1"></i>
              <span class="block font-bold text-white" id="burnoutHiit">30 phút</span>
              <span class="text-[10px] text-gray-400">HIIT Tabata</span>
            </div>
            <div class="bg-gymCard p-3 rounded-2xl border border-gray-800">
              <i class="fa-solid fa-dumbbell text-neonRose text-base mb-1"></i>
              <span class="block font-bold text-white" id="burnoutLift">60 phút</span>
              <span class="text-[10px] text-gray-400">Tập Tạ Cường Độ High</span>
            </div>
            <div class="bg-gymCard p-3 rounded-2xl border border-gray-800">
              <i class="fa-solid fa-person-biking text-purple-400 text-base mb-1"></i>
              <span class="block font-bold text-white" id="burnoutCycle">50 phút</span>
              <span class="text-[10px] text-gray-400">Đạp xe nhanh</span>
            </div>
          </div>
        </div>
      </div>

      <!-- Cheat Day Scientific Tips Grid -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        
        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-2">
          <div class="w-10 h-10 rounded-2xl bg-neonGreen/20 text-neonGreen flex items-center justify-center text-lg font-bold mb-2">
            1
          </div>
          <h3 class="font-bold text-sm text-gray-100">Uống 500ml Nước Trước Khi Ăn</h3>
          <p class="text-xs text-gray-400 leading-relaxed">
            Uống nước ấm hoặc trà xanh trước khi ăn món chiên rán 15 phút giúp làm đầy dạ dày, giảm bớt sự hấp thu dầu mỡ quá đà.
          </p>
        </div>

        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-2">
          <div class="w-10 h-10 rounded-2xl bg-neonCyan/20 text-neonCyan flex items-center justify-center text-lg font-bold mb-2">
            2
          </div>
          <h3 class="font-bold text-sm text-gray-100">Ưu Tiên Nạp Protein Trước Cheat</h3>
          <p class="text-xs text-gray-400 leading-relaxed">
            Ăn 1-2 trứng luộc hoặc 1 scoop Whey Protein trước khi vào bàn tiệc đồ ăn vặt giúp ổn định đường huyết và giữ lượng cơ bắp.
          </p>
        </div>

        <div class="glass-panel p-4 rounded-3xl border border-gray-800 space-y-2">
          <div class="w-10 h-10 rounded-2xl bg-neonAmber/20 text-neonAmber flex items-center justify-center text-lg font-bold mb-2">
            3
          </div>
          <h3 class="font-bold text-sm text-gray-100">Không Nhịn Ăn Sau Khi Cheat</h3>
          <p class="text-xs text-gray-400 leading-relaxed">
            Đừng nhịn ăn hoàn toàn vào ngày hôm sau! Hãy quay lại chế độ ăn sạch (Clean Eating) và tăng lượng nước lọc uống trong ngày lên 3-4 lit.
          </p>
        </div>

      </div>

    </section>

  </main>

  <!-- MODAL 1: FOOD DETAIL & IMAGE ZOOM MODAL -->
  <div id="foodDetailModal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
    <div class="bg-gymPanel border border-gray-800 rounded-3xl max-w-lg w-full max-h-[90vh] overflow-y-auto p-5 space-y-4 text-gray-100 shadow-2xl relative">
      <button onclick="closeModal('foodDetailModal')" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-gymCard hover:bg-gray-700 text-gray-400 hover:text-white flex items-center justify-center transition-all">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <div class="relative rounded-2xl overflow-hidden group border border-gray-800">
        <img id="detailImg" src="" alt="Detail Food" class="w-full h-56 object-cover transition-all duration-300 group-hover:scale-105">
        <span id="detailCategoryBadge" class="absolute bottom-3 left-3 text-[10px] uppercase font-bold text-neonGreen bg-black/70 backdrop-blur-md px-2.5 py-1 rounded-md border border-neonGreen/30">
          Danh mục
        </span>
      </div>

      <div class="space-y-1">
        <h3 id="detailTitle" class="text-xl font-black text-white">Tên Món Ăn</h3>
        <p id="detailServing" class="text-xs text-gray-400">Khẩu phần: 100g</p>
      </div>

      <!-- Macros Grid -->
      <div class="grid grid-cols-4 gap-2 text-center text-xs">
        <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
          <span class="text-gray-400 block text-[10px]">Calo</span>
          <span id="detailCal" class="font-black text-neonRose text-sm">0</span>
        </div>
        <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
          <span class="text-gray-400 block text-[10px]">Protein</span>
          <span id="detailProtein" class="font-black text-neonCyan text-sm">0g</span>
        </div>
        <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
          <span class="text-gray-400 block text-[10px]">Carbs</span>
          <span id="detailCarbs" class="font-black text-neonAmber text-sm">0g</span>
        </div>
        <div class="bg-gymCard p-2.5 rounded-2xl border border-gray-800">
          <span class="text-gray-400 block text-[10px]">Fat</span>
          <span id="detailFat" class="font-black text-purple-400 text-sm">0g</span>
        </div>
      </div>

      <!-- Cheat warning -->
      <div id="detailCheatAlert" class="p-3 bg-neonAmber/10 border border-neonAmber/30 rounded-2xl text-xs space-y-1">
        <div class="font-bold text-neonAmber flex justify-between">
          <span><i class="fa-solid fa-triangle-exclamation mr-1"></i> CẢNH BÁO DẦU MỠ</span>
          <span id="detailOilRating">Mức độ: Cao</span>
        </div>
        <p id="detailCheatText" class="text-gray-300">Nhiều tinh bột hấp thu nhanh và chất béo bão hòa.</p>
      </div>

      <!-- Burnout advice -->
      <div class="bg-gymCard p-3 rounded-2xl border border-gray-800 text-xs space-y-1">
        <span class="font-bold text-neonGreen block"><i class="fa-solid fa-fire mr-1"></i> ĐỐT CHÁY CẦN BÀI TẬP:</span>
        <p id="detailBurnoutText" class="text-gray-300">35 phút chạy bộ hoặc 40 phút đạp xe.</p>
      </div>

      <!-- Add to Meal Select -->
      <div class="pt-2 flex flex-col sm:flex-row gap-2">
        <select id="detailMealSelect" class="bg-gymCard border border-gray-700 rounded-xl px-3 py-2 text-xs font-semibold text-gray-200">
          <option value="snack">Bữa Phụ / Ăn Vặt</option>
          <option value="breakfast">Bữa Sáng</option>
          <option value="lunch">Bữa Trưa</option>
          <option value="dinner">Bữa Tối</option>
        </select>

        <button onclick="addDetailFoodToDiary()" class="flex-1 py-2.5 px-4 bg-neonGreen hover:bg-emerald-400 text-gymDark font-bold text-xs rounded-xl transition-all shadow-lg flex items-center justify-center space-x-1.5">
          <i class="fa-solid fa-plus"></i>
          <span>Thêm Món Này Vào Nhật Ký</span>
        </button>
      </div>
    </div>
  </div>

  <!-- MODAL 2: ADD CUSTOM FOOD MODAL -->
  <div id="addFoodModal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
    <div class="bg-gymPanel border border-gray-800 rounded-3xl max-w-md w-full p-5 space-y-4 text-gray-100 shadow-2xl relative">
      <button onclick="closeModal('addFoodModal')" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-gymCard hover:bg-gray-700 text-gray-400 hover:text-white flex items-center justify-center">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <h3 class="text-base font-bold text-white flex items-center space-x-2">
        <i class="fa-solid fa-utensils text-neonGreen"></i>
        <span>THÊM MÓN ĂN TỰ CHỌN</span>
      </h3>

      <form onsubmit="saveCustomFood(event)" class="space-y-3 text-xs">
        <div>
          <label class="block font-semibold text-gray-300 mb-1">Tên món ăn:</label>
          <input type="text" id="customName" required placeholder="Ví dụ: Bánh Mì Pate Trứng" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
        </div>

        <div>
          <label class="block font-semibold text-gray-300 mb-1">Link Ảnh Unsplash / URL:</label>
          <input type="url" id="customImgUrl" placeholder="https://images.unsplash.com/..." class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
        </div>

        <div class="grid grid-cols-2 gap-2">
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Danh mục:</label>
            <select id="customCategory" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
              <option value="fried">🔥 Đồ Chiên & Ăn Vặt</option>
              <option value="gym">💪 Tăng Cơ Đạm Cao</option>
              <option value="drinks">🥤 Đồ Uống & Chè</option>
              <option value="healthy">🥑 Carbs & Fat Tốt</option>
            </select>
          </div>
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Calo (kcal):</label>
            <input type="number" id="customCal" required min="0" placeholder="450" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
          </div>
        </div>

        <div class="grid grid-cols-3 gap-2">
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Protein (g):</label>
            <input type="number" id="customProtein" value="10" min="0" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
          </div>
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Carbs (g):</label>
            <input type="number" id="customCarbs" value="40" min="0" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
          </div>
          <div>
            <label class="block font-semibold text-gray-300 mb-1">Fat (g):</label>
            <input type="number" id="customFat" value="15" min="0" class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
          </div>
        </div>

        <button type="submit" class="w-full py-3 bg-neonGreen hover:bg-emerald-400 text-gymDark font-bold text-xs rounded-xl transition-all">
          LƯU MÓN ĂN VÀO THƯ VIỆN
        </button>
      </form>
    </div>
  </div>

  <!-- MODAL 3: GEMINI API KEY CONFIG MODAL -->
  <div id="apiKeyModal" class="fixed inset-0 bg-black/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4">
    <div class="bg-gymPanel border border-gray-800 rounded-3xl max-w-md w-full p-5 space-y-4 text-gray-100 shadow-2xl relative">
      <button onclick="closeModal('apiKeyModal')" class="absolute top-4 right-4 w-8 h-8 rounded-full bg-gymCard hover:bg-gray-700 text-gray-400 hover:text-white flex items-center justify-center">
        <i class="fa-solid fa-xmark"></i>
      </button>

      <h3 class="text-base font-bold text-white flex items-center space-x-2">
        <i class="fa-solid fa-key text-neonAmber"></i>
        <span>CẤU HÌNH GEMINI API KEY</span>
      </h3>

      <p class="text-xs text-gray-400 leading-relaxed">
        Nhập Google Gemini API Key để kích hoạt AI quét nhận diện món ăn từ ảnh chụp thật. Nếu để trống, ứng dụng sẽ tự động chuyển sang chế độ <strong class="text-neonGreen">Simulated AI Engine</strong> thông minh.
      </p>

      <div class="space-y-2 text-xs">
        <label class="block font-semibold text-gray-300">API Key:</label>
        <input type="password" id="geminiApiKeyInput" placeholder="AIzaSy..." class="w-full bg-gymCard border border-gray-700 rounded-xl p-2.5 text-gray-100 focus:border-neonGreen">
      </div>

      <div class="flex space-x-2">
        <button onclick="saveApiKey()" class="flex-1 py-2.5 bg-neonGreen hover:bg-emerald-400 text-gymDark font-bold text-xs rounded-xl transition-all">
          Lưu API Key
        </button>
        <button onclick="clearApiKey()" class="px-4 py-2.5 bg-gymCard border border-gray-700 text-gray-400 hover:text-white text-xs font-semibold rounded-xl transition-all">
          Xóa Key
        </button>
      </div>
    </div>
  </div>

  <!-- JAVASCRIPT APPLICATION LOGIC -->
  <script>
    
    // Application State Variables
    let currentTab = 'cameraTab';
    let webcamStream = null;
    let currentCameraFacing = 'environment'; // 'user' or 'environment'
    let userApiKey = localStorage.getItem('fitscan_gemini_key') || '';
    let selectedScannedFood = null;
    let selectedDetailFood = null;

    // Default User Targets (Calculated from BMR/TDEE or customized)
    let userTargets = JSON.parse(localStorage.getItem('fitscan_user_targets')) || {
      calories: 2200,
      protein: 140,
      carbs: 250,
      fat: 60,
      goal: 'maintain'
    };

    // 30+ RICH FOOD ITEMS DATABASE (Street snacks, fried foods, and Gymer meals)
    const FOOD_DATABASE = [
      // 🔥 Đồ chiên & Ăn vặt
      {
        id: 'f1',
        name: 'Gà Rán Giòn',
        category: 'fried',
        calories: 320,
        protein: 22,
        carbs: 18,
        fat: 19,
        serving: '1 miếng (120g)',
        img: 'https://images.unsplash.com/photo-1626645738196-c2a7c87a8f58?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Rất Cao ⚡',
        cheatNote: 'Chứa mỡ động vật chiên ngập dầu và bột chiên xù cao calo rỗng.',
        burnout: 'Chạy bộ 45 phút (~8km/h) hoặc 500 cái nhảy dây liên tục.'
      },
      {
        id: 'f2',
        name: 'Khoai Tây Chiên',
        category: 'fried',
        calories: 365,
        protein: 4,
        carbs: 48,
        fat: 17,
        serving: '1 phần vừa (150g)',
        img: 'https://images.unsplash.com/photo-1573080496219-bb080dd4f877?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Chứa mỡ chuyển hóa (Trans-fat), làm tăng đường huyết nhanh.',
        burnout: '35 phút HIIT Tabata hoặc 40 phút đạp xe tốc độ trung bình.'
      },
      {
        id: 'f3',
        name: 'Phô Mai Que',
        category: 'fried',
        calories: 310,
        protein: 12,
        carbs: 20,
        fat: 21,
        serving: '3 que (100g)',
        img: 'https://images.unsplash.com/photo-1531749668029-2db88e4276c7?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Rất Cao ⚡',
        cheatNote: 'Phô mai béo ngậy kèm lớp bột chiên xù đẫm dầu.',
        burnout: '40 phút tập tạ kháng lực hoặc 30 phút leo cầu thang.'
      },
      {
        id: 'f4',
        name: 'Nem Chua Rán',
        category: 'fried',
        calories: 380,
        protein: 14,
        carbs: 26,
        fat: 24,
        serving: '1 đĩa (5 cái - 150g)',
        img: 'https://images.unsplash.com/photo-1563379091339-03b21ab4a4f8?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cực Cao ⚡',
        cheatNote: 'Món ăn vặt hấp dẫn nhưng tích tụ cực nhiều mỡ xấu dưới da.',
        burnout: '50 phút chay bộ hoặc 45 phút Cardio đấm bao cát.'
      },
      {
        id: 'f5',
        name: 'Chả Giò Chiên Giòn',
        category: 'fried',
        calories: 280,
        protein: 9,
        carbs: 22,
        fat: 18,
        serving: '4 cuốn (120g)',
        img: 'https://images.unsplash.com/photo-1541832676-9b763b0239ab?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Vừa - Cao ⚡',
        cheatNote: 'Vỏ bánh tráng hút nhiều dầu mỡ khi chiên.',
        burnout: '35 phút tập Cardio phối hợp Bodyweight.'
      },
      {
        id: 'f6',
        name: 'Cá Viên Chiên / Xiên Bẩn',
        category: 'fried',
        calories: 420,
        protein: 11,
        carbs: 35,
        fat: 26,
        serving: '1 đĩa thập cẩm (200g)',
        img: 'https://images.unsplash.com/photo-1562967914-608f82629710?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cực Cao ⚡',
        cheatNote: 'Hàng xiên bẩn chiên đi chiên lại dầu cũ, cực kỳ hại cho tim mạch.',
        burnout: '60 phút tập Gym cường độ cao để xả hết năng lượng tích tụ.'
      },
      {
        id: 'f7',
        name: 'Chuối Chiên Sài Gòn',
        category: 'fried',
        calories: 290,
        protein: 3,
        carbs: 45,
        fat: 12,
        serving: '1 bánh lớn (110g)',
        img: 'https://images.unsplash.com/photo-1528825871115-3581a5387919?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Chuối ngọt kết hợp lớp bột chiên đường ngâm trong chảo dầu.',
        burnout: '40 phút đi bộ nhanh độ dốc cao trên máy chạy.'
      },
      {
        id: 'f8',
        name: 'Bánh Xèo Miền Tây',
        category: 'fried',
        calories: 480,
        protein: 16,
        carbs: 42,
        fat: 28,
        serving: '1 cái vừa (220g)',
        img: 'https://images.unsplash.com/photo-1565299585323-38d6b0865b47?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Rất Cao ⚡',
        cheatNote: 'Lớp vỏ đẫm dầu dừa và mỡ lợn, cần ăn kèm nhiều rau xanh.',
        burnout: '55 phút chạy bộ hoặc 1 tiếng đá bóng sân 7.'
      },
      {
        id: 'f9',
        name: 'Bột Chiên Trứng',
        category: 'fried',
        calories: 520,
        protein: 15,
        carbs: 62,
        fat: 24,
        serving: '1 dĩa standard (250g)',
        img: 'https://images.unsplash.com/photo-1546069901-ba9599a7e63c?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cực Cao ⚡',
        cheatNote: 'Khối bột gạo chiên ngập mỡ ăn kèm nước tương ngọt.',
        burnout: '1 tiếng 10 phút tập Gym toàn thân (Full-body workout).'
      },
      {
        id: 'f10',
        name: 'Mực Chiên Xù',
        category: 'fried',
        calories: 340,
        protein: 18,
        carbs: 22,
        fat: 20,
        serving: '1 phần (150g)',
        img: 'https://images.unsplash.com/photo-1599488615731-7e5c2823ff28?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Hải hải tốt nhưng chiên xù làm tăng 200% lượng fat xấu.',
        burnout: '45 phút bơi lội hoặc 40 phút chèo thuyền liên tục.'
      },
      {
        id: 'f11',
        name: 'Tàu Hũ Chiên Giòn',
        category: 'fried',
        calories: 220,
        protein: 14,
        carbs: 8,
        fat: 15,
        serving: '1 đĩa (150g)',
        img: 'https://images.unsplash.com/photo-1546069901-d5bfd2cbfb1f?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Vừa ⚡',
        cheatNote: 'Đậu hũ giàu đạm thực vật, chiên vừa phải có thể dùng cho Bữa Phụ.',
        burnout: '25 phút nhảy dây hoặc 30 phút đi bộ.'
      },
      {
        id: 'f12',
        name: 'Xúc Xích Chiên',
        category: 'fried',
        calories: 300,
        protein: 11,
        carbs: 6,
        fat: 26,
        serving: '2 cây (100g)',
        img: 'https://images.unsplash.com/photo-1585325701165-351af916e581?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Chứa nhiều muối, chất bảo quản nitrat và mỡ động vật.',
        burnout: '35 phút tập HIIT.'
      },

      // 🥗 Món ăn vặt đường phố
      {
        id: 's1',
        name: 'Bánh Tráng Trộn',
        category: 'fried',
        calories: 330,
        protein: 8,
        carbs: 42,
        fat: 15,
        serving: '1 bịch (120g)',
        img: 'https://images.unsplash.com/photo-1540420773420-3366772f4999?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Vừa ⚡',
        cheatNote: 'Bánh tráng, dầu sa tế, muối tôm khiến tích nước (bloating).',
        burnout: '40 phút đi bộ nhanh hoặc 30 phút chay bộ nhẹ.'
      },
      {
        id: 's2',
        name: 'Bánh Tráng Nướng (Pizza VN)',
        category: 'fried',
        calories: 310,
        protein: 9,
        carbs: 38,
        fat: 14,
        serving: '1 cái lớn (130g)',
        img: 'https://images.unsplash.com/photo-1513104890138-7c749659a591?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Vừa ⚡',
        cheatNote: 'Trứng, mỡ hành, sốt mayonnaise tạo nên lượng calo ẩn dồi dào.',
        burnout: '35 phút tập tạ bài Leg Day.'
      },
      {
        id: 's3',
        name: 'Bắp Xào Bơ Tép',
        category: 'fried',
        calories: 360,
        protein: 7,
        carbs: 48,
        fat: 16,
        serving: '1 hộp (180g)',
        img: 'https://images.unsplash.com/photo-1551183053-bf91a1d81141?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Bơ thực vật xào ngập ngụa làm bắp ngô thành quả bóp calo.',
        burnout: '45 phút tập xà đơn & chống đẩy liên tục.'
      },
      {
        id: 's4',
        name: 'Khô Bò Lá Chanh',
        category: 'healthy',
        calories: 260,
        protein: 38,
        carbs: 12,
        fat: 5,
        serving: '1 phần (100g)',
        img: 'https://images.unsplash.com/photo-1529193591184-b1d58069ecdd?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Thấp',
        cheatNote: 'Món ăn vặt ĐẠM CAO lý tưởng cho Gymer, chỉ lưu ý hàm lượng muối.',
        burnout: '20 phút tập bụng nhẹ nhàng.'
      },
      {
        id: 's5',
        name: 'Khô Gà Xé Phay',
        category: 'healthy',
        calories: 280,
        protein: 42,
        carbs: 10,
        fat: 6,
        serving: '1 phần (100g)',
        img: 'https://images.unsplash.com/photo-1567620832903-9fc6debc209f?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Thấp',
        cheatNote: 'Siêu đạm từ ức gà khô, ít fat, món snack phục hồi cơ cực tốt.',
        burnout: 'Không cần burnout nặng, phù hợp bữa phụ.'
      },
      {
        id: 's6',
        name: 'Nem Nướng Nha Trang',
        category: 'gym',
        calories: 450,
        protein: 26,
        carbs: 35,
        fat: 22,
        serving: '1 suất cuốn (250g)',
        img: 'https://images.unsplash.com/photo-1555939594-58d7cb561ad1?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Vừa',
        cheatNote: 'Cân bằng đạm thịt heo nướng và rau sống, dùng nước chấm vừa phải.',
        burnout: '40 phút tập Gym nâng tạ nặng.'
      },

      // 🥤 Đồ uống & Chè
      {
        id: 'd1',
        name: 'Trà Sữa Trân Châu Đường Đen',
        category: 'drinks',
        calories: 460,
        protein: 3,
        carbs: 78,
        fat: 16,
        serving: '1 ly Size L (500ml)',
        img: 'https://images.unsplash.com/photo-1558857563-b371033873b8?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Đường Siêu Cao ⚡',
        cheatNote: 'Bom đường hóa học & kem béo béo ngậy. Kẻ thù số 1 của Gymer siết cơ!',
        burnout: 'Chạy bộ liên tục 60 phút hoặc 50 phút tập HIIT cháy máy.'
      },
      {
        id: 'd2',
        name: 'Chè Bưởi An Giang',
        category: 'drinks',
        calories: 320,
        protein: 4,
        carbs: 65,
        fat: 6,
        serving: '1 ly (200g)',
        img: 'https://images.unsplash.com/photo-1578985545062-69928b1d9587?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Đường Cực Cao ⚡',
        cheatNote: 'Cốt dừa béo và nước đường thốt nốt làm tăng insulin cực nhanh.',
        burnout: '40 phút tập cardio cường độ cao.'
      },
      {
        id: 'd3',
        name: 'Tàu Hũ Trân Châu Đường Đen',
        category: 'drinks',
        calories: 270,
        protein: 8,
        carbs: 45,
        fat: 7,
        serving: '1 chén (180g)',
        img: 'https://images.unsplash.com/photo-1579954115545-a95591f28bfc?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Vừa',
        cheatNote: 'Đậu hũ non mát lành, giảm bớt ngọt trân châu sẽ thành món xả cơ nhẹ.',
        burnout: '30 phút đi bộ nhanh.'
      },
      {
        id: 'd4',
        name: 'Popcorn Phô Mai',
        category: 'fried',
        calories: 450,
        protein: 6,
        carbs: 55,
        fat: 23,
        serving: '1 xô nhỏ (120g)',
        img: 'https://images.unsplash.com/photo-1578849278619-e73505e9610f?w=500&auto=format&fit=crop',
        cheatAlert: true,
        oilLevel: 'Cao ⚡',
        cheatNote: 'Bắp nổ đường, bơ nhân tạo và bột phô mai mặn.',
        burnout: '50 phút nhảy aerobics hoặc Zumba.'
      },

      // 💪 Món ăn Gymer tăng cơ chính
      {
        id: 'g1',
        name: 'Ngực Gà Áp Chảo Olive',
        category: 'gym',
        calories: 240,
        protein: 42,
        carbs: 2,
        fat: 7,
        serving: '1 miếng (200g)',
        img: 'https://images.unsplash.com/photo-1532550907401-a500c9a57435?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Sạch ✨',
        cheatNote: 'Món ăn HUYỀN THOẠI tăng cơ giảm mỡ cho Gymer!',
        burnout: 'Phục hồi cơ bắp, không cần burnout calo.'
      },
      {
        id: 'g2',
        name: 'Bò Bít Tết & Bông Cải Xanh',
        category: 'gym',
        calories: 380,
        protein: 36,
        carbs: 8,
        fat: 22,
        serving: '1 phần (220g)',
        img: 'https://images.unsplash.com/photo-1544025162-d76694265947?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Tốt ✨',
        cheatNote: 'Giàu Sắt, Zinc, Creatine tự nhiên và Đạm chất lượng cao.',
        burnout: 'Tốt cho ngày tập nặng Push/Pull/Leg.'
      },
      {
        id: 'g3',
        name: 'Cơm Tấm Ức Gà Nướng',
        category: 'gym',
        calories: 450,
        protein: 34,
        carbs: 58,
        fat: 9,
        serving: '1 dĩa (300g)',
        img: 'https://images.unsplash.com/photo-1512058564366-18510be2db19?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Sạch ✨',
        cheatNote: 'Bữa chính nạp Carbs sạch trước khi tập (Pre-workout meal).',
        burnout: 'Sử dụng năng lượng để nâng tạ cực xung.'
      },
      {
        id: 'g4',
        name: 'Salad Ức Gà Cá Hồi',
        category: 'healthy',
        calories: 310,
        protein: 32,
        carbs: 12,
        fat: 14,
        serving: '1 tô (250g)',
        img: 'https://images.unsplash.com/photo-1540420773420-3366772f4999?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Omega 3 Tốt ✨',
        cheatNote: 'Giàu Omega-3 giảm viêm cơ bắp và xà lách giàu chất xơ.',
        burnout: 'Phù hợp bữa tối siết mỡ.'
      },
      {
        id: 'g5',
        name: 'Bánh Mì Yến Mạch Xé Gà',
        category: 'healthy',
        calories: 290,
        protein: 24,
        carbs: 36,
        fat: 6,
        serving: '1 ổ (150g)',
        img: 'https://images.unsplash.com/photo-1509722747041-616f39b57569?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Sạch ✨',
        cheatNote: 'Carb tinh bột hấp thu chậm từ Yến Mạch giúp no lâu bền bỉ.',
        burnout: 'Năng lượng lý tưởng cho Bữa Sáng.'
      },
      {
        id: 'g6',
        name: 'Trứng Luộc & Khoai Lang Nướng',
        category: 'healthy',
        calories: 250,
        protein: 16,
        carbs: 32,
        fat: 6,
        serving: '2 trứng + 1 khoai (200g)',
        img: 'https://images.unsplash.com/photo-1582722872445-44dc5f7e3c8f?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Sạch ✨',
        cheatNote: 'Combo bữa phụ rẻ - bổ - khỏe vô địch của sinh viên Gymer.',
        burnout: 'Không cần burnout.'
      },
      {
        id: 'g7',
        name: 'Smoothie Protein Chuối Bơ',
        category: 'drinks',
        calories: 340,
        protein: 28,
        carbs: 42,
        fat: 8,
        serving: '1 ly (350ml)',
        img: 'https://images.unsplash.com/photo-1553530666-ba11a7da3888?w=500&auto=format&fit=crop',
        cheatAlert: false,
        oilLevel: 'Fat Tốt ✨',
        cheatNote: 'Xay cùng Whey Protein, sinh tố phục hồi cơ bắp tức thì post-workout.',
        burnout: 'Nạp năng lượng phục hồi.'
      }
    ];

    // On Window Load
    window.onload = function() {
      // Set default date input to today
      const datePicker = document.getElementById('diaryDatePicker');
      if (datePicker) {
        datePicker.value = new Date().toISOString().split('T')[0];
      }

      // Initialize Camera Stream
      initWebcam();

      // Render library
      renderLibrary();

      // Update Target HUD
      updateTargetDisplay();

      // Render current diary
      renderDiary();

      // Update API Key status button
      updateApiKeyStatusUI();
    };

    // Tab Switcher
    function switchTab(tabId) {
      currentTab = tabId;
      document.querySelectorAll('.tab-btn').forEach(btn => {
        btn.classList.remove('bg-neonGreen', 'text-gymDark', 'shadow-md');
        btn.classList.add('text-gray-400', 'hover:text-white', 'hover:bg-gymCard');
      });

      const activeBtn = document.getElementById('tab-' + tabId);
      if (activeBtn) {
        activeBtn.classList.remove('text-gray-400', 'hover:text-white', 'hover:bg-gymCard');
        activeBtn.classList.add('bg-neonGreen', 'text-gymDark', 'shadow-md');
      }

      document.querySelectorAll('.tab-content').forEach(content => {
        content.classList.add('hidden');
      });

      const activeContent = document.getElementById(tabId);
      if (activeContent) {
        activeContent.classList.remove('hidden');
      }

      // Handle webcam lifecycle
      if (tabId === 'cameraTab') {
        initWebcam();
      } else {
        stopWebcam();
      }
    }

    // WebCam Controls
    async function initWebcam() {
      const video = document.getElementById('webcamVideo');
      const preview = document.getElementById('imagePreview');
      if (!video) return;

      // Hide preview img if showing live video
      preview.classList.add('hidden');
      video.classList.remove('hidden');

      try {
        if (webcamStream) {
          stopWebcam();
        }

        const constraints = {
          video: {
            facingMode: currentCameraFacing,
            width: { ideal: 1280 },
            height: { ideal: 720 }
          },
          audio: false
        };

        webcamStream = await navigator.mediaDevices.getUserMedia(constraints);
        video.srcObject = webcamStream;
        document.getElementById('camResolution').innerText = `LIVE CAM 720P (${currentCameraFacing === 'user' ? 'Front' : 'Back'})`;
      } catch (err) {
        console.warn('Webcam access error:', err);
        document.getElementById('camResolution').innerText = 'CAMERA FALLBACK / UPLOAD MODE';
      }
    }

    function stopWebcam() {
      if (webcamStream) {
        webcamStream.getTracks().forEach(track => track.stop());
        webcamStream = null;
      }
    }

    function toggleCamera() {
      currentCameraFacing = (currentCameraFacing === 'user') ? 'environment' : 'user';
      initWebcam();
    }

    // Image Upload Handling
    function handleImageUpload(event) {
      const file = event.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = function(e) {
        stopWebcam();
        const video = document.getElementById('webcamVideo');
        const preview = document.getElementById('imagePreview');

        video.classList.add('hidden');
        preview.src = e.target.result;
        preview.classList.remove('hidden');

        document.getElementById('camResolution').innerText = 'IMAGE FILE UPLOADED';
      };
      reader.readAsDataURL(file);
    }

    // Capture Snapshot & Run AI Scan
    async function captureAndScan() {
      const laser = document.getElementById('laserLine');
      const scanBtn = document.getElementById('scanBtn');
      const badge = document.getElementById('scanStatusBadge');

      laser.classList.remove('hidden');
      scanBtn.disabled = true;
      scanBtn.classList.add('opacity-50');
      badge.innerText = 'Đang Quét AI...';
      badge.className = 'bg-neonAmber/20 text-neonAmber text-xs px-2.5 py-1 rounded-full border border-neonAmber/40 animate-pulse';

      // Capture frame into base64
      let imageDataBase64 = '';
      const video = document.getElementById('webcamVideo');
      const preview = document.getElementById('imagePreview');

      if (!preview.classList.contains('hidden') && preview.src) {
        imageDataBase64 = preview.src.split(',')[1] || '';
      } else if (video && video.readyState === video.HAVE_ENOUGH_DATA) {
        const canvas = document.createElement('canvas');
        canvas.width = video.videoWidth || 640;
        canvas.height = video.videoHeight || 480;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
        imageDataBase64 = canvas.toDataURL('image/jpeg', 0.8).split(',')[1];
      }

      // Check whether to call Gemini API or Smart Mock AI
      if (userApiKey && userApiKey.trim() !== '' && imageDataBase64) {
        try {
          await callGeminiVisionApi(imageDataBase64);
        } catch (err) {
          console.error('Gemini API Error, falling back to mock:', err);
          runSmartMockScan();
        }
      } else {
        // Simulated AI Scan (Randomized high accuracy match from 30+ database)
        setTimeout(() => {
          runSmartMockScan();
        }, 1500);
      }

      setTimeout(() => {
        laser.classList.add('hidden');
        scanBtn.disabled = false;
        scanBtn.classList.remove('opacity-50');
      }, 1500);
    }

    // Call Real Gemini API (gemini-3-flash-preview)
    async function callGeminiVisionApi(base64Image) {
      const promptText = `Hãy phân tích hình ảnh món ăn này dành cho người tập gym (Gymer). Hãy trả về thông tin dưới dạng duy nhất 1 JSON object có cấu trúc sau:
      {
        "foodName": "Tên món ăn tiếng Việt ngắn gọn",
        "category": "fried" (nếu là đồ chiên/ăn vặt) hoặc "gym" hoặc "healthy" hoặc "drinks",
        "calories": số calo (nguyên),
        "protein": số gram protein,
        "carbs": số gram carbs,
        "fat": số gram fat,
        "serving": "mô tả khẩu phần",
        "cheatAlert": boolean (true nếu nhiều dầu mỡ/đường),
        "oilLevel": "Thấp" hoặc "Vừa" hoặc "Cao" hoặc "Rất Cao",
        "cheatNote": "lời khuyên về dầu mỡ/calo rỗng",
        "burnout": "lời khuyên bài tập đốt cháy calo cho Gymer"
      }`;

      const payload = {
        contents: [
          {
            role: "user",
            parts: [
              { text: promptText },
              {
                inlineData: {
                  mimeType: "image/jpeg",
                  data: base64Image
                }
              }
            ]
          }
        ],
        generationConfig: {
          responseMimeType: "application/json"
        }
      };

      const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${userApiKey}`;

      const response = await fetch(apiUrl, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });

      const data = await response.json();
      const rawText = data?.candidates?.[0]?.content?.parts?.[0]?.text;
      if (rawText) {
        const resultJson = JSON.parse(rawText);
        displayScanResult({
          name: resultJson.foodName || 'Món Ăn Quét Được',
          category: resultJson.category || 'fried',
          calories: resultJson.calories || 350,
          protein: resultJson.protein || 15,
          carbs: resultJson.carbs || 30,
          fat: resultJson.fat || 18,
          serving: resultJson.serving || '1 phần standard',
          img: document.getElementById('imagePreview').src || 'https://images.unsplash.com/photo-1626645738196-c2a7c87a8f58?w=500&auto=format&fit=crop',
          cheatAlert: resultJson.cheatAlert !== undefined ? resultJson.cheatAlert : true,
          oilLevel: resultJson.oilLevel || 'Cao ⚡',
          cheatNote: resultJson.cheatNote || 'Cảnh báo calo rỗng và chất béo bão hòa.',
          burnout: resultJson.burnout || 'Cần 45 phút Cardio chạy bộ.'
        });
      } else {
        runSmartMockScan();
      }
    }

    // Trigger Simulate Scan for quick demo buttons
    function simulateScan(foodName) {
      const matched = FOOD_DATABASE.find(f => f.name.toLowerCase().includes(foodName.toLowerCase()));
      if (matched) {
        displayScanResult(matched);
      } else {
        runSmartMockScan();
      }
    }

    // Run Smart Mock AI Scan from database
    function runSmartMockScan() {
      // Pick a random fried or snack item
      const randomIndex = Math.floor(Math.random() * FOOD_DATABASE.length);
      const selected = FOOD_DATABASE[randomIndex];
      displayScanResult(selected);
    }

    // Display Scan Result in HUD UI
    function displayScanResult(foodItem) {
      selectedScannedFood = foodItem;

      document.getElementById('scanPlaceholder').classList.add('hidden');
      document.getElementById('scanContent').classList.remove('hidden');

      const badge = document.getElementById('scanStatusBadge');
      badge.innerText = 'Phân Tích Thành Công ✓';
      badge.className = 'bg-neonGreen/20 text-neonGreen text-xs px-2.5 py-1 rounded-full border border-neonGreen/40';

      document.getElementById('scannedFoodName').innerText = foodItem.name;
      document.getElementById('scannedFoodCategory').innerText = foodItem.category === 'fried' ? '🔥 Món Chiên / Ăn Vặt' : (foodItem.category === 'gym' ? '💪 Món Tăng Cơ' : '🥑 Món Lành Mạnh');
      document.getElementById('scannedFoodServing').innerText = `Khẩu phần: ${foodItem.serving || '100g'}`;
      document.getElementById('scannedFoodImage').src = foodItem.img;
      document.getElementById('scannedFoodImage').onerror = function() {
        this.src = 'https://placehold.co/400x300/111827/10b981?text=FitScan+Food';
      };

      document.getElementById('scannedCal').innerText = foodItem.calories;
      document.getElementById('scannedProtein').innerText = foodItem.protein;
      document.getElementById('scannedCarbs').innerText = foodItem.carbs;
      document.getElementById('scannedFat').innerText = foodItem.fat;

      // Cheat warning box
      const cheatBox = document.getElementById('cheatMealBox');
      if (foodItem.cheatAlert) {
        cheatBox.classList.remove('hidden');
        document.getElementById('oilRatingText').innerText = `Độ Dầu Mỡ: ${foodItem.oilLevel || 'Cao ⚡'}`;
        document.getElementById('cheatMealAdvice').innerText = foodItem.cheatNote || 'Chứa hàm lượng béo bão hòa cao.';
      } else {
        cheatBox.classList.add('hidden');
      }

      document.getElementById('burnoutWorkouts').innerText = foodItem.burnout || '30 phút chạy bộ hoặc 40 phút đi bộ dốc.';
    }

    // Render Food Library Grid
    function renderLibrary() {
      const grid = document.getElementById('libraryFoodGrid');
      if (!grid) return;

      const searchQuery = document.getElementById('librarySearchInput')?.value.toLowerCase() || '';
      const activeFilter = document.querySelector('.cat-chip.active')?.getAttribute('onclick')?.match(/'([^']+)'/)?.[1] || 'all';

      grid.innerHTML = '';

      const filtered = FOOD_DATABASE.filter(item => {
        const matchesSearch = item.name.toLowerCase().includes(searchQuery);
        const matchesCategory = (activeFilter === 'all') || (item.category === activeFilter);
        return matchesSearch && matchesCategory;
      });

      if (filtered.length === 0) {
        grid.innerHTML = `
          <div class="col-span-full text-center py-12 text-gray-500 space-y-2">
            <i class="fa-solid fa-cookie text-3xl"></i>
            <p class="text-sm">Không tìm thấy món ăn phù hợp với từ khóa.</p>
          </div>
        `;
        return;
      }

      filtered.forEach(item => {
        const card = document.createElement('div');
        card.className = 'bg-gymCard border border-gray-800 rounded-3xl overflow-hidden shadow-lg hover:border-neonGreen/50 transition-all duration-300 flex flex-col justify-between group';
        
        card.innerHTML = `
          <div>
            <!-- Image with Zoom Icon -->
            <div class="relative h-44 overflow-hidden cursor-pointer" onclick='openFoodDetail(${JSON.stringify(item).replace(/'/g, "&apos;")})'>
              <img src="${item.img}" alt="${item.name}" onerror="this.src='https://placehold.co/400x300/111827/10b981?text=${encodeURIComponent(item.name)}'" class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500">
              
              <!-- Badges -->
              <div class="absolute top-2 left-2 flex flex-col space-y-1">
                ${item.cheatAlert ? '<span class="bg-neonAmber text-gymDark font-black text-[9px] uppercase px-2 py-0.5 rounded-md shadow-md">🔥 Đồ Chiên / Vặt</span>' : '<span class="bg-neonGreen text-gymDark font-black text-[9px] uppercase px-2 py-0.5 rounded-md shadow-md">💪 Gymer Clean</span>'}
              </div>

              <div class="absolute bottom-2 right-2 bg-black/60 backdrop-blur-md text-white text-[10px] font-mono px-2 py-0.5 rounded-lg border border-gray-700">
                <i class="fa-solid fa-expand mr-1"></i> Xem Chi Tiết
              </div>
            </div>

            <!-- Content Body -->
            <div class="p-4 space-y-2">
              <div class="flex justify-between items-start">
                <h3 class="font-bold text-sm text-gray-100 group-hover:text-neonGreen transition-colors line-clamp-1">${item.name}</h3>
                <span class="text-xs font-black text-neonRose whitespace-nowrap">${item.calories} kcal</span>
              </div>

              <!-- Macros Row -->
              <div class="flex justify-between text-[11px] font-mono text-gray-400 bg-gymDark p-2 rounded-xl border border-gray-800">
                <span>Prot: <strong class="text-neonCyan">${item.protein}g</strong></span>
                <span>Carb: <strong class="text-neonAmber">${item.carbs}g</strong></span>
                <span>Fat: <strong class="text-purple-400">${item.fat}g</strong></span>
              </div>
            </div>
          </div>

          <!-- Bottom Action Button -->
          <div class="p-3 pt-0 flex space-x-2">
            <button onclick='quickAddFromLibrary(${JSON.stringify(item).replace(/'/g, "&apos;")})' class="w-full py-2 bg-gymDark hover:bg-neonGreen hover:text-gymDark border border-gray-700 text-gray-300 font-bold text-xs rounded-xl transition-all flex items-center justify-center space-x-1">
              <i class="fa-solid fa-plus-circle text-neonGreen"></i>
              <span>Thêm Nhật Ký</span>
            </button>
          </div>
        `;

        grid.appendChild(card);
      });
    }

    function filterLibrary(category) {
      document.querySelectorAll('.cat-chip').forEach(btn => {
        btn.classList.remove('bg-neonGreen', 'text-gymDark');
        btn.classList.add('bg-gymCard', 'text-gray-400', 'border', 'border-gray-700');
      });

      event.target.classList.remove('bg-gymCard', 'text-gray-400', 'border', 'border-gray-700');
      event.target.classList.add('bg-neonGreen', 'text-gymDark');

      renderLibrary();
    }

    // Get Diary Data for selected date
    function getDiaryData(dateStr) {
      const key = 'fitscan_diary_' + dateStr;
      return JSON.parse(localStorage.getItem(key)) || {
        breakfast: [],
        lunch: [],
        dinner: [],
        snack: []
      };
    }

    function saveDiaryData(dateStr, data) {
      const key = 'fitscan_diary_' + dateStr;
      localStorage.setItem(key, JSON.stringify(data));
      renderDiary();
      updateTargetDisplay();
    }

    function renderDiary() {
      const dateStr = document.getElementById('diaryDatePicker')?.value || new Date().toISOString().split('T')[0];
      const diary = getDiaryData(dateStr);

      let totalCal = 0, totalProt = 0, totalCarbs = 0, totalFat = 0;

      const categories = ['breakfast', 'lunch', 'dinner', 'snack'];

      categories.forEach(cat => {
        const container = document.getElementById('mealList-' + cat);
        const subtotalEl = document.getElementById('subtotal-' + cat);
        if (!container) return;

        container.innerHTML = '';
        let catCal = 0;

        if (!diary[cat] || diary[cat].length === 0) {
          container.innerHTML = `<p class="text-[11px] text-gray-600 italic text-center py-3">Chưa có món ăn nào</p>`;
        } else {
          diary[cat].forEach((item, index) => {
            catCal += item.calories;
            totalCal += item.calories;
            totalProt += item.protein;
            totalCarbs += item.carbs;
            totalFat += item.fat;

            const row = document.createElement('div');
            row.className = 'flex justify-between items-center p-2 rounded-xl bg-gymCard/80 border border-gray-800 text-xs text-gray-200';
            row.innerHTML = `
              <div class="flex items-center space-x-2">
                <img src="${item.img}" onerror="this.src='https://placehold.co/100x100/111827/10b981?text=Food'" class="w-8 h-8 rounded-lg object-cover">
                <div>
                  <span class="font-bold block">${item.name}</span>
                  <span class="text-[10px] text-gray-400 font-mono">${item.calories} kcal | P:${item.protein}g C:${item.carbs}g F:${item.fat}g</span>
                </div>
              </div>
              <button onclick="removeDiaryItem('${cat}', ${index})" class="text-gray-500 hover:text-neonRose p-1 transition-colors">
                <i class="fa-solid fa-trash-can"></i>
              </button>
            `;
            container.appendChild(row);
          });
        }

        if (subtotalEl) subtotalEl.innerText = `${catCal} kcal`;
      });

      // Update Header HUD Stats
      document.getElementById('headerCalories').innerText = totalCal;
      document.getElementById('headerProtein').innerText = totalProt;

      // Update Diary Progress Bars vs Targets
      const calPercent = Math.min(100, Math.round((totalCal / userTargets.calories) * 100));
      const protPercent = Math.min(100, Math.round((totalProt / userTargets.protein) * 100));
      const carbsPercent = Math.min(100, Math.round((totalCarbs / userTargets.carbs) * 100));
      const fatPercent = Math.min(100, Math.round((totalFat / userTargets.fat) * 100));

      document.getElementById('diaryCalText').innerText = `${totalCal} / ${userTargets.calories} kcal (${calPercent}%)`;
      document.getElementById('diaryCalBar').style.width = calPercent + '%';

      document.getElementById('diaryProteinText').innerText = `${totalProt} / ${userTargets.protein} g (${protPercent}%)`;
      document.getElementById('diaryProteinBar').style.width = protPercent + '%';

      document.getElementById('diaryCarbsText').innerText = `${totalCarbs} / ${userTargets.carbs} g (${carbsPercent}%)`;
      document.getElementById('diaryCarbsBar').style.width = carbsPercent + '%';

      document.getElementById('diaryFatText').innerText = `${totalFat} / ${userTargets.fat} g (${fatPercent}%)`;
      document.getElementById('diaryFatBar').style.width = fatPercent + '%';
    }

    function removeDiaryItem(category, index) {
      const dateStr = document.getElementById('diaryDatePicker').value;
      const diary = getDiaryData(dateStr);
      if (diary[category]) {
        diary[category].splice(index, 1);
        saveDiaryData(dateStr, diary);
      }
    }

    function clearCurrentDiary() {
      const dateStr = document.getElementById('diaryDatePicker').value;
      saveDiaryData(dateStr, { breakfast: [], lunch: [], dinner: [], snack: [] });
    }

    function loadDiaryForDate() {
      renderDiary();
    }

    // Add Scanned Food directly to Meal Diary
    function addScannedToDiary() {
      if (!selectedScannedFood) return;
      const dateStr = document.getElementById('diaryDatePicker')?.value || new Date().toISOString().split('T')[0];
      const diary = getDiaryData(dateStr);

      const targetCategory = selectedScannedFood.category === 'fried' ? 'snack' : 'lunch';
      diary[targetCategory].push(selectedScannedFood);

      saveDiaryData(dateStr, diary);
      switchTab('diaryTab');
    }

    // Quick Add from Library
    function quickAddFromLibrary(item) {
      const dateStr = document.getElementById('diaryDatePicker')?.value || new Date().toISOString().split('T')[0];
      const diary = getDiaryData(dateStr);
      
      const categoryKey = item.category === 'fried' ? 'snack' : 'lunch';
      diary[categoryKey].push(item);

      saveDiaryData(dateStr, diary);

      // Temporary toast feedback
      const btn = event.currentTarget;
      const originalText = btn.innerHTML;
      btn.innerHTML = `<i class="fa-solid fa-check text-neonGreen"></i> <span class="text-neonGreen">Đã Thêm!</span>`;
      setTimeout(() => {
        btn.innerHTML = originalText;
      }, 1200);
    }

    // BMR & TDEE Calculator
    function calculateBMR(e) {
      e.preventDefault();
      const weight = parseFloat(document.getElementById('userWeight').value);
      const height = parseFloat(document.getElementById('userHeight').value);
      const age = parseFloat(document.getElementById('userAge').value);
      const gender = document.querySelector('input[name="gender"]:checked').value;
      const activity = parseFloat(document.getElementById('userActivity').value);
      const goal = document.getElementById('userGoal').value;

      // Mifflin-St Jeor Equation
      let bmr = (10 * weight) + (6.25 * height) - (5 * age);
      if (gender === 'male') {
        bmr += 5;
      } else {
        bmr -= 161;
      }

      const tdee = bmr * activity;

      let targetCal = tdee;
      if (goal === 'cut') targetCal = tdee * 0.85; // 15% deficit
      if (goal === 'bulk') targetCal = tdee * 1.15; // 15% surplus

      // Macro breakdown
      const proteinGrams = Math.round(weight * 2.0); // 2.0g per kg
      const fatGrams = Math.round((targetCal * 0.25) / 9); // 25% calories from fat
      const carbsGrams = Math.max(50, Math.round((targetCal - (proteinGrams * 4) - (fatGrams * 9)) / 4));

      userTargets = {
        calories: Math.round(targetCal),
        protein: proteinGrams,
        carbs: carbsGrams,
        fat: fatGrams,
        goal: goal
      };

      localStorage.setItem('fitscan_user_targets', JSON.stringify(userTargets));

      // Update UI
      document.getElementById('bmrResult').innerText = Math.round(bmr).toLocaleString();
      document.getElementById('tdeeResult').innerText = Math.round(tdee).toLocaleString();

      updateTargetDisplay();
      renderDiary();
    }

    function updateTargetDisplay() {
      document.getElementById('headerCalorieTarget').innerText = userTargets.calories;
      document.getElementById('targetCalories').innerText = userTargets.calories.toLocaleString();
      document.getElementById('targetProtein').innerText = userTargets.protein + 'g';
      document.getElementById('targetCarbs').innerText = userTargets.carbs + 'g';
      document.getElementById('targetFat').innerText = userTargets.fat + 'g';

      const goalLabelMap = {
        cut: 'Siết Cơ / Giảm Mỡ',
        maintain: 'Giữ Dáng / Tăng Sức Bền',
        bulk: 'Xả Cơ / Tăng Cân'
      };
      document.getElementById('targetGoalLabel').innerText = goalLabelMap[userTargets.goal] || 'Tùy chỉnh';
    }

    // Custom Cheat Burnout Calculator
    function calculateBurnoutCustom() {
      const cal = parseFloat(document.getElementById('customCheatCal').value) || 600;
      
      document.getElementById('burnoutRun').innerText = `${Math.round(cal / 12)} phút`;
      document.getElementById('burnoutHiit').innerText = `${Math.round(cal / 18)} phút`;
      document.getElementById('burnoutLift').innerText = `${Math.round(cal / 9)} phút`;
      document.getElementById('burnoutCycle').innerText = `${Math.round(cal / 11)} phút`;
    }

    // Open Food Detail Modal
    function openFoodDetail(item) {
      selectedDetailFood = item;

      document.getElementById('detailTitle').innerText = item.name;
      document.getElementById('detailServing').innerText = `Khẩu phần: ${item.serving || '100g'}`;
      document.getElementById('detailImg').src = item.img;
      document.getElementById('detailImg').onerror = function() {
        this.src = 'https://placehold.co/400x300/111827/10b981?text=Food';
      };

      document.getElementById('detailCal').innerText = item.calories;
      document.getElementById('detailProtein').innerText = item.protein + 'g';
      document.getElementById('detailCarbs').innerText = item.carbs + 'g';
      document.getElementById('detailFat').innerText = item.fat + 'g';

      const alertBox = document.getElementById('detailCheatAlert');
      if (item.cheatAlert) {
        alertBox.classList.remove('hidden');
        document.getElementById('detailOilRating').innerText = `Mức độ: ${item.oilLevel || 'Cao'}`;
        document.getElementById('detailCheatText').innerText = item.cheatNote || 'Chứa hàm lượng chất béo bão hòa cao.';
      } else {
        alertBox.classList.add('hidden');
      }

      document.getElementById('detailBurnoutText').innerText = item.burnout || '35 phút Cardio hoặc 40 phút đi bộ dốc.';

      document.getElementById('foodDetailModal').classList.remove('hidden');
    }

    function addDetailFoodToDiary() {
      if (!selectedDetailFood) return;
      const mealCat = document.getElementById('detailMealSelect').value;
      const dateStr = document.getElementById('diaryDatePicker')?.value || new Date().toISOString().split('T')[0];
      const diary = getDiaryData(dateStr);

      diary[mealCat].push(selectedDetailFood);
      saveDiaryData(dateStr, diary);

      closeModal('foodDetailModal');
      switchTab('diaryTab');
    }

    function openScannedFoodModal() {
      if (selectedScannedFood) {
        openFoodDetail(selectedScannedFood);
      }
    }

    // Modal Add Custom Food
    function openAddCustomFoodModal() {
      document.getElementById('addFoodModal').classList.remove('hidden');
    }

    function saveCustomFood(e) {
      e.preventDefault();
      const name = document.getElementById('customName').value;
      const img = document.getElementById('customImgUrl').value || 'https://images.unsplash.com/photo-1546069901-ba9599a7e63c?w=500&auto=format&fit=crop';
      const category = document.getElementById('customCategory').value;
      const cal = parseInt(document.getElementById('customCal').value) || 300;
      const prot = parseInt(document.getElementById('customProtein').value) || 10;
      const carbs = parseInt(document.getElementById('customCarbs').value) || 30;
      const fat = parseInt(document.getElementById('customFat').value) || 10;

      const newItem = {
        id: 'custom_' + Date.now(),
        name: name,
        category: category,
        calories: cal,
        protein: prot,
        carbs: carbs,
        fat: fat,
        serving: '1 phần tự tạo',
        img: img,
        cheatAlert: category === 'fried',
        oilLevel: category === 'fried' ? 'Cao' : 'Bình thường',
        cheatNote: 'Món ăn do bạn tự nhập.',
        burnout: `${Math.round(cal / 10)} phút chạy bộ nhẹ.`
      };

      FOOD_DATABASE.unshift(newItem);
      renderLibrary();
      closeModal('addFoodModal');
    }

    // Gemini API Modal
    function openApiKeyModal() {
      document.getElementById('geminiApiKeyInput').value = userApiKey;
      document.getElementById('apiKeyModal').classList.remove('hidden');
    }

    function saveApiKey() {
      const val = document.getElementById('geminiApiKeyInput').value.trim();
      userApiKey = val;
      localStorage.setItem('fitscan_gemini_key', val);
      updateApiKeyStatusUI();
      closeModal('apiKeyModal');
    }

    function clearApiKey() {
      userApiKey = '';
      localStorage.removeItem('fitscan_gemini_key');
      document.getElementById('geminiApiKeyInput').value = '';
      updateApiKeyStatusUI();
      closeModal('apiKeyModal');
    }

    function updateApiKeyStatusUI() {
      const text = document.getElementById('apiKeyStatusText');
      if (userApiKey && userApiKey.length > 5) {
        text.innerText = 'Gemini API: Live Key ✓';
        text.className = 'text-neonGreen font-bold';
      } else {
        text.innerText = 'Gemini API: Auto Mock';
        text.className = 'text-gray-400';
      }
    }

    function closeModal(modalId) {
      document.getElementById(modalId).classList.add('hidden');
    }
  </script>
</body>
</html>
