# Circuitos UMG

Diseños de circuitos electronicos y digitales de la coursework de la
Universidad Mariano Gálvez.

El repo esta organizado en dos subcarpetas, una por herramienta:

```
circuitos-umg/
├── proteus/     # diseños en Proteus ISIS
└── kicad/       # diseños en KiCad
```

---

## `proteus/` — Proteus ISIS

Los archivos `*.pdsprj` son los **proyectos de Proteus** (internamente un ZIP
con el esquematico). Git los maneja bien: son binarios, asi que los cambios se
registran como "archivo modificado" sin diff legible, pero el historial
funciona.

| Archivo | Descripcion |
|---|---|
| `AGUAS.pdsprj` | Proyecto AGUAS |
| `contador_8_estados.pdsprj` | Contador de 8 estados |
| `contador_8estados.pdsprj` | Variante del contador de 8 estados (sin digito en el nombre) |
| `Monitoreo_Medicamentos.pdsprj` | Sistema de monitoreo de medicamentos |
| `satan.pdsprj` | Proyecto satan |

> **Nota:** `contador_8_estados.pdsprj` y `contador_8estados.pdsprj` son
> proyectos distintos que coexisten en el disco. Se conservan ambos tal cual,
> por si son versiones paralelas del mismo ejercicio.

### Lo que NO se versiona

| Patron | Razon |
|---|---|
| `*.pdsprj.*.workspace` | Estado de la ventana de Proteus (posiciones de paneles, popups de depuracion). Es config local, no diseno. |
| `Project Backups/` | Autobacks con timestamp que genera Proteus. Son ruido en el historial. |

---

## `kicad/` — KiCad

| Archivo | Descripcion |
|---|---|
| `sumador_2bits/sumador_2bits.kicad_sch` | Esquematico del sumador de 2 bits (texto plano, diff legible) |
| `sumador_2bits/sumador_2bits.kicad_pcb` | Layout de PCB |
| `sumador_2bits/sumador_2bits.kicad_pro` | Preferencias del proyecto |
| `sumador_2bits.cir` | Netlist SPICE del sumador de 1 bit (Half Adder simple, compatible con Multisim) |
| `sumador_2bits_cascada_TTL.cir` | ⭐ Netlist SPICE del sumador de 2 bits con Half Adder + Full Adder en cascada y subcircuitos TTL 74LS |

### Sumador de 1 bit (`sumador_2bits.cir`)

Half Adder simple:

- **Entradas:** X, Y
- **Salidas:** F0 = suma (X XOR Y), F1 = acarreo (X AND Y)
- Alimentacion: VCC = 5V, VEE = GND
- Estimulo PWL con periodo de 40 ms por entrada
- Analisis transitorio: `.TRAN 1m 80m 0 1m`

### Sumador de 2 bits en cascada (`sumador_2bits_cascada_TTL.cir`)

**Es un diseño distinto, mas avanzado que el de arriba.** No es una version del
anterior: implementa un sumador completo de 2 bits con acarreo encadenado.

- **Entradas:** A0 B0 A1 B1
- **Salidas:** S0 S1 COUT
- **Etapa 1:** Half Adder sobre el bit 0 → S0, C0
- **Etapa 2:** Full Adder sobre el bit 1 → S1, COUT
  - Suma parcial N1 = A1 XOR B1
  - S1 = (A1 XOR B1) XOR C0
  - COUT = (A1 AND B1) OR ((A1 XOR B1) AND C0)
- **Subcircuitos TTL reales (serie 74LS):**
  - `SUB_XOR2` → 7486
  - `SUB_AND2` → 7408
  - `SUB_OR2` → 7432
- Estimulo: 4 fuentes `PULSE` (10 ms / 20 ms / 40 ms / 80 ms)
- Analisis transitorio: `.TRAN 1m 160m`

> El netlist del repo `kicad/sumador_2bits/` (KiCad) corresponde al circuito de
> **1 bit** de esta misma seccion.

### KiCad Local History

KiCad guarda snapshots automaticos del esquematico en `.history/`, que es un
**repositorio Git anidado** con 37 commits "Autosave" y 2 tags
(`Last_Save_sch`, `Save_sch_1`).

Ese historial **si se conserva**, pero movido a `docs/kicad-local-history/`
para que no quede como repo anidado (Git no puede versionar un repo dentro de
otro repo).

**Como consultarlo:**

```bash
cd docs/kicad-local-history
git log --oneline --all
git show Last_Save_sch:sumador_2bits.kicad_sch   # ver una version concreta
```

Los tags `Last_Save_sch` y `Save_sch_1` tambien estan disponibles:

```bash
git tag -l
git diff Save_sch_1 Last_Save_sch -- sumador_2bits.kicad_sch
```

> En KiCad se ve con: **File > Local History**. La carpeta se movio, asi que
> el menu dejara de listar estas versiones en la maquina local; para
> recuperarlas, usa los comandos `git` de arriba.

### Lo que NO se versiona

| Patron | Razon |
|---|---|
| `.history/` | Se versiona por separado (ver arriba) |
| `*.kicad_prl` | Preferencias locales de la ventana de KiCad (paneles abiertos, etc.) |
| `*-backups/`, `fp-info-cache` | Caché de KiCad |

---

## 📄 `docs/`

| Archivo | Descripcion |
|---|---|
| `Monitoreo_Medicamentos_Contexto.md` | Contexto completo del proyecto **Monitoreo de Medicamentos** (Electronica Digital) |
| `kicad-local-history/` | Historial de diseno de KiCad (37 autosaves + 2 tags) |

### `Monitoreo_Medicamentos_Contexto.md`

Documenta el proyecto grupal de Electronica Digital (UMG, sede El Naranjo):

- **Modalidad:** 100% simulado en Proteus 8, no se arma nada fisico
- **Tema:** Monitoreo de Medication (elegido porque los sistemas de seguridad no fueron aprobados)
- **Reloj DS1307** para la hora; el LCD muestra la hora actual y la proxima dosis (08:00, 14:00, 20:00)
- **Confirmacion:** al llegar la hora se presiona el boton, se enciende el LED verde y el LCD confirma la toma
- **Alerta por no toma:** LED rojo + buzzer parpadean; si pasan 10 minutos sin confirmar quedan fijos y el LCD muestra `DOSIS OMITIDA!`
- LEDs: `LED-RED` (D10) para la alerta, `LED-GREEN` (D11) para la confirmacion, ambos con 220 Ω

El diseno correspondiente esta en `proteus/Monitoreo_Medicamentos.pdsprj`. Antes de este
archivo, el proyecto solo existia como `.pdsprj` binario, sin explicacion del
contexto, los requisitos ni la interfaz.

---

## Requisitos

- **Proteus 8.x** (o superior) para los `.pdsprj`
- **KiCad 9.x** para los `.kicad_sch` / `.kicad_pcb`
- **Multisim** para los netlists SPICE `.cir`

---

## Nota sobre el historial de `sumador_2bits`

El esquematico `.kicad_sch` **ya viene con historial de diseño**: los 37
autosaves de KiCad documentan la evolución del circuito. Ese historial es
información real del proceso de diseño y por eso se conserva en vez de
borrarse.
