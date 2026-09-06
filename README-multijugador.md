# Zombie Maze — Multijugador (2-3 jugadores)

## Qué cambió respecto a la versión de un solo jugador

- Usa **Firebase Realtime Database** (el mismo proyecto que ya usabas para
  "juego-de-rocas") para sincronizar jugadores, zombies, puertas y cajas
  entre todos los que estén conectados a la misma sala.
- **Salas por código**: al abrir el archivo sin `?sala=...` en la URL, se
  genera un código nuevo y te muestra el link para compartir. Quien abra
  ese link entra a tu misma partida.
- **Colores y nombres**: cada jugador que entra elige un nombre y se le
  asigna automáticamente un color distinto (azul, rojo, verde, naranja,
  violeta, turquesa).
- **Cooldown de 1 segundo**: sigue igual que antes — tras recibir un golpe
  de un zombie, hay 1 segundo de invulnerabilidad (parpadeo visual) antes
  de que te puedan volver a golpear.

## Cómo funciona por dentro (para que sepas qué esperar)

- El **primer jugador que entra a una sala** se convierte automáticamente
  en el "host". El host es quien simula el movimiento e inteligencia de
  los zombies y decide cuándo termina la partida (victoria/derrota). Esto
  evita que cada jugador calcule su propia versión de los zombies y se
  desincronicen.
- Si el host cierra la pestaña, ese puesto queda libre — pero (en esta
  versión) nadie lo retoma automáticamente a mitad de partida. Si el host
  se desconecta, lo más simple es que todos recarguen y empiecen sala
  nueva. Si querés que reasigne el host automáticamente en pleno juego,
  decime y lo agrego.
- Cada jugador dispara y simula sus propias balas (para que se sientan
  instantáneas al jugar), y esas balas se replican a los demás por
  Firebase para que las vean.
- El laberinto, las puertas y las cajas son iguales para todos porque se
  generan con el mismo algoritmo determinista en cada navegador — no hace
  falta mandarlas por red, solo el ESTADO de las puertas (abierta/cerrada)
  y de las cajas (recogida/reparada) sí se sincroniza.

## Pasos para subir esto a tu proyecto existente

### 1. Reglas de la base de datos (importante)

Como ahora vas a tener varias salas activas al mismo tiempo compartiendo
el mismo proyecto de Firebase, anda a:

**Firebase Console → Realtime Database → Reglas**

y asegurate de tener algo así (abierto para lectura/escritura, igual que
en el juego de rocas):

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

Esto es lo mismo que ya tenías configurado para el juego de rocas, así
que si no lo tocaste, ya debería funcionar tal cual.

### 2. Reemplazar el archivo en GitHub

1. Andá a tu repositorio `juego-rocas-2` en GitHub.
2. Abrí el archivo `index.html` (o como se llame el archivo principal).
3. Reemplazá TODO su contenido por el contenido de
   `zombie-maze-multiplayer.html` (el que te acabo de generar).
4. Hacé commit de los cambios (el mismo flujo que ya usaste antes:
   "Commit changes" en verde, abajo).

### 3. Vercel redeploya solo

Como tu repo ya está conectado a Vercel, en cuanto hagas el commit,
Vercel va a detectar el cambio y redesplegar automáticamente. En 1-2
minutos tu link (`https://juego-rocas-2.vercel.app` o el que tengas)
va a mostrar el juego de zombies en vez del de rocas.

### 4. Probarlo con tus amigos

1. Abrí el link de Vercel vos primero (sin agregar nada a la URL) — vas a
   ver la pantalla de login con un código de sala generado y un link para
   compartir, algo como:
   `https://juego-rocas-2.vercel.app/?sala=AB12CD`
2. Poné tu nombre y entrá.
3. Copiá ese link con `?sala=...` y mandaselo a tus amigos.
4. Cuando ellos lo abran, van a ver directamente "Te vas a unir a la sala:
   AB12CD" — solo ponen su nombre y entran a tu misma partida.

## Si querés cambiar el proyecto de Firebase

El código ya trae puestas las credenciales de tu proyecto `juego-de-rocas`
(las mismas que usaste antes). Si preferís usar un proyecto de Firebase
nuevo y separado, buscá este bloque cerca del principio del `<script>` y
reemplazá los valores:

```javascript
const firebaseConfig = {
    apiKey: "...",
    databaseURL: "...",
    projectId: "...",
    appId: "..."
};
```

Esos valores los sacás de: Firebase Console → Configuración del proyecto
→ General → Tus apps → Configuración del SDK.
