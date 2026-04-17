# HTML Presentation Template

Reference architecture for generating slide presentations. Every presentation follows this exact structure.

## Base HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{PRESENTATION_TITLE}}</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,300;0,400;0,500;0,600;0,700;1,400&family=Quicksand:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg-dark: #1a1a2e;
    --accent: #e94560;
    --text-white: #FFFFFF;
    --text-gray: rgba(255, 255, 255, 0.7);
    --line-pink: rgba(252, 49, 101, 0.5);
    
    /* Animation timing */
    --ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
    --duration-normal: 0.6s;
  }

  body, html {
    margin: 0;
    padding: 0;
    background: #000;
    overflow: hidden;
    width: 100vw;
    height: 100vh;
  }

  /* 1920x1080 Container that dynamically scales to viewport */
  .slide {
    width: 1920px;
    height: 1080px;
    background-color: var(--bg-dark);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%) scale(var(--scale-factor, 1));
    overflow: hidden;
    font-family: 'Montserrat', sans-serif;
    color: var(--text-white);
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    opacity: 0;
    visibility: hidden;
    transition: opacity 0.5s ease, visibility 0.5s ease;
  }

  .slide.active {
    opacity: 1;
    visibility: visible;
    z-index: 10;
  }

  /* Presentation Controls */
  .progress-bar {
      position: fixed;
      top: 0;
      left: 0;
      height: 4px;
      background: var(--accent);
      transition: width 0.3s ease;
      z-index: 1000;
  }

  .nav-dots {
      position: fixed;
      right: 20px;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      flex-direction: column;
      gap: 10px;
      z-index: 1000;
  }

  .nav-dot {
      width: 12px;
      height: 12px;
      border-radius: 50%;
      background: rgba(255,255,255,0.3);
      cursor: pointer;
      transition: background 0.3s ease, transform 0.3s ease;
  }

  .nav-dot.active {
      background: var(--accent);
      transform: scale(1.3);
  }

  /* Backgrounds */
  .slide-title-bg {
    background-image: url('assets/bg-title.png');
  }

  .slide-content-bg {
    background-image: url('assets/bg-content.png');
  }

  /* Logos & Footer */
  .logo-top-left {
    position: absolute;
    top: 106px;
    left: 153px;
    width: 190px;
  }

  .footer {
    position: absolute;
    bottom: 50px;
    right: 50px;
    display: flex;
    align-items: center;
    gap: 15px;
  }
  .footer span {
    font-size: 20px;
    color: var(--text-gray);
    font-weight: 400;
  }
  .footer img {
    height: 35px;
  }

  /* Typography */
  h1 {
    font-family: 'Montserrat', sans-serif;
    font-size: 72px;
    font-weight: 700;
    margin: 0;
    position: absolute;
    top: 96px;
    left: 80px;
  }

  .title-slide-text {
    position: absolute;
    bottom: 150px;
    left: 153px;
    font-size: 80px;
    font-weight: 700;
    width: 1600px;
    line-height: 1.2;
  }

  /* Standard Content Area */
  .content-area {
    position: absolute;
    top: 190px;
    left: 80px;
    right: 80px;
    font-size: 28px;
    line-height: 1.4;
    font-weight: 400;
  }

  .subtitle {
    font-size: 32px;
    font-weight: 600;
    margin-bottom: 10px;
  }

  .intro-text {
    margin-bottom: 20px;
    color: var(--text-gray);
  }
  
  ul {
    margin: 0;
    padding-left: 40px;
    list-style-type: disc;
    font-size: 40px;
    line-height: 1.6;
  }
  li {
    margin-bottom: 15px;
  }
  ul ul {
    margin-top: 15px;
    list-style-type: circle;
    font-size: 36px;
  }

  /* Custom Layouts - Use these when generating content */
  .metrics-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    margin-bottom: 25px;
  }

  .metric-item {
    background: rgba(255,255,255,0.05);
    border-left: 4px solid var(--accent);
    padding: 20px;
    border-radius: 0 10px 10px 0;
  }

  .metric-value {
    font-size: 42px;
    font-weight: 700;
    color: var(--accent);
    margin-bottom: 5px;
    display: flex;
    align-items: center;
    gap: 15px;
  }

  .metric-desc {
    font-weight: 600;
    margin-bottom: 5px;
    font-size: 24px;
  }

  .metric-sub {
    font-size: 20px;
    color: var(--text-gray);
  }

  .bottom-text {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    font-size: 24px;
  }

  .bottom-box {
    background: rgba(0,0,0,0.2);
    padding: 20px;
    border-radius: 10px;
  }
  
  .bottom-box strong {
    color: var(--accent);
    font-weight: 600;
  }

  /* Agenda Layout */
  .agenda-container {
    position: absolute;
    top: 340px;
    left: 80px;
    right: 80px;
  }
  .agenda-row {
    border-bottom: 2px solid var(--line-pink);
    height: 100px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .agenda-row:first-child {
    border-top: 2px solid var(--line-pink);
  }
  .agenda-row .text {
    font-size: 40px;
    font-weight: 400;
  }
  .agenda-row .number {
    font-size: 64px;
    font-weight: 700;
    color: var(--accent);
  }

  /* Animations */
  .reveal {
      opacity: 0;
      transform: translateY(30px);
      transition: opacity var(--duration-normal) var(--ease-out-expo),
                  transform var(--duration-normal) var(--ease-out-expo);
  }

  .slide.active .reveal {
      opacity: 1;
      transform: translateY(0);
  }

  /* Stagger children for sequential reveal */
  .reveal:nth-child(1) { transition-delay: 0.1s; }
  .reveal:nth-child(2) { transition-delay: 0.2s; }
  .reveal:nth-child(3) { transition-delay: 0.3s; }
  .reveal:nth-child(4) { transition-delay: 0.4s; }
  .reveal:nth-child(5) { transition-delay: 0.5s; }
  .reveal:nth-child(6) { transition-delay: 0.6s; }
  .reveal:nth-child(7) { transition-delay: 0.7s; }
  .reveal:nth-child(8) { transition-delay: 0.8s; }


  /* Print to PDF support */
  @media print {
    @page {
      size: 1920px 1080px;
      margin: 0;
    }
    body, html {
      overflow: visible !important;
      height: auto !important;
    }
    .slide {
      position: relative !important;
      transform: none !important;
      opacity: 1 !important;
      visibility: visible !important;
      page-break-after: always;
      break-after: page;
      top: auto !important;
      left: auto !important;
      -webkit-print-color-adjust: exact;
      print-color-adjust: exact;
    }
    .nav-dots, .progress-bar, #speaker-notes-panel { display: none !important; }
    .reveal { opacity: 1 !important; transform: none !important; }
  }


  /* Speaker Notes Panel */
  body.presenter-mode .slide {
    left: 8.5% !important; /* Shift slides far left */
    transform: translate(-50%, -50%) scale(calc(var(--scale-factor) * 0.15)) !important;
  }
  
  #speaker-notes-panel {
    position: fixed;
    top: 0;
    right: -100%;
    width: 83.33%;
    min-width: 800px;
    height: 100vh;
    background: #111;
    color: #fff;
    z-index: 2000;
    transition: right 0.3s ease;
    border-left: 2px solid var(--accent);
    display: flex;
    flex-direction: column;
    font-family: 'Quicksand', sans-serif;
  }

  body.presenter-mode #speaker-notes-panel {
    right: 0;
  }

  #speaker-notes-header {
    background: #000;
    padding: 20px;
    border-bottom: 1px solid #333;
    font-weight: bold;
    font-size: 24px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  
  .font-controls {
    display: flex;
    gap: 10px;
    margin-right: 30px;
  }
  .font-btn {
    background: #333;
    color: #fff;
    border: 1px solid #555;
    border-radius: 4px;
    width: 36px;
    height: 36px;
    font-size: 24px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .font-btn:hover { background: var(--accent); border-color: var(--accent); }
  
  .time-container {
    display: flex;
    gap: 30px;
    align-items: center;
  }

  .time-block {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }

  .time-label {
    font-size: 14px;
    color: var(--text-gray);
    text-transform: uppercase;
    letter-spacing: 1px;
    margin-bottom: 4px;
  }

  #clock, #timer {
    color: var(--accent);
    font-family: monospace;
    font-size: 32px;
  }

  #speaker-notes-content {
    padding: 40px;
    flex-grow: 1;
    overflow-y: auto;
    font-size: 32px;
    line-height: 1.6;
    color: #ddd;
  }
  
  #speaker-notes-content p {
    margin-top: 0;
    margin-bottom: 25px;
  }
  
  #speaker-notes-content ul {
    font-size: 32px;
    padding-left: 30px;
    margin-bottom: 25px;
  }
  
  #speaker-notes-content li {
    margin-bottom: 15px;
  }

  /* Hide raw notes in the DOM */
  .notes { display: none !important; }
</style>
</head>
<body>

  <!-- ==================== SPEAKER NOTES PANEL ==================== -->
  <div id="speaker-notes-panel">
    <div id="speaker-notes-header">
      <div style="display: flex; align-items: center;">
        <span style="margin-right: 30px;">Speaker Notes</span>
        <div class="font-controls">
          <button class="font-btn" id="font-decrease" title="Decrease Font Size">-</button>
          <button class="font-btn" id="font-increase" title="Increase Font Size">+</button>
        </div>
      </div>
      <div class="time-container">
        <div class="time-block">
          <span class="time-label">Timer</span>
          <span id="timer">00:00:00</span>
        </div>
        <div class="time-block">
          <span class="time-label">Clock</span>
          <span id="clock">00:00</span>
        </div>
      </div>
    </div>
    <div id="speaker-notes-content">Press 'S' to toggle this panel. Open this HTML in two tabs: share one tab with your audience, and use the other tab with this panel open. They will sync automatically.</div>
  </div>

  <!-- ==================== EXAMPLE TITLE SLIDE ==================== -->
  <div class="slide slide-title-bg" id="slide1">
    <img src="assets/logo-large.png" alt="your brand Logo" class="logo-top-left reveal">
    <div class="title-slide-text reveal">{{SLIDE_TITLE_HERE}}</div>
    <div class="notes">
      <p>Welcome everyone to the presentation.</p>
      <p>Today we will be discussing...</p>
      <ul>
        <li>Point 1</li>
        <li>Point 2</li>
      </ul>
    </div>
  </div>

  <!-- ==================== EXAMPLE AGENDA SLIDE ==================== -->
  <div class="slide slide-content-bg" id="slide2">
    <h1 class="reveal">Agenda</h1>
    <div class="agenda-container reveal">
      <div class="agenda-row">
        <div class="text">Item 1</div>
        <div class="number">01</div>
      </div>
      <div class="agenda-row">
        <div class="text">Item 2</div>
        <div class="number">02</div>
      </div>
    </div>
    <!-- DO NOT change the "{{FOOTER_TEXT}}" footer text — it is required on every content slide -->
    <div class="footer reveal">
      <span>{{FOOTER_TEXT}}</span>
      <img src="assets/logo-small.png" alt="your brand Logo">
    </div>
  </div>

  <!-- ==================== EXAMPLE CONTENT SLIDE ==================== -->
  <div class="slide slide-content-bg" id="slide3">
    <h1 class="reveal">Content Title</h1>
    <div class="content-area">
      <div class="subtitle reveal">Optional Subtitle Text</div>
      <div class="intro-text reveal">Intro paragraph explaining the slide contents.</div>
      
      <div class="metrics-grid reveal">
        <div class="metric-item">
          <div class="metric-value">📈 Value</div>
          <div class="metric-desc">Metric Description</div>
          <div class="metric-sub">Subtext here</div>
        </div>
        <!-- Add more grid items as needed -->
      </div>
    </div>
    <!-- DO NOT change the "{{FOOTER_TEXT}}" footer text — it is required on every content slide -->
    <div class="footer reveal">
      <span>{{FOOTER_TEXT}}</span>
      <img src="assets/logo-small.png" alt="your brand Logo">
    </div>
  </div>

  <!-- ==================== INTERACTIVE JAVASCRIPT ==================== -->
  <script>
    class SlidePresentation {
        constructor() {
            this.slides = document.querySelectorAll('.slide');
            this.currentSlide = 0;
            this.channel = new BroadcastChannel('presentation-sync');
            this.isPresenterMode = false;
            
            this.setupScaling();
            this.setupNavigation();
            this.setupProgressBar();
            this.setupNavDots();
            this.setupClock();
            this.setupNotesControls();
            
            // Listen for sync events from other tabs
            this.channel.onmessage = (event) => {
                if (event.data.type === 'NAVIGATE' && event.data.slideIndex !== this.currentSlide) {
                    this.goToSlide(event.data.slideIndex, false);
                }
            };
            
            this.goToSlide(0, false);
        }

        setupScaling() {
            const resize = () => {
                const scale = Math.min(window.innerWidth / 1920, window.innerHeight / 1080);
                document.documentElement.style.setProperty('--scale-factor', scale);
            };
            window.addEventListener('resize', resize);
            resize();
        }

        setupNavigation() {
            // Keyboard
            document.addEventListener('keydown', (e) => {
                if (e.key === 'ArrowRight' || e.key === 'Space' || e.key === 'PageDown') {
                    this.nextSlide();
                } else if (e.key === 'ArrowLeft' || e.key === 'PageUp') {
                    this.prevSlide();
                } else if (e.key.toLowerCase() === 's') {
                    this.togglePresenterMode();
                }
            });

            // Touch
            let touchStartX = 0;
            document.addEventListener('touchstart', e => {
                touchStartX = e.changedTouches[0].screenX;
            });
            document.addEventListener('touchend', e => {
                const touchEndX = e.changedTouches[0].screenX;
                if (touchStartX - touchEndX > 50) this.nextSlide();
                else if (touchEndX - touchStartX > 50) this.prevSlide();
            });
            
            // Mouse wheel
            let isScrolling = false;
            document.addEventListener('wheel', (e) => {
                if (isScrolling) return;
                isScrolling = true;
                if (e.deltaY > 0) this.nextSlide();
                else if (e.deltaY < 0) this.prevSlide();
                setTimeout(() => isScrolling = false, 500);
            });
        }
        
        togglePresenterMode() {
            this.isPresenterMode = !this.isPresenterMode;
            if (this.isPresenterMode) {
                document.body.classList.add('presenter-mode');
                this.updateSpeakerNotes();
                if (this.startTimer) this.startTimer();
            } else {
                document.body.classList.remove('presenter-mode');
            }
        }
        

        setupNotesControls() {
            this.notesFontSize = 32;
            const btnInc = document.getElementById('font-increase');
            const btnDec = document.getElementById('font-decrease');
            const content = document.getElementById('speaker-notes-content');
            
            if (btnInc && btnDec && content) {
                btnInc.addEventListener('click', () => {
                    this.notesFontSize += 4;
                    content.style.fontSize = `${this.notesFontSize}px`;
                    content.querySelectorAll('ul').forEach(ul => ul.style.fontSize = `${this.notesFontSize}px`);
                });
                btnDec.addEventListener('click', () => {
                    this.notesFontSize = Math.max(16, this.notesFontSize - 4);
                    content.style.fontSize = `${this.notesFontSize}px`;
                    content.querySelectorAll('ul').forEach(ul => ul.style.fontSize = `${this.notesFontSize}px`);
                });
            }
        }

        setupClock() {
            // Standard Clock
            setInterval(() => {
                const now = new Date();
                const clock = document.getElementById('clock');
                if (clock) {
                    clock.innerText = now.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'});
                }
            }, 1000);
            
            // Presentation Timer
            this.startTime = null;
            this.timerInterval = null;
            
            // Start timer automatically when presenter mode is toggled for the first time
            this.startTimer = () => {
                if (!this.startTime) {
                    this.startTime = Date.now();
                    this.timerInterval = setInterval(() => {
                        const elapsed = Math.floor((Date.now() - this.startTime) / 1000);
                        const h = String(Math.floor(elapsed / 3600)).padStart(2, '0');
                        const m = String(Math.floor((elapsed % 3600) / 60)).padStart(2, '0');
                        const s = String(elapsed % 60).padStart(2, '0');
                        const timer = document.getElementById('timer');
                        if (timer) timer.innerText = `${h}:${m}:${s}`;
                    }, 1000);
                }
            };
            
            // Click to reset timer
            const timerEl = document.getElementById('timer');
            if (timerEl) {
                timerEl.style.cursor = 'pointer';
                timerEl.title = 'Click to reset timer';
                timerEl.addEventListener('click', () => {
                    this.startTime = Date.now();
                    timerEl.innerText = '00:00:00';
                });
            }
        }

        setupProgressBar() {
            const bar = document.createElement('div');
            bar.className = 'progress-bar';
            document.body.appendChild(bar);
            this.progressBar = bar;
        }

        setupNavDots() {
            const dotsContainer = document.createElement('nav');
            dotsContainer.className = 'nav-dots';
            this.slides.forEach((_, i) => {
                const dot = document.createElement('div');
                dot.className = 'nav-dot';
                dot.addEventListener('click', () => this.goToSlide(i));
                dotsContainer.appendChild(dot);
            });
            document.body.appendChild(dotsContainer);
            this.dots = Array.from(dotsContainer.children);
        }
        
        updateSpeakerNotes() {
            const notesContent = document.getElementById('speaker-notes-content');
            if (!notesContent) return;
            
            const activeSlide = this.slides[this.currentSlide];
            const notesEl = activeSlide.querySelector('.notes');
            
            if (notesEl) {
                notesContent.innerHTML = notesEl.innerHTML;
            } else {
                notesContent.innerHTML = '<p style="color:#666; font-style:italic;">No notes for this slide.</p>';
            }
        }

        goToSlide(index, broadcast = true) {
            if (index < 0 || index >= this.slides.length) return;
            
            this.slides[this.currentSlide].classList.remove('active');
            if (this.dots) this.dots[this.currentSlide].classList.remove('active');
            
            this.currentSlide = index;
            
            this.slides[this.currentSlide].classList.add('active');
            if (this.dots) this.dots[this.currentSlide].classList.add('active');
            
            if (this.progressBar) {
                const progress = (this.currentSlide / (this.slides.length - 1)) * 100;
                this.progressBar.style.width = `${progress}%`;
            }
            
            if (this.isPresenterMode) {
                this.updateSpeakerNotes();
            }
            
            if (broadcast) {
                this.channel.postMessage({ type: 'NAVIGATE', slideIndex: index });
            }
        }

        nextSlide() {
            this.goToSlide(this.currentSlide + 1);
        }

        prevSlide() {
            this.goToSlide(this.currentSlide - 1);
        }
    }

    new SlidePresentation();
  </script>
</body>
</html>
```