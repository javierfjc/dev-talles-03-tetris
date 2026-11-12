# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Tetris en JavaScript vanilla + HTML5 Canvas. Sin `package.json`, sin build, sin tests, sin linter. UI y README en español.

## Run

Abrir `index.html` en el navegador, o servir estático: `python -m http.server 8000` / `npx serve .`.

## Architecture

Tres archivos: `index.html` (DOM + canvases), `style.css`, `game.js` (toda la lógica, script global con `'use strict'`, sin módulos).

- `index.html` define IDs que `game.js` busca al cargar (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renombrar uno rompe el script.
- Estado global mutable en `game.js` (`board`, `current`, `next`, `score`, `level`, `paused`, `gameOver`...), reiniciado por `init()` (también usado por el botón reiniciar).
- Tablero: matriz `ROWS×COLS`, `0` = vacío, `1–7` = índice en `COLORS`/`PIECES` (el índice 0 es `null` a propósito).
- Flujo: `loop` (rAF) baja pieza por `dropInterval` → `lockPiece` = `merge` → `clearLines` → `spawn`. `spawn` llama `endGame` si la nueva pieza colisiona.
- Pausa/game over cancelan el rAF (`cancelAnimationFrame(animId)`); reanudar relanza `loop` manualmente.
- Velocidad: `max(100, 1000 - (level-1)*90)` ms, recalculada en `clearLines`.
- Puntuación: `LINE_SCORES[cleared] * level`; hard drop +2/celda, soft drop +1/fila.
- Rotación: `rotateCW` + wall kicks simples en `tryRotate` (`[0,-1,1,-2,2]`).

## Gotchas

- Cambiar `COLS`/`ROWS`/`BLOCK` exige actualizar `width`/`height` del `<canvas id="board">` en `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`).
- Canvas de siguiente pieza es 120×120 con celdas fijas de 30px (`NB` en `drawNext`), asumiendo caja 4×4.
