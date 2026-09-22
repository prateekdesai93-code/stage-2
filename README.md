# Restock Ledger

A single-file tool for Olympic Paints: drop in the outbound **Batch Transfer PDFs** you export from Odoo, and it consolidates them into one **Production Requirement List** — grouped by department, with the quantity math and batch references, ready to download or print as a PDF.

## How to open it

`restock-ledger.html` is a normal web page — no install, no server. Double-click it (or right-click → Open With → your browser). It needs an internet connection the first time it loads, because it pulls in three small libraries (fonts, the PDF reader, the PDF writer) from a public CDN. Once loaded, all the parsing and report-building happens on your machine — none of your batch data is sent anywhere.

## How to use it

1. **Add PDFs** — drag your Batch Transfer PDFs (the ones with the barcode, vehicle, and product table — the same files as `Batch Transfer-3.pdf` … `-8.pdf`) onto the drop zone, or tap "Add PDFs" to pick them.
2. Each file becomes a chip showing its batch number (`#00219` etc.), vehicle, and line count. A red chip means it couldn't be read — hover/tap it to see why, and remove it with the ×.
3. The ledger below fills in automatically, split into **Putty Department** and **Paint Department**, each sorted alphabetically. Quantities that come from more than one batch are shown as `10+10 = 20`, with the contributing batch numbers listed.
4. The totals row at the top gives quick pack-size totals (20L / 5L / 1L / 500ML, plus anything else that shows up).
5. When you're happy with the batches loaded, press **Generate PDF**. It will either prompt you to save the file directly, or — if your browser blocks that — it opens the print dialog so you can "Save as PDF" from there. **Print / Save as PDF** does the same thing on demand.
6. Your batches are remembered in the browser between sessions (until you remove them), so you don't lose work on a reload.

The app opens with a small labeled **example batch** so you can see the layout before adding real data — press "Clear example & start fresh" (or just drop your first real PDF, which clears it automatically).

## Batches To Make

This is the section that turns "what went out" into "what to run." For every product, it adds up demand across *all* its pack sizes — 20L, 5L, 1L, 500ML, even oddball cartons like `12X750ML` — converted to a single litre (or kg) total. It then divides that by the product's standard batch size and rounds up, so you get a clean "make N batches" plus how much surplus that leaves you.

It only works for a product once you've told it that product's batch size. Open **"Batch sizes (from your BOM)"** and add one line per product:

```
Decor Cream = 1500 L
Decor White = 1500 L
Oxide Black = 3000 KG
```

Only `Decor Cream` and `Decor White` are pre-filled, from what you told me directly — everything else is left for you to add from your live Odoo BOM quantities, on purpose, rather than guessed. Any product without a batch size on file just gets listed as "no batch size on file" instead of a wrong number.

## Putty vs. Paint — how it decides

A product line is filed under **Putty Department** if its name contains any of these words (case-insensitive):

```
carbolineum, crack filler, crackfiller, lacquer thinner, turpentine,
stainer, spirit of salt, sanding sealer, etch primer, bonding liquid,
plaster n tile bond, putty, oxide black, oxide red, oxide brown
```

Everything else goes to **Paint Department**. Open the **"Putty Department rules"** panel in the app to edit this list yourself — one rule per line, save, and it re-sorts immediately. Your edits are remembered in the browser.

## What it does *not* do

- It doesn't check anything against your live Bills of Materials list in Odoo — it purely totals up what went out, by product name and pack size, the same way your existing Production Requirement List reports do.
- It only reads the standard Odoo Batch Transfer PDF layout (bracketed SKU, product name + size, quantity, `Each`, transfer reference). A differently formatted PDF may fail to parse — the chip will turn red and tell you why.
- Nothing is uploaded anywhere. If you're offline after the first load, PDF parsing and report generation still work; only the very first page load needs the internet (for the fonts/PDF libraries).

## Troubleshooting

- **"PDF engine failed to load"** at the bottom of the screen — you're likely offline on first load, or something blocked the CDN scripts. Reload with a connection.
- **A batch chip turns red** — the file probably isn't a Batch Transfer PDF, or it's a scanned/flattened image rather than a real PDF export from Odoo (this tool needs actual selectable text in the PDF, not a picture of one).
- **"Batch #X is already loaded"** — you dropped the same batch twice; it's ignored, not duplicated.
