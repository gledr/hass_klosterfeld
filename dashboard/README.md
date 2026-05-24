# Dashboards — migration to yaml mode

Eight git-tracked yaml dashboards, replacing the 10 storage-mode JSON files.

## Files in this folder

| File | Dashboard | Sidebar title |
|---|---|---|
| `../ui-lovelace.yaml` | Main / landing | Übersicht (auto, no entry in `dashboards:`) |
| `beschattung.yaml` | Operational shading | Beschattung |
| `klima.yaml` | Climate overview + trends | Klima |
| `sicherheit.yaml` | Smoke + windows + doors merged | Sicherheit |
| `luften.yaml` | Cellar ventilation recommendations | Lüften |
| `erdgeschoss.yaml` | Ground floor per-room | Erdgeschoss |
| `obergeschoss.yaml` | Upper floor per-room | Obergeschoss |
| `aussen.yaml` | Garage / Terrasse / Wetterstation | Aussen |

The 11 `lovelace.*` JSON files in this folder are the old snapshot. Delete once the yaml migration is verified live (see step 4 below).

## HACS frontend cards required

These must be installed via HACS → Frontend before HASS loads `configuration.yaml` with `lovelace: mode: yaml`:

| Card | HACS repo |
|---|---|
| Mushroom | `piitaya/lovelace-mushroom` |
| button-card | `custom-cards/button-card` |
| mini-graph-card | `kalkih/mini-graph-card` |
| apexcharts-card | `RomRider/apexcharts-card` |
| auto-entities | `thomasloven/lovelace-auto-entities` |
| layout-card | `thomasloven/lovelace-layout-card` |

Resource URLs in `configuration.yaml` use the `/hacsfiles/...` paths. If HACS on your install uses `/local/community/...` instead, adjust the URLs in `configuration.yaml`.

## Deployment

1. **Verify HACS cards installed** (you already did this). Restart HASS once after install so the JS resources register.
2. **Push these files** to the HASS server. The yaml include path resolves relative to `/config/`, so the repo root should map to `/config/`:
   ```
   /config/configuration.yaml      (updated)
   /config/ui-lovelace.yaml        (new)
   /config/dashboard/*.yaml        (new — 7 files)
   ```
3. **Check config**: Developer Tools → YAML → Check Configuration. Should pass.
4. **Restart HASS**. Open each dashboard from the sidebar:
   - Übersicht
   - Beschattung
   - Klima
   - Sicherheit
   - Lüften
   - Erdgeschoss
   - Obergeschoss
   - Aussen
5. **If everything renders**, delete the old storage files in `/config/.storage/`:
   ```
   lovelace
   lovelace.dashboard_aussen
   lovelace.dashboard_beschattung
   lovelace.dashboard_
   lovelace.dashboard_erdgeschoss
   lovelace.dashboard_fenster
   lovelace.dashboard_klima
   lovelace.dashboard_luften
   lovelace.dashboard_obergeschoss
   lovelace.dashboard_rauchmelder
   lovelace.beschattung_ui
   lovelace_dashboards
   lovelace_resources
   ```
   Then in this repo, delete the JSON snapshots in `dashboard/lovelace.*` and `dashboard/lovelace_dashboards`.

## Rollback

If a dashboard fails to load:
- Comment out the entire `lovelace:` block in `configuration.yaml` and restart. HASS falls back to `.storage/` storage mode (old dashboards return).
- Or: fix the offending yaml file and reload via Settings → System → YAML configuration reloading → "Lovelace".

**Don't delete the `.storage/` files until step 4 passes.**

## TODOs flagged inside the dashboards

These are entity assignments I guessed and you should verify in the HASS UI:

| Dashboard | Entity | Question |
|---|---|---|
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_2cbcbbf6852c_smoke` | Which OG room? |
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_2cbcbbf68278_smoke` | Which OG room? |
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_2cbcbbf68028_smoke` | Which OG room? |
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_2cbcbbf59758_smoke` | Which OG room? |
| `sicherheit.yaml` | `binary_sensor.shellyplussmoke_2cbcbbf70698_smoke` | Which Keller room? |
| `klima.yaml` / `obergeschoss.yaml` | `sensor.kind2_temperature` | Confirm = Büro temp; rename to `sensor.office_temperature` |
| `beschattung.yaml` / `aussen.yaml` | `sensor.helligkeit_sud` vs `sensor.helligkeit_sued` | Old `beschattung_ui` used the *_sued spelling; `template.yaml` uses *_sud. Check which actually exists in Developer Tools and fix the other references. |
| `aussen.yaml` | Außenbeleuchtung | Only `light.main_door_light` listed — add any other Außenleuchten you have. |
| `aussen.yaml` | Terrassentür | Placeholder commented out, fill in after Shelly install. |

## What changed semantically (vs. old dashboards)

- **One operational Beschattung dashboard** with the sensors + flags the automations actually use (sonne_trifft_X, override toggle, threshold sliders, wind alarm) — replaces the two overlapping ones.
- **Sicherheit** unifies Fenster + Rauchmelder + Garage + Wind into one dashboard with auto-filtered alert lists at the top.
- **Per-floor dashboards** no longer duplicate smoke detectors / windows (those live on Sicherheit).
- **Übersicht** is a true landing page (alerts + quick actions + navigation), not the auto-generated areas mess.
- **Aussen** demotes garage internals (vent/half/step) into an expander that only shows when the door is open.
- **`cover.livingroom_blinks` typo fixed** to `cover.livingroom_blinds`.
- **Empty "Neuer Abschnitt" section** in old Obergeschoss removed.
- **Lüften** kept as-is (it was the only dashboard that was already well-designed) — just modernized layout.
