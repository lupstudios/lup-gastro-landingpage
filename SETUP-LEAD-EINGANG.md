# Gastro-Landingpage — live schalten

Die Landingpage schickt jede Anfrage direkt in euer **lup-hub Cockpit**: dort erscheint sie als neuer Kontakt in der **Kundenliste mit Status «Lead»** (mrr 0, kein Paket, nicht in Analytics — stört also weder Finanzen noch Auswertung). Nische + Anliegen stehen in den Notizen.

Der Server-Code ist gebaut und committet (Branch `claude/offerten-rechner`, Commit `c32a5e0`). Es fehlt nur noch dein Deploy + ein Token.

## 1. Token festlegen (einmal, an zwei Stellen derselbe Wert)

Denk dir einen schwer erratbaren Wert aus, z. B. `lup-lead-9f3xq2`.

**a) Auf dem VPS** in die `.env` von `/opt/lup-hub` (per SSH, `nano .env`):
```
LEAD_INTAKE_TOKEN=lup-lead-9f3xq2
LEAD_INTAKE_ORIGINS=https://DEINE-LANDINGPAGE-DOMAIN
```
`LEAD_INTAKE_ORIGINS` ist optional (Komma-Liste der erlaubten Landingpage-Domains). Leer lassen = jede Herkunft erlaubt, dann schützt nur das Token.

**b) In der Landingpage** `index.html` (Script-Block unten):
```
var LEAD_TOKEN = "lup-lead-9f3xq2";
```
Muss exakt derselbe Wert sein.

## 2. lup-hub deployen

`deploy-lupo.sh` ausführen (tar-over-ssh, nimmt den committeten Stand mit). Danach den Dienst neu starten, damit die neue `.env` greift: `sudo systemctl restart lup-hub`.

Keine Schema-Migration nötig — der Endpoint nutzt nur bestehende Felder der `clients`-Tabelle.

## 3. Landingpage deployen

`index.html` + `vsl.mp4` auf einen Webhost stellen (Netlify Drag&Drop, Cloudflare Pages o. ä.). Fertig.

## 4. Testen

Formular auf der Landingpage absenden → in wenigen Sekunden erscheint ein neuer Kontakt in der Kundenliste (Status «Lead»). Filtere die Kundenliste nach Status, um die Akquise-Leads zu sehen.

---

## Jede weitere Nische (Bau, Ärzte …)

In der `index.html` der neuen Seite nur:
- **denselben** `LEAD_ENDPOINT` und `LEAD_TOKEN` lassen
- `var QUELLE = "…"` auf die Nische setzen (z. B. `"Bau & Handwerk"`)

Alle Leads laufen automatisch in dieselbe Kundenliste, die Nische steht in den Notizen.

## Noch offen (deine Seite)

- **Meta-Pixel** in `index.html` oben einsetzen (Platz ist markiert, das `Lead`-Event feuert beim Absenden automatisch).
- **Video:** aktuell direkt eingebettet (`vsl.mp4`, 65 MB). Läuft. Bei viel Ads-Traffic später besser auf Vimeo/YouTube (ungelistet) — dann in der Videobox `data-video=""` leeren und `data-embed="…"` mit der Player-URL füllen.
