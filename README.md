[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/XfC33h1O)

# STM32 Smart Fan Controller

Projekt zaliczeniowy z przedmiotu Programowanie Niskopoziomowe.
System sterowania wentylatorem w oparciu o temperaturę z maszyną stanów (FSM) i interfejsem UART.

## Funkcjonalności

- **Pomiar temperatury:** Symulacja za pomocą potencjometru (ADC).
- **Sterowanie wentylatorem:** Algorytm proporcjonalny (P-regulator) z histerezą.
- **Bezpieczeństwo:** Wykrywanie przegrzania (FAULT) i blokada systemu.
- **Telemetria:** Podgląd parametrów pracy w czasie rzeczywistym przez UART.

## Hardware (Pinout)

Platforma: STM32F429I-DISC1

- **PA5 (ADC1_IN5):** Potencjometr (Symulacja czujnika).
- **PG13 (LED Green):** Sygnalizacja pracy (RUN) / Heartbeat (IDLE).
- **PG14 (LED Red):** Sygnalizacja awarii (FAULT).
- **PA0 (User Button):** Reset awarii.
- **PA9/PA10 (UART1):** Komunikacja z PC (115200 bps).

## Logika (FSM)

1. **IDLE:** Temp < 30°C. Wentylator STOP.
2. **RUN:** Temp > 30°C. Wentylator regulowany płynnie. Powrót do IDLE przy < 28°C (Histereza).
3. **FAULT:** Temp > 75°C. Awaryjne zatrzymanie. Wymagany reset ręczny przyciskiem.

## Uruchomienie

1. Otwórz projekt w STM32CubeIDE.
2. Skompiluj i wgraj na płytkę.
3. Podłącz terminal PuTTY: Baudrate 115200.

## Działanie

<video width="320" height="240" controls>
  <source src="filmik_pn.mp4" type="video/mp4">
</video>
