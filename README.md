# Blog-Karussell mit automatischer Rotation

Dieses Projekt implementiert ein intelligentes Blog-Karussell, das die 6 neuesten Beiträge aus einem RSS-Feed anzeigt. Die Rotationslogik passt sich dynamisch an das Veröffentlichungsdatum des neuesten Artikels an.

## 🎯 Hauptmerkmale

*   **Anzeige der 6 neuesten Blogbeiträge**: Das Karussell zeigt stets die aktuellsten Inhalte.
*   **Intelligente Rotations-Logik**:
    *   **Chronologische Rotation**: Wenn der neueste Artikel weniger als 7 Tage alt ist, werden die Artikel in chronologischer Reihenfolge rotiert.
    *   **Zufällige Rotation**: Wenn seit mehr als 7 Tagen kein neuer Artikel erschienen ist, wechselt das Karussell in einen Zufallsmodus, um die Inhalte frisch zu halten.
*   **RSS-Integration**: Daten werden direkt von einer RSS-API bezogen und verarbeitet.
*   **Automatische Rotation**: Die Artikel wechseln automatisch (z.B. alle 8 Sekunden), mit Pause bei Benutzerinteraktion (Hover).

## 🏗️ Technische Architektur

Das System ist in mehrere Module unterteilt:

1.  **RSS Fetcher**: Abrufen des XML-Feeds von der API.
2.  **Transformer**: Umwandlung der XML-Daten in ein strukturiertes JSON-Datenmodell.
3.  **Rotations-Logik**: Berechnung des Alters des neuesten Beitrags und Festlegung der Sortierreihenfolge.
4.  **HTML-Generator**: Erzeugung des HTML-Markups für das Frontend.
5.  **Client-Side Controller**: Steuerung der Animationen, Transitionen und Benutzerinteraktionen im Browser.

## 🛠️ Technologie-Stack

*   **Backend**: Node.js
*   **Dependencies**: `axios`, `xml2js`, `node-cache`, `express`
*   **Frontend**: HTML5, CSS3 (Transitions), Vanilla JavaScript

## 🚀 Installation und Ausführung

### Voraussetzungen

*   Node.js (LTS Version empfohlen)
*   npm

### Installation

```bash
npm install
```

### Verwendung

Die Hauptlogik kann entweder zur Build-Zeit (statische Generierung) oder zur Laufzeit (Server-Side mit Caching) ausgeführt werden.

**Beispiel für die Generierung:**

```javascript
const { generateBlogCarousel } = require('./blog-carousel-controller');

generateBlogCarousel('IHRE_RSS_FEED_URL')
  .then(result => {
    console.log('HTML generiert:', result.html);
  });
```

## 📝 Geplante Implementierungsschritte

1.  **Phase 1**: Grundstruktur und RSS-Fetching.
2.  **Phase 2**: Implementierung der Rotations-Logik (7-Tage-Regel).
3.  **Phase 3**: HTML-Generierung und CSS-Styling.
4.  **Phase 4**: Client-seitige Animations-Logik.
5.  **Phase 5**: Integration und Testing.
