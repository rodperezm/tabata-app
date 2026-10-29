# CLAUDE.md

Temporizador Tabata: una sola página web (`www/`) empaquetada con Capacitor 7 para Android/iOS y publicada también como web instalable en GitHub Pages. Textos de la interfaz, comentarios y commits en español.

## Estructura
- `www/index.html` — toda la app (HTML+CSS+JS en un archivo, sin framework ni build). Aquí van casi todos los cambios.
- `www/sw.js` — service worker (primero la red y, si falla, la caché). `www/manifest.webmanifest`, `www/icons/`, `www/instalar-iphone.html` (instrucciones para iPhone; `instalar.html` solo redirige ahí) y `www/instalar-android.html` (para Android).
- `android/`, `ios/` — proyectos nativos generados por Capacitor; casi sin modificar. No editar los assets web copiados ahí: se regeneran con `npx cap sync`.
- `capacitor.config.json` — `appId` (`com.tabata.timer`, permanente en las tiendas), `webDir: www`.

## Cómo funciona el temporizador
- Configuración en `cfg` (claves R, E, W, D, RR, P, C), guardada en `localStorage` con la clave `tabata_cfg`.
- Al iniciar, `buildTrack()` genera un WAV (8 kHz, máx. 40 min) con todos los pitidos; el reloj es `trackAudio.currentTime`, así sigue con la pantalla bloqueada. Si la pista falla, usa el "modo en vivo" (`performance.now()` + WebAudio). Mantener ambos caminos coherentes.
- Pantalla encendida: Wake Lock y, en iOS, un video vacío (`NOSLEEP_SRC`). Controles de la pantalla de bloqueo: Media Session.

## Probar
- `npx serve www` o abrir `www/index.html` en el navegador. No hay tests ni linter.
- Nativo: `npx cap sync` y luego `npm run android` / `npm run ios`.

## Publicación (GitHub Actions, en cada push a `main`)
- `pages.yml`: si cambia `www/**`, publica `www/` tal cual en https://rodperezm.github.io/tabata-app/. **Al cambiar archivos de la web, subir la versión de `CACHE` en `www/sw.js`** (ej. `tabata-v3` → `tabata-v4`); si no, los usuarios siguen con la versión vieja guardada.
- `android.yml`: `npm ci` → `cap sync android` → APK de prueba + AAB sin firmar (en *Artifacts*). Sin firma todavía.
- `ios.yml`: solo comprueba que compila para el simulador, sin firma. No genera ningún archivo instalable.
- Versión de Android: `versionCode`/`versionName` en `android/app/build.gradle`, se sube a mano.
- Nunca subir llaves de firma (`*.keystore`, `*.jks`, `*.p12`, `*.mobileprovision` ya están en `.gitignore`); deben ir como Secrets de GitHub.
