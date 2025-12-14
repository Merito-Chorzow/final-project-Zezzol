# Projekt zaliczeniowy - Programowanie niskopoziomowe

## Wymagania ogólne projektu

Każdy projekt (niezależnie od tematu) musi zawierać:
1. Wejście (sensor) – dane z czujnika rzeczywistego lub emulowanego (np. temperatura, wilgotność gleby, przycisk/klawiatura, „wirtualny” ADC itp.).
2. Wyjście (aktuator/interfejs) – sygnał sterujący (np. PWM/LED/serwo) lub interfejs użytkownika (UART CLI / Web UI).
3. Warstwowy interfejs + protokół – np. proste ramkowanie na UART/CLI lub REST/HTTP (jeśli środowisko pozwala).
4. FSM (maszyna stanów) – minimum: INIT → IDLE → RUN → FAULT/SAFE + warunki przejść.
5. Pętla sterowania / logika decyzji – histereza, PI/PID, scheduler lub inny algorytm deterministyczny.
6. Telemetria i logi – liczniki błędów (CRC/overflow/timeouts), wybrane metryki działania (np. overshoot, drop, latency).
7. Testy – skrypty uruchomieniowe w emulatorze + testy funkcjonalne (hostowe lub integracyjne).
8. Dokumentacja – README.md (uruchomienie), design.md (architektura), tests.md (scenariusze i wyniki), krótki film/gif z działania (opcjonalnie).
9. Repozytorium – czytelna struktura, build przez idf.py (ESP-IDF) lub standard Wokwi/PlatformIO; CI opcjonalnie.

## Minimalny artefakt (checklista)
- src/ (kod), include/ (nagłówki), drivers/ (HAL/protokół), app/ (logika), tests/
- README.md – krok po kroku: setup, build, uruchomienie w QEMU/Wokwi
- design.md – diagram blokowy, FSM (graf), opis protokołu/komend
- tests.md – lista testów + wyniki (logi, zrzuty)
- Skrypt uruchomieniowy (np. run_qemu.sh / wokwi.toml)
- Tag release na GitHub/GitLab (v1.0.0) w dniu oddania

## Proponowane tematy (do wyboru / modyfikacji)

Każdy temat zawiera MVP oraz rozszerzenia (opcjonalnie). Możesz łączyć pomysły, podmieniać sensory/aktuatory lub zaproponować własny (po zatwierdzeniu).
1. Stacja pogodowa (temp./wilg./ciśnienie)
    - MVP: odczyt sensora, średnia krocząca, alarm temperaturowy, CLI na UART (GET/SET/STAT).
    - Plus: zapis logu do pseudo-FS, wykres (eksport CSV), prosty HTTP (jeśli środowisko pozwala).
2. Czujnik wilgotności gleby + podlewanie
    - MVP: próbkowanie, histereza, anty-flapping, sterowanie „pompą” (emulowany PWM), FSM SAFE (sucho/awaria).
    - Plus: harmonogram, kalibracja progu przez CLI, statystyki dzienne.
3. Termostat / regulator PI
    - MVP: PI (lub PID) w pętli, setpoint, ograniczenia, saturacja i anti-windup, telemetria overshoot.
    - Plus: bumpless transfer (OPEN↔CLOSED), auto-tuning (eksperymentalnie).
4. Sterownik oświetlenia (PWM + sceny)
    - MVP: płynne zmiany jasności (slew-rate), sceny (SUNSET, NIGHT), FSM.
    - Plus: harmonogram, krzywe gamma.
5. Datalogger środowiskowy
    - MVP: próbnik co N sekund, rolling buffer, STAT (min/max/avg).
    - Plus: eksport CSV przez UART, prosty filtr IIR.
6. Sterownik wentylatora (temp→PWM)
    - MVP: mapa LUT (temp→duty), rampy antyhałasowe, alarm FAULT (sensor brak/nieprawidłowy).
    - Plus: zdalna zmiana mapy przez protokół.
7. Sygnalizacja świetlna (FSM + wejścia)
    - MVP: logika świateł + przycisk „pieszy”, czasy, awaria do SAFE (migające żółte).
    - Plus: tryb nocny, telemetria cykli.
8. Alarm/bezpieczeństwo (czujnik ruchu, strefy)
    - MVP: strefy, uzbrojenie/rozbrojenie, opóźnienia wejścia/wyjścia, log zdarzeń.
    - Plus: sabotaż, licznik fałszywych alarmów.
9. Mini-webserver statusu (jeśli środowisko/ramy na to pozwalają)
    - MVP: endpoint /status, /set?param=; JSON; prosta strona statusu.
    - Plus: wskaźniki czasu rzeczywistego (aktualizacja co X s).
10. Sterownik serwa/pozycjonera
    - MVP: pozycja docelowa, rampa, okno tolerancji, SAFE przy przeciążeniu (symulowany limit).
    - Plus: P/PI na pozycji.
11. Sterownik nawadniania wielostrefowego
    - MVP: 2–4 strefy, arbitraż czasu, blokady kolizyjne.
    - Plus: reguły oparte o „opady” (symulowane), priorytety.
12. Logger jakości zasilania (symulacja)
    - MVP: wykrywanie „brownout” (sym), bezpieczne zatrzymanie do SAFE, raport zdarzeń.
    - Plus: testy wstrzykiwania błędów (fault injection).

 > Uwaga: Wokwi ma bogatszy katalog „wirtualnych” czujników i podzespołów; QEMU/ESP-IDF jest bliżej „systemowego” podejścia (UART, GPIO, timery, FreeRTOS). Dobierz platformę pod temat.


## Kryteria oceny (100 pkt)
1. Funkcjonalność i poprawność (25 pkt)
    - Spełnienie wymagań MVP, działająca pętla/logika, brak „wiszących” błędów.
2. Architektura i jakość kodu (20 pkt)
    - Warstwowość (driver/HAL ↔ logika), interfejsy, FSM, spójny styl, komentarze techniczne.
3. Interfejs i protokół (15 pkt)
    - Czytelne komendy (CLI/REST), obsługa błędów (CRC/timeout/overflow), telemetria.
4. Sterowanie / algorytm (15 pkt)
    - Histereza/PI/PID/scheduler – poprawne zastosowanie, metryki (np. overshoot, czasy).
5. Testy i demonstracja (15 pkt)
    - Skrypty uruchomieniowe w emulatorze, scenariusze testów, logi/wyniki, krótkie demo.
6. Dokumentacja i repo (10 pkt)
    - README.md, design.md, tests.md, struktura i instrukcje krok-po-kroku.

> Bonus (+5 pkt): automaty testowe (hostowe lub w CI), generowanie artefaktów (log, CSV), rozszerzenia wyjątkowe.


## Wskazówki techniczne:
- ESP-IDF + QEMU: trzymaj się komponentów wspieranych w symulacji (UART, GPIO, timery, podstawy FreeRTOS).
- Wokwi: korzystaj z gotowych „wirtualnych” czujników/aktuatorów (I²C, SPI, UART, przyciski, LED, serwo).
- Deterministyczność: logika czasu w oparciu o tick/timer; unikaj niekontrolowanych opóźnień.
- Bezpieczeństwo: stan SAFE/FAULT, watchdog/timouty, walidacja wejść.
- Anti-AI: wymagany commit history, demo live i krótkie Q&A dot. kodu.


## Szablon struktury repo (propozycja)
```
project-root/
├─ app/            # logika aplikacji, FSM, pętla
├─ drivers/        # HAL/protokół/sensory
├─ include/        # nagłówki publiczne
├─ src/            # glue/main
├─ tests/          # testy hostowe/integracyjne
├─ scripts/        # run_qemu.sh / narzędzia
├─ wokwi/          # pliki projektu Wokwi (jeśli wybrano)
├─ README.md       # uruchomienie (QEMU lub Wokwi)
├─ design.md       # architektura + FSM + protokół
└─ tests.md        # scenariusze + wyniki
```