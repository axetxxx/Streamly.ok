streamly/
├─ index.html        ← Landing / Browse page
├─ watch.html        ← Watch page
├─ videos.json       ← Video metadata
├─ videos/           ← Video files + thumbnails
├─ css/
│  └─ styles.css
├─ js/
│  └─ main.js       ← Browse/search/player logic
└─ README.md
[
  {
    "id": "1",
    "title": "Sample Movie",
    "description": "A short sample video",
    "url": "videos/sample.mp4",
    "thumbnail": "videos/sample.jpg"
  },
  {
    "id": "2",
    "title": "Nature Clip",
    "description": "A beautiful nature short",
    "url": "videos/nature.mp4",
    "thumbnail": "videos/nature.jpg"
  }
]
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Streamly</title>
  <link href="css/styles.css" rel="stylesheet">
</head>
<body class="bg-gray-900 text-white font-sans">
  <header class="p-4 bg-gray-800 flex justify-between items-center">
    <h1 class="text-2xl font-bold">Streamly</h1>
    <input type="text" id="search" placeholder="Search..." class="px-2 py-1 rounded text-black">
  </header>

  <main class="p-4 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4" id="video-grid">
    <!-- Videos will load here -->
  </main>

  <script src="js/main.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Watch Video</title>
  <link href="css/styles.css" rel="stylesheet">
</head>
<body class="bg-gray-900 text-white font-sans p-4">
  <a href="index.html" class="text-blue-400 underline mb-4 inline-block">← Back</a>
  <h1 id="title" class="text-2xl font-bold mb-2"></h1>
  <p id="description" class="mb-4"></p>
  <video id="player" controls class="w-full max-w-4xl rounded" poster="">
    Your browser does not support the video tag.
  </video>

  <script src="js/main.js"></script>
</body>
</html>
// Load videos.json and render
async function loadVideos() {
  const res = await fetch('videos.json');
  const videos = await res.json();
  return videos;
}

// Render browse grid
async function renderBrowse() {
  const grid = document.getElementById('video-grid');
  if (!grid) return;
  const videos = await loadVideos();
  grid.innerHTML = '';

  const searchInput = document.getElementById('search');
  let filtered = videos;

  searchInput?.addEventListener('input', (e) => {
    const term = e.target.value.toLowerCase();
    filtered = videos.filter(v => v.title.toLowerCase().includes(term));
    renderGrid(filtered);
  });

  function renderGrid(list) {
    grid.innerHTML = '';
    list.forEach(video => {
      const card = document.createElement('div');
      card.className = 'bg-gray-800 rounded overflow-hidden cursor-pointer hover:scale-105 transform transition';
      card.innerHTML = `
        <img src="${video.thumbnail}" class="w-full h-48 object-cover">
        <div class="p-2">
          <h2 class="font-bold">${video.title}</h2>
          <p class="text-sm text-gray-300">${video.description}</p>
        </div>
      `;
      card.addEventListener('click', () => {
        window.location.href = `watch.html?id=${video.id}`;
      });
      grid.appendChild(card);
    });
  }

  renderGrid(filtered);
}

// Render watch page
async function renderWatch() {
  const params = new URLSearchParams(window.location.search);
  const id = params.get('id');
  if (!id) return;

  const videos = await loadVideos();
  const video = videos.find(v => v.id === id);
  if (!video) return;

  document.getElementById('title').textContent = video.title;
  document.getElementById('description').textContent = video.description;
  const player = document.getElementById('player');
  player.src = video.url;
  player.poster = video.thumbnail;
}

// Detect page
if (document.getElementById('video-grid')) {
  renderBrowse();
} else if (document.getElementById('player')) {
  renderWatch();
}
@import url('https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css');

/* Custom styles if needed */
body {
  background-color: #111827;
}
# Streamly - Static Video Streaming

This is a **static Netflix-style site** that can be hosted on GitHub Pages.

## How to use

1. Upload videos to `/videos/` and add metadata in `videos.json`.
2. Browse videos on `index.html` and click to watch on `watch.html`.
3. Customize thumbnails, titles, and descriptions.

## Hosting on GitHub Pages

1. Create a GitHub repository and push all files.
2. Go to **Settings → Pages → Source → main branch / root**.
3. Your site will be live at `https://username.github.io/repo-name/`.

> Note: This is a static site, no backend. Uploading new videos requires editing `videos.json` and committing to GitHub.
