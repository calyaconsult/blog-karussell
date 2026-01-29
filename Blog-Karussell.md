# Blog-Karussell mit automatischer Rotation

User: Der Kunde beschreibt sein Anliegen so: "Immer wenn im Blog ein neuer Artikel kommt, dreht sich das Karussell der auf meiner Startseite angezeigten 6 letzten Blogbeiträge. Und wenn nach 7 Tagen nichts Neues kommt, dreht es sich trotzdem weiter und wechselt zufällig die Artikel. Also, immer nur ein frischer Artikel."
Kannst du daraus einen Plan erstellen, was zu programmieren ist?

myAI: 📋 Programmierplan: Blog-Karussell mit automatischer Rotation
Basierend auf der Beschreibung erstelle ich dir einen strukturierten Umsetzungsplan:
🎯 Funktionale Anforderungen
Hauptfeatures:

Karussell zeigt die 6 neuesten Blogbeiträge
Automatische Rotation der Artikel
Intelligente Anzeige-Logik basierend auf Veröffentlichungsdatum


🏗️ Technische Komponenten
1. Backend/Datenstruktur
```javascript
// Blogpost-Datenmodell
{
  id: string,
  title: string,
  content: string,
  publishedDate: timestamp,
  imageUrl: string,
  excerpt: string
}
```
Zu implementieren:

Funktion zum Abrufen der 6 neuesten Blogbeiträge
Prüfung des letzten Veröffentlichungsdatums
Logik zur Bestimmung des Rotationsmodus

2. Rotations-Logik
IF (neuerster Artikel < 7 Tage alt)
  → Chronologische Rotation (neuester zuerst)
ELSE
  → Zufällige Rotation der 6 Artikel

Zu programmieren:

Zeitberechnung: Differenz zwischen aktuellem Datum und letztem Artikel
Shuffle-Algorithmus für zufällige Reihenfolge (z.B. Fisher-Yates)
Rotation-Timer/Interval

3. Frontend-Karussell-Komponente
Features:

Anzeige eines Artikels zur Zeit
Automatischer Wechsel (z.B. alle 5-10 Sekunden)
Smooth Transitions/Animationen
Optional: Navigationspfeile und Indikatoren

Zu entwickeln:
```javascript
// Pseudo-Code
class BlogCarousel {
  - articles: Array[6]
  - currentIndex: number
  - rotationMode: 'chronological' | 'random'
  - rotationInterval: timer

  methods:
  - fetchLatestArticles()
  - determineRotationMode()
  - rotateToNext()
  - shuffleArticles()
  - startAutoRotation()
  - stopAutoRotation()
}
```
4. Auto-Update Mechanismus
Optionen:

Polling: Regelmässige Überprüfung auf neue Artikel (z.B. alle 5 Minuten)
WebSocket/SSE: Push-Benachrichtigung bei neuem Artikel
Page Load: Beim Laden der Startseite neue Artikel abrufen


📝 Implementierungsschritte
Phase 1: Backend

✅ API-Endpoint: GET /api/blog/latest?limit=6
✅ Funktion zur Berechnung der Tage seit letzter Veröffentlichung
✅ Logik für Rotationsmodus-Bestimmung

Phase 2: Frontend-Komponente

✅ Karussell-UI erstellen (HTML/CSS)
✅ State-Management für aktuelle Artikel und Index
✅ Animation/Transition-Effekte
✅ Auto-Rotation Timer implementieren

Phase 3: Logik-Integration

✅ Artikel vom Backend laden
✅ Rotationsmodus anwenden (chronologisch vs. zufällig)
✅ Automatischen Wechsel starten

Phase 4: Optimierung

✅ Preloading von Bildern
✅ Pause bei Benutzerinteraktion
✅ Responsive Design
✅ Performance-Optimierung


🔧 Technologie-Empfehlungen
Frontend:

React/Vue/Svelte für Komponente
CSS Transitions oder Framer Motion für Animationen
SwiperJS oder selbstgebaute Lösung

Backend:

REST API oder GraphQL
Caching für Performance (Redis)


❓ Zu klärende Punkte

Rotationsgeschwindigkeit: Wie viele Sekunden pro Artikel?
Neue Artikel-Trigger: Soll das Karussell sofort reagieren oder erst beim nächsten Seitenaufruf?
Benutzerinteraktion: Manuelle Navigation erlauben? Auto-Rotation pausieren bei Hover?
Mobile-Ansicht: Swipe-Gesten implementieren?

Soll ich für einen bestimmten Teil detaillierteren Code oder eine spezifische Technologie-Implementierung ausarbeiten? 🚀

User: Die Frequenz der Blog-Posts ist niedrig, im Durchschnitt weniger als einmal am Tag.
Ich hatte daran gedacht, den Blog Feed zunächst als RSS herunterzuladen; dafür ist eine API vorhanden. Das RSS (XML) wird dann in das Blogpost-Datenmodell (JSON) überführt.
Aus den JSON-Daten wird dann mit Nodejs HTML generiert.

myAI: 📋 Überarbeiteter Programmierplan: RSS-basiertes Blog-Karussell
Perfekt! Mit diesen Details wird die Architektur deutlich klarer. Hier ist der angepasste Plan:
🏗️ Architektur-Übersicht
RSS Feed (API) → XML Parser → JSON Transformation → HTML Generation → Karussell-Logik


📦 Zu entwickelnde Module
Modul 1: RSS Feed Fetcher
```javascript
// rss-fetcher.js
const axios = require('axios');
const xml2js = require('xml2js');

async function fetchRSSFeed(feedUrl) {
  try {
    const response = await axios.get(feedUrl);
    const parser = new xml2js.Parser();
    const xmlData = await parser.parseStringPromise(response.data);
    return xmlData;
  } catch (error) {
    console.error('RSS Fetch Error:', error);
    throw error;
  }
}

module.exports = { fetchRSSFeed };
```

Dependencies:

axios für HTTP-Requests
xml2js oder fast-xml-parser für XML-Parsing


Modul 2: XML → JSON Transformer
```javascript
// blog-transformer.js

function transformRSSToJSON(rssData) {
  const items = rssData.rss.channel[0].item || [];

  const blogPosts = items.slice(0, 6).map(item => ({
    id: generateId(item.link[0]),
    title: item.title[0],
    excerpt: stripHtml(item.description[0]).substring(0, 200) + '...',
    content: item.description[0] || item['content:encoded'][0],
    publishedDate: new Date(item.pubDate[0]),
    link: item.link[0],
    imageUrl: extractImage(item),
    author: item['dc:creator']?.[0] || 'Unknown'
  }));

  return blogPosts;
}

function extractImage(item) {
  // Verschiedene RSS-Formate berücksichtigen
  return item['media:thumbnail']?.[0]?.$.url
    || item.enclosure?.[0]?.$.url
    || extractFirstImageFromContent(item.description[0])
    || '/default-blog-image.jpg';
}

function stripHtml(html) {
  return html.replace(/<[^>]*>/g, '');
}

function generateId(url) {
  return Buffer.from(url).toString('base64').substring(0, 16);
}

module.exports = { transformRSSToJSON };
```

Modul 3: Rotations-Logik
```javascript
// rotation-logic.js

function determineRotationMode(blogPosts) {
  if (blogPosts.length === 0) return 'empty';

  const newestPost = blogPosts[0];
  const daysSinceLastPost = getDaysDifference(newestPost.publishedDate, new Date());

  return daysSinceLastPost <= 7 ? 'chronological' : 'random';
}

function getDaysDifference(date1, date2) {
  const diffTime = Math.abs(date2 - date1);
  const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
  return diffDays;
}

function shuffleArray(array) {
  // Fisher-Yates Shuffle
  const shuffled = [...array];
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
}

function getOrderedPosts(blogPosts) {
  const mode = determineRotationMode(blogPosts);

  if (mode === 'random') {
    return shuffleArray(blogPosts);
  }
  return blogPosts; // Bereits chronologisch sortiert
}

module.exports = { determineRotationMode, getOrderedPosts, getDaysDifference };
```

Modul 4: HTML Generator
```javascript
// html-generator.js

function generateCarouselHTML(blogPosts, rotationMode) {
  const orderedPosts = rotationMode === 'random'
    ? shuffleArray(blogPosts)
    : blogPosts;

  const carouselItems = orderedPosts.map((post, index) => `
    <div class="carousel-item ${index === 0 ? 'active' : ''}" data-post-id="${post.id}">
      <div class="blog-card">
        <img src="${post.imageUrl}" alt="${escapeHtml(post.title)}" class="blog-image">
        <div class="blog-content">
          <h3 class="blog-title">${escapeHtml(post.title)}</h3>
          <p class="blog-excerpt">${escapeHtml(post.excerpt)}</p>
          <span class="blog-date">${formatDate(post.publishedDate)}</span>
          <a href="${post.link}" class="read-more">Weiterlesen →</a>
        </div>
      </div>
    </div>
  `).join('');

  return `
    <div class="blog-carousel" data-rotation-mode="${rotationMode}">
      <div class="carousel-container">
        ${carouselItems}
      </div>
      <div class="carousel-controls">
        <button class="carousel-prev" aria-label="Vorheriger Artikel">‹</button>
        <div class="carousel-indicators">
          ${orderedPosts.map((_, i) => `
            <span class="indicator ${i === 0 ? 'active' : ''}" data-index="${i}"></span>
          `).join('')}
        </div>
        <button class="carousel-next" aria-label="Nächster Artikel">›</button>
      </div>
    </div>
  `;
}

function escapeHtml(text) {
  const map = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;'
  };
  return text.replace(/[&<>"']/g, m => map[m]);
}

function formatDate(date) {
  return new Intl.DateTimeFormat('de-CH', {
    day: '2-digit',
    month: 'long',
    year: 'numeric'
  }).format(date);
}

module.exports = { generateCarouselHTML };
```

Modul 5: Main Controller
```javascript
// blog-carousel-controller.js

const { fetchRSSFeed } = require('./rss-fetcher');
const { transformRSSToJSON } = require('./blog-transformer');
const { getOrderedPosts, determineRotationMode } = require('./rotation-logic');
const { generateCarouselHTML } = require('./html-generator');

async function generateBlogCarousel(rssFeedUrl) {
  try {
    // 1. RSS Feed abrufen
    const rssData = await fetchRSSFeed(rssFeedUrl);

    // 2. In JSON transformieren
    const blogPosts = transformRSSToJSON(rssData);

    // 3. Rotationsmodus bestimmen
    const rotationMode = determineRotationMode(blogPosts);

    // 4. Posts sortieren/mischen
    const orderedPosts = getOrderedPosts(blogPosts);

    // 5. HTML generieren
    const html = generateCarouselHTML(orderedPosts, rotationMode);

    return {
      html,
      posts: orderedPosts,
      mode: rotationMode,
      generatedAt: new Date().toISOString()
    };
  } catch (error) {
    console.error('Carousel Generation Error:', error);
    return generateErrorHTML();
  }
}

function generateErrorHTML() {
  return {
    html: '<div class="carousel-error">Blog-Beiträge konnten nicht geladen werden.</div>',
    posts: [],
    mode: 'error',
    generatedAt: new Date().toISOString()
  };
}

module.exports = { generateBlogCarousel };
```

🎨 Client-Side JavaScript (Karussell-Animation)
```javascript
// carousel-client.js (im Browser)

class BlogCarousel {
  constructor(containerSelector, rotationInterval = 8000) {
    this.container = document.querySelector(containerSelector);
    this.items = this.container.querySelectorAll('.carousel-item');
    this.indicators = this.container.querySelectorAll('.indicator');
    this.currentIndex = 0;
    this.rotationInterval = rotationInterval;
    this.timer = null;

    this.init();
  }

  init() {
    this.setupControls();
    this.startAutoRotation();
    this.setupHoverPause();
  }

  setupControls() {
    const prevBtn = this.container.querySelector('.carousel-prev');
    const nextBtn = this.container.querySelector('.carousel-next');

    prevBtn?.addEventListener('click', () => this.prev());
    nextBtn?.addEventListener('click', () => this.next());

    this.indicators.forEach((indicator, index) => {
      indicator.addEventListener('click', () => this.goTo(index));
    });
  }

  next() {
    this.goTo((this.currentIndex + 1) % this.items.length);
  }

  prev() {
    this.goTo((this.currentIndex - 1 + this.items.length) % this.items.length);
  }

  goTo(index) {
    this.items[this.currentIndex].classList.remove('active');
    this.indicators[this.currentIndex].classList.remove('active');

    this.currentIndex = index;

    this.items[this.currentIndex].classList.add('active');
    this.indicators[this.currentIndex].classList.add('active');

    this.resetAutoRotation();
  }

  startAutoRotation() {
    this.timer = setInterval(() => this.next(), this.rotationInterval);
  }

  stopAutoRotation() {
    clearInterval(this.timer);
  }

  resetAutoRotation() {
    this.stopAutoRotation();
    this.startAutoRotation();
  }

  setupHoverPause() {
    this.container.addEventListener('mouseenter', () => this.stopAutoRotation());
    this.container.addEventListener('mouseleave', () => this.startAutoRotation());
  }
}

// Initialisierung
document.addEventListener('DOMContentLoaded', () => {
  new BlogCarousel('.blog-carousel', 8000); // 8 Sekunden pro Artikel
});
```

🚀 Integration & Deployment
Option A: Server-Side mit Caching
```javascript
// express-route.js
const express = require('express');
const NodeCache = require('node-cache');
const { generateBlogCarousel } = require('./blog-carousel-controller');

const app = express();
const cache = new NodeCache({ stdTTL: 300 }); // 5 Minuten Cache

app.get('/blog-carousel', async (req, res) => {
  const cached = cache.get('carousel');

  if (cached) {
    return res.json(cached);
  }

  const result = await generateBlogCarousel('https://example.com/rss');
  cache.set('carousel', result);

  res.json(result);
});
```
Option B: Statische Generation (Build-Time)
```javascript
// build-carousel.js
const fs = require('fs').promises;
const { generateBlogCarousel } = require('./blog-carousel-controller');

async function buildCarousel() {
  const result = await generateBlogCarousel('https://example.com/rss');

  await fs.writeFile(
    './public/carousel.html',
    result.html,
    'utf8'
  );

  await fs.writeFile(
    './public/carousel-data.json',
    JSON.stringify(result, null, 2),
    'utf8'
  );

  console.log('✅ Carousel generated successfully');
}

buildCarousel();
```

📋 Implementierungs-Checkliste
Phase 1: Grundstruktur

 Node.js Projekt initialisieren (npm init)
 Dependencies installieren (axios, xml2js)
 RSS Fetcher implementieren
 XML → JSON Transformer schreiben

Phase 2: Logik

 Rotationsmodus-Logik implementieren
 Shuffle-Funktion für zufällige Rotation
 Datumsberechnung (7-Tage-Regel)

Phase 3: HTML-Generierung

 Template-Funktion für Karussell
 CSS-Styling hinzufügen
 Client-Side JavaScript für Animation

Phase 4: Integration

 Main Controller zusammenbauen
 Caching-Strategie implementieren
 Error Handling

Phase 5: Testing & Optimierung

 Mit echtem RSS Feed testen
 Performance optimieren (Bilder lazy-loading)
 Responsive Design prüfen


🔧 package.json
```json
{
  "name": "blog-carousel",
  "version": "1.0.0",
  "dependencies": {
    "axios": "^1.6.0",
    "xml2js": "^0.6.2",
    "node-cache": "^5.1.2",
    "express": "^4.18.2"
  }
}
```
Soll ich einen bestimmten Teil noch detaillierter ausarbeiten oder hast du Fragen zur Implementierung? 💻✨
