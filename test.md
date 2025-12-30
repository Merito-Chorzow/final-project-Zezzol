# Raport z Testów - Sterownik Wentylatora STM32

**Platforma:** STM32F429I-DISC1  
**Autor:** [Twoje Imię i Nazwisko]  
**Data:** 2025-01-30  
**Narzędzia:** STM32CubeIDE, CoolTerm, Potencjometr (Symulacja temperatury)

## 1. Zakres testów

Celem testów była weryfikacja poprawności działania Maszyny Stanów (FSM), algorytmu sterowania wentylatorem oraz zabezpieczeń systemowych. Testy przeprowadzono metodą "Black Box" poprzez interfejs UART (CLI) oraz obserwację zachowania diod LED i terminala.

---

## 2. Scenariusze Testowe (Test Cases)

### TC-01: Uruchomienie Systemu (INIT -> IDLE)

**Cel:** Sprawdzenie poprawności inicjalizacji i stanu spoczynku.

- **Warunki:** Zasilanie włączone, potencjometr w pozycji min (zimno).
- **Oczekiwany rezultat:** Stan IDLE, temperatura bliska 0°C, Wentylator 0%.
- **Wynik:** POZYTYWNY (PASS)
- **Log z terminala:**

```text
--- STM32 FAN CONTROLLER START ---
STATE: IDLE  | TEMP: 000.1 C | FAN: 0 %
STATE: IDLE  | TEMP: 002.8 C | FAN: 0 %
STATE: IDLE  | TEMP: 016.3 C | FAN: 0 %
```

### TC-02: Aktywacja Wentylatora (IDLE -> RUN)

**Cel:** Weryfikacja progu włączenia (30°C).

- **Akcja:** Zwiększenie temperatury powyżej 30°C.

- **Oczekiwany rezultat:** Przejście do stanu RUN, uruchomienie wentylatora.

- **Wynik:** POZYTYWNY (PASS)

- **Log z terminala:**

```text
STATE: IDLE  | TEMP: 027.6 C | FAN: 0 %
STATE: RUN   | TEMP: 031.5 C | FAN: 4 %  <-- START
STATE: RUN   | TEMP: 034.0 C | FAN: 12 %
STATE: RUN   | TEMP: 045.1 C | FAN: 45 %
```

### TC-03: Regulacja Prędkości (Proportional Control)

**Cel:** Sprawdzenie czy wentylator przyspiesza wraz z temperaturą.

- **Akcja:** Płynne zwiększanie temperatury w zakresie roboczym (30°C - 70°C).

- **Oczekiwany rezultat:** Wartość FAN % rośnie proporcjonalnie do TEMP.

- **Wynik:** POZYTYWNY (PASS)

- **Log z terminala:**

```text
STATE: RUN   | TEMP: 046.2 C | FAN: 48 %
STATE: RUN   | TEMP: 052.9 C | FAN: 68 %
STATE: RUN   | TEMP: 061.0 C | FAN: 92 %
STATE: RUN   | TEMP: 064.9 C | FAN: 100 %
```

### TC-04: Awaria Krytyczna (RUN -> FAULT)

**Cel:** Test bezpieczeństwa przy przegrzaniu (>75°C).

- **Akcja:** Zwiększenie temperatury powyżej progu alarmowego.

- **Oczekiwany rezultat:** Natychmiastowe przejście do FAULT, wentylator w trybie awaryjnym/stop.

- **Wynik:** POZYTYWNY (PASS)

- **Log z terminala:**

```text
STATE: RUN   | TEMP: 064.9 C | FAN: 100 %
STATE: FAULT | TEMP: 076.2 C | FAN: 100 % <-- ALARM!
STATE: FAULT | TEMP: 086.8 C | FAN: 0 %
STATE: FAULT | TEMP: 099.6 C | FAN: 0 %
```

### TC-05: Reset Awarii i Histereza

**Cel:** Sprawdzenie blokady awarii i powrotu do normy.

- **Akcja:** Obniżenie temperatury do 0°C i wciśnięcie przycisku USER.

- **Oczekiwany rezultat:** System wraca do IDLE dopiero po interwencji użytkownika (Przycisk).

- **Wynik:** POZYTYWNY (PASS)

- **Log z terminala:**

```text
STATE: FAULT | TEMP: 000.4 C | FAN: 0 % <-- Czekanie na reset
STATE: IDLE  | TEMP: 000.1 C | FAN: 0 % <-- Po wciśnięciu przycisku
```

## 3. Dokumentacja wizualna

Zrzut logów z sesji testowej
Pełny przebieg testu zarejestrowany w programie CoolTerm:
![image](log.png)
Demonstracja Wideo<br>
<video width="320" height="240" controls>

  <source src="filmik_pn.mp4" type="video/mp4">
</video><br>
Załączony plik wideo (filmik_pn.mp4) prezentuje:

Reakcję diod LED na zmiany temperatury.

Działanie przycisku RESET.

Podgląd danych w terminalu w czasie rzeczywistym.

## 4. Podsumowanie

System działa stabilnie i zgodnie z założeniami projektowymi. Zaimplementowana maszyna stanów poprawnie obsługuje przejścia między trybami pracy oraz sytuacje awaryjne.
