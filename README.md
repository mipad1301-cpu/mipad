<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Психолог Онлайн | Путь к Гармонии</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            overflow-x: hidden;
            touch-action: manipulation;
        }
        #canvas-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            pointer-events: none;
        }
        .glass-card {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .btn-glow {
            box-shadow: 0 0 20px rgba(168, 85, 247, 0.4);
        }
    </style>
</head>
<body class="relative min-h-screen flex flex-col justify-between">

    <!-- 3D Canvas Background -->
    <div id="canvas-container"></div>

    <!-- Main Content -->
    <main class="relative z-10 px-4 pt-8 pb-12 max-w-md mx-auto flex-grow flex flex-col justify-center">
        
        <!-- Header -->
        <header class="text-center mb-6">
            <span class="text-xs uppercase tracking-widest text-purple-400 font-semibold bg-purple-950/60 px-3 py-1 rounded-full border border-purple-800/50">
                Онлайн-консультации
            </span>
            <h1 class="text-3xl font-bold mt-4 bg-gradient-to-r from-purple-200 via-pink-200 to-indigo-200 bg-clip-text text-transparent">
                Обретите внутренний баланс
            </h1>
            <p class="text-slate-400 text-sm mt-2 leading-relaxed">
                Безопасное пространство для работы с тревогой, выгоранием и поиском себя.
            </p>
        </header>

        <!-- Benefits Grid -->
        <div class="grid grid-cols-2 gap-3 mb-6">
            <div class="glass-card p-3 rounded-2xl text-center">
                <div class="text-purple-400 text-xl font-bold mb-1">100%</div>
                <div class="text-xs text-slate-300">Конфиденциально</div>
            </div>
            <div class="glass-card p-3 rounded-2xl text-center">
                <div class="text-pink-400 text-xl font-bold mb-1">50 мин</div>
                <div class="text-xs text-slate-300">Длительность сессии</div>
            </div>
        </div>

        <!-- Form Card -->
        <div class="glass-card p-6 rounded-3xl shadow-2xl">
            <h2 class="text-lg font-semibold text-center mb-4 text-white">Запись на консультацию</h2>
            
            <form id="tgForm" class="space-y-4">
                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Ваше имя</label>
                    <input type="text" id="name" required placeholder="Анна" 
                        class="w-full px-4 py-3 rounded-xl bg-slate-900/60 border border-slate-700 text-white placeholder-slate-500 focus:outline-none focus:border-purple-500 text-sm transition">
                </div>

                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Телефон или @telegram</label>
                    <input type="text" id="contact" required placeholder="@username или +7..." 
                        class="w-full px-4 py-3 rounded-xl bg-slate-900/60 border border-slate-700 text-white placeholder-slate-500 focus:outline-none focus:border-purple-500 text-sm transition">
                </div>

                <div>
                    <label class="block text-xs font-medium text-slate-300 mb-1">Что вас беспокоит? (кратко)</label>
                    <textarea id="message" rows="2" placeholder="Тревожность, отношение к себе..." 
                        class="w-full px-4 py-3 rounded-xl bg-slate-900/60 border border-slate-700 text-white placeholder-slate-500 focus:outline-none focus:border-purple-500 text-sm transition"></textarea>
                </div>

                <button type="submit" id="submitBtn" 
                    class="w-full py-4 rounded-xl bg-gradient-to-r from-purple-600 to-pink-600 text-white font-medium text-sm btn-glow active:scale-95 transition duration-200 flex items-center justify-center">
                    <span>Записаться на сеанс</span>
                </button>
            </form>

            <div id="statusMessage" class="hidden text-center text-xs mt-3 p-2 rounded-lg"></div>
        </div>

        <!-- Footer Info -->
        <footer class="text-center mt-6 text-xs text-slate-500">
            <p>Бот для связи: <a href="https://t.me/marina_wsh_bot" class="text-purple-400 underline">@marina_wsh_bot</a></p>
        </footer>
    </main>

    <!-- 3D Logic & Form Handler -->
    <script>
        // === НАСТРОЙКА TELEGRAM БОТА ===
        // 1. Вставьте токен вашего бота от @BotFather
        const TELEGRAM_BOT_TOKEN = 'YOUR_BOT_TOKEN_HERE'; 
        // 2. Вставьте ваш ваш личный Chat ID (узнать в @userinfobot)
        const TELEGRAM_CHAT_ID = 'YOUR_CHAT_ID_HERE'; 

        // === TELEGRAM FORM SUBMIT ===
        const form = document.getElementById('tgForm');
        const submitBtn = document.getElementById('submitBtn');
        const statusMessage = document.getElementById('statusMessage');

        form.addEventListener('submit', async (e) => {
            e.preventDefault();
            
            submitBtn.disabled = true;
            submitBtn.innerText = 'Отправка...';

            const name = document.getElementById('name').value;
            const contact = document.getElementById('contact').value;
            const message = document.getElementById('message').value;

            const text = `🧠 *Новая запись на психотерапию!*\n\n` +
                         `👤 *Имя:* ${name}\n` +
                         `📱 *Контакты:* ${contact}\n` +
                         `💬 *Запрос:* ${message || 'Не указан'}`;

            try {
                const response = await fetch(`https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: TELEGRAM_CHAT_ID,
                        text: text,
                        parse_mode: 'Markdown'
                    })
                });

                if (response.ok) {
                    showStatus('Заявка успешно отправлена! Я свяжусь с вами.', 'bg-green-900/50 text-green-300 border border-green-700');
                    form.reset();
                } else {
                    throw new Error();
                }
            } catch (error) {
                showStatus('Ошибка отправки. Напишите напрямую в бота @marina_wsh_bot', 'bg-red-900/50 text-red-300 border border-red-700');
            } finally {
                submitBtn.disabled = false;
                submitBtn.innerText = 'Записаться на сеанс';
            }
        });

        function showStatus(text, classes) {
            statusMessage.className = `text-center text-xs mt-3 p-3 rounded-xl ${classes}`;
            statusMessage.innerText = text;
            statusMessage.classList.remove('hidden');
        }

        // === ОПТИМИЗИРОВАННАЯ 3D СЦЕНА (Three.js) ===
        const container = document.getElementById('canvas-container');
        
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        camera.position.z = 3.2;

        // Renderer с ограничениями производительности для мобилок
        const renderer = new THREE.WebGLRenderer({ antialias: false, alpha: true, powerPreference: "high-performance" });
        renderer.setSize(window.innerWidth, window.innerHeight);
        // Ограничиваем PixelRatio до 1.5, чтобы не перегружать 4K/Retina экраны телефонов
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));
        container.appendChild(renderer.domElement);

        // Геометрия сферы (Низкополигональная для скорости)
        const geometry = new THREE.IcosahedronGeometry(1.5, 3);
        
        // Материал (мягкийWireframe переливающийся)
        const material = new THREE.MeshBasicMaterial({
            color: 0xa855f7,
            wireframe: true,
            transparent: true,
            opacity: 0.25
        });

        const sphere = new THREE.Mesh(geometry, material);
        scene.add(sphere);

        // Сохраняем изначальные вершины для волновой анимации
        const count = geometry.attributes.position.count;
        const initialPositions = new Float32Array(geometry.attributes.position.array);

        // Анимация "Дыхания" (Психологический эффект релаксации)
        let clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);

            const elapsedTime = clock.getElapsedTime();

            // Вращение
            sphere.rotation.y = elapsedTime * 0.1;
            sphere.rotation.x = elapsedTime * 0.05;

            // Волновой эффект сферы
            const positions = geometry.attributes.position.array;
            for (let i = 0; i < count; i++) {
                const u = i * 3;
                const x = initialPositions[u];
                const y = initialPositions[u + 1];
                const z = initialPositions[u + 2];

                // Формула плавных волн
                const wave = Math.sin(elapsedTime * 1.5 + x * 2 + y * 2) * 0.08;
                
                positions[u] = x + x * wave;
                positions[u + 1] = y + y * wave;
                positions[u + 2] = z + z * wave;
            }
            geometry.attributes.position.needsUpdate = true;

            renderer.render(scene, camera);
        }

        animate();

        // Оптимизированный обработчик ресайза
        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
