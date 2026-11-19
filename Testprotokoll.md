# Testprotokoll – Montageanlage V18

**Umgebung:** TIA Portal V18, S7-PLCSIM V18 (CPU 1511-1 PN), WinCC-Runtime-Simulation (KTP700 Basic PN)
**Vorgehen:** Sensoren und Taster werden in der PLCSIM-SIM-Tabelle gesetzt; Ausgänge, Zähler und HMI werden beobachtet.
**Hinweis:** Die Bereiche A, B und C wurden in einer früheren Programmversion im Labor am realen Aufbau erfolgreich getestet. Die folgenden Tests betreffen die Neuentwicklung mit HMI und sind Simulationstests.

## 1. Programmtest in PLCSIM (17.11.2026)

| Nr. | Test | Eingaben | Erwartet | Ergebnis |
|---|---|---|---|---|
| 1.1 | Start | NotAus = 1, T_Ein kurz 1 | Anlage_Ein = 1, Band + Kettenband ein, Zähler = 0 | ✅ |
| 1.2 | Selbsthaltung | T_Ein wieder 0 | Anlage läuft weiter | ✅ |
| 1.3 | Aus | T_Aus kurz 1 | Anlage_Ein = 0, alles aus | ✅ |
| 1.4 | NOT-AUS-Sperre | NotAus = 0, T_Ein 1 | Anlage startet nicht | ✅ |
| 1.5 | NOT-AUS im Betrieb | Anlage läuft, NotAus = 0 | Anlage stoppt | ✅ |
| 1.6 | Bereich A Plastik | AluPlast kurz 1, Alu = 0 | Push A, Gesamt + 1, Plastik + 1, Lager + 1 | ✅ |
| 1.7 | Bereich A Alu | AluPlast + Alu kurz 1 | kein Push A, Gesamt + 1, Alu + 1 | ✅ |
| 1.8 | Lager voll | 4 Plastikteile, dann ein weiteres | Push A gesperrt, Überlauf + 1 | ✅ |
| 1.9 | Bereich B | SensB = 0, Lager > 0 | Push B 500 ms, Lager − 1 | ✅ |
| 1.10 | Bereich C gut | Kapazitiv 1, dann SensC_Push | Gut + 1, kein Push C | ✅ |
| 1.11 | Bereich C schlecht | Kapazitiv 0, SensC_Push | Schlecht + 1, Push C 500 ms | ✅ |

Bei der Inbetriebnahme gefunden: `T_Ein` ließ sich in der SIM-Tabelle nicht setzen → zum Testen über die Forcetabelle gesetzt; seit der HMI-Bedienung nicht mehr nötig.

## 2. HMI-Test (18.11.2026)

| Nr. | Test | Erwartet | Ergebnis |
|---|---|---|---|
| 2.1 | Verbindung | NotAus-Lampe grün, Rest grau | ✅ |
| 2.2 | EIN am Panel | Anlage läuft, Band, Kettenband grün | ✅ |
| 2.3 | Plastikteil | Push A, Gesamt / Plastik / Lager = 1 | ✅ |
| 2.4 | Aluteil | Push A bleibt aus, Alu = 1 | ✅ |
| 2.5 | AUS am Panel | alles grau, Zähler bleiben | ✅ |
| 2.6 | NotAus = 0 | Lampe rot, EIN ohne Wirkung | ✅ |
| 2.7 | Neustart | Zähler auf 0 | ✅ |

## 3. Störmeldungen (18.11.2026)

| Nr. | Test | Erwartet | Ergebnis |
|---|---|---|---|
| 3.1 | Anlage aus | Meldung „Anlage ausgeschaltet“ | ✅ |
| 3.2 | NotAus = 0 | Meldung „NOT-AUS betaetigt – Anlage gestoppt“ (Errors, bleibt bis zur Quittierung stehen) | ✅ |
| 3.3 | Lager voll | Meldung „Zwischenlager voll – Push A gesperrt“ | offen |
| 3.4 | EIN | Meldung „Anlage ausgeschaltet“ geht | offen |

Lehre aus dem Test: Nach Änderungen an DB und OB1 muss die **PLC neu geladen** werden. Nur die HMI-Simulation neu zu starten reicht nicht; die Meldungen erschienen erst nach dem Laden der PLC.
