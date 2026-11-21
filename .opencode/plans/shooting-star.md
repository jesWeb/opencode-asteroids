# Implementación: asteroide especial "estrella fugaz"

## Objetivo
Añadir un asteroide especial que:
- Se mueve más rápido que los asteroides normales.
- Desaparece automáticamente tras un tiempo (TTL).
- Requiere 6 impactos de bala para destruirse (hp=6).
- Color naranja rojizo con naranja.
- Deja un rastro de llamas como sombra.

## Archivos a modificar
- `game.js` únicamente (sin dependencias).

## Diseño

### 1. Partículas coloreadas
Modificar `Particle` para aceptar color RGB:
- Constructor: `constructor(x, y, r=255, g=255, b=255)`.
- Guardar `this.r/g/b`.
- `draw()` usar `rgba(r,g,b,alpha)`.
- `explode(x, y, count=8, r=255, g=255, b=255)` pasar color a `Particle`.

### 2. Clase `ShootingStar`
Añadir tras `Asteroid`:
- `constructor()`: posición aleatoria, dirección aleatoria, velocidad 260–360 px/s, radio ~18, `hp=6`, `ttl` 7–10s, `trail=[]`, `flash=0`.
- `update(dt)`: guardar posición anterior en `trail` (máx. 18), borrar trail si hay salto por wrap, mover con wrap, decrementar ttl/flash, marcar `dead` si ttl<=0 o hp<=0.
- `hit()`: `hp--`, `flash=0.12`, spawn de 4 partículas naranjas; si hp<=0 marcar dead y explosión grande.
- `draw()`: rastro de llamas (círculos naranja/rojo decrecientes), núcleo naranja rojizo con borde naranja y brillo radial.

### 3. Estado y spawn
- Variables globales: `shootingStars`, `shootingStarSpawnTimer`.
- `spawnShootingStar()`: crear solo si no hay una activa (máx. 1), posición aleatoria.
- `initGame()`: inicializar arrays/timer.
- `nextLevel()`: limpiar `shootingStars`, resetear timer.

### 4. Update (estado playing)
- Decrementar `shootingStarSpawnTimer`; si <=0 y sin estrella activa, spawn y resetear timer (20–35s).
- Actualizar `shootingStars`, filtrar dead.
- Colisión bala vs estrella: en el mismo bucle de balas, si `dist(b, star) < star.radius`, marcar bala dead, `star.hit()`, si muere `score += 500` y explosión naranja, filtrar.
- Colisión nave vs estrella: incluir en bucle de asteroides (o separado) -> `killShip()`.

### 5. Estados dead/gameover
- Actualizar y filtrar `shootingStars` para que sigan moviéndose/ desapareciendo.

### 6. Draw
- Dibujar `shootingStars` (después de asteroides, antes de balas o partículas según capas).

### 7. Nivel completado
- Mantener condición actual (`asteroids.length === 0`); las estrellas no bloquean el nivel.
- `nextLevel()` limpia estrellas.

## Verificación manual
- Abrir `index.html`, comprobar que aparece una estrella fugaz cada ~20-35s, se mueve rápido, desaparece sola, necesita 6 disparos, color naranja rojizo con rastro de llamas, da 500 puntos al destruirse, colisión con nave mata.
- Asegurar que dt se mantiene y no hay errores en consola.

## Notas de diseño
- "6 disparos consecutivos" se interpreta como 6 impactos acumulados (hp=6); si expira el TTL antes, desaparece sin puntos.
- Máximo 1 estrella activa para evitar saturación.