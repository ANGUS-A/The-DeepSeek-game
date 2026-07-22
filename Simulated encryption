<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🔐 文件加密·解密工具 (Fernet 风格)</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, system-ui, -apple-system, sans-serif;
            margin: 0;
            padding: 0;
        }
        body {
            background: linear-gradient(145deg, #0b1219 0%, #1a2634 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        .container {
            max-width: 1400px;
            width: 100%;
            background: rgba(22, 34, 48, 0.75);
            backdrop-filter: blur(8px);
            border-radius: 48px;
            padding: 30px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.8), inset 0 1px 2px rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.03);
        }
        h1 {
            text-align: center;
            font-weight: 400;
            letter-spacing: 1px;
            color: #d6e3f0;
            font-size: 2.2rem;
            margin-bottom: 10px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.5);
        }
        h1 small {
            font-size: 0.9rem;
            color: #8aa1b9;
            display: block;
            font-weight: 300;
            margin-top: 4px;
        }
        .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 28px;
            margin-top: 20px;
        }
        @media (max-width: 780px) {
            .grid {
                grid-template-columns: 1fr;
                gap: 40px;
            }
        }
        .panel {
            background: rgba(13, 22, 33, 0.7);
            border-radius: 32px;
            padding: 24px 24px 30px;
            border: 1px solid rgba(255, 255, 255, 0.04);
            box-shadow: inset 0 2px 6px rgba(0,0,0,0.6);
            backdrop-filter: blur(4px);
            transition: 0.2s;
        }
        .panel h2 {
            font-weight: 400;
            color: #b8cee5;
            font-size: 1.5rem;
            display: flex;
            align-items: center;
            gap: 10px;
            border-bottom: 1px solid rgba(255,255,255,0.05);
            padding-bottom: 16px;
            margin-bottom: 22px;
        }
        .panel h2 span {
            background: #2a3a4e;
            padding: 2px 14px;
            border-radius: 40px;
            font-size: 0.8rem;
            color: #b0cbeb;
            margin-left: auto;
        }
        .dropzone {
            background: rgba(0, 0, 0, 0.3);
            border: 2px dashed #3f556b;
            border-radius: 28px;
            padding: 32px 12px;
            text-align: center;
            color: #8ba3bd;
            transition: 0.25s;
            cursor: pointer;
            position: relative;
            min-height: 200px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .dropzone.dragover {
            border-color: #7bb3ff;
            background: rgba(52, 110, 184, 0.15);
            box-shadow: 0 0 20px rgba(70, 150, 255, 0.2);
        }
        .dropzone .icon {
            font-size: 3.8rem;
            line-height: 1;
            margin-bottom: 8px;
            opacity: 0.7;
        }
        .dropzone p {
            font-size: 0.95rem;
            margin: 6px 0;
        }
        .dropzone .filename {
            background: #1f3143;
            padding: 8px 18px;
            border-radius: 40px;
            color: #d4e4fa;
            font-weight: 500;
            display: inline-block;
            margin-top: 12px;
            font-size: 0.9rem;
            max-width: 90%;
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
        }
        .file-input {
            display: none;
        }
        .key-display {
            margin: 20px 0 16px;
            background: #0d1822;
            border-radius: 60px;
            padding: 4px 6px 4px 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            border: 1px solid #2d4057;
        }
        .key-display input {
            background: transparent;
            border: none;
            color: #c3d9f2;
            font-size: 0.85rem;
            flex: 1;
            padding: 12px 0;
            font-family: 'Courier New', monospace;
            letter-spacing: 0.3px;
            outline: none;
            min-width: 0;
        }
        .key-display button {
            background: #2c405a;
            border: none;
            color: white;
            padding: 8px 20px;
            border-radius: 40px;
            font-weight: 500;
            font-size: 0.8rem;
            cursor: pointer;
            transition: 0.2s;
            white-space: nowrap;
            border: 1px solid #45607c;
        }
        .key-display button:hover {
            background: #3d5779;
            border-color: #6b8bb3;
        }
        .action-buttons {
            display: flex;
            gap: 12px;
            margin: 18px 0 6px;
            flex-wrap: wrap;
        }
        .btn {
            background: #23374d;
            border: none;
            padding: 12px 24px;
            border-radius: 60px;
            color: #deecfc;
            font-size: 0.95rem;
            font-weight: 500;
            flex: 1;
            min-width: 120px;
            cursor: pointer;
            transition: 0.2s;
            border: 1px solid #36506b;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .btn-primary {
            background: #2b6c9e;
            border-color: #4b8fc9;
            color: white;
        }
        .btn-primary:hover {
            background: #3782bb;
            border-color: #70b0f0;
            transform: scale(1.01);
        }
        .btn-primary:disabled {
            opacity: 0.3;
            pointer-events: none;
            filter: grayscale(0.5);
        }
        .btn-success {
            background: #1d7a5c;
            border-color: #31a57e;
        }
        .btn-success:hover {
            background: #269b74;
            border-color: #4bcba0;
        }
        .btn-danger {
            background: #8f3e4b;
            border-color: #bc5a6a;
        }
        .btn-danger:hover {
            background: #b04e5d;
            border-color: #d97a8a;
        }
        .btn-outline {
            background: transparent;
            border: 1px solid #45607c;
            color: #b0cbe7;
        }
        .btn-outline:hover {
            background: #1e3145;
        }
        .info-box {
            background: #0a141f;
            border-radius: 20px;
            padding: 14px 18px;
            margin: 18px 0 6px;
            color: #a5bed9;
            font-size: 0.85rem;
            border-left: 3px solid #3b6b94;
            word-break: break-all;
        }
        .info-box strong {
            color: #d3e5ff;
        }
        .status-badge {
            display: inline-block;
            padding: 4px 16px;
            border-radius: 30px;
            background: #1e3349;
            color: #8eb3d9;
            font-size: 0.75rem;
            letter-spacing: 0.5px;
        }
        .row {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            align-items: center;
        }
        .flex-spacer {
            flex: 1;
        }
        .footer-note {
            text-align: center;
            margin-top: 28px;
            color: #48617c;
            font-size: 0.8rem;
            border-top: 1px solid rgba(255,255,255,0.03);
            padding-top: 20px;
        }
        .download-link {
            color: #7db3e6;
            text-decoration: none;
            border-bottom: 1px dotted #3d6188;
        }
        .download-link:hover {
            color: #b3d6ff;
        }
        .clear-btn {
            background: none;
            border: none;
            color: #6b8aa8;
            cursor: pointer;
            font-size: 0.8rem;
            padding: 6px 12px;
            border-radius: 30px;
            transition: 0.2s;
        }
        .clear-btn:hover {
            background: #1f3145;
            color: #c0d8f0;
        }
    </style>
</head>
<body>
<div class="container">
    <h1>🔐 文件加密 · 解密 <small>拖拽文件 · 密钥本地生成 · 仿 Fernet 对称加密</small></h1>
    <div class="grid">
        <!-- ========= 左侧：加密区 ========= -->
        <div class="panel">
            <h2>📤 加密 <span>丢文件 → 得密钥</span></h2>
            <div id="encryptDrop" class="dropzone">
                <div class="icon">📂</div>
                <p><strong>拖拽文件</strong> 或点击上传</p>
                <p style="font-size:0.8rem; opacity:0.6;">最大 50MB</p>
                <div id="encryptFileName" class="filename" style="display:none;">未选择</div>
                <input type="file" id="encryptFileInput" class="file-input">
            </div>

            <div class="key-display">
                <input type="text" id="generatedKey" readonly placeholder="点击「加密」生成密钥" value="">
                <button id="copyKeyBtn" title="复制密钥">📋 复制</button>
            </div>

            <div class="action-buttons">
                <button class="btn btn-primary" id="encryptActionBtn">🔒 加密文件</button>
                <button class="btn btn-outline" id="clearEncryptBtn">🗑️ 清空</button>
            </div>

            <div id="encryptStatus" class="info-box">
                <span class="status-badge">⏳ 等待</span> 准备就绪
            </div>
            <div id="encryptMeta" class="info-box" style="display:none;">
                <strong>🔑 密钥已生成</strong><br>
                <span id="encryptKeyDisplay"></span>
            </div>
        </div>

        <!-- ========= 右侧：解密区 ========= -->
        <div class="panel">
            <h2>📥 解密 <span>丢加密文件 + 密钥</span></h2>
            <div id="decryptDrop" class="dropzone">
                <div class="icon">🔓</div>
                <p><strong>拖拽加密文件</strong> 或点击上传</p>
                <p style="font-size:0.8rem; opacity:0.6;">.enc 或任意二进制</p>
                <div id="decryptFileName" class="filename" style="display:none;">未选择</div>
                <input type="file" id="decryptFileInput" class="file-input">
            </div>

            <div style="display:flex; gap:12px; margin-top:18px; flex-wrap:wrap;">
                <div style="flex:2; min-width:180px;">
                    <input type="text" id="decryptKeyInput" placeholder="粘贴密钥 (或左侧复制)" 
                           style="width:100%; background:#0d1822; border:1px solid #2d4057; border-radius:40px; padding:12px 20px; color:#c3d9f2; font-family:monospace; font-size:0.8rem; outline:none;">
                </div>
                <button class="btn btn-success" id="decryptActionBtn" style="flex:1;">🔓 解密</button>
            </div>

            <div class="action-buttons" style="margin-top:8px;">
                <button class="btn btn-outline" id="clearDecryptBtn">🗑️ 清空</button>
                <button class="btn btn-outline" id="pasteKeyFromLeftBtn" style="flex:0.8;">📋 粘贴左侧密钥</button>
            </div>

            <div id="decryptStatus" class="info-box">
                <span class="status-badge">⏳ 等待</span> 准备就绪
            </div>
            <div id="decryptResult" class="info-box" style="display:none;">
                <strong>✅ 解密成功</strong><br>
                <span id="decryptFileNameResult"></span>  (<span id="decryptFileSizeResult"></span> 字节)
                <div style="margin-top:10px;">
                    <a id="decryptDownloadLink" class="download-link" href="#">⬇️ 下载解密文件</a>
                </div>
            </div>
        </div>
    </div>
    <div class="footer-note">
        ⚡ 使用 Web Crypto API (AES-GCM) 模拟 Fernet 对称加密 · 密钥仅存在于浏览器内存，不会上传
    </div>
</div>
<script>
    (function() {
        "use strict";

        // ---------- DOM refs ----------
        const encryptDrop = document.getElementById('encryptDrop');
        const encryptInput = document.getElementById('encryptFileInput');
        const encryptFileName = document.getElementById('encryptFileName');
        const generatedKeyInput = document.getElementById('generatedKey');
        const encryptActionBtn = document.getElementById('encryptActionBtn');
        const clearEncryptBtn = document.getElementById('clearEncryptBtn');
        const encryptStatus = document.getElementById('encryptStatus');
        const encryptMeta = document.getElementById('encryptMeta');
        const encryptKeyDisplay = document.getElementById('encryptKeyDisplay');
        const copyKeyBtn = document.getElementById('copyKeyBtn');

        const decryptDrop = document.getElementById('decryptDrop');
        const decryptInput = document.getElementById('decryptFileInput');
        const decryptFileName = document.getElementById('decryptFileName');
        const decryptKeyInput = document.getElementById('decryptKeyInput');
        const decryptActionBtn = document.getElementById('decryptActionBtn');
        const clearDecryptBtn = document.getElementById('clearDecryptBtn');
        const decryptStatus = document.getElementById('decryptStatus');
        const decryptResult = document.getElementById('decryptResult');
        const decryptFileNameResult = document.getElementById('decryptFileNameResult');
        const decryptFileSizeResult = document.getElementById('decryptFileSizeResult');
        const decryptDownloadLink = document.getElementById('decryptDownloadLink');
        const pasteKeyFromLeftBtn = document.getElementById('pasteKeyFromLeftBtn');

        // ---------- 状态 ----------
        let encryptFileData = null;      // 原始 ArrayBuffer
        let encryptFileNameStr = '';
        let currentKey = '';            // 当前加密生成的密钥 (base64)

        let decryptFileData = null;     // 加密文件 ArrayBuffer
        let decryptFileNameStr = '';
        let decryptedBlob = null;       // 解密后的 Blob

        // ---------- 辅助函数 ----------
        function setStatus(el, text, type = 'info') {
            const badge = el.querySelector('.status-badge');
            if (badge) {
                if (type === 'success') badge.textContent = '✅ 成功';
                else if (type === 'error') badge.textContent = '❌ 错误';
                else if (type === 'warning') badge.textContent = '⚠️ 警告';
                else badge.textContent = '⏳ 等待';
            }
            const textNode = el.childNodes[el.childNodes.length - 1];
            if (textNode && textNode.nodeType === Node.TEXT_NODE) {
                textNode.textContent = ' ' + text;
            } else {
                el.appendChild(document.createTextNode(' ' + text));
            }
        }

        function showEncryptMeta(key) {
            encryptMeta.style.display = 'block';
            encryptKeyDisplay.textContent = key;
        }

        function hideEncryptMeta() {
            encryptMeta.style.display = 'none';
        }

        function resetEncryptUI() {
            encryptFileData = null;
            encryptFileNameStr = '';
            encryptFileName.style.display = 'none';
            encryptFileName.textContent = '';
            encryptActionBtn.disabled = true;
            hideEncryptMeta();
            setStatus(encryptStatus, '准备就绪', 'info');
            if (!generatedKeyInput.value) {
                // keep
            }
        }

        function resetDecryptUI() {
            decryptFileData = null;
            decryptFileNameStr = '';
            decryptFileName.style.display = 'none';
            decryptFileName.textContent = '';
            decryptResult.style.display = 'none';
            decryptedBlob = null;
            decryptDownloadLink.removeAttribute('href');
            setStatus(decryptStatus, '准备就绪', 'info');
        }

        // ---------- 生成密钥 (模拟 Fernet.generate_key) ----------
        function generateKey() {
            const arr = new Uint8Array(32);
            crypto.getRandomValues(arr);
            return btoa(String.fromCharCode(...arr));  // base64
        }

        // ---------- 加密 (AES-GCM 模拟 Fernet) ----------
        async function encryptData(data, keyBase64) {
            // 导入密钥
            const rawKey = Uint8Array.from(atob(keyBase64), c => c.charCodeAt(0));
            const cryptoKey = await crypto.subtle.importKey(
                'raw', rawKey, { name: 'AES-GCM' }, false, ['encrypt']
            );
            const iv = crypto.getRandomValues(new Uint8Array(12));
            const encrypted = await crypto.subtle.encrypt(
                { name: 'AES-GCM', iv: iv },
                cryptoKey,
                data
            );
            // 将 iv + 密文 合并返回 (类似Fernet打包)
            const result = new Uint8Array(iv.length + encrypted.byteLength);
            result.set(iv, 0);
            result.set(new Uint8Array(encrypted), iv.length);
            return result.buffer;
        }

        // ---------- 解密 (AES-GCM) ----------
        async function decryptData(encryptedBuffer, keyBase64) {
            const rawKey = Uint8Array.from(atob(keyBase64), c => c.charCodeAt(0));
            const cryptoKey = await crypto.subtle.importKey(
                'raw', rawKey, { name: 'AES-GCM' }, false, ['decrypt']
            );
            const encrypted = new Uint8Array(encryptedBuffer);
            const iv = encrypted.slice(0, 12);
            const data = encrypted.slice(12);
            try {
                const decrypted = await crypto.subtle.decrypt(
                    { name: 'AES-GCM', iv: iv },
                    cryptoKey,
                    data
                );
                return decrypted;
            } catch (e) {
                throw new Error('解密失败: 密钥错误或数据损坏');
            }
        }

        // ---------- 加密流程 ----------
        async function handleEncrypt() {
            if (!encryptFileData) {
                setStatus(encryptStatus, '请先上传文件', 'error');
                return;
            }
            try {
                // 生成密钥
                const key = generateKey();
                currentKey = key;
                generatedKeyInput.value = key;
                showEncryptMeta(key);

                setStatus(encryptStatus, '加密中...', 'info');
                const encryptedBuffer = await encryptData(encryptFileData, key);

                // 构建下载文件
                const blob = new Blob([encryptedBuffer], { type: 'application/octet-stream' });
                const link = document.createElement('a');
                const baseName = encryptFileNameStr || 'file';
                const ext = baseName.includes('.') ? baseName.substring(baseName.lastIndexOf('.')) : '';
                const nameWithoutExt = baseName.replace(/\.[^.]+$/, '');
                link.download = `${nameWithoutExt}_encrypted.enc`;
                link.href = URL.createObjectURL(blob);
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
                setTimeout(() => URL.revokeObjectURL(link.href), 5000);

                setStatus(encryptStatus, `加密完成！已下载 .enc 文件 (密钥已显示)`, 'success');
            } catch (e) {
                console.error(e);
                setStatus(encryptStatus, `加密失败: ${e.message}`, 'error');
            }
        }

        // ---------- 解密流程 ----------
        async function handleDecrypt() {
            if (!decryptFileData) {
                setStatus(decryptStatus, '请先上传加密文件', 'error');
                return;
            }
            const key = decryptKeyInput.value.trim();
            if (!key) {
                setStatus(decryptStatus, '请输入或粘贴密钥', 'error');
                return;
            }
            try {
                setStatus(decryptStatus, '解密中...', 'info');
                const decrypted = await decryptData(decryptFileData, key);
                decryptedBlob = new Blob([decrypted], { type: 'application/octet-stream' });
                const size = decrypted.byteLength;
                const name = decryptFileNameStr || 'decrypted_file';
                decryptFileNameResult.textContent = name;
                decryptFileSizeResult.textContent = size;

                // 生成下载链接
                const url = URL.createObjectURL(decryptedBlob);
                decryptDownloadLink.href = url;
                decryptDownloadLink.download = `decrypted_${name}`;
                decryptResult.style.display = 'block';
                setStatus(decryptStatus, '解密成功，点击下方下载', 'success');
            } catch (e) {
                console.error(e);
                setStatus(decryptStatus, `解密失败: ${e.message}`, 'error');
                decryptResult.style.display = 'none';
                decryptedBlob = null;
            }
        }

        // ---------- 拖拽/上传 加密区 ----------
        function handleEncryptFile(file) {
            if (!file) return;
            if (file.size > 50 * 1024 * 1024) {
                setStatus(encryptStatus, '文件超过 50MB', 'error');
                return;
            }
            encryptFileNameStr = file.name;
            encryptFileName.textContent = file.name;
            encryptFileName.style.display = 'inline-block';
            const reader = new FileReader();
            reader.onload = async (e) => {
                encryptFileData = e.target.result;
                encryptActionBtn.disabled = false;
                setStatus(encryptStatus, `已加载: ${file.name} (${file.size} 字节)`, 'info');
                // 自动生成密钥预览
                if (!generatedKeyInput.value) {
                    const key = generateKey();
                    currentKey = key;
                    generatedKeyInput.value = key;
                    showEncryptMeta(key);
                }
            };
            reader.onerror = () => setStatus(encryptStatus, '读取文件失败', 'error');
            reader.readAsArrayBuffer(file);
        }

        encryptDrop.addEventListener('click', () => encryptInput.click());
        encryptInput.addEventListener('change', (e) => {
            if (e.target.files.length) handleEncryptFile(e.target.files[0]);
            e.target.value = '';
        });

        encryptDrop.addEventListener('dragover', (e) => {
            e.preventDefault();
            encryptDrop.classList.add('dragover');
        });
        encryptDrop.addEventListener('dragleave', () => {
            encryptDrop.classList.remove('dragover');
        });
        encryptDrop.addEventListener('drop', (e) => {
            e.preventDefault();
            encryptDrop.classList.remove('dragover');
            if (e.dataTransfer.files.length) handleEncryptFile(e.dataTransfer.files[0]);
        });

        // ---------- 拖拽/上传 解密区 ----------
        function handleDecryptFile(file) {
            if (!file) return;
            decryptFileNameStr = file.name;
            decryptFileName.textContent = file.name;
            decryptFileName.style.display = 'inline-block';
            const reader = new FileReader();
            reader.onload = (e) => {
                decryptFileData = e.target.result;
                setStatus(decryptStatus, `已加载: ${file.name} (${file.size} 字节)`, 'info');
                // 自动尝试从文件名提取密钥？不自动，让用户粘贴。
                decryptResult.style.display = 'none';
                decryptedBlob = null;
            };
            reader.onerror = () => setStatus(decryptStatus, '读取文件失败', 'error');
            reader.readAsArrayBuffer(file);
        }

        decryptDrop.addEventListener('click', () => decryptInput.click());
        decryptInput.addEventListener('change', (e) => {
            if (e.target.files.length) handleDecryptFile(e.target.files[0]);
            e.target.value = '';
        });

        decryptDrop.addEventListener('dragover', (e) => {
            e.preventDefault();
            decryptDrop.classList.add('dragover');
        });
        decryptDrop.addEventListener('dragleave', () => {
            decryptDrop.classList.remove('dragover');
        });
        decryptDrop.addEventListener('drop', (e) => {
            e.preventDefault();
            decryptDrop.classList.remove('dragover');
            if (e.dataTransfer.files.length) handleDecryptFile(e.dataTransfer.files[0]);
        });

        // ---------- 按钮事件 ----------
        encryptActionBtn.addEventListener('click', handleEncrypt);

        clearEncryptBtn.addEventListener('click', () => {
            resetEncryptUI();
            generatedKeyInput.value = '';
            currentKey = '';
            encryptActionBtn.disabled = true;
            hideEncryptMeta();
            encryptFileName.style.display = 'none';
            encryptFileData = null;
            setStatus(encryptStatus, '已清空', 'info');
        });

        decryptActionBtn.addEventListener('click', handleDecrypt);

        clearDecryptBtn.addEventListener('click', () => {
            resetDecryptUI();
            decryptFileData = null;
            decryptFileName.style.display = 'none';
            decryptResult.style.display = 'none';
            decryptedBlob = null;
            setStatus(decryptStatus, '已清空', 'info');
        });

        copyKeyBtn.addEventListener('click', () => {
            const val = generatedKeyInput.value;
            if (!val) {
                setStatus(encryptStatus, '没有密钥可复制', 'warning');
                return;
            }
            navigator.clipboard?.writeText(val).then(() => {
                setStatus(encryptStatus, '密钥已复制', 'success');
            }).catch(() => {
                // fallback
                const ta = document.createElement('textarea');
                ta.value = val;
                document.body.appendChild(ta);
                ta.select();
                document.execCommand('copy');
                document.body.removeChild(ta);
                setStatus(encryptStatus, '密钥已复制 (fallback)', 'success');
            });
        });

        pasteKeyFromLeftBtn.addEventListener('click', () => {
            const key = generatedKeyInput.value;
            if (!key) {
                setStatus(decryptStatus, '左侧没有密钥', 'error');
                return;
            }
            decryptKeyInput.value = key;
            setStatus(decryptStatus, '已粘贴左侧密钥', 'success');
        });

        // 初始生成一个示例密钥
        (function init() {
            const key = generateKey();
            currentKey = key;
            generatedKeyInput.value = key;
            showEncryptMeta(key);
            setStatus(encryptStatus, '就绪，可拖入文件', 'info');
            encryptActionBtn.disabled = true;
        })();

        // 辅助：点击下载链接撤销blob
        decryptDownloadLink.addEventListener('click', function(e) {
            // 不立即撤销，浏览器会处理
        });

    })();
</script>
</body>
</html>
