# movie-recommendation
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CinePick - 영화 추천</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700&display=swap');
        
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #0f172a;
            color: #f1f5f9;
        }

        .movie-card {
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .movie-card:hover {
            transform: translateY(-10px);
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5);
        }

        .glass-nav {
            background: rgba(15, 23, 42, 0.8);
            backdrop-filter: blur(12px);
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .modal-enter {
            animation: fadeIn 0.2s ease-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.95); }
            to { opacity: 1; transform: scale(1); }
        }

        /* 스크롤바 커스텀 */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
        ::-webkit-scrollbar-thumb {
            background: #334155;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #475569;
        }
    </style>
</head>
<body class="min-h-screen">

    <!-- 네비게이션 -->
    <nav class="glass-nav sticky top-0 z-50 px-6 py-4 flex justify-between items-center">
        <div class="flex items-center space-x-2">
            <i class="fas fa-film text-indigo-500 text-2xl"></i>
            <h1 class="text-2xl font-bold tracking-tight">CinePick</h1>
        </div>
        <div class="hidden md:flex space-x-8 text-sm font-medium">
            <a href="#" class="hover:text-indigo-400 transition">홈</a>
            <a href="#" class="hover:text-indigo-400 transition">인기순</a>
            <a href="#" class="hover:text-indigo-400 transition">최신순</a>
            <a href="#" class="hover:text-indigo-400 transition">내 보관함</a>
        </div>
        <div class="flex items-center space-x-4">
            <button class="p-2 hover:bg-slate-800 rounded-full transition">
                <i class="fas fa-search"></i>
            </button>
            <button class="bg-indigo-600 hover:bg-indigo-700 px-4 py-2 rounded-lg text-sm transition">로그인</button>
        </div>
    </nav>

    <!-- 히어로 섹션 -->
    <header class="relative h-[400px] flex items-center justify-center overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-r from-slate-950 to-transparent z-10"></div>
        <img src="https://images.unsplash.com/photo-1536440136628-849c177e76a1?auto=format&fit=crop&q=80&w=2000" 
             alt="Hero Background" 
             class="absolute inset-0 w-full h-full object-cover opacity-50">
        <div class="relative z-20 text-center px-4">
            <h2 class="text-4xl md:text-6xl font-bold mb-4">당신을 위한 완벽한 영화</h2>
            <p class="text-slate-400 text-lg md:text-xl mb-8">오늘 밤, 무엇을 볼지 고민하지 마세요.</p>
            <button onclick="scrollToMovies()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-8 py-3 rounded-full font-bold transition transform hover:scale-105">
                지금 탐색하기
            </button>
        </div>
    </header>

    <!-- 메인 컨텐츠 -->
    <main id="movie-section" class="max-w-7xl mx-auto px-6 py-12">
        
        <!-- 필터 탭 -->
        <div class="flex flex-wrap gap-3 mb-10 overflow-x-auto pb-2">
            <button onclick="filterMovies('전체')" class="filter-btn active bg-indigo-600 px-5 py-2 rounded-full text-sm font-medium whitespace-nowrap">전체</button>
            <button onclick="filterMovies('SF')" class="filter-btn bg-slate-800 hover:bg-slate-700 px-5 py-2 rounded-full text-sm font-medium transition whitespace-nowrap">SF</button>
            <button onclick="filterMovies('액션')" class="filter-btn bg-slate-800 hover:bg-slate-700 px-5 py-2 rounded-full text-sm font-medium transition whitespace-nowrap">액션</button>
            <button onclick="filterMovies('드라마')" class="filter-btn bg-slate-800 hover:bg-slate-700 px-5 py-2 rounded-full text-sm font-medium transition whitespace-nowrap">드라마</button>
            <button onclick="filterMovies('스릴러')" class="filter-btn bg-slate-800 hover:bg-slate-700 px-5 py-2 rounded-full text-sm font-medium transition whitespace-nowrap">스릴러</button>
            <button onclick="filterMovies('애니메이션')" class="filter-btn bg-slate-800 hover:bg-slate-700 px-5 py-2 rounded-full text-sm font-medium transition whitespace-nowrap">애니메이션</button>
        </div>

        <!-- 영화 그리드 -->
        <div id="movie-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8">
            <!-- JS에서 동적으로 생성됨 -->
        </div>
    </main>

    <!-- 영화 상세 모달 -->
    <div id="movie-modal" class="fixed inset-0 z-[100] hidden items-center justify-center p-4">
        <div class="absolute inset-0 bg-black/80 backdrop-blur-sm" onclick="closeModal()"></div>
        <div class="relative bg-slate-900 w-full max-w-4xl rounded-2xl overflow-hidden shadow-2xl modal-enter flex flex-col md:flex-row">
            <button onclick="closeModal()" class="absolute top-4 right-4 z-10 w-10 h-10 bg-black/50 hover:bg-black/80 rounded-full flex items-center justify-center transition">
                <i class="fas fa-times"></i>
            </button>
            <div id="modal-image-container" class="md:w-1/2 h-64 md:h-auto overflow-hidden">
                <img id="modal-image" src="" alt="" class="w-full h-full object-cover">
            </div>
            <div class="md:w-1/2 p-8 flex flex-col justify-center">
                <div id="modal-genre" class="text-indigo-400 text-sm font-bold mb-2 uppercase tracking-widest"></div>
                <h3 id="modal-title" class="text-3xl font-bold mb-4"></h3>
                <div class="flex items-center space-x-4 mb-6">
                    <span class="flex items-center text-yellow-500">
                        <i class="fas fa-star mr-1"></i>
                        <span id="modal-rating" class="font-bold"></span>
                    </span>
                    <span id="modal-year" class="text-slate-400"></span>
                </div>
                <p id="modal-desc" class="text-slate-300 leading-relaxed mb-8"></p>
                <div class="flex space-x-3">
                    <button class="flex-1 bg-indigo-600 hover:bg-indigo-700 py-3 rounded-xl font-bold transition">예고편 보기</button>
                    <button class="w-12 h-12 border border-slate-700 hover:bg-slate-800 rounded-xl flex items-center justify-center transition">
                        <i class="far fa-heart"></i>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 푸터 -->
    <footer class="bg-slate-950 border-t border-slate-900 py-12 px-6">
        <div class="max-w-7xl mx-auto text-center">
            <div class="flex justify-center space-x-6 mb-6">
                <a href="#" class="text-slate-500 hover:text-white transition"><i class="fab fa-instagram text-2xl"></i></a>
                <a href="#" class="text-slate-500 hover:text-white transition"><i class="fab fa-twitter text-2xl"></i></a>
                <a href="#" class="text-slate-500 hover:text-white transition"><i class="fab fa-youtube text-2xl"></i></a>
            </div>
            <p class="text-slate-600 text-sm">© 2024 CinePick. All rights reserved.</p>
        </div>
    </footer>

    <script>
        // 영화 데이터
        const movies = [
            {
                id: 1,
                title: "인터스텔라",
                genre: "SF",
                rating: 9.1,
                year: 2014,
                desc: "지구의 마지막 희망을 찾아 우주로 떠나는 탐험가들의 이야기를 그린 SF 대서사시.",
                image: "https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 2,
                title: "존 윅 4",
                genre: "액션",
                rating: 8.5,
                year: 2023,
                desc: "자유를 향한 마지막 전투. 존 윅은 전 세계의 킬러들과 맞서 싸우며 최고 회의를 무너뜨리려 합니다.",
                image: "https://images.unsplash.com/photo-1594909122845-11baa439b7bf?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 3,
                title: "너의 이름은.",
                genre: "애니메이션",
                rating: 8.9,
                year: 2016,
                desc: "몸이 바뀌어버린 두 소년 소녀가 서로를 찾아 나서는 기적 같은 사랑과 운명의 이야기.",
                image: "https://images.unsplash.com/photo-1578632292335-df3abbb0d586?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 4,
                title: "기생충",
                genre: "드라마",
                rating: 9.2,
                year: 2019,
                desc: "전혀 다른 두 가족의 만남이 걷잡을 수 없는 사건으로 번지는 사회적 풍자 스릴러.",
                image: "https://images.unsplash.com/photo-1593014631832-6a182062539f?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 5,
                title: "다크 나이트",
                genre: "액션",
                rating: 9.5,
                year: 2008,
                desc: "혼돈의 상징 조커에 맞서 도시를 구하려는 배트맨의 고뇌와 정의에 대한 질문.",
                image: "https://images.unsplash.com/photo-1509248961158-e54f6934749c?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 6,
                title: "인셉션",
                genre: "SF",
                rating: 9.0,
                year: 2010,
                desc: "타인의 꿈에 들어가 생각을 훔치거나 심는 정교한 작전을 수행하는 특수 요원들의 이야기.",
                image: "https://images.unsplash.com/photo-1626814026160-2237a95fc5a0?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 7,
                title: "조커",
                genre: "스릴러",
                rating: 8.7,
                year: 2019,
                desc: "고담시의 소외된 광대 아서 플렉이 어떻게 악의 화신 조커로 변해가는지 추적하는 심리극.",
                image: "https://images.unsplash.com/photo-1531259683007-016a7b628fc3?auto=format&fit=crop&q=80&w=800"
            },
            {
                id: 8,
                title: "어바웃 타임",
                genre: "드라마",
                rating: 8.4,
                year: 2013,
                desc: "시간을 되돌릴 수 있는 능력을 가진 남자가 진정한 행복과 사랑의 의미를 깨닫는 과정.",
                image: "https://images.unsplash.com/photo-1518709268805-4e9042af9f23?auto=format&fit=crop&q=80&w=800"
            }
        ];

        // 영화 렌더링 함수
        function renderMovies(movieList) {
            const grid = document.getElementById('movie-grid');
            grid.innerHTML = '';

            movieList.forEach(movie => {
                const card = document.createElement('div');
                card.className = 'movie-card bg-slate-900 rounded-2xl overflow-hidden cursor-pointer group';
                card.onclick = () => openModal(movie);
                
                card.innerHTML = `
                    <div class="relative h-72 overflow-hidden">
                        <img src="${movie.image}" alt="${movie.title}" class="w-full h-full object-cover transition duration-500 group-hover:scale-110">
                        <div class="absolute top-3 left-3 bg-indigo-600 px-3 py-1 rounded-lg text-xs font-bold uppercase tracking-wider">
                            ${movie.genre}
                        </div>
                    </div>
                    <div class="p-5">
                        <h4 class="text-xl font-bold mb-2 truncate">${movie.title}</h4>
                        <div class="flex justify-between items-center text-sm">
                            <span class="text-slate-400">${movie.year}</span>
                            <span class="text-yellow-500 font-bold">
                                <i class="fas fa-star mr-1"></i> ${movie.rating}
                            </span>
                        </div>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        // 필터링 함수
        function filterMovies(genre) {
            // 버튼 스타일 업데이트
            const buttons = document.querySelectorAll('.filter-btn');
            buttons.forEach(btn => {
                if(btn.innerText === genre) {
                    btn.classList.add('bg-indigo-600');
                    btn.classList.remove('bg-slate-800');
                } else {
                    btn.classList.add('bg-slate-800');
                    btn.classList.remove('bg-indigo-600');
                }
            });

            if (genre === '전체') {
                renderMovies(movies);
            } else {
                const filtered = movies.filter(m => m.genre === genre);
                renderMovies(filtered);
            }
        }

        // 모달 열기
        function openModal(movie) {
            document.getElementById('modal-title').innerText = movie.title;
            document.getElementById('modal-genre').innerText = movie.genre;
            document.getElementById('modal-rating').innerText = movie.rating;
            document.getElementById('modal-year').innerText = movie.year + "년 개봉";
            document.getElementById('modal-desc').innerText = movie.desc;
            document.getElementById('modal-image').src = movie.image;
            
            const modal = document.getElementById('movie-modal');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
            document.body.style.overflow = 'hidden';
        }

        // 모달 닫기
        function closeModal() {
            const modal = document.getElementById('movie-modal');
            modal.classList.add('hidden');
            modal.classList.remove('flex');
            document.body.style.overflow = 'auto';
        }

        // 스크롤 이동
        function scrollToMovies() {
            document.getElementById('movie-section').scrollIntoView({ behavior: 'smooth' });
        }

        // 초기 렌더링
        window.onload = () => {
            renderMovies(movies);
        };
    </script>
</body>
</html>
