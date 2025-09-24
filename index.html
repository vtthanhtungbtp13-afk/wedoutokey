<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Lấy giá trị key từ link</title>
  <style>
    body { font-family: system-ui,Segoe UI,Roboto,Arial; padding: 24px; max-width: 760px; margin: auto; }
    label { display:block; margin-top: 12px; }
    input[type="text"] { width:100%; padding:8px; font-size:16px; box-sizing:border-box; }
    button { margin-top:8px; padding:8px 12px; font-size:16px; cursor:pointer; }
    pre { background:#f6f6f6; padding:12px; border-radius:6px; }
  </style>
</head>
<body>
  <h1>Lấy giá trị <code>key</code> từ link</h1>

  <section>
    <h2>1. Từ URL trang hiện tại</h2>
    <p>Mở trang này với tham số ví dụ: <code>page.html?key=abc123</code></p>
    <div><strong>Giá trị key tìm được:</strong></div>
    <pre id="currentKey">—</pre>
    <button id="copyCurrent">Sao chép giá trị</button>
  </section>

  <section>
    <h2>2. Lấy từ 1 link do bạn nhập</h2>
    <label for="urlInput">Dán link vào đây (ví dụ: <code>https://domain.com/?a=1&amp;key=giatri</code>)</label>
    <input id="urlInput" type="text" placeholder="Dán link vào..." />
    <div>
      <button id="btnExtract">Lấy giá trị key</button>
      <button id="btnExtractDecode">Lấy + Giải mã (decodeURIComponent)</button>
    </div>
    <div style="margin-top:8px"><strong>Kết quả:</strong></div>
    <pre id="result">—</pre>
  </section>

  <script>
    // Hàm lấy giá trị param 'key' từ một URL string
    function getKeyFromURLString(urlString) {
      try {
        // Nếu người dùng chỉ nhập query string như "key=abc" thì thêm base tạm
        if (urlString.trim().startsWith("key=") || urlString.trim().startsWith("?key=")) {
          urlString = "http://example.com/?" + urlString.replace(/^\?/, "");
        }
        const url = new URL(urlString);
        // URLSearchParams tự động giải mã percent-encoding khi dùng .get()
        return url.searchParams.get('key'); // trả về null nếu không tồn tại
      } catch (e) {
        // Không phải URL hợp lệ -> thử parse query string thủ công
        try {
          const qs = urlString.split('?')[1] || urlString;
          const params = new URLSearchParams(qs);
          return params.get('key');
        } catch (e2) {
          return null;
        }
      }
    }

    // Hiển thị giá trị từ URL hiện tại
    function showKeyFromCurrentURL() {
      const key = new URLSearchParams(window.location.search).get('key');
      document.getElementById('currentKey').textContent = (key === null) ? 'Không tìm thấy parameter "key"' : key;
    }

    // Sự kiện nút lấy từ link nhập tay
    document.getElementById('btnExtract').addEventListener('click', () => {
      const url = document.getElementById('urlInput').value.trim();
      if (!url) {
        document.getElementById('result').textContent = 'Vui lòng nhập link hoặc query string.';
        return;
      }
      const key = getKeyFromURLString(url);
      document.getElementById('result').textContent = (key === null) ? 'Không tìm thấy parameter "key"' : key;
    });

    // Nút lấy và decode (nếu giá trị có percent-encoding muốn giải mã thêm)
    document.getElementById('btnExtractDecode').addEventListener('click', () => {
      const url = document.getElementById('urlInput').value.trim();
      if (!url) {
        document.getElementById('result').textContent = 'Vui lòng nhập link hoặc query string.';
        return;
      }
      const k = getKeyFromURLString(url);
      if (k === null) {
        document.getElementById('result').textContent = 'Không tìm thấy parameter "key"';
        return;
      }
      try {
        // decodeURIComponent an toàn hơn nếu rỗng hoặc không phải percent-encoding sẽ ném lỗi => catch
        const decoded = decodeURIComponent(k);
        document.getElementById('result').textContent = decoded;
      } catch (e) {
        document.getElementById('result').textContent = k + '\n(giải mã thất bại — trả về nguyên bản)';
      }
    });

    // Sao chép giá trị hiện tại
    document.getElementById('copyCurrent').addEventListener('click', async () => {
      const txt = document.getElementById('currentKey').textContent;
      if (!txt || txt.includes('Không tìm thấy')) { alert('Không có giá trị để sao chép'); return; }
      try {
        await navigator.clipboard.writeText(txt);
        alert('Đã sao chép vào clipboard');
      } catch (e) {
        alert('Sao chép thất bại — có thể trình duyệt không cho phép. Hãy bôi đen và Ctrl+C.');
      }
    });

    // Chạy khi load
    showKeyFromCurrentURL();
  </script>
</body>
</html>
