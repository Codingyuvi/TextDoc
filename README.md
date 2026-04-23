<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TextDoc - Multi-Specialty Messenger</title>
    <style>
        /* Unified Professional Styles */
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: linear-gradient(135deg, #1a6fc4 0%, #0d4d8d 100%); min-height: 100vh; display: flex; justify-content: center; align-items: center; padding: 20px; }
        .container { width: 100%; max-width: 950px; background: white; border-radius: 15px; box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2); overflow: hidden; display: flex; flex-direction: column; height: 90vh; }
        
        /* Navigation & Header */
        .menu-bar { background: #1a3c6e; padding: 15px 25px; display: flex; flex-direction: column; gap: 15px; border-bottom: 2px solid rgba(255,255,255,0.1); }
        .menu-row { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px; }
        .logo { color: white; font-size: 24px; font-weight: bold; }
        .selector-group { display: flex; gap: 10px; align-items: center; flex-wrap: wrap; }
        .label { color: #8ab4f8; font-size: 11px; font-weight: bold; text-transform: uppercase; }
        
        /* Controls */
        .btn { background: rgba(255, 255, 255, 0.1); border: 1px solid rgba(255, 255, 255, 0.3); color: white; padding: 6px 12px; border-radius: 20px; cursor: pointer; font-size: 13px; transition: 0.3s; }
        .btn.active { background: #4CAF50; border-color: #4CAF50; }
        .spec-btn.active { background: #ff9800; border-color: #ff9800; }

        /* Chat Layout */
        .chat-main { flex: 1; display: flex; flex-direction: column; background: #f5f7fa; overflow: hidden; }
        .chat-header { background: #2a4d7c; color: white; padding: 12px 25px; display: flex; justify-content: space-between; align-items: center; }
        .chat-messages { flex: 1; padding: 20px; overflow-y: auto; display: flex; flex-direction: column; gap: 15px; }
        
        /* Message Bubbles */
        .message { display: flex; flex-direction: column; max-width: 80%; animation: fadeIn 0.3s ease; }
        .message.doctor { align-self: flex-start; }
        .message.patient { align-self: flex-end; }
        .bubble { padding: 12px 18px; border-radius: 18px; position: relative; box-shadow: 0 2px 5px rgba(0,0,0,0.05); font-size: 15px; }
        .doctor .bubble { background: white; color: #333; border: 1px solid #e0e0e0; border-bottom-left-radius: 4px; }
        .patient .bubble { background: #1a6fc4; color: white; border-bottom-right-radius: 4px; }
        .sender-name { font-size: 11px; font-weight: bold; margin-bottom: 4px; color: #666; }
        .patient .sender-name { text-align: right; color: #1a3c6e; }

        /* Bottom Control Panel */
        .controls { padding: 15px 25px; background: white; border-top: 1px solid #eee; display: flex; gap: 10px; align-items: center; }
        .message-input { flex: 1; padding: 12px 20px; border: 1px solid #ddd; border-radius: 25px; outline: none; font-size: 15px; }
        .icon-btn { width: 45px; height: 45px; border-radius: 50%; border: none; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; transition: 0.2s; }
        .send-btn { background: #1a6fc4; color: white; padding: 0 25px; border-radius: 25px; border: none; height: 45px; cursor: pointer; font-weight: bold; }
        .download-btn { background: #ff5722; color: white; padding: 8px 15px; border-radius: 20px; border: none; cursor: pointer; font-size: 12px; }

        .recording { background: #f44336 !important; color: white; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(244,67,54,0.4); } 70% { box-shadow: 0 0 0 10px rgba(244,67,54,0); } 100% { box-shadow: 0 0 0 0 rgba(244,67,54,0); } }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>
    <div class="container">
        <div class="menu-bar">
            <div class="menu-row">
                <div class="logo">TextDoc 🏥</div>
                <div class="selector-group">
                    <span class="label">Choose Doctor:</span>
                    <button class="btn spec-btn active" data-spec="ortho">Orthopedic</button>
                    <button class="btn spec-btn" data-spec="pulmo">Pulmonologist</button>
                    <button class="btn spec-btn" data-spec="psych">Psychiatrist</button>
                </div>
            </div>
            <div class="menu-row">
                <div class="selector-group">
                    <span class="label">Language:</span>
                    <button class="btn lang-btn active" data-lang="en-US">English</button>
                    <button class="btn lang-btn" data-lang="hi-IN">हिन्दी</button>
                    <button class="btn lang-btn" data-lang="ta-IN">தமிழ்</button>
                    <button class="btn lang-btn" data-lang="mr-IN">मराठी</button>
                    <button class="btn lang-btn" data-lang="bn-IN">বাংলা</button>
                </div>
                <button class="download-btn" id="downloadBtn">📥 Download Case Summary</button>
            </div>
        </div>

        <div class="chat-main">
            <div class="chat-header">
                <span id="headerTitle">Orthopedic Consultation</span>
                <span id="statusIndicator" style="font-size: 12px;">● Dr. Bindra is Online</span>
            </div>
            <div class="chat-messages" id="chatMessages"></div>
        </div>

        <div class="controls">
            <input type="text" class="message-input" id="messageInput" placeholder="Describe your symptoms...">
            <button class="icon-btn" id="sttBtn" title="Speak" style="background:#4caf50; color:white;">🎤</button>
            <button class="icon-btn" id="ttsBtn" title="Listen" style="background:#9c27b0; color:white;">🔊</button>
            <button class="send-btn" id="sendBtn">SEND</button>
        </div>
    </div>

    <script>
        class UnifiedMessenger {
            constructor() {
                this.lang = 'en-US';
                this.specialty = 'ortho';
                this.chatHistory = [];
                
                // Integrated Databases from your files
                this.doctors = {
                    ortho: { name: "Dr. Bindra", title: "Orthopedic", welcome: "Hello, I am Dr. Bindra. How can I help with your bone or joint concern today?", keys: ['pain', 'knee', 'back', 'bone'] },
                    pulmo: { name: "Dr. Kulkarni", title: "Pulmonologist", welcome: "Hello, I am Dr. Kulkarni. Are you experiencing any breathing difficulties or persistent coughing?", keys: ['cough', 'breath', 'chest', 'asthma'] },
                    psych: { name: "Dr. Mehra", title: "Psychiatrist", welcome: "Hello, I am Dr. Mehra. This is a safe space. How have you been feeling mentally lately?", keys: ['anxiety', 'depressed', 'sleep', 'stress'] }
                };

                this.synth = window.speechSynthesis;
                const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
                this.recognition = SR ? new SR() : null;

                this.init();
            }

            init() {
                // Event Listeners
                document.querySelectorAll('.spec-btn').forEach(b => b.onclick = (e) => this.switchSpecialty(e.target.dataset.spec));
                document.querySelectorAll('.lang-btn').forEach(b => b.onclick = (e) => this.switchLanguage(e.target));
                document.getElementById('sendBtn').onclick = () => this.handleMessage();
                document.getElementById('messageInput').onkeypress = (e) => e.key === 'Enter' && this.handleMessage();
                document.getElementById('sttBtn').onclick = () => this.startSTT();
                document.getElementById('ttsBtn').onclick = () => this.playLastDoctorMessage();
                document.getElementById('downloadBtn').onclick = () => this.generateSummary();

                this.switchSpecialty('ortho');
            }

            switchSpecialty(spec) {
                this.specialty = spec;
                document.querySelectorAll('.spec-btn').forEach(b => b.classList.toggle('active', b.dataset.spec === spec));
                document.getElementById('headerTitle').innerText = `${this.doctors[spec].title} Consultation`;
                document.getElementById('statusIndicator').innerText = `● ${this.doctors[spec].name} is Online`;
                this.clearChat();
                this.addMessage('doctor', this.doctors[spec].welcome);
            }

            switchLanguage(btn) {
                this.lang = btn.dataset.lang;
                document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
            }

            handleMessage() {
                const input = document.getElementById('messageInput');
                const text = input.value.trim();
                if (!text) return;

                this.addMessage('patient', text);
                input.value = '';

                // Messenger Logic: Doctor replies based on user input keywords
                setTimeout(() => {
                    const reply = this.generateReply(text);
                    this.addMessage('doctor', reply);
                    this.autoSpeak(reply);
                }, 1000);
            }

            generateReply(input) {
                const low = input.toLowerCase();
                const doc = this.doctors[this.specialty];
                
                if (this.specialty === 'ortho') {
                    if (low.includes('knee')) return "Is the knee pain sharp, or does it happen mostly when you are active?";
                    if (low.includes('back')) return "Does the back pain radiate down your legs or stay in one area?";
                } else if (this.specialty === 'pulmo') {
                    if (low.includes('cough')) return "How long have you had this cough? Is it producing any mucus?";
                } else if (this.specialty === 'psych') {
                    if (low.includes('anxiety') || low.includes('stress')) return "I understand. Have these feelings been affecting your sleep or appetite?";
                }
                return "Thank you for sharing. Could you tell me more about when these symptoms first started?";
            }

            addMessage(role, text) {
                const name = role === 'doctor' ? this.doctors[this.specialty].name : "Patient";
                const time = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
                this.chatHistory.push({ role, name, text, time });

                const msgDiv = document.createElement('div');
                msgDiv.className = `message ${role}`;
                msgDiv.innerHTML = `
                    <div class="sender-name">${name} • ${time}</div>
                    <div class="bubble">${text}</div>
                `;
                const container = document.getElementById('chatMessages');
                container.appendChild(msgDiv);
                container.scrollTop = container.scrollHeight;
            }

            autoSpeak(text) {
                this.synth.cancel();
                const utter = new SpeechSynthesisUtterance(text);
                utter.lang = this.lang;
                this.synth.speak(utter);
            }

            playLastDoctorMessage() {
                const docMsgs = this.chatHistory.filter(m => m.role === 'doctor');
                if (docMsgs.length > 0) this.autoSpeak(docMsgs[docMsgs.length - 1].text);
            }

            startSTT() {
                if (!this.recognition) return alert("STT not supported");
                this.recognition.lang = this.lang;
                this.recognition.start();
                const btn = document.getElementById('sttBtn');
                btn.classList.add('recording');
                this.recognition.onresult = (e) => {
                    document.getElementById('messageInput').value = e.results[0][0].transcript;
                    btn.classList.remove('recording');
                };
                this.recognition.onerror = () => btn.classList.remove('recording');
            }

            generateSummary() {
                const symptoms = this.chatHistory.filter(m => m.role === 'patient').map(m => m.text);
                let report = `CASE SUMMARY REPORT\n====================\n`;
                report += `Date: ${new Date().toLocaleDateString()}\n`;
                report += `Specialty: ${this.doctors[this.specialty].title}\n`;
                report += `Doctor: ${this.doctors[this.specialty].name}\n\n`;
                report += `PATIENT SYMPTOMS:\n- ${symptoms.length > 0 ? symptoms.join('\n- ') : 'None recorded.'}\n\n`;
                report += `FULL CHAT LOG:\n` + this.chatHistory.map(m => `[${m.time}] ${m.name}: ${m.text}`).join('\n');

                const blob = new Blob([report], { type: 'text/plain' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `Case_Summary_${this.specialty}.txt`;
                a.click();
            }

            clearChat() {
                document.getElementById('chatMessages').innerHTML = '';
                this.chatHistory = [];
            }
        }

        document.addEventListener('DOMContentLoaded', () => new UnifiedMessenger());
    </script>
</body>
</html>
