<!doctype html>
<html lang="vi">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>GetKey TungAPI</title>
<style>
  /* --- Base --- */
  :root{
    --bg:#ffffff; --card:#f7f7fb; --text:#111;
    --muted:#666; --accent:#0b84ff; --ok:#0fbf5b;
    --border: rgba(0,0,0,0.06);
  }
  /* Dark theme under .dark class */
  .dark {
    --bg:#0b1020; --card:#0d1222; --text:#e6eef8;
    --muted:#9fb0c8; --accent:#39a7ff; --ok:#39ff9b;
    --border: rgba(255,255,255,0.06);
  }

  html,body{height:100%;margin:0;font-family:Inter,ui-sans-serif,system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial; background:var(--bg); color:var(--text)}
  .wrap{min-height:100%;display:flex;align-items:center;justify-content:center;padding:28px;box-sizing:border-box}
  .card{
    width:100%;max-width:900px;background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent);
    border-radius:12px;padding:18px;border:1px solid var(--border);box-shadow: 0 12px 30px rgba(0,0,0,0.06);
    background-color:var(--card);
  }

  .header{display:flex;align-items:center;justify-content:space-between;gap:12px;margin-bottom:14px}
  .title{font-weight:700;font-size:18px;letter-spacing:0.2px}
  .subtitle{font-size:13px;color:var(--muted)}

  .row{display:flex;gap:10px;align-items:center}
  .controls{display:flex;gap:8px;align-items:center}
  input[type="text"]{
    flex:1;padding:10px 12px;border-radius:8px;border:1px solid var(--border);background:transparent;color:var(--text);
    outline:none;font-size:14px;
  }
  button{
    padding:10px 12px;border-radius:8px;border:1px solid var(--border);background:transparent;color:var(--text);cursor:pointer;font-weight:600;
  }
  .btn-primary{background:linear-gradient(90deg, rgba(11,132,255,0.12), rgba(11,132,255,0.06));border-color:rgba(11,132,255,0.18)}
  .btn-copy{border-color:rgba(57,255,20,0.08)}
  .result{margin-top:14px;padding:14px;border-radius:10px;border:1px dashed var(--border);background:rgba(0,0,0,0.01)}
  .klabel{display:inline-block;background:rgba(0,0,0,0.02);padding:6px 10px;border-radius:8px;font-weight:700;color:var(--accent)}
  .muted{color:var(--muted);font-size:13px}
  .history{margin-top:12px}
  .hist-item{display:flex;justify-content:space-between;gap:8px;padding:8px;border-radius:8px;border:1px solid var(--border);margin-bottom:8px;background:transparent}
  .small{font-size:13px;color:var(--muted)}

  /* responsive */
  @media (max-width:640px){
    .row{flex-direction:column;align-items:stretch}
    .controls{width:100%}
  }
</style>
</head>
<body class="dark"> <!-- Thêm / bỏ class 'dark' để bật/tắt giao diện -->
<div class="wrap">
  <div class="card" role="region" aria-label="Key extractor">
    <div class="header">
      <div>
        <div class="title">Key Extractor</div>
        <div class="subtitle">Tự động lấy giá trị <code>key=</code> từ URL (query hoặc hash)</div>
      </div>

      <div class="row">
        <div class="small" id="mode-note">Theme: <strong id="theme-name">Dark</strong></div>
        <button id="toggle-theme" title="Bật/tắt theme">Theme</button>
      </div>
    </div>

    <div>
      <div class="row">
        <input id="url-input" type="text" placeholder="Nhập hoặc dán URL ở đây (mặc định dùng URL hiện tại)" aria-label="URL input"/>
        <div class="controls">
          <button id="check-btn" class="btn-primary">Kiểm tra</button>
          <button id="copy-btn" class="btn-copy">Sao chép</button>
        </div>
      </div>

      <div class="result" id="result">
        <div class="muted">Kết quả sẽ hiển thị ở đây.</div>
      </div>

      <div class="history" id="history-section" aria-live="polite"></div>
    </div>

    <div style="margin-top:14px;display:flex;justify-content:space-between;align-items:center">
      <div class="small"></div>
      <div class="small"></div>
    </div>
  </div>
</div>

<script>
  // --- Extract function robust ---
  function extractKeyFromUrlString(urlString){
    if (!urlString || urlString.trim() === '') {
      // try from current location
      urlString = window.location.href;
    }
    try {
      // If looks like full or relative URL, use URL parser with base
      let url;
      if (/^[a-zA-Z][a-zA-Z0-9+.-]*:\/\//.test(urlString) || urlString.startsWith('/') || urlString.startsWith('?') || urlString.startsWith('#')) {
        url = new URL(urlString, window.location.origin);
      } else {
        // fallback: if user pasted something like "example.com/?key=1"
        if (!/^[a-zA-Z][a-zA-Z0-9+.-]*:/.test(urlString)) {
          url = new URL(urlString, window.location.origin);
        } else {
          url = new URL(urlString);
        }
      }

      // 1) search params
      const sp = new URLSearchParams(url.search);
      let k = sp.get('key');
      if (k) return k;

      // 2) hash (e.g. #key=abc or #/path?key=abc)
      if (url.hash) {
        let hash = url.hash.replace(/^#/, '');
        // If hash contains '?', split and parse query part
        const qIndex = hash.indexOf('?');
        if (qIndex !== -1) {
          const maybeQuery = hash.slice(qIndex);
          const hp = new URLSearchParams(maybeQuery);
          const k2 = hp.get('key');
          if (k2) return k2;
        }
        // plain key= in hash
        const hp2 = new URLSearchParams(hash);
        const k3 = hp2.get('key');
        if (k3) return k3;

        // regex fallback
        const m = hash.match(/(?:^|&)key=([^&]+)/);
        if (m) return decodeURIComponent(m[1]);
      }

      // 3) fallback: anywhere in the original string
      const anywhere = urlString.match(/(?:\?|&|#|\/)key=([^&#\s]+)/);
      if (anywhere) return decodeURIComponent(anywhere[1]);

      return null;
    } catch(e){
      // try simple regex fallback
      const m = String(urlString).match(/key=([^&\s#]+)/);
      return m ? decodeURIComponent(m[1]) : null;
    }
  }

  // --- UI & state ---
  const urlInput = document.getElementById('url-input');
  const checkBtn = document.getElementById('check-btn');
  const copyBtn = document.getElementById('copy-btn');
  const resultBox = document.getElementById('result');
  const historySection = document.getElementById('history-section');
  const toggleTheme = document.getElementById('toggle-theme');
  const themeName = document.getElementById('theme-name');

  let history = [];

  function renderResult(key){
    if (key === null) {
      resultBox.innerHTML = '<div class="muted">Không tìm thấy tham số <code>key=</code>.</div>';
    } else {
      resultBox.innerHTML = `<div>Giá trị <span class="klabel">${escapeHtml(key)}</span></div><div class="small" style="margin-top:8px;color:var(--muted)">Nguồn: query/hash/trong URL</div>`;
    }
  }

  function addHistory(key){
    if (!key) return;
    // tránh trùng lặp liên tiếp
    if (history[0] === key) return;
    history.unshift(key);
    if (history.length > 10) history.pop();
    renderHistory();
  }

  function renderHistory(){
    if (history.length === 0) {
      historySection.innerHTML = '';
      return;
    }
    const list = history.map(k => `<div class="hist-item"><div><strong>${escapeHtml(k)}</strong><div class="small">${new Date().toLocaleString()}</div></div><div><button class="use-btn" data-val="${encodeURIComponent(k)}">Dùng</button></div></div>`).join('');
    historySection.innerHTML = `<h4 class="small" style="margin:6px 0 8px 0">Lịch sử</h4>${list}`;
    // attach listeners for use buttons
    document.querySelectorAll('.use-btn').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const v = decodeURIComponent(btn.dataset.val);
        urlInput.value = v;
        // show it in result (note: v is a key value, not a URL, but user may want it)
        renderResult(v);
      });
    });
  }

  function escapeHtml(s){ return String(s).replace(/[&<>"']/g, m=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[m])); }

  // --- Events ---
  checkBtn.addEventListener('click', ()=>{
    const v = urlInput.value.trim() || window.location.href;
    const k = extractKeyFromUrlString(v);
    renderResult(k);
    if (k) addHistory(k);
  });

  // Auto-detect on load from current location
  window.addEventListener('load', ()=>{
    const detected = extractKeyFromUrlString(window.location.href);
    if (detected) {
      renderResult(detected);
      addHistory(detected);
      // put current URL into input for convenience
      urlInput.value = window.location.href;
    } else {
      renderResult(null);
    }
  });

  copyBtn.addEventListener('click', async ()=>{
    // try to copy the found key in result
    const label = resultBox.querySelector('.klabel');
    let text = label ? label.textContent : null;
    if (!text) {
      // try extracting from input or current URL
      const k = extractKeyFromUrlString(urlInput.value.trim() || window.location.href);
      text = k;
    }
    if (!text) {
      alert('Không có giá trị key để sao chép.');
      return;
    }
    try {
      await navigator.clipboard.writeText(text);
      copyBtn.textContent = 'Đã sao chép';
      setTimeout(()=> copyBtn.textContent = 'Sao chép', 1500);
    } catch(e){
      // fallback: prompt
      const ok = prompt('Copy thủ công, hãy sao chép giá trị bên dưới:', text);
      if (ok !== null) {
        copyBtn.textContent = 'Đã sao chép';
        setTimeout(()=> copyBtn.textContent = 'Sao chép', 1500);
      }
    }
  });

  // Enter trong input
  urlInput.addEventListener('keydown', (e)=> { if (e.key === 'Enter') checkBtn.click(); });

  // Theme toggle: thêm / bỏ class 'dark' trên body
  toggleTheme.addEventListener('click', ()=>{
    document.body.classList.toggle('dark');
    const isDark = document.body.classList.contains('dark');
    themeName.textContent = isDark ? 'Dark' : 'Light';
  });
</script>
</body>
</html>
