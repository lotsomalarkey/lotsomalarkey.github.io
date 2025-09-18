<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>0.4 GPA Activities</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      background: #000;
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: Arial, sans-serif;
      overflow: hidden;
      text-align: center;
    }
    .rainbow {
      background: linear-gradient(90deg, red, orange, yellow, green, blue, indigo, violet);
      -webkit-background-clip: text;
      background-clip: text;
      -webkit-text-fill-color: transparent;
      animation: rainbowShift 5s linear infinite;
      background-size: 400% 100%;
    }
    @keyframes rainbowShift {
      0% { background-position: 0% 50%; }
      100% { background-position: 100% 50%; }
    }
    .page {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.7s ease;
    }
    .page.active {
      opacity: 1;
      pointer-events: auto;
    }
    a.linklike {
      display: block;
      margin: 6px 0;
      color: white;
      text-decoration: none;
      cursor: pointer;
    }
    a.linklike:hover { text-decoration: underline; }
    .visually-hidden { position: absolute; left: -10000px; top: auto; width: 1px; height: 1px; overflow: hidden; }
  </style>
</head>
<body oncontextmenu="return false;">
  <div id="page1" class="page active" role="region" aria-label="Main links">
    <h3 class="rainbow">No matter how hard you try...</h3>
    <h1 class="rainbow">Enjoy every moment of your defeat.</h1>
    <a class="linklike" href="https://classroom.google.com" target="_blank" rel="noopener">Google Classroom</a>
    <a class="linklike" href="https://web.kamihq.com" target="_blank" rel="noopener">Kami</a>
    <a class="linklike" href="https://drive.google.com" target="_blank" rel="noopener">Google Drive</a>
    <a class="linklike" href="https://aeries.guhsd.net" target="_blank" rel="noopener">Aeries</a>
    <a class="linklike" href="https://scholar.google.com" target="_blank" rel="noopener">Google Scholar</a>
    <a class="linklike" href="https://translate.google.com" target="_blank" rel="noopener">Google Translate</a>
  </div>

  <div id="page2" class="page" role="region" aria-label="Hidden links">
    <h3 class="rainbow">You found the hidden side...</h3>
    <h1 class="rainbow">Welcome.</h1>
    <a id="toPage3" class="linklike" role="button" tabindex="0">Reveal next</a>
    <a class="linklike" href="#" data-url="https://canlite.org/">Kami (blank)</a>
    <a class="linklike" href="#" data-url="https://drive.google.com">Google Drive (blank)</a>
    <a class="linklike" href="#" data-url="https://aeries.guhsd.net">Aeries (blank)</a>
    <a class="linklike" href="#" data-url="https://scholar.google.com">Google Scholar (blank)</a>
    <a class="linklike" href="#" data-url="https://translate.google.com">Google Translate (blank)</a>
  </div>

  <div id="page3" class="page" role="region" aria-label="Enter code">
    <h1 class="rainbow">Enjoy</h1>
    <p>(Type the next code...)</p>
  </div>

  <div id="page4" class="page" role="region" aria-label="Final gateway">
    <h3 class="rainbow">Final Gateway</h3>
    <a class="linklike" href="#" data-url="https://gn-math.github.io/">gn-math</a>
    <a class="linklike" href="#" data-url="https://canlite.org/">canlite</a>
    <a class="linklike" href="#" data-url="https://drive.google.com">drive</a>
    <a class="linklike" href="#" data-url="https://aeries.guhsd.net">aeries</a>
    <a class="linklike" href="#" data-url="https://scholar.google.com">scholar</a>
    <a class="linklike" href="#" data-url="https://translate.google.com">translate</a>
  </div>

  <script>
    // disable some devtools shortcuts (best-effort; can't fully prevent users)
    (function() {
      document.addEventListener('keydown', function(e) {
        // F12
        if (e.key === 'F12') { e.preventDefault(); }
        // Ctrl+Shift+I / J / C and Ctrl+U
        if (e.ctrlKey && e.shiftKey && (e.key === 'I' || e.key === 'J' || e.key === 'C')) e.preventDefault();
        if (e.ctrlKey && (e.key === 'u' || e.key === 'U')) e.preventDefault();
      });
    })();

    // secret codes
    const code1 = 'piks';
    const code2 = 'siks';
    let buffer = '';

    document.addEventListener('keydown', function(e) {
      if (!e.key) return;
      buffer += e.key.toLowerCase();
      if (buffer.length > 10) buffer = buffer.slice(-10);

      if (buffer.endsWith(code1)) {
        switchPage('page1', 'page2');
        buffer = '';
      } else if (buffer.endsWith(code2)) {
        switchPage('page3', 'page4');
        buffer = '';
      }
    });

    // open a URL in a new tab/window without using document.write
    // (document.write was causing Live Server's live-reload script to be injected into the string)
    function openBlank(url) {
      try {
        // prefer direct open to let the browser handle the target site
        const win = window.open(url, '_blank', 'noopener');
        if (win) return win;
      } catch (err) {
        // fallback below
      }

      // fallback: create a temporary anchor and click it
      try {
        const a = document.createElement('a');
        a.href = url;
        a.target = '_blank';
        a.rel = 'noopener';
        a.style.display = 'none';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        return true;
      } catch (err) {
        console.warn('Unable to open link', err);
        return null;
      }
    }

    // wire data-url links
    // helper: open about:blank and create an iframe inside it (no document.write)
    function openBlankWithIframe(url) {
      try {
        const win = window.open('about:blank', '_blank');
        if (!win) return null;
        // ensure document exists and create content via DOM APIs
        try {
          win.document.title = url;
          // clear default content
          win.document.body.innerHTML = '';
          win.document.body.style.margin = '0';
          const iframe = win.document.createElement('iframe');
          iframe.src = url;
          iframe.style.border = 'none';
          iframe.style.width = '100%';
          iframe.style.height = '100vh';
          win.document.body.appendChild(iframe);
          return win;
        } catch (innerErr) {
          // fallback to simple navigation
          win.location.href = url;
          return win;
        }
      } catch (err) {
        console.warn('Unable to open about:blank iframe', err);
        return null;
      }
    }

    // wire data-url links; open gn-math and canlite discreetly in about:blank+iframe
    const discreetHosts = ['gn-math.github.io', 'canlite.org'];
    document.querySelectorAll('a[data-url]').forEach(a => {
      a.addEventListener('click', function(ev) {
        ev.preventDefault();
        const url = this.getAttribute('data-url');
        let hostname = '';
        try { hostname = new URL(url).hostname; } catch(e) { hostname = ''; }
        if (discreetHosts.includes(hostname)) {
          openBlankWithIframe(url);
        } else {
          openBlank(url);
        }
      });
    });

    // make 'toPage3' keyboard accessible
    const toPage3 = document.getElementById('toPage3');
    if (toPage3) {
      toPage3.addEventListener('click', () => switchPage('page2', 'page3'));
      toPage3.addEventListener('keydown', (e) => { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); switchPage('page2', 'page3'); } });
    }

    function switchPage(from, to) {
      const f = document.getElementById(from);
      const t = document.getElementById(to);
      if (!f || !t) return;
      f.classList.remove('active');
      setTimeout(() => t.classList.add('active'), 120);
    }
  </script>
</body>
