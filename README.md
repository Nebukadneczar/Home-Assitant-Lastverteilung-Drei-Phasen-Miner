# Home Assistant – Lastverteilung Drei-Phasen-Miner

**Version: 1.0.0**

Blueprint für Home Assistant zur Begrenzung der elektrischen Last einer Miner-Phase auf maximal **9.000 W**.

Die Automation überwacht die drei Phasen A, B und C und passt das Leistungsziel eines ausgewählten Miners an dessen zugeordnete Phase an. Die Prüfung erfolgt standardmäßig alle **5 Minuten**. Eine zusätzliche Sofortreaktion erfolgt, wenn die überwachte Phase die 9.000-W-Grenze überschreitet.

## Funktionsprinzip

```
Phase A ─ aktuelle Leistung ─┐
Phase B ─ aktuelle Leistung ─┼─> Home Assistant ─> Miner Power Target
Phase C ─ aktuelle Leistung ─┘
                                │
                                └─ maximal 9.000 W je Phase
```

Für den ausgewählten Miner wird die zulässige Leistung aus der Last seiner Phase berechnet.

```
Grundlast = Phasenleistung - aktuelles Miner-Leistungsziel

verfügbare Minerleistung =
    9.000 W - Grundlast - Sicherheitsreserve
```

Das Ergebnis wird anschließend auf den konfigurierten maximalen Minerwert begrenzt.

## Eigenschaften in v1.0.0

- Überwachung von Phase A, B und C
- Auswahl der Phase, an der der zu regelnde Miner angeschlossen ist
- maximal 9.000 W als konfigurierbarer Phasengrenzwert
- konfigurierbare Sicherheitsreserve
- konfigurierbare maximale Minerleistung
- konfigurierbare minimale Änderungsstufe
- automatische Leistungsanpassung
- Regelintervall standardmäßig 5 Minuten
- zusätzliche Sofortreaktion bei einer Phasenleistung über 9.000 W
- für unterschiedliche Entity-IDs als Blueprint wiederverwendbar

## Wichtiger Hinweis

v1.0.0 regelt **einen ausgewählten Miner** anhand **seiner zugeordneten Phase**.

Die Phasenleistung wird als Gesamtleistung der jeweiligen Phase betrachtet. Für die Berechnung wird das aktuelle Miner-Power-Target als Anteil des Miners an der Phasenlast verwendet. Ein separater Ist-Leistungssensor des Miners ist in dieser Version noch nicht erforderlich.

Die Automation kann keine Last einer anderen Phase reduzieren, wenn der Miner nicht an dieser Phase angeschlossen ist. Eine echte Lastverteilung über mehrere Miner und mehrere Phasen ist deshalb als mögliche spätere Erweiterung vorgesehen.

## Beispiel

Konfiguration:

- Phase A: `sensor.phase_a_leistung`
- Phase B: `sensor.phase_b_leistung`
- Phase C: `sensor.phase_c_leistung`
- Miner hängt an Phase B
- Miner Power Target: `number.antminer36_power_target`
- maximale Minerleistung: 2.832 W
- Sicherheitsreserve: 100 W
- Änderungsstufe: 100 W
- Prüfintervall: 5 Minuten

Beträgt die Grundlast der Miner-Phase beispielsweise 6.500 W, stehen bei 9.000 W Grenzwert und 100 W Reserve rechnerisch 2.400 W für den Miner zur Verfügung.

## Installation

Die Blueprint-Datei liegt unter:

`blueprints/automation/miner/lastverteilung_drei_phasen.yaml`

In Home Assistant: **Einstellungen → Automatisierungen & Szenen → Blueprints → Blueprint importieren**.

## Repository

Dieses Repository ist bewusst getrennt vom Projekt **Home-Assistant**, damit beide Automationen unabhängig versioniert und gepflegt werden können.

Version **1.0.0** ist die erste veröffentlichte Grundlage für die Drei-Phasen-Lastverteilung.
