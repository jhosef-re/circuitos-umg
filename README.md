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
| `sumador_2bits.cir` | Netlist SPICE equivalente del sumador de 2 bits (compatible con Multisim) |

El esquema del sumador de 2 bits:

- **Entradas:** X, Y
- **Salidas:** F0 = suma (X XOR Y), F1 = acarreo (X AND Y)
- Alimentacion: VCC = 5V, VEE = GND
- Estimulo PWL con periodo de 40 ms por entrada
- Analisis transitorio: `.TRAN 1m 80m 0 1m`

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

## Requisitos

- **Proteus 8.x** (o superior) para los `.pdsprj`
- **KiCad 9.x** para los `.kicad_sch` / `.kicad_pcb`
- **Multisim** para el netlist SPICE `.cir`

---

## Nota sobre el historial de `sumador_2bits`

El esquematico `.kicad_sch` **ya viene con historial de diseño**: los 37
autosaves de KiCad documentan la evolución del circuito. Ese historial es
información real del proceso de diseño y por eso se conserva en vez de
borrarse.
