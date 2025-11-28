# Steam Workshop Collection Link Extractor

A small JavaScript snippet for extracting item IDs, URLs, and titles from any Steam Workshop Collection page.  
Output format and structure can be customized directly inside the script.

## Usage
1. Open any Steam Workshop Collection page.
2. Press F12 to open Developer Tools.
3. Open the Console tab.
4. Paste the script below and press Enter.
5. Copy the printed output from the console.

## Script
```javascript
(function(){

  // ==============================
  // FORMAT TYPE OPTIONS:
  // "id"       = only ID
  // "url"      = only URL
  // "urlTitle" = URL + title
  // "full"     = ID + URL + title
  // ==============================
  const formatType = "url";

  // ==============================
  // OUTPUT MODE OPTIONS:
  // "newline" = one item per line
  // "comma"   = comma-separated
  // ==============================
  const outputMode = "newline";
  // ==============================

  try {
    const container = document.querySelector('.collectionChildren');
    if (!container) return console.warn('collectionChildren not found.');

    const blocks = Array.from(container.querySelectorAll('.collectionItemDetails'));
    const results = [];
    const seen = new Set();

    blocks.forEach(block => {
      const link = block.querySelector('a[href*="filedetails/?id="]');
      if (!link) return;

      const match = link.href.match(/filedetails\/\?id=(\d+)/);
      if (!match) return;

      const id = match[1];
      const url = "https://steamcommunity.com/sharedfiles/filedetails/?id=" + id;

      const titleElem = block.querySelector('.workshopItemTitle');
      const title = titleElem ? titleElem.textContent.trim() : "(no title)";

      if (!seen.has(id)) {
        seen.add(id);
        results.push({ id, url, title });
      }
    });

    if (results.length === 0) return console.warn('No workshop items found.');

    const formatted = results.map(item => {
      switch (formatType) {
        case "id":       return item.id;
        case "url":      return item.url;
        case "urlTitle": return `${item.url} - ${item.title}`;
        case "full":     return `${item.id} ${item.url} ${item.title}`;
        default:         return item.url;
      }
    });

    const output = (outputMode === "comma")
      ? formatted.join(",")
      : formatted.join("\n");

    console.log("=== OUTPUT (" + results.length + ") ===");
    console.log(output);

  } catch (e) {
    console.error(e);
  }

})();
