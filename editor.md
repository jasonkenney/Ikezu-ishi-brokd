<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Basic Word Processor PWA">
  <meta name="theme-color" content="#000000">
  <link rel="manifest" href="manifest.json">
  <title>Simple Word Processor</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      text-align: center;
    }
    #toolbar {
      margin: 20px;
    }
    #editor {
      border: 1px solid #ccc;
      padding: 10px;
      min-height: 300px;
      max-width: 800px;
      margin: 20px auto;
      text-align: left;
    }
    button {
      margin-right: 5px;
      padding: 10px;
    }
  </style>
</head>
<body>
  <div id="toolbar">
    <button onclick="document.execCommand('bold', false, '');">Bold</button>
    <button onclick="document.execCommand('italic', false, '');">Italic</button>
    <button onclick="document.execCommand('underline', false, '');">Underline</button>
    <button onclick="downloadTextFile()">Save as .txt</button>
  </div>

  <div id="editor" contenteditable="true">
    Start typing here...
  </div>

  <script>
    // Service Worker Registration
    if ('serviceWorker' in navigator) {
      window.addEventListener('load', () => {
        navigator.serviceWorker.register('service-worker.js').then(registration => {
          console.log('Service Worker registered with scope:', registration.scope);
        }).catch(error => {
          console.log('Service Worker registration failed:', error);
        });
      });
    }

    // Function to download the content as a text file
    function downloadTextFile() {
      const text = document.getElementById('editor').innerText;
      const blob = new Blob([text], { type: 'text/plain' });
      const anchor = document.createElement('a');
      anchor.download = 'document.txt';
      anchor.href = window.URL.createObjectURL(blob);
      anchor.click();
    }
  </script>

  <!-- Service Worker Script -->
  <script id="service-worker-script" type="text/javascript">
    const CACHE_NAME = 'word-processor-cache';
    const urlsToCache = [
      '/',
      '/index.html',
      '/style.css',
      '/app.js'
    ];

    self.addEventListener('install', event => {
      event.waitUntil(
        caches.open(CACHE_NAME)
          .then(cache => {
            return cache.addAll(urlsToCache);
          })
      );
    });

    self.addEventListener('fetch', event => {
      event.respondWith(
        caches.match(event.request)
          .then(response => {
            return response || fetch(event.request);
          })
      );
    });
  </script>

  <!-- Manifest JSON (can be served inline or as a separate file) -->
  <script>
    const manifest = {
      "name": "Simple Word Processor",
      "short_name": "WordProc",
      "start_url": "/index.html",
      "display": "standalone",
      "background_color": "#ffffff",
      "theme_color": "#000000",
      "icons": [
        {
          "src": "icon.png",
          "sizes": "192x192",
          "type": "image/png"
        }
      ]
    };
    const blob = new Blob([JSON.stringify(manifest)], {type: 'application/json'});
    const manifestLink = document.createElement('link');
    manifestLink.rel = 'manifest';
    manifestLink.href = URL.createObjectURL(blob);
    document.head.appendChild(manifestLink);
  </script>
</body>
</html>