# Tabata — app para iOS y Android

Temporizador Tabata con rondas, ejercicios, descansos configurables, cuenta previa, enfriamiento y audio.
La app es una página web (`www/index.html`) empaquetada con [Capacitor](https://capacitorjs.com) en proyectos nativos para iOS y Android.

## Estructura

- `www/index.html`: la app (aquí se hacen los cambios de diseño y funciones).
- `android/` e `ios/`: proyectos nativos generados por Capacitor.
- `capacitor.config.json`: nombre de la app e identificador.
- `.github/workflows/`: compilación automática en la nube (`android.yml` e `ios.yml`).

## Antes de publicar: cambia el identificador

En `capacitor.config.json` el `appId` es `com.tabata.timer`. Es un identificador **permanente** en las tiendas, así que cámbialo ahora por uno propio (por ejemplo `cl.tunombre.tabata`).
Después de cambiarlo, borra las carpetas `android` e `ios` y regenera con:

```
npm ci
npx cap add android
npx cap add ios
```

## Cómo se compila en la nube

Cada vez que subes cambios a la rama `main` de GitHub, se ejecutan dos flujos (pestaña **Actions** del repositorio):

| Flujo | Qué produce |
|---|---|
| Android | Un `.apk` para instalar directo en un teléfono Android y un `.aab` sin firmar (formato de Google Play). Se descargan desde la sección *Artifacts* de la ejecución. |
| iOS | Verifica que la app compila en iOS (sin firma). Todavía **no** genera un archivo para la App Store. |

## Publicar en Google Play

1. Crea una cuenta de desarrollador en Google Play Console (pago único de unos US$25).
2. Crea una llave de firma (keystore) y guárdala en un lugar seguro; sin ella no podrás actualizar la app.
3. Configura la firma del `.aab` (se agrega al flujo `android.yml` con *Secrets* de GitHub).
4. Sube el `.aab` firmado, completa la ficha de la tienda y envía a revisión.

## Publicar en la App Store (iOS)

1. Crea una cuenta de Apple Developer (unos US$99 al año).
2. Crea el certificado de distribución y el perfil de aprovisionamiento.
3. Agrega esos datos como *Secrets* de GitHub y amplía `ios.yml` para firmar y subir la app a TestFlight.
4. Desde App Store Connect, completa la ficha y envía a revisión.

Apple suele rechazar apps que son solo una página web sin nada nativo. Conviene agregar funciones nativas (por ejemplo, mantener la pantalla encendida durante el entrenamiento y sonido en segundo plano) antes de enviarla.

## Probar en el computador

Abre `www/index.html` en un navegador, o ejecuta `npx serve www`.
