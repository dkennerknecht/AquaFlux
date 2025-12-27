# Update-Check (kontrolliert, ohne Anlage zu schießen)

## Grundsatz
- In Produktion werden keine „spontanen“ Updates gemacht.
- Änderungen passieren nur über einen Update-Branch + Test-Prozedur.
- Alles ist hart gepinnt (platform + libs). Updates werden bewusst angehoben.

## 1) Ausgangspunkt festhalten
- Commit-Hash der aktuell laufenden Firmware notieren
- `pio run -e release` muss sauber durchlaufen
- Firmware-Binary/Artefakt archivieren (z.B. im Release/GitHub)

## 2) Update-Branch anlegen
- Branch: `chore/update-YYYY-MM`
- NUR EINE Änderungskategorie pro PR:
    - a) Platform (espressif32)
    - b) Libraries (lib_deps)
    - c) Tooling/CI

## 3) PlatformIO Platform anheben (wenn nötig)
- `platform = espressif32@X.Y.Z` eine Version höher setzen
- Build: `pio run -e dev` und `pio run -e release`

## 4) Libraries anheben (wenn nötig)
- Jede Lib einzeln anheben (nicht alle auf einmal)
- Nach jeder Änderung:
    - Build dev/release
    - Smoke-Test am Testgerät

## 5) Smoke-Test (Minimum)
- Boot: startet ohne WDT-Reset / ohne Bootloop
- Ethernet (W5500):
    - Link up
    - DHCP oder statische IP ok
    - Web-UI erreichbar
- Modbus TCP:
    - Verbindung ok
    - Read/Write Basisregister ok
- Stepper:
    - Enable/Disable ok
    - Referenzfahrt startet und endet korrekt
    - Soft-/Hard-Limits greifen
- Logging/SD:
    - Config Laden ok (Fail-fast bleibt korrekt)
    - Logging schreibt (oder sauberer Fallback)

## 6) Regression (kurz)
- 30–60 min Dauerlauf
- 50x Stellbewegungen (beide Nadeln)
- Netzwerk-Disconnect simulieren (Switch kurz weg): Reconnect ok

## 7) Rollback-Plan (Pflicht)
- Letztes funktionierendes Release-Binary verfügbar
- Rollback-Prozedur dokumentiert (Upload/OTA/Serial)

## 8) Merge nur wenn:
- Alle Tests grün
- Release-Env gebaut
- Changelog kurz ergänzt (was wurde angehoben, warum)