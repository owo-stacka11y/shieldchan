# Shield-chan — Mod de Forge para Minecraft Java 1.20.1

Este mod convierte el resource pack de Bedrock **shield-chan** en un mod de
Forge: añade un ítem de escudo (`shieldchan:shield_chan`) que se comporta
como un escudo vanilla (bloquea igual) pero se dibuja usando el modelo/textura
original del pack, convertido automáticamente desde
`models/entity/shield.geo.json`.

## ⚠️ Importante: esto es el código fuente, no un .jar compilado

No pude compilar el `.jar` final en el entorno donde generé este mod porque
no tiene acceso a los servidores de Forge/Mojang (necesarios para descargar
el toolchain de ForgeGradle). Te dejo el proyecto listo para compilar tú
mismo. Los pasos son sencillos:

### Requisitos
- **JDK 17** instalado (Forge 1.20.1 lo requiere).
- Conexión a internet (para que Gradle descargue Forge/Minecraft la primera vez).

### Pasos para compilar
1. Descarga el **MDK oficial de Forge 1.20.1** desde
   https://files.minecraftforge.net/net/minecraftforge/forge/index_1.20.1.html
   (elige una versión recomendada, p. ej. 47.2.0 o superior) y descomprímelo.
2. Copia el contenido de la carpeta `src/` de este proyecto sobre la carpeta
   `src/` del MDK descargado (reemplazando el `ExampleMod.java` de ejemplo).
3. Copia también `build.gradle`, `gradle.properties` y `settings.gradle` de
   este proyecto sobre los del MDK (o combínalos si el MDK trae otra versión
   de Forge y prefieres mantenerla — solo actualiza el `mod_id`).
4. Desde una terminal, dentro de la carpeta del proyecto:
   ```
   ./gradlew build
   ```
   (en Windows: `gradlew.bat build`)
5. El `.jar` final quedará en `build/libs/shieldchan-1.0.0.jar`. Cópialo a la
   carpeta `mods` de tu instalación de Forge 1.20.1.

También puedes abrir la carpeta directamente en **IntelliJ IDEA** con el
plugin de Gradle — importará el proyecto y podrás ejecutar la tarea
`runClient` para probarlo sin empaquetar nada.

## Sobre la conversión del modelo

El modelo original de Bedrock (formato `geo.json`, sistema de huesos) fue
traducido a un `ModelPart`/`LayerDefinition` de Java automáticamente:

- Las posiciones de los "pivots" y las cajas (cubes) se recalcularon a
  coordenadas relativas, como requiere el formato de Java.
- Las rotaciones se convirtieron invirtiendo el signo de los ejes X e Y
  (convención estándar Bedrock → Java), manteniendo Z igual.
- La textura (64×64) se copió tal cual a
  `assets/shieldchan/textures/item/shield_chan.png`.

**Es muy probable que necesites ajustar a mano algunos valores** al probarlo
en el juego, sobre todo:
- La posición/escala global en `ShieldChanItemRenderer.java`
  (`poseStack.translate(...)`, `poseStack.scale(...)`), para que quede bien
  centrado en la mano/GUI.
- Algún ángulo de rotación puntual, si algún hueso se ve "torcido" (los
  motores de Bedrock y Java no son 100% idénticos en cómo interpretan el
  orden de rotación en casos límite).

No tuve forma de renderizar el modelo dentro de Minecraft en este entorno,
así que la conversión es matemáticamente correcta pero no ha sido validada
visualmente in-game.

## Estructura del proyecto

```
src/main/java/com/shieldchan/mod/
├── ShieldChanMod.java          # clase principal, registro, pestaña creativa
├── ModItems.java                # DeferredRegister del ítem
├── ShieldChanItem.java          # ítem (extiende ShieldItem vanilla)
└── client/
    ├── ShieldChanModel.java         # modelo convertido desde el geo.json
    ├── ShieldChanItemRenderer.java  # renderer personalizado (BEWLR)
    └── ClientModEvents.java         # registro de la capa del modelo

src/main/resources/
├── META-INF/mods.toml
├── pack.mcmeta
└── assets/shieldchan/
    ├── lang/{es_es,en_us}.json
    ├── models/item/shield_chan.json
    └── textures/item/shield_chan.png
```
