# Monitoreo de Medicamentos — Contexto completo del proyecto

**Curso:** Electrónica Digital — Ingeniería en Sistemas, UMG Centro Universitario El Naranjo
**Modalidad:** Proyecto grupal con Arduino, 100% simulado en Proteus 8 (no se compra ni se arma nada físico)

---

## 1. Contexto y requisitos

- El ingeniero pidió un proyecto con Arduino **relacionado con el tiempo**.
- Ejemplos de otros grupos: sistema de riego y caja fuerte con cambio de contraseña cada media hora.
- Los sistemas de **seguridad no fueron aprobados**, por lo que se eligió otro tema.
- Proyecto elegido (registrado en la hoja de proyectos como Grupo 3): **Monitoreo de Medicamentos**.

### Especificaciones del grupo

| Especificación | Cómo se cumple |
| --- | --- |
| Indicador de toma por horas | Un reloj DS1307 lleva la hora; el LCD muestra la hora actual y la próxima dosis (08:00, 14:00 y 20:00). |
| Confirmación de medicina tomada | Al llegar la hora se presiona el botón; se enciende el LED verde y el LCD dice "Medicina tomada". |
| Alerta por no toma | LED rojo y buzzer parpadean; si pasan 10 minutos sin confirmar, quedan fijos y el LCD dice "DOSIS OMITIDA!". |

---

## 2. Software necesario

1. **Proteus 8** (Professional, 8.9 o superior recomendado).
2. **Arduino IDE** (1.8.x o 2.x).
3. **Librería RTClib de Adafruit**: Arduino IDE → Herramientas → Administrar bibliotecas → buscar "RTClib" → Instalar (elegir "Instalar todo" si pregunta por dependencias).
4. **Librería de Arduino para Proteus** (solo si Proteus no trae el ARDUINO UNO): "Arduino library for Proteus" (The Engineering Projects). Copiar los `.LIB` e `.IDX` en la carpeta `LIBRARY` de Proteus (ej. `C:\ProgramData\Labcenter Electronics\Proteus 8 Professional\LIBRARY`) y reiniciar Proteus.

`Wire` y `LiquidCrystal` ya vienen incluidas en Arduino IDE.

---

## 3. Componentes en Proteus

Se buscan con la tecla **P** (Pick Devices).

| Componente | Buscar como | Cantidad | Valor / nota |
| --- | --- | --- | --- |
| Arduino UNO | ARDUINO UNO | 1 | 16 MHz |
| Pantalla LCD 16x2 | LM016L | 1 | — |
| Reloj de tiempo real | DS1307 | 1 | Bus I2C |
| Cristal del reloj | CRYSTAL | 1 | 32.768 kHz |
| Batería del reloj | BATTERY | 1 | 3 V |
| Potenciómetro (contraste) | POT-HG | 1 | 10 kΩ |
| Resistencias pull-up I2C | RES | 2 | 4.7 kΩ |
| Resistencias de LEDs | RES | 2 | 220 Ω |
| Botón de confirmación | BUTTON | 1 | — |
| Zumbador | BUZZER | 1 | — |
| LED rojo (alerta) | LED-RED | 1 | — |
| LED verde (confirmado) | LED-GREEN | 1 | — |

POWER (5 V) y GROUND se colocan desde **Terminals Mode** (barra izquierda).

---

## 4. Diseño del circuito

```
                        +---------------------+
 +----------------+     |     ARDUINO UNO     |     +---------------------------+
 | LM016L (LCD)   |     |                     |     | DS1307 (reloj)            |
 |            RS  |-----| D7              A4  |-----| SDA  (4.7k a POWER)       |
 |            E   |-----| D6              A5  |-----| SCL  (4.7k a POWER)       |
 |            D4  |-----| D5                  |     | Cristal 32.768k + bat 3V  |
 |            D5  |-----| D4              D8  |-----| BUTTON  -> GROUND         |
 |            D6  |-----| D3              D9  |-----| BUZZER  -> GROUND         |
 |            D7  |-----| D2              D10 |-----| LED-RED   (220Ω) -> GND   |
 | VDD a POWER    |     |                 D11 |-----| LED-GREEN (220Ω) -> GND   |
 | VSS, RW a GND  |     |                     |     +---------------------------+
 +-------+--------+     | 5V a POWER          |
         |              | GND a GROUND        |
 +-------+--------+     +---------------------+
 | POT-HG 10kΩ    |
 | cursor a VEE   |
 +----------------+
```

---

## 5. Conexiones pin por pin

| Componente | Pin del componente | Se conecta a |
| --- | --- | --- |
| LCD LM016L | VSS | GROUND |
| LCD LM016L | VDD | POWER (5 V) |
| LCD LM016L | VEE (contraste) | Pin central del POT-HG |
| LCD LM016L | RS | Arduino D7 |
| LCD LM016L | RW | GROUND |
| LCD LM016L | E | Arduino D6 |
| LCD LM016L | D0, D1, D2, D3 | Sin conectar |
| LCD LM016L | D4 | Arduino D5 |
| LCD LM016L | D5 | Arduino D4 |
| LCD LM016L | D6 | Arduino D3 |
| LCD LM016L | D7 | Arduino D2 |
| POT-HG 10 kΩ | Extremo 1 / extremo 2 | POWER / GROUND |
| DS1307 | SDA | Arduino A4 + 4.7 kΩ a POWER |
| DS1307 | SCL | Arduino A5 + 4.7 kΩ a POWER |
| DS1307 | VCC / GND | POWER / GROUND |
| DS1307 | X1, X2 | Un extremo del CRYSTAL cada uno |
| DS1307 | VBAT | Positivo de BATTERY 3 V (negativo a GROUND) |
| DS1307 | SOUT | Sin conectar |
| BUTTON | Pata 1 / pata 2 | Arduino D8 / GROUND |
| BUZZER | + / − | Arduino D9 / GROUND |
| LED-RED | Ánodo / cátodo | D10 por 220 Ω / GROUND |
| LED-GREEN | Ánodo / cátodo | D11 por 220 Ω / GROUND |

El botón no lleva resistencia externa: el código usa `INPUT_PULLUP` (sin presionar = HIGH, presionado = LOW).

---

## 6. Montaje en Proteus paso a paso

1. Proteus 8 → **New Project** → nombre `Monitoreo_Medicamentos` → schematic DEFAULT → sin PCB → sin firmware → **Finish**.
2. Tecla **P** → agregar todos los componentes de la tabla.
3. ARDUINO UNO al centro.
4. LM016L a la izquierda y POT-HG debajo del LCD.
5. DS1307 arriba a la derecha, con CRYSTAL junto a X1/X2 y BATTERY junto a VBAT.
6. BUTTON, BUZZER, LEDs y sus resistencias a la derecha, de arriba hacia abajo.
7. Valores: RES de LEDs = `220`, RES de SDA/SCL = `4.7k`, CRYSTAL = `32.768kHz`, POT-HG = `10k`.
8. Colocar POWER y GROUND cerca de cada componente.
9. Cablear fila por fila según la tabla de conexiones.
10. Verificar que cátodos de LEDs, botón y buzzer terminen en GROUND.
11. Guardar (**Ctrl + S**).

Si un cable no se une al pin, acercar el zoom: Proteus solo une cuando aparece la cruz roja en la punta del pin.

---

## 7. Código de Arduino

Guardar como `monitoreo_medicamentos.ino`. Con `MODO_DEMO 1` el reloj arranca a las 07:59:30 (la primera alerta sale a los 30 s). Con `MODO_DEMO 0` usa la hora real.

```cpp
/*
  PROYECTO: Monitoreo de Medicamentos
  Electronica Digital - Simulacion en Proteus 8 + Arduino UNO
  - Indicador de toma por horas
  - Confirmacion de medicina tomada (boton)
  - Alerta por no toma (LED rojo + buzzer)
*/

#include <Wire.h>
#include <RTClib.h>
#include <LiquidCrystal.h>

#define MODO_DEMO 1   // 1 = arranca 07:59:30 para presentar; 0 = hora normal

RTC_DS1307 rtc;
LiquidCrystal lcd(7, 6, 5, 4, 3, 2); // RS, E, D4, D5, D6, D7

const int pinBoton    = 8;
const int pinBuzzer   = 9;
const int pinLedRojo  = 10;
const int pinLedVerde = 11;

// Horario de dosis (se puede editar)
const int numDosis = 3;
int horaDosis[numDosis] = {8, 14, 20};
int minDosis[numDosis]  = {0, 0, 0};
bool dosisTomada[numDosis] = {false, false, false};

const int ventanaMin = 10;   // minutos para confirmar antes de "omitida"
int dosisActivaIdx = -1;     // dosis en alerta (-1 = ninguna)
int ultimoDia = -1;

void setup() {
  lcd.begin(16, 2);
  pinMode(pinBoton, INPUT_PULLUP);
  pinMode(pinBuzzer, OUTPUT);
  pinMode(pinLedRojo, OUTPUT);
  pinMode(pinLedVerde, OUTPUT);

  if (!rtc.begin()) {
    lcd.print("Error RTC");
    while (1);
  }

#if MODO_DEMO
  rtc.adjust(DateTime(2026, 10, 4, 7, 59, 30));
#else
  if (!rtc.isrunning()) {
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
  }
#endif

  lcd.clear();
  lcd.print("Monitoreo Med.");
  delay(1000);
  lcd.clear();
}

void loop() {
  DateTime ahora = rtc.now();

  // Reinicia las dosis al cambiar de dia
  if (ahora.day() != ultimoDia) {
    for (int i = 0; i < numDosis; i++) dosisTomada[i] = false;
    ultimoDia = ahora.day();
    dosisActivaIdx = -1;
  }

  mostrarPantalla(ahora);
  revisarDosis(ahora);

  if (dosisActivaIdx != -1) {
    manejarAlerta(ahora);
  } else {
    digitalWrite(pinBuzzer, LOW);
    digitalWrite(pinLedRojo, LOW);
  }

  delay(200);
}

void imprimirDosDigitos(int v) {
  if (v < 10) lcd.print("0");
  lcd.print(v);
}

void mostrarPantalla(DateTime ahora) {
  lcd.setCursor(0, 0);
  lcd.print("Hora: ");
  imprimirDosDigitos(ahora.hour());   lcd.print(":");
  imprimirDosDigitos(ahora.minute()); lcd.print(":");
  imprimirDosDigitos(ahora.second()); lcd.print("  ");

  if (dosisActivaIdx != -1) return; // la linea 2 la maneja la alerta

  lcd.setCursor(0, 1);
  int prox = proximaDosisPendiente();
  if (prox == -1) {
    lcd.print("Dosis completas ");
  } else {
    lcd.print("Prox dosis ");
    imprimirDosDigitos(horaDosis[prox]); lcd.print(":");
    imprimirDosDigitos(minDosis[prox]);
  }
}

int proximaDosisPendiente() {
  for (int i = 0; i < numDosis; i++) {
    if (!dosisTomada[i]) return i;
  }
  return -1;
}

void revisarDosis(DateTime ahora) {
  if (dosisActivaIdx != -1) return;
  for (int i = 0; i < numDosis; i++) {
    if (dosisTomada[i]) continue;
    if (ahora.hour() == horaDosis[i] && ahora.minute() == minDosis[i]) {
      dosisActivaIdx = i;
      break;
    }
  }
}

void manejarAlerta(DateTime ahora) {
  if (digitalRead(pinBoton) == LOW) {
    confirmarToma(dosisActivaIdx);
    return;
  }

  int minutosDesde = (ahora.hour() - horaDosis[dosisActivaIdx]) * 60
                   + (ahora.minute() - minDosis[dosisActivaIdx]);

  lcd.setCursor(0, 1);
  if (minutosDesde >= ventanaMin) {
    // Dosis omitida: alarma fija
    digitalWrite(pinLedRojo, HIGH);
    digitalWrite(pinBuzzer, HIGH);
    lcd.print("DOSIS OMITIDA!  ");
  } else {
    // Dentro de la ventana: parpadeo
    bool estado = (millis() / 300) % 2;
    digitalWrite(pinLedRojo, estado);
    digitalWrite(pinBuzzer, estado);
    lcd.print("TOME SU MEDICINA");
  }
}

void confirmarToma(int idx) {
  dosisTomada[idx] = true;
  dosisActivaIdx = -1;
  digitalWrite(pinBuzzer, LOW);
  digitalWrite(pinLedRojo, LOW);
  digitalWrite(pinLedVerde, HIGH);
  lcd.clear();
  lcd.print("Medicina tomada");
  delay(1500);
  digitalWrite(pinLedVerde, LOW);
  lcd.clear();
}
```

---

## 8. Compilar y cargar el .hex

1. Arduino IDE: **Herramientas → Placa → Arduino Uno**.
2. **Verificar** (✓) sin errores. Si falta `RTClib.h`, instalar la librería.
3. **Programa → Exportar binario compilado**.
4. **Programa → Mostrar carpeta del programa** (en IDE 2.x el archivo está en `build/arduino.avr.uno/`).
5. Usar `monitoreo_medicamentos.ino.hex` (el que **no** dice `with_bootloader`).
6. Proteus: doble clic en ARDUINO UNO → **Program File** → seleccionar el `.hex`.
7. Confirmar **Clock Frequency = 16MHz** → OK.

Cada cambio en el código requiere volver a exportar el binario.

---

## 9. Prueba de la simulación (~11 min con MODO_DEMO 1)

1. **Play** en Proteus.
2. LCD: "Monitoreo Med." → `Hora: 07:59:30` y `Prox dosis 08:00` → **indicador de toma por horas**.
3. A las 08:00:00: LED rojo y buzzer parpadean, LCD `TOME SU MEDICINA`.
4. Presionar BUTTON: se apaga la alerta, LED verde, `Medicina tomada`, luego `Prox dosis 14:00` → **confirmación**.
5. Stop → Play otra vez y **no** presionar el botón: a las 08:10 LED rojo y buzzer fijos, `DOSIS OMITIDA!` → **alerta por no toma**.
6. Presionar el botón en ese momento también apaga la alarma.

Demo corta: cambiar `ventanaMin` de `10` a `1`, exportar de nuevo; la omisión aparece a las 08:01.

---

## 10. Solución de errores comunes

| Síntoma | Causa probable | Solución |
| --- | --- | --- |
| No aparece ARDUINO UNO | Falta la librería | Instalar la librería de Arduino para Proteus y reiniciar |
| LCD encendido sin texto | Contraste o pines mal | Mover el POT-HG; revisar RS→D7, E→D6, D4–D7 a D5–D2 |
| LCD con cuadros negros | VEE directo a 5 V | VEE al pin central del potenciómetro |
| LCD muestra "Error RTC" | DS1307 no responde | Revisar SDA→A4, SCL→A5 y pull-ups de 4.7 kΩ |
| La hora no avanza | Sin cristal o sin VBAT | CRYSTAL 32.768 kHz en X1/X2 y BATTERY 3 V en VBAT |
| El botón no hace nada | Botón sin tierra | Una pata a D8, otra a GROUND |
| "RTClib.h: No such file" | Librería no instalada | Instalar RTClib de Adafruit |
| Proteus no encuentra el programa | Ruta mal o .hex con bootloader | Cargar el `.ino.hex` sin `with_bootloader` |
| Simulación lenta | Pocos recursos | Normal; el DS1307 avanza con el tiempo de simulación |
| No suena el buzzer | Audio de Proteus/PC | El parpadeo del LED rojo confirma la alerta |
