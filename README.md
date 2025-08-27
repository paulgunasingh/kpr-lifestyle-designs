// public/service-worker.js

const CACHE_NAME = "kpr-cache-v2";
const urlsToCache = [
  "/",
  "/index.html",
  "/offline.html",
  "/favicon-16x16.png",
  "/favicon-32x32.png",
  "/favicon-180x180.png",
  "/favicon-192x192.png",
  "/favicon-512x512.png",
];

// Install and pre-cache
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => cache.addAll(urlsToCache))
  );
});

// Fetch
self.addEventListener("fetch", (event) => {
  event.respondWith(
    fetch(event.request).catch(() =>
      caches.match(event.request).then((response) =>
        response || caches.match("/offline.html")
      )
    )
  );
});

// Activate and clear old caches
self.addEventListener("activate", (event) => {
  event.waitUntil(
    caches.keys().then((cacheNames) =>
      Promise.all(
        cacheNames.map((name) => {
          if (name !== CACHE_NAME) {
            return caches.delete(name);
          }
        })
      )
    )
  );
});
