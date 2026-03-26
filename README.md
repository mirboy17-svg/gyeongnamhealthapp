<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>경남 산업보건 자료 공유 시스템</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #f8fafc; }
        .fade-in { animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        .drag-active { border-color: #f59e0b !important; background-color: #fffbeb !important; }
        .loader { border: 3px solid #f3f3f3; border-top: 3px solid #f59e0b; border-radius: 50%; width: 24px; height: 24px; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body class="text-slate-800 antialiased overflow-hidden h-screen flex flex-col">

    <!-- 전역 로딩 오버레이 -->
    <div id="globalLoader" class="fixed inset-0 bg-white/80 backdrop-blur-sm z-[100] flex flex-col items-center justify-center fade-in">
        <div class="loader mb-4 w-12 h-12"></div>
        <p class="text-lg font-bold text-slate-700" id="loaderText">시스템을 준비하고 있습니다...</p>
    </div>

    <!-- [1] 로그인 화면 -->
    <div id="loginView" class="absolute inset-0 z-50 flex flex-col items-center justify-center p-4 bg-slate-50 fade-in hidden">
        <div class="bg-white p-10 rounded-3xl shadow-[0_8px_30px_rgb(0,0,0,0.04)] w-full max-w-md border border-slate-100 relative overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-2 bg-gradient-to-r from-amber-400 to-orange-500"></div>
            
            <div class="text-center mb-10 mt-2">
                <div class="bg-amber-50 w-20 h-20 rounded-2xl flex items-center justify-center mx-auto mb-6 transform rotate-3">
                    <i class="fas fa-cloud-upload-alt text-4xl text-amber-500 transform -rotate-3"></i>
                </div>
                <h1 class="text-3xl font-bold text-slate-800 tracking-tight">산업보건 자료실</h1>
                <p class="text-sm text-slate-500 mt-3 font-medium">대용량 클라우드 연동 안전보건 허브</p>
            </div>
            
            <div class="space-y-6">
                <div>
                    <label class="block text-sm font-bold text-slate-700 mb-2">사업장명 접속</label>
                    <div class="relative">
                        <i class="fas fa-building absolute left-4 top-1/2 transform -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="clientCompany" placeholder="사업장명을 입력하세요 (예: 00공장)" 
                               class="w-full pl-11 pr-4 py-3.5 bg-slate-50 border border-slate-200 rounded-xl focus:ring-2 focus:ring-amber-500 focus:border-amber-500 focus:bg-white outline-none transition-all"
                               onkeypress="if(event.key === 'Enter') loginAsClient()">
                    </div>
                </div>
                <button onclick="loginAsClient()" class="w-full bg-slate-800 text-white py-3.5 rounded-xl font-bold text-lg hover:bg-amber-500 transition-colors shadow-lg shadow-slate-200 flex items-center justify-center gap-2">
                    <span>자료실 입장하기</span>
                    <i class="fas fa-arrow-right text-sm"></i>
                </button>
            </div>
        </div>
        
        <div class="mt-10">
            <button onclick="showAdminModal()" class="text-xs font-semibold text-slate-400 hover:text-slate-600 transition flex items-center gap-1.5">
                <i class="fas fa-lock"></i> 관리자 전용 접속
            </button>
        </div>
    </div>

    <!-- [1-1] 관리자 로그인 모달 -->
    <div id="adminModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4 hidden z-[60] fade-in">
        <div class="bg-white p-8 rounded-2xl shadow-2xl w-full max-w-sm border border-slate-200">
            <h2 class="text-xl font-bold mb-6 text-slate-800 flex items-center">
                <div class="w-8 h-8 rounded-full bg-slate-100 flex items-center justify-center mr-3">
                    <i class="fas fa-user-shield text-slate-600"></i>
                </div>
                시스템 관리자 인증
            </h2>
            <div class="space-y-4 mb-8">
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">아이디</label>
                    <input type="text" id="adminId" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-lg outline-none focus:ring-2 focus:ring-slate-800 transition">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">비밀번호</label>
                    <input type="password" id="adminPw" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-lg outline-none focus:ring-2 focus:ring-slate-800 transition"
                           onkeypress="if(event.key === 'Enter') loginAsAdmin()">
                </div>
            </div>
            <div class="flex gap-3">
                <button onclick="hideAdminModal()" class="flex-1 bg-white border border-slate-200 text-slate-600 py-3 rounded-lg font-bold hover:bg-slate-50 transition">취소</button>
                <button onclick="loginAsAdmin()" class="flex-1 bg-slate-800 text-white py-3 rounded-lg font-bold hover:bg-slate-900 transition shadow-md">로그인</button>
            </div>
        </div>
    </div>

    <!-- [2] 대시보드 화면 -->
    <div id="dashboardView" class="hidden flex-1 flex overflow-hidden fade-in bg-slate-50">
        
        <!-- 사이드바 -->
        <aside class="w-72 bg-slate-900 text-slate-300 flex flex-col flex-shrink-0 shadow-2xl z-20">
            <div class="p-6 border-b border-white/10">
                <div class="flex items-center gap-3 mb-4">
                    <div class="bg-amber-500 w-10 h-10 rounded-lg flex items-center justify-center shadow-lg shadow-amber-500/30">
                        <i class="fas fa-database text-white text-xl"></i>
                    </div>
                    <h1 class="text-xl font-bold text-white tracking-wide">Health<span class="text-amber-400">Hub</span></h1>
                </div>
                <div id="userInfo" class="w-full text-sm px-4 py-2.5 bg-white/10 rounded-lg border border-white/5 text-amber-200 font-medium flex items-center">
                    <!-- 유저 정보 표시 -->
                </div>
            </div>
            
            <div class="p-5 text-xs font-bold text-slate-500 uppercase tracking-widest flex justify-between items-center mt-2">
                <span>카테고리 분류</span>
            </div>
            <nav class="flex-1 overflow-y-auto px-4 space-y-1.5 pb-6" id="categoryNav">
                <!-- 카테고리 렌더링 영역 -->
            </nav>
            
            <div class="p-5 border-t border-white/10 bg-slate-950/30">
                <button onclick="logout()" class="w-full flex items-center justify-center gap-2 py-3 text-sm font-medium text-slate-400 hover:text-white hover:bg-white/10 rounded-lg transition-all">
                    <i class="fas fa-sign-out-alt"></i> 시스템 로그아웃
                </button>
            </div>
        </aside>

        <!-- 메인 콘텐츠 영역 -->
        <main class="flex-1 flex flex-col h-full overflow-hidden relative">
            
            <!-- 상단 헤더 & 통계 요약 -->
            <header class="bg-white px-8 py-6 border-b border-slate-200 flex flex-col sm:flex-row justify-between items-start sm:items-center flex-shrink-0 z-10 shadow-[0_2px_10px_rgb(0,0,0,0.02)] gap-4">
                <div>
                    <h2 class="text-2xl font-bold text-slate-800 tracking-tight">산업보건 자료 대시보드</h2>
                    <p class="text-sm text-slate-500 mt-1">대용량 파일도 빠르고 안전하게 공유하세요.</p>
                </div>
                
                <div class="flex gap-3">
                    <div class="bg-slate-50 border border-slate-100 px-5 py-3 rounded-xl flex items-center gap-4">
                        <div class="bg-amber-100 text-amber-600 w-10 h-10 rounded-full flex items-center justify-center">
                            <i class="fas fa-file-alt text-lg"></i>
                        </div>
                        <div>
                            <p class="text-xs font-bold text-slate-400 uppercase tracking-wider">총 보유 자료</p>
                            <p class="text-xl font-extrabold text-slate-800" id="totalFilesStat">0<span class="text-sm font-medium text-slate-500 ml-1">건</span></p>
                        </div>
                    </div>
                </div>
            </header>

            <!-- 본문 영역 -->
            <div class="flex-1 overflow-y-auto p-8 scroll-smooth relative">
                
                <!-- [관리자 전용] 워크스페이스 -->
                <div id="adminPanel" class="hidden mb-10 fade-in">
                    <div class="mb-4 flex items-center gap-2">
                        <span class="bg-slate-800 text-white text-xs font-bold px-3 py-1 rounded-full uppercase tracking-wider"><i class="fas fa-cloud mr-1"></i> Cloud Storage <span id="cloudStatusText">연동됨</span></span>
                        <hr class="flex-1 border-slate-200">
                    </div>
                    
                    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                        <!-- 1. 카테고리 관리 섹션 -->
                        <section class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 flex flex-col h-[380px]">
                            <h3 class="text-lg font-bold text-slate-800 flex items-center mb-5">
                                <i class="fas fa-folder-tree text-amber-500 mr-2"></i> 카테고리 관리
                            </h3>
                            <div class="flex gap-2 mb-4">
                                <input type="text" id="newCategoryInput" placeholder="새 분류명 입력..." class="flex-1 p-3 bg-slate-50 border border-slate-200 rounded-lg text-sm focus:ring-2 focus:ring-amber-500 outline-none transition">
                                <button onclick="addNewCategory()" class="bg-amber-50 text-amber-700 border border-amber-100 px-4 rounded-lg font-bold hover:bg-amber-500 hover:text-white transition-colors whitespace-nowrap">추가</button>
                            </div>
                            <div class="flex-1 overflow-y-auto pr-2 custom-scrollbar">
                                <ul id="adminCategoryList" class="space-y-2 text-sm"></ul>
                            </div>
                        </section>

                        <!-- 2. 대용량 자료 업로드 섹션 -->
                        <section class="lg:col-span-2 bg-white p-6 rounded-2xl shadow-sm border border-slate-200 h-[380px] flex flex-col">
                            <h3 class="text-lg font-bold text-slate-800 flex items-center mb-5">
                                <i class="fas fa-cloud-upload-alt text-amber-500 mr-2"></i> 클라우드 대용량 파일 배포
                            </h3>
                            
                            <div class="grid grid-cols-2 gap-4 mb-4">
                                <div>
                                    <label class="block text-xs font-bold text-slate-500 mb-1.5">메인 카테고리</label>
                                    <select id="uploadMainCat" class="w-full p-3 border border-slate-200 rounded-lg bg-slate-50 text-sm focus:ring-2 focus:ring-amber-500 outline-none cursor-pointer"></select>
                                </div>
                                <div>
                                    <label class="block text-xs font-bold text-slate-500 mb-1.5">하위 폴더/태그 (선택)</label>
                                    <input type="text" id="uploadSubCat" placeholder="예: 2026년 3월, 벤젠 등" class="w-full p-3 border border-slate-200 rounded-lg bg-slate-50 text-sm focus:ring-2 focus:ring-amber-500 outline-none">
                                </div>
                            </div>
                            
                            <!-- Drag & Drop Area -->
                            <div id="dropZone" class="flex-1 border-2 border-dashed border-slate-300 rounded-xl bg-slate-50 flex flex-col items-center justify-center text-center p-6 cursor-pointer hover:bg-amber-50 hover:border-amber-400 transition-all group relative">
                                <input type="file" id="uploadFile" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer" onchange="updateFileNameDisplay(this)">
                                
                                <div id="uploadPrompt">
                                    <div class="w-14 h-14 bg-white rounded-full shadow-sm flex items-center justify-center mx-auto mb-3 group-hover:scale-110 transition-transform">
                                        <i class="fas fa-file-import text-2xl text-slate-400 group-hover:text-amber-500 transition-colors"></i>
                                    </div>
                                    <p class="text-sm font-bold text-slate-700">클릭하거나 파일을 이곳에 드롭하세요</p>
                                    <p class="text-xs text-slate-500 mt-1">대용량 PDF, 고화질 이미지, 엑셀 파일 등 용량 제한 없음</p>
                                </div>

                                <div id="fileSelectedUI" class="hidden flex flex-col items-center w-full">
                                    <i class="fas fa-file-check text-4xl text-green-500 mb-2"></i>
                                    <p id="selectedFileName" class="text-sm font-bold text-slate-800 truncate w-full max-w-xs mt-2"></p>
                                    <p id="selectedFileSize" class="text-xs text-slate-500 mt-1"></p>
                                    <button onclick="clearFileInput(event)" class="mt-3 text-xs text-red-500 hover:text-red-700 font-semibold bg-red-50 px-3 py-1 rounded-full">파일 취소</button>
                                </div>
                            </div>

                            <button onclick="handleUpload()" class="mt-4 w-full bg-slate-800 text-white py-3.5 rounded-xl font-bold text-[15px] hover:bg-amber-500 transition-colors shadow-md flex items-center justify-center gap-2">
                                <i class="fas fa-paper-plane"></i> 자료 업로드 및 전사 공유
                            </button>
                        </section>
                    </div>
                </div>

                <!-- 자료 목록 테이블 -->
                <section class="bg-white rounded-2xl shadow-[0_2px_15px_rgb(0,0,0,0.03)] border border-slate-200 flex flex-col">
                    <div class="p-6 border-b border-slate-100 flex flex-col sm:flex-row justify-between items-center gap-4 bg-white rounded-t-2xl">
                        <div class="flex items-center gap-3">
                            <h3 class="text-lg font-bold text-slate-800" id="currentCategoryTitle">전체 자료 보기</h3>
                            <span id="filteredCountBadge" class="bg-amber-100 text-amber-700 text-xs font-bold px-2.5 py-1 rounded-full">0건</span>
                        </div>
                        
                        <div class="relative w-full sm:w-72">
                            <i class="fas fa-search absolute left-4 top-1/2 transform -translate-y-1/2 text-slate-400"></i>
                            <input type="text" id="searchInput" placeholder="자료명, 태그 검색..." onkeyup="renderTable()" 
                                   class="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-full text-sm focus:outline-none focus:ring-2 focus:ring-amber-500 focus:bg-white transition-all">
                        </div>
                    </div>
                    
                    <div class="overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 border-b border-slate-200 text-slate-500 text-xs font-bold uppercase tracking-wider">
                                    <th class="px-6 py-4 w-1/4">분류</th>
                                    <th class="px-6 py-4 w-1/6">태그/폴더</th>
                                    <th class="px-6 py-4">자료명</th>
                                    <th class="px-6 py-4 text-center w-32">업데이트일</th>
                                    <th class="px-6 py-4 text-center w-24">액션</th>
                                </tr>
                            </thead>
                            <tbody id="fileTableBody" class="divide-y divide-slate-100 text-[14px]"></tbody>
                        </table>
                        
                        <div id="emptyState" class="hidden py-20 text-center flex flex-col items-center justify-center bg-slate-50/50">
                            <div class="w-20 h-20 bg-slate-100 rounded-full flex items-center justify-center mb-4">
                                <i class="fas fa-box-open text-3xl text-slate-300"></i>
                            </div>
                            <h4 class="text-lg font-bold text-slate-700 mb-1">해당하는 자료가 없습니다.</h4>
                            <p class="text-sm text-slate-500">다른 검색어를 입력하거나 카테고리를 변경해보세요.</p>
                        </div>
                    </div>
                </section>

                <footer class="mt-12 text-center text-xs text-slate-400 pb-4">
                    &copy; 2026 HealthHub 산업보건 자료 관리 시스템. Vercel Cloud Edition
                </footer>
            </div>
        </main>
    </div>

    <!-- 토스트 알림 -->
    <div id="toast" class="fixed bottom-6 right-6 bg-slate-900 text-white px-5 py-3.5 rounded-xl shadow-2xl transform translate-y-24 opacity-0 transition-all duration-400 z-[110] flex items-center gap-3 border border-slate-700">
        <div id="toastIcon" class="text-green-400 text-lg"><i class="fas fa-check-circle"></i></div>
        <span id="toastMsg" class="font-medium text-sm">알림 메시지</span>
    </div>

    <!-- Firebase SDK 모듈 -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, updateDoc, deleteDoc, doc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getStorage, ref, uploadBytes, getDownloadURL, deleteObject } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-storage.js";

        // =====================================================================
        // 고객님의 Firebase 프로젝트 연동 정보 (절대 덮어쓰지 않음)
        // =====================================================================
        const firebaseConfig = {
            apiKey: "AIzaSyDZYFxUJVJT7_DKJHtleAgVv0Uv-lvITaY",
            authDomain: "gyeongnamhealth-90cc3.firebaseapp.com",
            projectId: "gyeongnamhealth-90cc3",
            storageBucket: "gyeongnamhealth-90cc3.firebasestorage.app",
            messagingSenderId: "1037047664886",
            appId: "1:1037047664886:web:330f8612d3edf843441cd7",
            measurementId: "G-KRVHSD824D"
        };
        
        // Canvas 내부 설정 덮어쓰기 로직 완전히 제거! 오직 고객님의 DB로만 직결됨

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'gyeongnamhealth-90cc3';

        // --- [전역 상태] ---
        const ADMIN_ID = 'aniag0000';
        const ADMIN_PW = 'wjdwodyd21';
        let currentUser = null; 
        let categories = []; 
        let files = []; 
        let currentFilterMain = 'all'; // 대분류 필터 상태
        let currentFilterSub = null;   // 하위폴더 필터 상태
        let isLocalMode = false;

        // --- [로컬 데모 모드 전환 함수] ---
        function enableLocalMode(reason) {
            if (isLocalMode) return;
            isLocalMode = true;
            console.warn("로컬 데모 모드 활성화 원인:", reason);
            
            showToast("데이터베이스 권한 잠금: 테스트용 데모 모드로 작동합니다.", "info");
            document.getElementById('cloudStatusText').innerText = "데모(로컬) 모드";
            document.getElementById('cloudStatusText').classList.add("text-yellow-400");
            
            categories = [
                { id: 'cat1', name: "1. 정기안전보건교육자료", timestamp: 1 },
                { id: 'cat2', name: "2. 특별관리물질 취급고지및일지", timestamp: 2 },
                { id: 'cat3', name: "3. CMR물질 유해성 정보", timestamp: 3 },
                { id: 'cat4', name: "4. 초과공정개선보고서", timestamp: 4 },
                { id: 'cat5', name: "5. 프로그램(청력, 밀폐, 호흡기)", timestamp: 5 }
            ];
            
            files = [
                { id: 'f1', mainCat: "1. 정기안전보건교육자료", subCat: "3월 자료", name: "[샘플] 2026년_산업안전보건법_가이드.pdf", date: new Date().toISOString().split('T')[0], timestamp: Date.now(), fileUrl: null }
            ];
            
            updateAllCategoryViews();
            updateDashboardStats();
            renderTable();
        }

        // Firebase 초기 인증 (고객님의 Firebase Auth를 사용)
        const initAuth = async () => {
            try {
                await signInAnonymously(auth);
            } catch (error) {
                console.error("인증 에러:", error);
                enableLocalMode("인증 실패: " + error.code);
                document.getElementById('loginView').classList.remove('hidden');
                hideLoader();
            }
        };

        initAuth();

        onAuthStateChanged(auth, (user) => {
            document.getElementById('loginView').classList.remove('hidden');
            hideLoader();
            if (user && !isLocalMode) {
                setupRealtimeSync();
            }
        });

        window.onload = () => {
            const dropZone = document.getElementById('dropZone');
            ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(eventName => {
                dropZone.addEventListener(eventName, preventDefaults, false);
            });
            ['dragenter', 'dragover'].forEach(eventName => {
                dropZone.addEventListener(eventName, () => dropZone.classList.add('drag-active'), false);
            });
            ['dragleave', 'drop'].forEach(eventName => {
                dropZone.addEventListener(eventName, () => dropZone.classList.remove('drag-active'), false);
            });
            dropZone.addEventListener('drop', handleDrop, false);
        };

        // --- [권한 에러 처리용 UI 함수] ---
        function handlePermissionError(error) {
            if (error.code === 'permission-denied' || (error.message && error.message.includes('permissions'))) {
                showToast("데이터베이스 권한이 잠겨있습니다.", "error");
                const emptyState = document.getElementById('emptyState');
                if (emptyState) {
                    emptyState.innerHTML = `
                        <div class="bg-red-50 text-red-600 p-8 rounded-2xl border border-red-200 text-center w-full max-w-2xl mx-auto shadow-sm">
                            <i class="fas fa-lock text-5xl mb-4 text-red-400"></i>
                            <h4 class="text-xl font-bold mb-3">데이터베이스 접근이 차단되었습니다!</h4>
                            <p class="text-sm mb-5 text-red-500 font-medium">Firebase 콘솔에서 <b>Firestore Database</b>의 보안 규칙(Rules)을 아직 열어주지 않으셨습니다.<br>아래 코드를 복사하여 규칙 탭에 붙여넣고 <b>[게시(Publish)]</b> 버튼을 눌러주세요.</p>
                            <div class="bg-slate-900 text-left text-green-400 p-5 rounded-xl text-sm font-mono overflow-x-auto shadow-inner">
                                rules_version = '2';<br>
                                service cloud.firestore {<br>
                                &nbsp;&nbsp;match /databases/{database}/documents {<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;match /{document=**} {<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;allow read, write: if true;<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;}<br>
                                &nbsp;&nbsp;}<br>
                                }
                            </div>
                        </div>
                    `;
                    emptyState.classList.remove('hidden');
                    emptyState.classList.add('flex');
                    document.getElementById('fileTableBody').innerHTML = '';
                }
            }
        }

        // --- [실시간 동기화 (Firestore)] ---
        function setupRealtimeSync() {
            try {
                // 고객님 DB에 저장될 심플한 컬렉션 이름 사용
                const filesRef = collection(db, 'health_files');
                onSnapshot(filesRef, (snapshot) => {
                    if (isLocalMode) return;
                    files = [];
                    snapshot.forEach(doc => { files.push({ id: doc.id, ...doc.data() }); });
                    updateDashboardStats();
                    renderSidebarCategories(); // 파일이 업데이트되면 사이드바 하위폴더도 실시간 즉시 갱신
                    renderTable();
                }, (error) => {
                    console.error("파일 데이터 읽기 에러:", error);
                    handlePermissionError(error);
                });

                const catRef = collection(db, 'health_categories');
                onSnapshot(catRef, (snapshot) => {
                    if (isLocalMode) return;
                    categories = [];
                    snapshot.forEach(doc => { categories.push({ id: doc.id, ...doc.data() }); });
                    
                    if (categories.length === 0) {
                        const defaultCats = [
                            "1. 정기안전보건교육자료", "2. 특별관리물질 취급고지및일지", 
                            "3. CMR물질 유해성 정보", "4. 초과공정개선보고서", "5. 프로그램(청력, 밀폐, 호흡기)"
                        ];
                        defaultCats.forEach(async (name, idx) => {
                            try {
                                await addDoc(catRef, { name: name, timestamp: Date.now() + idx });
                            } catch (err) {}
                        });
                    } else {
                        categories.sort((a, b) => a.timestamp - b.timestamp);
                        updateAllCategoryViews();
                    }
                }, (error) => {
                    console.error("카테고리 데이터 읽기 에러:", error);
                    handlePermissionError(error);
                });
            } catch (err) {
                enableLocalMode("DB 연결 자체 실패");
            }
        }

        function preventDefaults(e) { e.preventDefault(); e.stopPropagation(); }
        function handleDrop(e) {
            const dt = e.dataTransfer;
            if(dt.files.length > 0) {
                const fileInput = document.getElementById('uploadFile');
                fileInput.files = dt.files; 
                updateFileNameDisplay(fileInput);
            }
        }

        // --- [UI 헬퍼 함수] ---
        function showLoader(text) {
            document.getElementById('loaderText').innerText = text;
            document.getElementById('globalLoader').style.display = 'flex';
        }
        function hideLoader() {
            document.getElementById('globalLoader').style.display = 'none';
        }

        let toastTimeout;
        window.showToast = function(msg, type='success') {
            const toast = document.getElementById('toast');
            const icon = document.getElementById('toastIcon');
            document.getElementById('toastMsg').innerText = msg;
            
            if(type === 'error') icon.innerHTML = '<i class="fas fa-exclamation-circle text-red-400"></i>';
            else if(type === 'info') icon.innerHTML = '<i class="fas fa-info-circle text-amber-400"></i>';
            else icon.innerHTML = '<i class="fas fa-check-circle text-green-400"></i>';

            toast.classList.remove('translate-y-24', 'opacity-0');
            clearTimeout(toastTimeout);
            toastTimeout = setTimeout(() => {
                toast.classList.add('translate-y-24', 'opacity-0');
            }, 3500);
        }

        function updateDashboardStats() {
            const totalStatEl = document.getElementById('totalFilesStat');
            if (totalStatEl) totalStatEl.innerHTML = `${files.length}<span class="text-sm font-medium text-slate-500 ml-1">건</span>`;
            
            const today = new Date().toISOString().split('T')[0];
            const todayCount = files.filter(f => f.date === today).length;
            const todayStatEl = document.getElementById('todayUpdateStat');
            if (todayStatEl) todayStatEl.innerHTML = `${todayCount}<span class="text-sm font-medium text-slate-500 ml-1">건 (오늘)</span>`;
        }

        window.updateFileNameDisplay = function(input) {
            if(input.files && input.files.length > 0) {
                const file = input.files[0];
                document.getElementById('uploadPrompt').classList.add('hidden');
                document.getElementById('fileSelectedUI').classList.remove('hidden');
                document.getElementById('selectedFileName').innerText = file.name;
                
                let sizeText = '';
                if (file.size > 1024 * 1024) sizeText = (file.size / (1024 * 1024)).toFixed(2) + ' MB';
                else sizeText = (file.size / 1024).toFixed(1) + ' KB';
                document.getElementById('selectedFileSize').innerText = sizeText;
            }
        }
        
        window.clearFileInput = function(e) {
            if(e) e.stopPropagation();
            const input = document.getElementById('uploadFile');
            input.value = '';
            document.getElementById('uploadPrompt').classList.remove('hidden');
            document.getElementById('fileSelectedUI').classList.add('hidden');
        }

        // --- [로그인/로그아웃] ---
        window.loginAsClient = function() {
            const company = document.getElementById('clientCompany').value.trim();
            if(!company) return alert('접속하실 사업장명을 입력해주세요.');
            currentUser = { role: 'client', name: company };
            document.getElementById('clientCompany').value = '';
            enterDashboard();
            showToast(`${company} 권한으로 접속되었습니다.`);
        }

        window.showAdminModal = () => document.getElementById('adminModal').classList.remove('hidden');
        window.hideAdminModal = () => document.getElementById('adminModal').classList.add('hidden');

        window.loginAsAdmin = function() {
            const id = document.getElementById('adminId').value;
            const pw = document.getElementById('adminPw').value;
            if(id === ADMIN_ID && pw === ADMIN_PW) {
                hideAdminModal();
                currentUser = { role: 'admin', name: '시스템 관리자' };
                enterDashboard();
                showToast('관리자 모드가 활성화되었습니다.');
            } else {
                showToast('아이디 또는 비밀번호가 일치하지 않습니다.', 'error');
            }
        }

        window.logout = function() {
            currentUser = null;
            document.getElementById('dashboardView').classList.add('hidden');
            document.getElementById('loginView').classList.remove('hidden');
            document.getElementById('searchInput').value = '';
            filterByCategory('all');
        }

        function enterDashboard() {
            document.getElementById('loginView').classList.add('hidden');
            document.getElementById('dashboardView').classList.remove('hidden');
            
            const uiSpan = document.getElementById('userInfo');
            if(currentUser.role === 'admin') {
                uiSpan.innerHTML = '<div class="w-6 h-6 rounded-full bg-amber-500/20 text-amber-400 flex items-center justify-center mr-2"><i class="fas fa-crown text-xs"></i></div> 시스템 관리자';
                document.getElementById('adminPanel').classList.remove('hidden');
            } else {
                uiSpan.innerHTML = `<div class="w-6 h-6 rounded-full bg-amber-500/20 text-amber-300 flex items-center justify-center mr-2"><i class="fas fa-building text-xs"></i></div> ${currentUser.name}`;
                document.getElementById('adminPanel').classList.add('hidden');
            }

            updateAllCategoryViews();
            updateDashboardStats();
            renderTable();
        }

        // --- [카테고리 관리] ---
        function updateAllCategoryViews() {
            renderSidebarCategories();
            renderUploadCategorySelect();
            renderAdminCategoryManager();
        }

        window.addNewCategory = async function() {
            const name = document.getElementById('newCategoryInput').value.trim();
            if(!name) return showToast('카테고리명을 입력해주세요.', 'error');
            
            if (isLocalMode) {
                categories.push({ id: Date.now().toString(), name: name, timestamp: Date.now() });
                updateAllCategoryViews();
                showToast('카테고리가 생성되었습니다. (로컬)');
                document.getElementById('newCategoryInput').value = '';
                return;
            }

            showLoader('추가 중...');
            try {
                await addDoc(collection(db, 'health_categories'), { name: name, timestamp: Date.now() });
                showToast('카테고리가 생성되었습니다.');
            } catch(error) { 
                showToast('권한 문제로 오류가 발생했습니다.', 'error'); 
            }
            document.getElementById('newCategoryInput').value = '';
            hideLoader();
        }

        window.editCategory = async function(id, currentName) {
            const newName = prompt('카테고리명을 수정하세요:', currentName);
            if(newName && newName.trim() !== '' && newName !== currentName) {
                if (isLocalMode) {
                    categories = categories.map(c => c.id === id ? { ...c, name: newName } : c);
                    updateAllCategoryViews();
                    showToast('카테고리가 수정되었습니다. (로컬)');
                    return;
                }

                showLoader('업데이트 중...');
                try {
                    await updateDoc(doc(db, 'health_categories', id), { name: newName });
                    showToast('카테고리가 수정되었습니다.');
                } catch(error) { 
                    showToast('권한 문제로 오류가 발생했습니다.', 'error'); 
                }
                hideLoader();
            }
        }

        window.deleteCategory = async function(id) {
            if(confirm('이 카테고리를 삭제하시겠습니까?')) {
                if (isLocalMode) {
                    categories = categories.filter(c => c.id !== id);
                    updateAllCategoryViews();
                    showToast('카테고리가 삭제되었습니다. (로컬)');
                    return;
                }

                showLoader('삭제 중...');
                try {
                    await deleteDoc(doc(db, 'health_categories', id));
                    showToast('카테고리가 삭제되었습니다.');
                } catch(error) { 
                    showToast('권한 문제로 오류가 발생했습니다.', 'error'); 
                }
                hideLoader();
            }
        }

        function renderAdminCategoryManager() {
            const list = document.getElementById('adminCategoryList');
            list.innerHTML = '';
            categories.forEach(cat => {
                list.innerHTML += `
                    <li class="p-3 bg-white border border-slate-100 rounded-lg mb-2 flex justify-between items-center hover:border-amber-200 transition-colors shadow-sm">
                        <span class="font-bold text-slate-700 truncate pr-2"><i class="far fa-folder text-slate-400 mr-2"></i>${cat.name}</span>
                        <div class="flex gap-1.5 flex-shrink-0">
                            <button onclick="editCategory('${cat.id}', '${cat.name}')" class="w-7 h-7 flex items-center justify-center bg-slate-100 text-slate-500 rounded hover:bg-slate-200 hover:text-slate-700 transition"><i class="fas fa-pen text-xs"></i></button>
                            <button onclick="deleteCategory('${cat.id}')" class="w-7 h-7 flex items-center justify-center bg-red-50 text-red-500 rounded hover:bg-red-100 hover:text-red-700 transition"><i class="fas fa-trash text-xs"></i></button>
                        </div>
                    </li>
                `;
            });
        }

        window.filterByCategory = function(mainCat, subCat = null) {
            currentFilterMain = mainCat;
            currentFilterSub = subCat;
            
            let titleText = mainCat === 'all' ? '전체 자료 보기' : mainCat;
            if (subCat) titleText += ` > ${subCat}`;
            document.getElementById('currentCategoryTitle').innerText = titleText;
            
            renderSidebarCategories();
            renderTable();
        }

        function renderSidebarCategories() {
            const nav = document.getElementById('categoryNav');
            let html = `
                <a href="#" id="nav_all" onclick="filterByCategory('all')" class="${currentFilterMain === 'all' ? 'bg-amber-500 text-white shadow-md' : 'text-slate-400 hover:bg-white/5'} block px-4 py-2.5 rounded-xl transition-all mb-3 font-bold text-[13px] flex items-center">
                    <i class="fas fa-layer-group w-5 text-center mr-2"></i> 모든 자료
                </a>
            `;
            categories.forEach(cat => {
                const isActiveMain = currentFilterMain === cat.name && currentFilterSub === null;
                const activeClassMain = isActiveMain ? 'bg-amber-500 text-white shadow-md' : 'text-slate-400 hover:bg-white/5 hover:text-slate-200';
                
                html += `
                    <div class="mb-1">
                        <a href="#" onclick="filterByCategory('${cat.name}')" class="${activeClassMain} block px-4 py-2.5 rounded-xl transition-all text-[13px] font-medium flex items-center truncate" title="${cat.name}">
                            <i class="far fa-folder w-5 text-center mr-2 opacity-70"></i> <span class="truncate">${cat.name}</span>
                        </a>
                `;

                // 해당 대분류에 속하는 고유한 하위 폴더(태그) 목록 추출
                const subCats = [...new Set(files.filter(f => f.mainCat === cat.name && f.subCat).map(f => f.subCat))].sort();
                
                if (subCats.length > 0) {
                    html += `<div class="ml-6 mt-1 mb-2 space-y-1 border-l border-white/10 pl-2">`;
                    subCats.forEach(sub => {
                        const isActiveSub = currentFilterMain === cat.name && currentFilterSub === sub;
                        const activeClassSub = isActiveSub ? 'bg-amber-500/20 text-amber-300 font-bold' : 'text-slate-400 hover:text-amber-200 hover:bg-white/5';
                        html += `
                            <a href="#" onclick="filterByCategory('${cat.name}', '${sub}')" class="${activeClassSub} block px-3 py-1.5 rounded-lg transition-all text-[12px] flex items-center truncate" title="${sub}">
                                <i class="fas fa-level-up-alt transform rotate-90 mr-2 opacity-50 text-[10px]"></i> <span class="truncate">${sub}</span>
                            </a>
                        `;
                    });
                    html += `</div>`;
                }
                html += `</div>`;
            });
            nav.innerHTML = html;
        }

        function renderUploadCategorySelect() {
            const select = document.getElementById('uploadMainCat');
            select.innerHTML = '';
            categories.forEach(cat => {
                select.innerHTML += `<option value="${cat.name}">${cat.name}</option>`;
            });
        }

        // --- [자료 테이블 렌더링 & 처리] ---
        window.renderTable = function() {
            const tbody = document.getElementById('fileTableBody');
            const emptyState = document.getElementById('emptyState');
            const searchKeyword = document.getElementById('searchInput').value.toLowerCase();
            
            tbody.innerHTML = '';
            
            let filteredFiles = files.filter(f => {
                const matchMainCat = currentFilterMain === 'all' || f.mainCat === currentFilterMain;
                const matchSubCat = currentFilterSub === null || f.subCat === currentFilterSub;
                const matchSearch = f.name.toLowerCase().includes(searchKeyword) || (f.subCat && f.subCat.toLowerCase().includes(searchKeyword));
                return matchMainCat && matchSubCat && matchSearch;
            });

            const countBadge = document.getElementById('filteredCountBadge');
            if(countBadge) countBadge.innerText = `${filteredFiles.length}건`;

            if(filteredFiles.length === 0) {
                if(emptyState) { emptyState.classList.remove('hidden'); emptyState.classList.add('flex'); }
            } else {
                if(emptyState) { emptyState.classList.add('hidden'); emptyState.classList.remove('flex'); }
                
                filteredFiles.sort((a, b) => (b.timestamp || 0) - (a.timestamp || 0)).forEach(file => {
                    let actionBtn = '';
                    if(currentUser && currentUser.role === 'admin') {
                        actionBtn = `<button onclick="deleteFile('${file.id}', '${file.storagePath}')" class="flex-1 bg-red-50 text-red-600 py-1.5 px-3 rounded-lg hover:bg-red-500 hover:text-white transition font-semibold text-xs border border-red-100 whitespace-nowrap">삭제</button>`;
                    } else {
                        actionBtn = `<button onclick="downloadFile('${file.fileUrl}')" class="flex-1 bg-amber-50 text-amber-600 py-1.5 px-3 rounded-lg hover:bg-amber-500 hover:text-white transition font-semibold text-xs border border-amber-100 flex items-center justify-center gap-1 whitespace-nowrap"><i class="fas fa-download"></i> 다운로드</button>`;
                    }

                    let catShort = file.mainCat;
                    if(file.mainCat && file.mainCat.includes('.')) {
                        catShort = file.mainCat.split('.')[0] + '. ' + (file.mainCat.split(' ')[1] || '');
                    }

                    let iconClass = "fa-file-alt text-slate-400";
                    if(file.name.endsWith('.pdf')) iconClass = "fa-file-pdf text-red-500";
                    else if(file.name.endsWith('.xlsx') || file.name.endsWith('.xls')) iconClass = "fa-file-excel text-green-600";
                    else if(file.name.endsWith('.doc') || file.name.endsWith('.docx') || file.name.endsWith('.hwp')) iconClass = "fa-file-word text-blue-500";
                    else if(file.name.endsWith('.zip')) iconClass = "fa-file-archive text-yellow-600";

                    const isNew = file.date === new Date().toISOString().split('T')[0];
                    const newBadge = isNew ? `<span class="ml-2 px-1.5 py-0.5 bg-red-100 text-red-600 text-[10px] font-bold rounded uppercase">New</span>` : '';

                    const row = `
                        <tr class="hover:bg-slate-50/80 transition-colors group border-b border-slate-50 last:border-0">
                            <td class="px-6 py-4"><span class="bg-slate-100 text-slate-600 px-2.5 py-1 rounded-md text-xs font-bold border border-slate-200 truncate block max-w-full" title="${file.mainCat}">${catShort}</span></td>
                            <td class="px-6 py-4">${file.subCat ? `<span class="text-[11px] font-bold text-amber-600 bg-amber-50 border border-amber-100 px-2 py-1 rounded-md whitespace-nowrap"><i class="fas fa-hashtag mr-1 opacity-70"></i>${file.subCat}</span>` : '<span class="text-slate-300">-</span>'}</td>
                            <td class="px-6 py-4">
                                <div class="flex items-center font-bold text-slate-700">
                                    <i class="fas ${iconClass} text-xl w-6 text-center mr-3 opacity-90"></i> 
                                    <span class="truncate cursor-default" title="${file.name}">${file.name}</span>${newBadge}
                                </div>
                            </td>
                            <td class="px-6 py-4 text-center"><span class="text-slate-500 font-medium text-xs">${file.date.replace(/-/g, '.')}</span></td>
                            <td class="px-6 py-4"><div class="flex gap-2 justify-center">${actionBtn}</div></td>
                        </tr>
                    `;
                    tbody.insertAdjacentHTML('beforeend', row);
                });
            }
        }

        window.handleUpload = async function() {
            const mainCat = document.getElementById('uploadMainCat').value;
            const subCat = document.getElementById('uploadSubCat').value.trim();
            const fileInput = document.getElementById('uploadFile');
            
            if(!mainCat) return showToast('먼저 카테고리를 선택하거나 생성해주세요.', 'error');
            if(fileInput.files.length === 0) return showToast('업로드할 파일을 드롭하거나 선택해주세요.', 'error');

            const file = fileInput.files[0];
            showLoader('업로드 중입니다...');

            if (isLocalMode) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    files.unshift({
                        id: Date.now().toString(),
                        mainCat: mainCat,
                        subCat: subCat,
                        name: file.name,
                        date: new Date().toISOString().split('T')[0],
                        timestamp: Date.now(),
                        fileUrl: e.target.result,
                        storagePath: null
                    });
                    updateDashboardStats();
                    renderSidebarCategories(); // 로컬 모드에서도 사이드바 즉시 갱신
                    renderTable();
                    document.getElementById('uploadSubCat').value = '';
                    clearFileInput(new Event('clear'));
                    filterByCategory('all');
                    hideLoader();
                    showToast('자료가 업로드 되었습니다. (테스트용 데모 모드)');
                };
                reader.readAsDataURL(file);
                return;
            }

            try {
                // 1. Storage에 실제 대용량 파일 업로드 - 복잡한 앱ID 없이 심플한 경로 사용
                const path = `health_files/${Date.now()}_${file.name}`;
                const fileRef = ref(storage, path);
                await uploadBytes(fileRef, file);
                
                // 2. 다운로드 링크 생성
                const downloadUrl = await getDownloadURL(fileRef);

                // 3. Firestore 데이터베이스에 문서 정보 저장
                await addDoc(collection(db, 'health_files'), {
                    mainCat: mainCat,
                    subCat: subCat,
                    name: file.name,
                    date: new Date().toISOString().split('T')[0],
                    timestamp: Date.now(),
                    fileUrl: downloadUrl,
                    storagePath: path
                });

                showToast('자료가 성공적으로 업로드 되었습니다.');
            } catch(error) {
                console.error("업로드 에러 상세내역:", error);
                // 에러가 발생하면 무조건 '권한 에러'라고 뭉뚱그리지 않고, 실제 파이어베이스가 뱉은 에러 메시지를 띄워줍니다.
                showToast(`업로드 실패: ${error.message}`, 'error');
            }
            
            document.getElementById('uploadSubCat').value = '';
            clearFileInput(new Event('clear'));
            filterByCategory('all');
            hideLoader();
        }

        window.deleteFile = async function(id, storagePath) {
            if(confirm('이 자료를 삭제하시겠습니까?')) {
                if (isLocalMode) {
                    files = files.filter(f => f.id !== id);
                    updateDashboardStats();
                    renderSidebarCategories(); // 로컬 모드 삭제 시 사이드바 즉시 갱신
                    renderTable();
                    showToast('자료가 삭제되었습니다. (로컬)');
                    return;
                }

                showLoader('자료 삭제 중...');
                try {
                    // 1. Storage에서 실제 파일 삭제
                    if (storagePath && storagePath !== 'null') {
                        const fileRef = ref(storage, storagePath);
                        await deleteObject(fileRef);
                    }
                    // 2. Firestore에서 기록 삭제
                    await deleteDoc(doc(db, 'health_files', id));
                    showToast('자료가 완전히 삭제되었습니다.');
                } catch(error) {
                    console.error(error);
                    showToast("삭제 중 권한 오류가 발생했습니다.", 'error');
                }
                hideLoader();
            }
        }

        window.downloadFile = function(url) {
            if(url) {
                if (url.startsWith('data:')) {
                    const a = document.createElement('a');
                    a.href = url;
                    a.download = "downloaded_file";
                    document.body.appendChild(a);
                    a.click();
                    document.body.removeChild(a);
                } else {
                    window.open(url, '_blank');
                }
            } else {
                showToast('다운로드 링크를 찾을 수 없습니다.', 'error');
            }
        }
    </script>
</body>
</html>
