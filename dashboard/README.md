# Dashboards

YAML-Dashboards, git-getrackt. Registriert in `configuration.yaml` unter `lovelace: dashboards:`.

## Struktur (Sidebar)

| Sidebar | Datei | Tabs |
|---|---|---|
| Übersicht | `../ui-lovelace.yaml` | — (Alarme, Schnell-Aktionen, Außen, Navigation) |
| Räume | `raeume.yaml` | EG (`erdgeschoss.yaml`) · OG (`obergeschoss.yaml`) · Keller (`keller.yaml`) · Außen (`aussen.yaml`) |
| Beschattung | `beschattung.yaml` | Räume · Einstellungen |
| Klima | `klima.yaml` | Übersicht · Heizung · Lüften |
| Sicherheit | `sicherheit.yaml` | — |
| Energie | `energie.yaml` | Übersicht · Details |

Die Stockwerks-Dateien sind **einzelne Views** (kein `title:`/`views:`-Kopf), `raeume.yaml`
bindet sie per `!include` ein. URLs: `/lovelace-raeume/eg`, `/og`, `/keller`, `/aussen`.

Früher waren das 10 Sidebar-Einträge (Erdgeschoss, Obergeschoss, Keller, Aussen, Lüften
einzeln). Alte Lesezeichen / Handy-Shortcuts auf `/lovelace-erdgeschoss` usw. zeigen ins Leere.

## Bausteine: `templates.yaml`

Wiederkehrende Karten sind decluttering-Templates, eingebunden per
`decluttering_templates: !include templates.yaml`:

| Template | Wofür | Benutzt in |
|---|---|---|
| `beschattung_raum` | Raum-Zeile mit Stand, Blockier-Grund, 🔒/✋ | Beschattung |
| `sollwert` | Heizungs-Sollwert eines OG-Stellantriebs (Slider + −/+ / 20 °C / Status) | Räume → OG, Klima → Heizung |
| `lueften` | Lüften-Kachel (Taupunkt + Empfehlung) | Räume |
| `lueften_raum` | Lüften-Detail (Werte + Empfehlung) | Klima → Lüften, Räume → Keller |
| `klima_raum` | Temp · Feuchte, Icon = Lüften-Logo | Klima → Übersicht |

Variablen werden **textuell** ersetzt (`[[name]]`), bevor HA das Jinja rendert.

## Konventionen

- Sections nehmen `column_span`, nicht `columns` (sonst still 1/4 breit).
- Halbe Breite innerhalb einer Section: `grid_options: columns: 6`.
- Temperatur/Feuchte/Präsenz eines Raums stehen als `badges:` in der `heading`-Karte,
  nicht als eigene Chips-Zeile.
- Erklärtexte, Diagnose und selten verstellte Schwellen stecken in `custom:expander-card`
  (eingeklappt).
- Badges/Alarme mit `visibility:` — "0 offen" wird nicht angezeigt.
- mushroom `icon`/`icon_color` einzeilig halten (`startsWith("mdi:")`, exakter Farbvergleich).
- Zählen nur über `sensor.offene_oeffnungen`, `sensor.beschattung_blocker`, `sensor.lichter_an`
  (template.yaml), nie per eigenem Regex im Dashboard.

## HACS-Frontend-Karten

| Karte | HACS-Repo | Ressource |
|---|---|---|
| Mushroom | `piitaya/lovelace-mushroom` | `/hacsfiles/lovelace-mushroom/mushroom.js` |
| mini-graph-card | `kalkih/mini-graph-card` | `/hacsfiles/mini-graph-card/mini-graph-card-bundle.js` |
| apexcharts-card | `RomRider/apexcharts-card` | `/hacsfiles/apexcharts-card/apexcharts-card.js` |
| auto-entities | `thomasloven/lovelace-auto-entities` | `/hacsfiles/lovelace-auto-entities/auto-entities.js` |
| decluttering-card | `custom-cards/decluttering-card` | `/hacsfiles/decluttering-card/decluttering-card.js` |
| expander-card | `MelleD/lovelace-expander-card` | `/hacsfiles/lovelace-expander-card/expander-card.js` |

`resource_mode: yaml` → die Ressourcen stehen in `configuration.yaml`, nicht in der UI.
Pfade bei Bedarf in HACS → Karte → "Ressource" gegenprüfen.

## Offene TODOs

| Wo | Entity | Frage |
|---|---|---|
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_*` | Raum-Zuordnung der Shelly-Melder per MAC bestätigen |
| Klima / OG | `sensor.kind2_temperature` | = Büro-Temp? Rename auf `sensor.office_temperature` |
| `aussen.yaml` | Außenbeleuchtung | Weitere Außenleuchten ergänzen |
