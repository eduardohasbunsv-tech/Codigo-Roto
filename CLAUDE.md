# CÓDIGO ROTO

## Proyecto

RPG de acción pixel art cyberpunk ambientado en El Salvador 2089. Free-to-play con monetización freemium ética. El jugador controla a **Kael**, un joven de 16 años que puede manipular el código de la realidad ("Glitcher"). El antagonista es **CIPHER**, una IA que busca reescribir el código de la nación.

- **Género:** Action RPG / Hack-n-Slash / Pixel Art
- **Motor:** HTML5 Canvas (Vanilla JS, migración a Phaser.js planeada)
- **Resolución nativa:** 320x180
- **Plataforma:** Web Adaptive (mobile-first) + PWA + iOS vía Capacitor
- **Modelo:** Free-to-Play con microtransacciones cosméticas y de conveniencia
- **Idioma del juego:** Español (salvadoreño)
- **Duración estimada:** ~25 horas (historia principal + sidequests)

## Estructura de Archivos

- `codigo_roto_v1.html` — Juego completo v1.0 (single-file: HTML + CSS + JS)
- `codigo_roto_gdd.html` — Game Design Document (GDD) en formato HTML estilizado

## Arquitectura Técnica

### Single-File Architecture
Todo el juego está contenido en un único archivo HTML. El código JS está inline en un `<script>` al final del body. No hay dependencias externas excepto Google Fonts (Press Start 2P).

### Variables Globales Principales
- `G` — Estado global del juego (cámara, mapa actual, frame counter, estado de input, transiciones)
- `P` — Estado del jugador (posición, stats, inventario, habilidades, cooldowns)
- `M` — Definición de todos los mapas (tiles, NPCs, enemigos, items, portales)
- `C` — Paleta de colores del juego
- `T` — Tamaño de tile (16px)
- `SH` — Items de la tienda
- `GM` — Paquetes de gemas (IAP)
- `AA` — Habilidades Glitch disponibles

### Convenciones de Código
- Variables y funciones usan nombres ultra-cortos para minimizar tamaño (ej: `cv`=canvas, `X`=context2D, `P`=player, `G`=game)
- Colores definidos en objeto `C`: `c`=cyan, `p`=pink, `v`=violet, `g`=green, `o`=orange, `y`=yellow, `r`=red, `w`=white
- IDs de elementos DOM también abreviados: `S`=splash, `G`=canvas, `TC`=touch controls, `DB`=dialog box, `NF`=notification, `OV`=overlay menu
- Tile types son numéricos: 0=suelo, 1=muro, 2=edificio, 5=puesto mercado, 6=arena, 7=agua, 8=árbol, 9=roca, 10=flores, 11=lava
- Funciones clave: `bm()`=build map, `upd()`=update loop, `drw()`=draw loop, `hEn()`=hit enemy

### Game Loop
- `loop()` → llama `upd()` y `drw()` cada frame vía `requestAnimationFrame`
- `upd()` — Input, movimiento, colisiones, combate, cooldowns, portales, partículas, cámara
- `drw()` — Renderiza tiles, portales, items, enemigos, NPCs, jugador, HUD, efectos

### Sistema de Tiles
Cada mapa se define como grid 2D (`m.tl[y][x]`). La función `sol(t)` determina si un tile es sólido (colisionable). Mapas se construyen proceduralmente en `bm()`.

### Controles
- **PC:** WASD/Flechas=mover, Z=atacar, X=glitch, V=dash, C/Espacio=interactuar, I/Escape=menú
- **Móvil:** Joystick analógico (izquierda) + 4 botones de acción (derecha): ATK, GLT, ACT, DSH

## Zonas del Juego (6 mapas)

| Zona | ID en código | Tamaño (tiles) | Nivel | Descripción |
|------|-------------|-----------------|-------|-------------|
| Distrito Centro | `centro` | 25x18 | 1-3 | Hub principal, ciudad cyberpunk neón |
| Mercado Central | `mercado` | 20x15 | 2-4 | Comercio, pupuseras, herreros |
| Catacumbas | `catacumbas` | 22x16 | 3-6 | Dungeon, base Código Libre, Mini Boss |
| Costa Tunco | `costatunco` | 28x16 | 4-7 | Playa, agua animada, boss Tiburón Glitch |
| Ruta de las Flores | `rutaflores` | 30x14 | 5-8 | Vegetación, 4 pueblos, pétalos cayendo |
| Cerro Verde | `cerroverde` | 26x20 | 8-12 | Endgame, volcán, lava, boss final Izalco Core |

### Conexiones entre zonas (portales)
```
              mercado
                ↑
rutaflores ← centro → costatunco
                ↓          ↑
           catacumbas   rutaflores
                           ↓
                       cerroverde
```

## Personajes

- **Kael** (protagonista) — Glitcher, 16 años, stats: HACK 85%, VEL 70%, FZA 45%, DEF 50%
- **Luna** — Hacker, 17 años, lidera "Código Libre". Aparece en Centro y Catacumbas
- **Rudo** — Tank/Mecánico, brazo cibernético. Construye implantes en Centro
- **CIPHER** — IA antagonista, busca "optimizar" la humanidad reescribiendo el código

### NPCs por zona
- **Centro:** Luna, Rudo, Vendor (tienda)
- **Mercado:** Pupusera, Herrero (tienda)
- **Catacumbas:** Luna
- **Costa Tunco:** Surfer, Bartender, Pescadora (tienda)
- **Ruta de las Flores:** Doña Carmen, Caficultor, Artesana (tienda), Guía
- **Cerro Verde:** Guardián, Ermitaño, Científica (tienda)

## Sistema de Combate

- **Ataque básico:** Golpe direccional, daño = 10 + LV*3 + random(0-4)
- **Glitch (AoE):** Radio 45px, daño = 18 + LV*4, cuesta 25 GP, cooldown 50 frames
- **Dash:** Desplazamiento rápido con i-frames, cooldown 22 frames
- **Game feel:** Freeze frames (3-5), screen shake, partículas, sistema de combos

### Habilidades Glitch
| Habilidad | Costo GP | Obtención |
|-----------|----------|-----------|
| GRAVITY FLIP | 25 | Gratis (inicial) |
| CORRUPT | 25 | Gratis (inicial) |
| CLONE.EXE | 30 | Gratis (inicial) |
| ROLLBACK | 35 | 8 gemas |
| DECRYPT | 20 | 5 gemas |
| OVERFLOW | 50 | 12 gemas |

## Progresión

- XP base para nivel 2: 80, escala x1.4 por nivel
- Por nivel: +12 HP máx, +8 GP máx, +0.04 velocidad, restaura HP/GP
- Stats iniciales: 100 HP, 80 GP (máx 100), velocidad 1.4

## Economía y Monetización

### Moneda dual
- **Monedas (💰):** Gratis, drops de enemigos y mapa. Compra items básicos
- **Gemas (💎):** Premium (10% drop enemigos, 50% bosses, mapa, gema diaria, o IAP). Compra habilidades, skins, items premium

### Tienda de Gemas (IAP)
| Paquete | Gemas | Precio |
|---------|-------|--------|
| Puñado | 10 | $0.99 |
| Bolsa | 55 | $4.99 (+10%) |
| Cofre | 120 | $9.99 (+20%) |
| Bóveda | 300 | $19.99 (+35%) |

### Principios de monetización ética
- Todo el contenido de historia accesible sin pagar
- Gemas obtenibles gratis jugando
- Skins puramente cosméticas
- Habilidades premium son alternativas, no superiores
- Sin loot boxes, sin publicidad intrusiva, sin paywalls

## Distribución

- **Fase 1:** PWA (costo $0, hosting en GitHub Pages)
- **Fase 2:** App Store vía Capacitor ($99/año Apple, $25 único Google)
- **PWA meta tags** ya incluidos en el HTML (apple-mobile-web-app-capable, theme-color, etc.)

## Enemigos

| Enemigo | HP | ATK | XP | Monedas | Zonas |
|---------|-----|-----|-----|---------|-------|
| Drone | 30-40 | 8-12 | 15-20 | 5-8 | Centro, Mercado, Catacumbas |
| Glitch Bug | 45 | 12 | 25 | 8 | Centro |
| Bug Élite | 50 | 14 | 35 | 12 | Catacumbas |
| Mini Boss | 80 | 20 | 60 | 25 | Catacumbas |
| Medusa Digital | 55 | 15 | 30 | 10 | Costa Tunco |
| Cangrejo Cyber | 50 | 14 | 28 | 12 | Costa Tunco |
| Tiburón Glitch (boss) | 120 | 24 | 80 | 35 | Costa Tunco |
| Planta Virus | 50 | 14 | 28 | 10 | Ruta Flores |
| Escarabajo | 55 | 16 | 32 | 12 | Ruta Flores |
| Tucán Corrupto | 70 | 20 | 45 | 18 | Ruta Flores |
| Golem Volcánico | 80 | 22 | 50 | 20 | Cerro Verde |
| Águila Cyber | 70 | 18 | 42 | 16 | Cerro Verde |
| Serpiente Lava | 90 | 24 | 58 | 24 | Cerro Verde |
| Izalco Core (boss final) | 250 | 35 | 180 | 100 | Cerro Verde |

- Los enemigos respawnean cada 25 segundos
- Bosses tienen 50% de drop de gema, enemigos normales 10%

## Items

### Tienda (monedas)
Nano Poción (+30HP, 15💰), Célula Energía (+40GP, 20💰), Escudo Nano (-10% daño, 30💰), Pupusa HP (+50HP, 25💰), Chip Velocidad (+Speed temporal, 45💰)

### Tienda (gemas)
Poción XL (+80HP, 3💎), ROLLBACK (habilidad, 8💎), DECRYPT (habilidad, 5💎), OVERFLOW (habilidad, 12💎), Skin Neon (cosmético, 10💎)

### Items únicos de zona
- **Costa Tunco:** Concha (venta +15💰)
- **Ruta Flores:** Flor Digital (+20HP +20GP), Café Apaneca (+60GP +Speed temporal)
- **Cerro Verde:** Cristal Volcánico (+15 GP máx permanente), Poción XL

## Roadmap

| Versión | Contenido | Estado |
|---------|-----------|--------|
| v1.0 | 6 zonas, 4 personajes, combate, tienda, monetización, touch | Completo |
| v1.1 | Más enemigos, misiones secundarias, logros | Planeado |
| v1.5 | Nuevas zonas (Lago Coatepeque, Joya de Cerén, Suchitoto) | Planeado |
| v2.0 | Co-Op 2-4 jugadores, PvP arena, ranking | Concepto |
| v2.5 | Historia expandida, múltiples finales, New Game+ | Concepto |

## Propiedad Intelectual

- "CÓDIGO ROTO" es término genérico, sin marca registrada en videojuegos
- Nombres de zonas son referencias geográficas reales de El Salvador
- Personajes son nombres ficticios genéricos sin conflictos
- Todo el arte es generado por código (Canvas), 100% original, sin assets de terceros
- Recomendación: registrar marca en CNR (El Salvador) y USPTO (EE.UU.)

## Notas para Desarrollo

- El CSS usa clases ultra-cortas (`.jz`=joystick zone, `.ab`=action buttons, `.si`=shop item, etc.)
- La fuente del juego es "Press Start 2P" (Google Fonts)
- El GDD menciona Phaser.js pero la v1.0 usa Vanilla Canvas API
- Touch detection: `'ontouchstart' in window || navigator.maxTouchPoints > 0`
- El juego auto-escala manteniendo aspect ratio 320:180
- Los diálogos de NPCs son arrays de strings que avanzan con ACT
- NPCs con `sh:true` abren la tienda al terminar el diálogo
