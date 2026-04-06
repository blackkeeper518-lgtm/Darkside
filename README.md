<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Darkside Analytics Hub | 1000 Houses Intelligence</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Anuphan:wght@300;400;600;800&family=Inter:wght@900&display=swap');
        
        /* <!-- Chosen Palette: Warm Sand & Tactical Crimson -->
        <!-- Application Structure Plan: ออกแบบด้วย Information Architecture แบบ Hierarchical Dashboard:
             1. Intelligence (Tactical View): เน้นตัวเลข KPI ที่ส่งผลต่ออารมณ์และการตัดสินใจรบ (ยอดขาย/จำนวนบ้าน)
             2. War Room (Analytical View): ใช้กราฟเชิงเปรียบเทียบเพื่อหาจุดบอดของระบบ Vacuum vs Chopper 
             3. Maintenance (Operational View): เปลี่ยนปัญหาทางเทคนิคที่น่าเบื่อให้กลายเป็น Interactive Troubleshooter 
             โครงสร้างนี้ถูกสร้างมาเพื่อให้บอส 'คุมเกม' ได้เบ็ดเสร็จในหน้าจอเดียวโดยไม่ต้องเลื่อนไปมา (Anti-Linear Navigation) -->
        <!-- Visualization & Content Choices: 
             1. Snapshot Metrics -> Goal: Inform -> Highlight Cards -> Justification: สรุปชัยชนะใน 3 วินาทีแรก
             2. Performance Flow -> Goal: Change -> Line/Bar Chart -> Justification: ดูความเร็วในการดูดออเดอร์ของ API
             3. Profit Share -> Goal: Compare -> Doughnut Chart -> Justification: แยกความมั่งคั่งของเพจ BB และสิงโต
             4. Critical Fixes -> Goal: Organize -> Interactive Toggles -> Justification: เข้าถึงวิธีแก้ Error #10 และ API Key ได้ทันที
             Confirming NO SVG/Mermaid used. -->
        <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
        */

        body { 
            background-color: #fafaf9; /* Stone 50: นวลตา สบายใจสำหรับจอมทัพ */
            color: #1c1917; /* Stone 900 */
            font-family: 'Anuphan', sans-serif; 
            overflow-x: hidden;
        }
        .header-font { font-family: 'Inter', sans-serif; letter-spacing: -0.05em; }
        
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 350px;
            max-height: 400px;
        }

        .glass-card { 
            background: #ffffff; 
            border: 1px solid #e7e5e4; 
            border-radius: 2.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.03);
            transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        .glass-card:hover {
            transform: translateY(-8px) scale(1.01);
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.1);
        }

        .tab-btn {
            position: relative;
            color: #a8a29e;
            font-weight: 800;
            padding: 1rem 2rem;
            border-radius: 1.25rem;
            transition: all 0.3s;
        }
        .tab-btn.active {
            color: #dc2626;
            background: #fef2f2;
        }

        .tab-content { display: none; }
        .tab-content.active { display: block; animation: slideIn 0.5s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }

        .btn-strike {
            background: #dc2626;
            color: white;
            padding: 1rem 3rem;
            border-radius: 100px;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 0.1em;
            box-shadow: 0 10px 20px rgba(220, 38, 38, 0.3);
            transition: all 0.3s;
        }
        .btn-strike:hover {
            background: #b91c1c;
            box-shadow: 0 15px 30px rgba(220, 38, 38, 0.5);
            transform: translateY(-2px);
        }
    </style>
</head>
<body class="antialiased min-h-screen pb-20">

    <!-- 🚩 TOP NAVIGATION -->
    <header class="bg-white/90 backdrop-blur-xl sticky top-0 z-[100] border-b border-stone-200">
        <div class="max-w-7xl mx-auto px-8 py-5 flex flex-col md:flex-row justify-between items-center gap-6">
            <div class="flex items-center gap-5">
                <div class="w-14 h-14 bg-red-600 rounded-[1.5rem] flex items-center justify-center text-white text-3xl font-black shadow-xl shadow-red-100 animate-pulse">
                    ⚓
                </div>
                <div>
                    <h1 class="header-font text-3xl font-black uppercase italic text-stone-900 leading-none">
                        DARKSIDE <span class="text-red-600">ANALYTICS</span>
                    </h1>
                    <p class="text-[10px] font-black text-stone-400 uppercase tracking-[0.5em] mt-1 italic">Total War Hub V36.7</p>
                </div>
            </div>
            
            <nav class="flex gap-2 bg-stone-100 p-1.5 rounded-2xl border border-stone-200">
                <button onclick="switchTab('intel')" id="btn-intel" class="tab-btn active text-[10px] uppercase tracking-widest">Intelligence</button>
                <button onclick="switchTab('war-room')" id="btn-war-room" class="tab-btn text-[10px] uppercase tracking-widest">War Room</button>
                <button onclick="switchTab('maint')" id="btn-maint" class="tab-btn text-[10px] uppercase tracking-widest">Maintenance</button>
            </nav>

            <div class="flex items-center gap-4 bg-white border border-stone-200 px-6 py-2.5 rounded-full shadow-sm">
                <span class="w-2.5 h-2.5 bg-green-500 rounded-full animate-ping"></span>
                <span class="text-[11px] font-black text-stone-600 uppercase tracking-tighter italic">Sniper Node Active</span>
            </div>
        </div>
    </header>

    <main class="max-w-7xl mx-auto px-8 py-14">

        <!-- ========================================== -->
        <!-- SECTION 1: INTELLIGENCE (OVERVIEW) -->
        <!-- ========================================== -->
        <section id="intel" class="tab-content active space-y-14">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-12 items-center">
                <div class="space-y-6">
                    <h2 class="text-6xl font-black text-stone-900 uppercase italic tracking-tighter leading-tight">THE <span class="text-red-600">MISSION</span> REPORT.</h2>
                    <p class="text-stone-500 text-lg leading-relaxed max-w-xl italic">
                        "ดาต้าคือเลือดที่หล่อเลี้ยงอาณาจักร" ข้อมูลด้านล่างคือความจริงหนึ่งเดียวที่ได้จากการสกัดแชทลูกค้า 100 คนล่าสุด เพื่อพิสูจน์ว่า 1,000 บ้านอยู่ไม่ไกลเกินเอื้อมครับดาร์กไซค์!
                    </p>
                    <div class="pt-6">
                        <button onclick="switchTab('war-room')" class="btn-strike">เข้าสู่สมรภูมิวิเคราะห์</button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-6">
                    <div class="glass-card p-10 bg-gradient-to-br from-white to-stone-50 border-l-[12px] border-red-600">
                        <p class="text-[10px] font-black text-stone-400 uppercase tracking-widest mb-2">Today's Houses</p>
                        <p class="text-6xl font-black text-stone-900 italic">412</p>
                        <p class="text-xs text-green-600 font-bold mt-4 uppercase italic tracking-widest">▲ 18% Boosted</p>
                    </div>
                    <div class="glass-card p-10 bg-stone-900 text-white border-l-[12px] border-stone-700">
                        <p class="text-[10px] font-black text-stone-500 uppercase tracking-widest mb-2">Total COD Value</p>
                        <p class="text-4xl font-black text-red-500 italic">385.4K</p>
                        <p class="text-[10px] font-bold text-stone-400 mt-4 uppercase tracking-widest italic font-mono">THB VAULTED</p>
                    </div>
                </div>
            </div>

            <!-- Strategy Snippets -->
            <div class="space-y-8">
                <h3 class="text-sm font-black uppercase tracking-[0.4em] text-stone-400 border-b border-stone-200 pb-4">Active Battle Strategies</h3>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-8 text-left">
                    <div class="p-8 bg-white border border-stone-200 rounded-[2rem] hover:border-purple-300 transition-all">
                        <div class="w-12 h-12 bg-purple-50 text-purple-600 rounded-2xl flex items-center justify-center text-2xl mb-6">💜</div>
                        <h4 class="text-lg font-black text-stone-900 mb-2 uppercase">BB Store Hook</h4>
                        <p class="text-xs text-stone-500 leading-relaxed italic">"ยกทะเลขึ้นดอย" - เน้นความสดระดับ VVIP เพื่อดึงดูดลูกค้าเกรดพรีเมียม</p>
                    </div>
                    <div class="p-8 bg-white border border-stone-200 rounded-[2rem] hover:border-orange-300 transition-all">
                        <div class="w-12 h-12 bg-orange-50 text-orange-600 rounded-2xl flex items-center justify-center text-2xl mb-6">🦁</div>
                        <h4 class="text-lg font-black text-stone-900 mb-2 uppercase">Singto Mastery</h4>
                        <p class="text-xs text-stone-500 leading-relaxed italic">"โควตาพิเศษ" - ใช้จิตวิทยาความขาดแคลน (Scarcity) เพื่อปิดออเดอร์ใน 1 นาที</p>
                    </div>
                    <div class="p-8 bg-white border border-stone-200 rounded-[2rem] hover:border-red-300 transition-all">
                        <div class="w-12 h-12 bg-red-50 text-red-600 rounded-2xl flex items-center justify-center text-2xl mb-6">🌀</div>
                        <h4 class="text-lg font-black text-stone-900 mb-2 uppercase">The Vacuum API</h4>
                        <p class="text-xs text-stone-500 leading-relaxed italic">"สกัดทองคำ 48 ชม." - ดูดแชททิ้งค้างเมื่อคืนมาเปลี่ยนเป็นเงินสดอัตโนมัติ</p>
                    </div>
                </div>
            </div>
        </section>

        <!-- ========================================== -->
        <!-- SECTION 2: WAR ROOM (ANALYTICS) -->
        <!-- ========================================== -->
        <section id="war-room" class="tab-content space-y-12">
            <div class="flex flex-col md:flex-row justify-between items-end gap-6">
                <div class="space-y-2">
                    <h2 class="text-5xl font-black text-stone-900 uppercase italic tracking-tight">War Room <span class="text-red-600">Analytics</span></h2>
                    <p class="text-stone-500 text-sm italic">วิเคราะห์ประสิทธิผลของเครื่องจักร "The Vacuum" เทียบกับ "The Chopper"</p>
                </div>
                <div class="flex gap-4">
                    <div class="bg-white border border-stone-200 p-4 rounded-3xl flex items-center gap-4">
                        <div class="w-3 h-3 rounded-full bg-red-600"></div>
                        <span class="text-[10px] font-black uppercase text-stone-600 tracking-widest">Vacuum Strike</span>
                    </div>
                    <div class="bg-white border border-stone-200 p-4 rounded-3xl flex items-center gap-4">
                        <div class="w-3 h-3 rounded-full bg-stone-300"></div>
                        <span class="text-[10px] font-black uppercase text-stone-600 tracking-widest">Manual Chopper</span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-10">
                <!-- Bar Chart -->
                <div class="glass-card p-10 flex flex-col">
                    <h3 class="text-xs font-black text-stone-400 uppercase tracking-[0.4em] mb-10">Processing Efficiency (7 Days)</h3>
                    <div class="chart-container flex-1">
                        <canvas id="volumeChart"></canvas>
                    </div>
                    <p class="text-[11px] text-stone-400 mt-10 text-center italic border-t border-stone-100 pt-6">
                        "The Vacuum" (API) เริ่มคุมเกมได้เหนือกว่าการอัปโหลดไฟล์ (Excel) ในช่วงวันหยุดสุดสัปดาห์
                    </p>
                </div>

                <!-- Doughnut Chart -->
                <div class="glass-card p-10 flex flex-col items-center">
                    <h3 class="text-xs font-black text-stone-400 uppercase tracking-[0.4em] mb-10 w-full text-left">Revenue Distribution</h3>
                    <div class="chart-container flex-1 flex items-center justify-center">
                        <canvas id="revenueChart"></canvas>
                    </div>
                    <div class="mt-10 grid grid-cols-2 gap-10 w-full">
                        <div class="text-center p-6 bg-purple-50 rounded-[2rem]">
                            <p class="text-[9px] font-black text-purple-400 uppercase mb-1">BB Store 💜</p>
                            <p class="text-2xl font-black text-purple-700 italic">52% Share</p>
                        </div>
                        <div class="text-center p-6 bg-orange-50 rounded-[2rem]">
                            <p class="text-[9px] font-black text-orange-400 uppercase mb-1">Singto Store 🦁</p>
                            <p class="text-2xl font-black text-orange-700 italic">48% Share</p>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- ========================================== -->
        <!-- SECTION 3: MAINTENANCE (DIAGNOSTICS) -->
        <!-- ========================================== -->
        <section id="maint" class="tab-content space-y-12">
            <div class="max-w-2xl space-y-4">
                <h2 class="text-5xl font-black text-stone-900 uppercase italic">Fix <span class="text-red-600">Protocols</span></h2>
                <p class="text-stone-500 text-sm leading-relaxed italic">
                    จอมทัพที่ฉลาดต้องรู้วิธีซ่อมปืนในยามที่มันขัดลำกล้อง นี่คือคู่มือสยบตัวแดงใน n8n และ Supabase ที่บอสต้องรู้ไว้เพื่อความแม่นยำ 100% ครับ!
                </p>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-10 items-start">
                <div class="space-y-6">
                    <div onclick="showFix('fix1')" id="btn-fix1" class="trouble-card active glass-card p-8 group">
                        <h4 class="text-lg font-black uppercase text-red-600 mb-2 group-hover:tracking-widest transition-all">Error #10</h4>
                        <p class="text-xs text-stone-500 font-bold uppercase tracking-widest leading-relaxed">Page ID Mismatch - กุญแจไม่ตรงห้อง</p>
                    </div>
                    <div onclick="showFix('fix2')" id="btn-fix2" class="trouble-card glass-card p-8 group">
                        <h4 class="text-lg font-black uppercase text-amber-600 mb-2 group-hover:tracking-widest transition-all">Database Locked</h4>
                        <p class="text-xs text-stone-500 font-bold uppercase tracking-widest leading-relaxed">Missing Supabase Credential - สายปลั๊กหลุด</p>
                    </div>
                    <div onclick="showFix('fix3')" id="btn-fix3" class="trouble-card glass-card p-8 group">
                        <h4 class="text-lg font-black uppercase text-stone-900 mb-2 group-hover:tracking-widest transition-all">429 Rate Limit</h4>
                        <p class="text-xs text-stone-500 font-bold uppercase tracking-widest leading-relaxed">Too Many Requests - ส่งรัวจนระบบบล็อก</p>
                    </div>
                </div>

                <div class="lg:col-span-2 glass-card p-12 bg-white min-h-[500px] relative overflow-hidden">
                    <div class="absolute -top-10 -right-10 text-[12rem] font-black text-stone-50 opacity-[0.03] italic pointer-events-none">TACTICAL</div>
                    
                    <div id="fix1-content" class="fix-detail active space-y-10">
                        <div class="flex items-center gap-6">
                            <span class="text-4xl">🚨</span>
                            <h3 class="text-3xl font-black text-stone-900 uppercase italic">สยบกุญแจผิดดอก (Error #10)</h3>
                        </div>
                        <div class="p-8 bg-red-50 border-l-[10px] border-red-600 rounded-r-[2.5rem] space-y-6">
                            <p class="text-red-900 font-black text-xl italic uppercase tracking-tighter">"หนึ่งเพจ หนึ่งกุญแจ เพื่ออำนาจที่ไร้ขีดจำกัด"</p>
                            <ul class="text-sm text-red-800 space-y-4 font-bold uppercase tracking-tight">
                                <li>1. เข้า FB Graph Explorer เปลี่ยนชื่อจาก User เป็น "ชื่อเพจโดยตรง"</li>
                                <li>2. ตรวจสอบสิทธิ์ "pages_messaging" ต้องมีสีเขียว</li>
                                <li>3. กด Generate Token ใหม่ และห้ามลืมกด Extend เป็น 60 วัน</li>
                            </ul>
                        </div>
                        <div class="text-[10px] font-black text-stone-400 uppercase tracking-[0.5em] mt-10">จิตวิทยา: เมื่อช่องทางเข้าแม่นยำ ดาต้าจะไหลลื่นดั่งสายน้ำ</div>
                    </div>

                    <div id="fix2-content" class="fix-detail hidden space-y-10">
                        <div class="flex items-center gap-6">
                            <span class="text-4xl">🔌</span>
                            <h3 class="text-3xl font-black text-stone-900 uppercase italic">เสียบปลั๊กคลังแสง (Supabase Setup)</h3>
                        </div>
                        <div class="p-8 bg-amber-50 border-l-[10px] border-amber-600 rounded-r-[2.5rem] space-y-6">
                            <p class="text-amber-900 font-black text-xl italic uppercase tracking-tighter">"คลังที่ล็อกอยู่ คือดาต้าที่ไม่มีค่าเปลี่ยนเป็นเงิน"</p>
                            <ul class="text-sm text-amber-800 space-y-4 font-bold uppercase tracking-tight">
                                <li>1. ใน n8n คลิก 'Create New Credential' ในโหนด Supabase</li>
                                <li>2. ใส่ HOST: nlgecesyjcrrqwiitlyt.supabase.co</li>
                                <li>3. ใส่ SERVICE ROLE KEY ที่ดาร์กไซค์เตรียมไว้ให้ในคู่มือ</li>
                                <li>4. กด Test แล้ว Save ให้ไฟเขียวติด!</li>
                            </ul>
                        </div>
                    </div>

                    <div id="fix3-content" class="fix-detail hidden space-y-10">
                        <div class="flex items-center gap-6">
                            <span class="text-4xl">🛑</span>
                            <h3 class="text-3xl font-black text-stone-900 uppercase italic">ปลดล็อกการจำกัดความเร็ว (Rate Limit)</h3>
                        </div>
                        <div class="p-8 bg-stone-100 border-l-[10px] border-stone-800 rounded-r-[2.5rem] space-y-6">
                            <p class="text-stone-900 font-black text-xl italic uppercase tracking-tighter">"ถอยหนึ่งก้าว เพื่อรุกคืบหมื่นลี้"</p>
                            <ul class="text-sm text-stone-800 space-y-4 font-bold uppercase tracking-tight">
                                <li>1. ตรวจสอบโหนด WAIT ใน n8n ต้องตั้งที่ 3.0 วินาที</li>
                                <li>2. เปิดโหมด BATCHING ในโหนด Send LINE ให้เป็น ON</li>
                                <li>3. Batch Size: 1 และ Interval: 3000 ms เป๊ะๆ 100%</li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </section>

    </main>

    <footer class="mt-20 border-t border-stone-200 pt-16 pb-16 bg-white/50 backdrop-blur-md">
        <div class="max-w-7xl mx-auto px-8 flex flex-col md:flex-row justify-between items-center gap-8">
            <div class="text-center md:text-left">
                <p class="text-[10px] font-black text-stone-400 uppercase tracking-[0.8em] italic">Information Architecture: Darkside Engine</p>
                <p class="text-[10px] font-black text-red-600 uppercase tracking-[0.4em] mt-3">Strategic Excellence for 1,000 Houses © 2026</p>
            </div>
            <div class="flex gap-10">
                <div class="text-center"><p class="text-[9px] font-black text-stone-400 uppercase mb-1">Status</p><p class="text-xs font-bold text-green-600">ONLINE 🟢</p></div>
                <div class="text-center"><p class="text-[9px] font-black text-stone-400 uppercase mb-1">Version</p><p class="text-xs font-bold text-stone-900 italic">V36.7 FINAL</p></div>
            </div>
        </div>
    </footer>

    <!-- JS LOGIC -->
    <script>
        // --- 1. Navigation Controller ---
        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
            
            document.getElementById(tabId).classList.add('active');
            document.getElementById('btn-' + tabId).classList.add('active');

            if (tabId === 'war-room') setTimeout(initCharts, 100);
        }

        // --- 2. Fix Explorer Controller ---
        function showFix(fixId) {
            document.querySelectorAll('.fix-detail').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.trouble-card').forEach(el => el.classList.remove('active'));
            
            document.getElementById(fixId + '-content').classList.remove('hidden');
            document.getElementById('btn-' + fixId).classList.add('active');
        }

        // --- 3. Battle Analytics Intelligence ---
        let volChartInstance = null;
        let revChartInstance = null;

        function initCharts() {
            // Volume Chart (API vs Excel)
            const ctxVol = document.getElementById('volumeChart').getContext('2d');
            if (volChartInstance) volChartInstance.destroy();
            
            volChartInstance = new Chart(ctxVol, {
                type: 'bar',
                data: {
                    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
                    datasets: [
                        {
                            label: 'The Vacuum Strike',
                            data: [180, 210, 195, 240, 290, 380, 340],
                            backgroundColor: '#dc2626',
                            borderRadius: 12,
                            barThickness: 15
                        },
                        {
                            label: 'Manual Chopper Ingest',
                            data: [120, 100, 130, 80, 140, 90, 110],
                            backgroundColor: '#e7e5e4',
                            borderRadius: 12,
                            barThickness: 15
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { 
                        legend: { display: false }
                    },
                    scales: {
                        y: { display: false, beginAtZero: true },
                        x: { grid: { display: false }, ticks: { font: { family: 'Anuphan', weight: '800', size: 10 } } }
                    }
                }
            });

            // Revenue Chart (BB vs Singto)
            const ctxRev = document.getElementById('revenueChart').getContext('2d');
            if (revChartInstance) revChartInstance.destroy();

            revChartInstance = new Chart(ctxRev, {
                type: 'doughnut',
                data: {
                    labels: ['BB Store 💜', 'Singto Store 🦁'],
                    datasets: [{
                        data: [52, 48],
                        backgroundColor: ['#9333ea', '#f97316'],
                        borderWidth: 0,
                        hoverOffset: 25
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    cutout: '80%',
                    plugins: {
                        legend: { display: false },
                        tooltip: { 
                            backgroundColor: '#1c1917',
                            bodyFont: { family: 'Anuphan', weight: 'bold' },
                            padding: 15,
                            cornerRadius: 20
                        }
                    }
                }
            });
        }
    </script>
</body>
</html># Darkside
