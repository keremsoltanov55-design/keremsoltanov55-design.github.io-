<!DOCTYPE html>
<html lang="az">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tələbə Bələdçisi & Bal Hesablayıcı</title>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        :root { 
            --primary: #38bdf8; --bg: #0f172a; --card: #1e293b; --text: #f8fafc; 
            --accent: #94a3b8; --danger: #f87171; --success: #10b981; 
            --gold: #fbbf24;
        }
        .light-mode {
            --bg: #f1f5f9; --card: #ffffff; --text: #1e293b; --accent: #64748b;
        }
        
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: var(--bg); color: var(--text); display: flex; flex-direction: column; align-items: center; padding: 20px; margin: 0; min-height: 100vh; transition: 0.3s; overflow-x: hidden; }

        .sidebar { position: fixed; left: -110%; top: 0; width: 290px; height: 100%; background: var(--card); border-right: none; transition: 0.4s cubic-bezier(0.4, 0, 0.2, 1); z-index: 1005; padding: 25px 20px; overflow-y: auto; }
        .sidebar.active { left: 0; box-shadow: 15px 0 40px rgba(0,0,0,0.6); border-right: 1px solid rgba(56, 189, 248, 0.3); }
        .sidebar-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); display: none; z-index: 1004; backdrop-filter: blur(4px); }
        .sidebar-overlay.active { display: block; }

        .top-btns { position: fixed; top: 15px; width: 90%; max-width: 500px; display: flex; justify-content: space-between; z-index: 1001; }
        .control-btn { background: var(--primary); color: #0f172a; border: none; padding: 12px 18px; border-radius: 12px; font-weight: 800; cursor: pointer; box-shadow: 0 4px 15px rgba(56, 189, 248, 0.3); }
        
        .app-card { width: 100%; max-width: 500px; background: var(--card); padding: 25px; border-radius: 24px; box-shadow: 0 20px 40px rgba(0,0,0,0.3); border: 1px solid rgba(148, 163, 184, 0.1); margin-top: 70px; box-sizing: border-box; }
        
        h1 { text-align: center; color: var(--primary); margin: 0; font-size: 22px; text-transform: uppercase; letter-spacing: 1px; }
        .sub-title { text-align: center; font-size: 11px; color: var(--accent); margin-bottom: 25px; }
        h3 { color: var(--accent); font-size: 13px; margin: 15px 0 10px; border-bottom: 1px solid var(--accent); padding-bottom: 5px; text-transform: uppercase; }
        
        input, select { width: 100%; padding: 12px; border-radius: 10px; border: 1px solid var(--accent); background: rgba(15, 23, 42, 0.05); color: var(--text); box-sizing: border-box; font-size: 15px; outline: none; margin-bottom: 10px; }

        .quick-select { display: flex; gap: 5px; margin-bottom: 10px; flex-wrap: wrap; }
        .q-btn { background: rgba(148, 163, 184, 0.1); color: var(--text); padding: 6px 10px; border-radius: 8px; font-size: 12px; border: 1px solid rgba(148, 163, 184, 0.2); cursor: pointer; }

        .row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
        .dynamic-inputs { display: grid; grid-template-columns: repeat(auto-fill, minmax(85px, 1fr)); gap: 8px; }

        .btn-group { display: grid; grid-template-columns: 2fr 1fr; gap: 10px; margin-top: 20px; }
        .btn-calc { background: var(--primary); color: #0f172a; }
        .btn-reset { background: #334155; color: white; }
        button { padding: 15px; border-radius: 12px; font-weight: 800; cursor: pointer; border: none; transition: 0.2s; }

        .result-box { margin-top: 20px; padding: 20px; border-radius: 15px; text-align: center; display: none; background: rgba(56, 189, 248, 0.1); border: 1px solid var(--primary); }
        .motivation-msg { margin: 10px 0; font-weight: bold; font-size: 14px; color: var(--gold); }
        .whatsapp-btn { background: #25d366; color: white; width: 100%; margin-top: 15px; font-size: 14px; }

        .info-group { margin-bottom: 10px; border-radius: 8px; overflow: hidden; background: #1e293b; border: 1px solid #334155; }
        .info-header { padding: 12px 15px; background: #1e293b; cursor: pointer; display: flex; justify-content: space-between; align-items: center; font-size: 14px; font-weight: 600; color: var(--primary); }
        .info-content { padding: 0 15px; max-height: 0; overflow: hidden; transition: 0.3s ease-out; background: #0f172a; font-size: 13px; color: #cbd5e1; }
        .info-group.open .info-content { padding: 15px; max-height: 1000px; }
        
        .price-table { width: 100%; border-collapse: collapse; margin: 10px 0; }
        .price-table td { padding: 8px; border-bottom: 1px solid #334155; }
        .price-table b { color: var(--success); }

        .footer { margin-top: auto; padding: 30px 0; text-align: center; font-size: 13px; color: var(--accent); width: 100%; }
        .footer b a { color: var(--primary); text-decoration: none; }
    </style>
</head>
<body onload="initApp()">

<div class="top-btns">
    <button class="control-btn" onclick="toggleSidebar()">☰ Menyu</button>
    <button class="control-btn" onclick="toggleTheme()" id="themeBtn">🌙</button>
</div>

<div class="sidebar-overlay" id="overlay" onclick="toggleSidebar()"></div>

<div class="sidebar" id="sidebar">
    <h2 style="color: var(--primary); font-size: 20px; margin-bottom: 20px;">Məlumat Mərkəzi</h2>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">ÜOMG Hesablayıcı 📈 <span>+</span></div>
        <div class="info-content">
            <div id="uomgFields"><div class="uomg-input-row" style="display:flex; gap:5px; margin-bottom:8px;"><input type="number" class="uomg-kredit" placeholder="Kredit"><input type="number" class="uomg-bal" placeholder="Bal"></div></div>
            <button onclick="addUomgField()" style="width:100%; background:#334155; color:white; padding:10px; border-radius:8px; margin-bottom:5px; cursor:pointer; border:none;">+ Fənn Əlavə Et</button>
            <button onclick="calculateUOMG()" style="width:100%; background:var(--primary); color:#0f172a; padding:10px; border-radius:8px; font-weight:bold; cursor:pointer; border:none;">ÜOMG Hesabla</button>
            <div id="uomgResult" style="margin-top:10px; font-weight:bold; color:var(--primary); text-align:center;"></div>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Hesablama Qaydası ⚙ <span>+</span></div>
        <div class="info-content">
            <p>Giriş balı aşağıdakı düsturla hesablanır:</p>
            <p>1. <b>Seminar:</b> Ortalaması tapılır və <b>0.4</b>-ə vurulur.</p>
            <p>2. <b>Kollokvium:</b> Ortalaması tapılır və <b>0.6</b>-ə vurulur.</p>
            <p>3. Bu iki nəticə toplanır və <b>3</b>-ə vurulur (maksimum 30 bal).</p>
            <p>4. Üzərinə <b>Davamiyyət</b> (maksimum 10 bal) və <b>Sərbəst iş</b> (maksimum 10 bal) əlavə edilir.</p>
            <p><b>Yekun Giriş:</b> Maksimum 50 bal ola bilər.</p>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Kimlər Təqaüd Ala Bilər? <span>+</span></div>
        <div class="info-content">
            <p>• <b>Əlaçı:</b> Bütün fənlərdən <b>A (91-100)</b> almış tələbələrə verilir.</p>
            <p>• <b>Həvəsləndirici:</b> Ən azı bir fəndən <b>A (91-100)</b>, digər bütün fənlərdən isə ən azı <b>C (71-80)</b> almış tələbələrə verilir.</p>
            <p>• <b>Dövlət sifarişli:</b> I semestr hər kəs alır. Sonra kəsrsiz bitirmək şərtilə müsabiqə yolu ilə verilir.</p>
            <p>• <b>Ödənişli:</b> I semestr təqaüd yoxdur. II semestrdən etibarən kəsrsiz bitirənlər müsabiqədə iştirak edə bilər.</p>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Təqaüd Məbləğləri <span>+</span></div>
        <div class="info-content">
            <p><b>Bakalavriat:</b></p>
            <table class="price-table">
                <tr><td>Əlaçı</td><td><b>200 AZN</b></td></tr>
                <tr><td>Həvəsləndirici</td><td><b>160 AZN</b></td></tr>
                <tr><td>Adi</td><td><b>110 AZN</b></td></tr>
            </table>
            <p><b>Magistratura:</b></p>
            <table class="price-table">
                <tr><td>Əlaçı</td><td><b>240 AZN</b></td></tr>
                <tr><td>Həvəsləndirici</td><td><b>180 AZN</b></td></tr>
                <tr><td>Adi</td><td><b>120 AZN</b></td></tr>
            </table>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Akademik Borc (Kəsr) & Limit <span>+</span></div>
        <div class="info-content">
            <b>Kəsr nə vaxt yaranır?</b>
            <ul>
                <li>Davamiyyət limiti (25%) keçildikdə.</li>
                <li>İmtahan balı 17-dən az olduqda.</li>
                <li>Ümumi nəticə 51 balı keçmədikdə.</li>
            </ul>
            <p><b>Kəsr bağlamaq:</b> Fənnin 25%-ni ödəyib təkrar imtahan vermək (ən çox 2 fənn) və ya yay semestrində dərsi yenidən dinləmək mümkündür.</p>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Məxfilik və Hüquqlar ⚖ <span>+</span></div>
        <div class="info-content">
            <p><b>Məxfilik:</b> Bu sayt heç bir şəxsi məlumatı toplamır. Hesablamalar tam anonimdir.</p>
            <p><b>Müəllif Hüquqları:</b> Dizayn və kod strukturu <b>Kərəm Soltanov</b> tərəfindən hazırlanmışdır.</p>
            <p><b>Məsuliyyət:</b> Hesablamalar rəsmi qaydalara əsaslanır, lakin yekun nəticə üçün universitet portalınıza istinad edin.</p>
        </div>
    </div>

    <div class="info-group">
        <div class="info-header" onclick="toggleAccordion(this)">Faydalı Linklər 🔗 <span>+</span></div>
        <div class="info-content">
            <a href="https://portal.edu.az" target="_blank" style="display:block; padding:8px; color:var(--primary); text-decoration:none;">Təhsil Portalı (Portal.edu.az)</a>
            <a href="https://e-telebe.edu.az" target="_blank" style="display:block; padding:8px; color:var(--primary); text-decoration:none;">e-Tələbə Portalı</a>
        </div>
    </div>
</div>

<div class="app-card">
    <h1>BAL HESABLAYICI</h1>
    <div class="sub-title">Sürətli Seçim & Motivasiya ilə</div>

    <div class="row">
        <div>
            <label style="font-size:11px; font-weight:bold; color:var(--accent)">Fənn Saatı</label>
            <select id="hours">
                <option value="75" selected>75 Saat</option><option value="135">135 Saat</option>
                <option value="120">120 Saat</option><option value="90">90 Saat</option>
                <option value="60">60 Saat</option><option value="45">45 Saat</option>
            </select>
        </div>
        <div>
            <label style="font-size:11px; font-weight:bold; color:var(--accent)">Qayıb Sayı</label>
            <input type="number" id="qb" placeholder="0" inputmode="numeric">
        </div>
    </div>

    <h3>Kollokvium</h3>
    <div class="quick-select">
        <button class="q-btn" onclick="setQuick('koll', 1)">1 Koll.</button>
        <button class="q-btn" onclick="setQuick('koll', 2)">2 Koll.</button>
        <button class="q-btn" onclick="setQuick('koll', 3)">3 Koll.</button>
    </div>
    <div id="kollInputs" class="dynamic-inputs" style="margin-bottom:10px;"><input type="number" class="koll-val" placeholder="Bal 1"></div>

    <h3>Seminar</h3>
    <div class="quick-select">
        <button class="q-btn" onclick="setQuick('sem', 1)">1 Sem.</button>
        <button class="q-btn" onclick="setQuick('sem', 2)">2 Sem.</button>
        <button class="q-btn" onclick="setQuick('sem', 3)">3 Sem.</button>
        <button class="q-btn" onclick="setQuick('sem', 4)">4 Sem.</button>
        <button class="q-btn" onclick="setQuick('sem', 5)">5 Sem.</button>
    </div>
    <div id="semInputs" class="dynamic-inputs" style="margin-bottom:10px;"><input type="number" class="sem-val" placeholder="Bal 1"></div>

    <h3>Sərbəst İş</h3>
    <input type="number" id="serb" placeholder="0 - 10 bal" step="0.1">

    <div class="btn-group">
        <button class="btn-calc" onclick="calculate()">HESABLA 🔥</button>
        <button class="btn-reset" onclick="resetAll()">SIFIRLA</button>
    </div>

    <div id="result" class="result-box">
        <div id="entryDisp" style="font-weight:bold;"></div>
        <div id="motivationMsg" class="motivation-msg"></div>
        <div id="targets" class="targets-grid" style="display:grid; grid-template-columns:1fr 1fr; gap:10px; margin-top:10px;"></div>
        <div id="finalSection" style="margin-top:15px; border-top:1px solid var(--accent); padding-top:15px; display:none;">
            <label style="color:var(--primary); font-weight:bold;">İmtahan Balını Yoxla:</label>
            <input type="number" id="examProb" oninput="showFinal()" placeholder="Məs: 35">
            <div id="finalGrade" style="margin-top:10px; font-weight:bold; font-size:18px;"></div>
        </div>
        <button class="whatsapp-btn" onclick="shareWhatsApp()" style="padding:12px; border-radius:10px; border:none; cursor:pointer; font-weight:bold;">WhatsApp ilə Paylaş 💬</button>
    </div>
</div>

<div class="footer">
    &copy; 2025 - Hazırladı: <b><a href="https://instagram.com/kerems0ltan0v" target="_blank">Kərəm Soltanov</a></b>
</div>

<script>
    let currentEntry = 0;

    function initApp() {
        if(localStorage.getItem('theme') === 'light') toggleTheme(true);
    }

    function toggleTheme(init = false) {
        if(!init) {
            document.body.classList.toggle('light-mode');
            localStorage.setItem('theme', document.body.classList.contains('light-mode') ? 'light' : 'dark');
        } else { document.body.classList.add('light-mode'); }
        document.getElementById('themeBtn').innerText = document.body.classList.contains('light-mode') ? "☀" : "🌙";
    }

    function toggleSidebar() { 
        document.getElementById('sidebar').classList.toggle('active'); 
        document.getElementById('overlay').classList.toggle('active'); 
    }
    function toggleAccordion(h) { h.parentElement.classList.toggle('open'); }

    function setQuick(type, count) {
        const cont = document.getElementById(type+'Inputs');
        cont.innerHTML = '';
        for(let i=1; i<=count; i++) {
            const inp = document.createElement('input'); inp.type = 'number'; inp.className = type+'-val'; inp.placeholder = (type==='koll'?'Koll ':'Sem ')+i;
            cont.appendChild(inp);
        }
    }

    const attendanceTable = { "135":[10,9.85,9.7,9.55,9.41,9.26,9.11,8.96,8.81,8.66,8.52,8.37,8.22,8.07,7.93,7.77,7.63], "120":[10,9.83,9.66,9.5,9.33,9.16,9,8.83,8.66,8.5,8.33,8.16,8,7.83,7.66,7.5], "90":[10,9.77,9.55,9.33,9.11,8.88,8.66,8.44,8.22,8,7.77,7.55], "75":[10,9.73,9.46,9.2,8.93,8.66,8.4,8.13,7.86,7.6], "60":[10,9.66,9.33,9,8.66,8.33,8,7.66], "45":[10,9.55,9.11,8.66,8.22,7.77], "30":[10,9.33,8.66,8] };

    function calculate() {
        const h = document.getElementById('hours').value;
        const q = parseInt(document.getElementById('qb').value) || 0;
        let s = parseFloat(document.getElementById('serb').value) || 0;
        const qList = attendanceTable[h] || [10];
        const resBox = document.getElementById('result');

        if(q >= qList.length) {
            resBox.style.display = "block";
            resBox.innerHTML = "<b style='color:var(--danger)'>❌ LİMİT KEÇİLDİ (KƏSR)</b>";
            return;
        }

        const getAvg = (cls) => {
            const inps = document.getElementsByClassName(cls);
            let sum = 0, count = 0;
            for(let i=0; i<inps.length; i++) { if(inps[i].value !== "") { sum += parseFloat(inps[i].value); count++; } }
            return count > 0 ? sum / count : 0;
        };

        currentEntry = ((getAvg('sem-val') * 0.4) + (getAvg('koll-val') * 0.6)) * 3 + qList[q] + (s > 10 ? 10 : s);
        if(currentEntry > 50) currentEntry = 50;

        resBox.style.display = "block";
        document.getElementById('entryDisp').innerHTML = Giriş Balı: <b style="font-size:22px; color:var(--primary)">${currentEntry.toFixed(2)}</b>;
        
        let msg = "";
        if(currentEntry >= 45) msg = "Mükəmməl! A almaq üçün hər şey hazırdır! 🚀";
        else if(currentEntry >= 35) msg = "Çox yaxşı! Təqaüd üçün şansın yüksəkdir. 💪";
        else if(currentEntry >= 17) msg = "Normal. İmtahana ciddi hazırlaşsan keçəcəksən. 📚";
        else msg = "Giriş balın aşağıdır, ruhdan düşmə! 🎯";
        document.getElementById('motivationMsg').innerText = msg;

        const calcNeeded = (target) => {
            let n = target - currentEntry;
            if(n < 17) return 17;
            return n > 50 ? "X" : n.toFixed(1);
        };
        document.getElementById('targets').innerHTML = `
            <div style="background:rgba(15,23,42,0.3); padding:8px; border-radius:8px; font-size:12px;">51 (E) üçün: <b>${calcNeeded(51)}</b></div>
            <div style="background:rgba(15,23,42,0.3); padding:8px; border-radius:8px; font-size:12px;">71 (C) üçün: <b>${calcNeeded(71)}</b></div>
            <div style="background:rgba(15,23,42,0.3); padding:8px; border-radius:8px; font-size:12px;">81 (B) üçün: <b>${calcNeeded(81)}</b></div>
            <div style="background:rgba(15,23,42,0.3); padding:8px; border-radius:8px; font-size:12px;">91 (A) üçün: <b>${calcNeeded(91)}</b></div>
        `;

        document.getElementById('finalSection').style.display = "block";
        if(currentEntry >= 17) confetti({ particleCount: 80, spread: 60 });
    }

    function showFinal() {
        let ex = parseFloat(document.getElementById('examProb').value) || 0;
        const tot = currentEntry + ex;
        let g = tot >= 91 ? "A" : tot >= 81 ? "B" : tot >= 71 ? "C" : tot >= 61 ? "D" : tot >= 51 ? "E" : "F";
        document.getElementById('finalGrade').innerHTML = Yekun: ${tot.toFixed(2)} - ${g};
    }

    function shareWhatsApp() {
        const text = Mənim giriş balım: ${currentEntry.toFixed(2)}! 🎯 Sən də buradan hesabla: [SAYT_LİNKİNİZ];
        window.open(https://wa.me/?text=${encodeURIComponent(text)}, '_blank');
    }

    function addUomgField() {
        const div = document.createElement('div'); div.className = 'uomg-input-row'; div.style.display="flex"; div.style.gap="5px"; div.style.marginBottom="5px";
        div.innerHTML = '<input type="number" class="uomg-kredit" placeholder="Kredit"><input type="number" class="uomg-bal" placeholder="Bal">';
        document.getElementById('uomgFields').appendChild(div);
    }

    function calculateUOMG() {
        const kredits = document.getElementsByClassName('uomg-kredit'); const bals = document.getElementsByClassName('uomg-bal');
        let totalK = 0, sum = 0;
        for(let i=0; i<kredits.length; i++) {
            let k = parseFloat(kredits[i].value) || 0; let b = parseFloat(bals[i].value) || 0;
            if(k > 0) { totalK += k; sum += (k * b); }
        }
        document.getElementById('uomgResult').innerText = "ÜOMG: " + (totalK > 0 ? (sum / totalK).toFixed(2) : 0);
    }

    function resetAll() { location.reload(); }
</script>

</body>
</html>
