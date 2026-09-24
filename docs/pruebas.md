# Plan de Pruebas y Matriz de QA — Entrega Parcial 1

## Característica evaluada
**Integración de la Skin Jugable "El Calvo con Capa" (`CALVO_CON_CAPA`) con manejo de estado persistente y fallback ante datos inválidos.**

---

## 1. Matriz de Casos de Prueba Ejecutados

| ID | Tipo de Prueba | Descripción | Datos de Entrada | Resultado Esperado | Resultado Observado | Estado |
|---|---|---|---|---|---|---|
| **CP-01** | **Ruta Feliz** | Selección y renderizado de la skin "El Calvo con Capa" en el mapa exterior. | Seleccionar `CALVO_CON_CAPA` en `SkinSelectorDialog`. | El personaje se dibuja con uniforme amarillo, capa blanca y 4 animaciones operativas (Idle, Walk, Run, Special). | Renderizado fluido a 60 fps, animaciones y proporciones correctas. | **PASS ✅** |
| **CP-02** | **Estado Alterno (Dato Inválido / Corrupto)** | Manejo de identificador de skin corrupto o inexistente en preferencias guardadas. | `KEY_PLAYER_SKIN = "INVALID_SKIN_XYZ"` en `SettingsRepository`. | La aplicación captura el error mediante `runCatching` y retorna el fallback seguro `PlayerSkin.LAZARO` sin crashear. | Fallback ejecutado limpiamente; no hay excepciones no controladas. | **PASS ✅** |
| **CP-03** | **Navegación y Recreación** | Conservación del personaje seleccionado al rotar dispositivo o recrear la pantalla / Activity. | Cambiar orientación / recrear `WorldMapScreen`. | El estado del jugador y la skin seleccionada persisten sin resetearse. | La skin seleccionada se conserva intacta a través del ciclo de vida. | **PASS ✅** |
| **CP-04** | **Accesibilidad y UI** | Legibilidad de texto en selector de personajes y área táctil del diálogo. | Abrir diálogo de selección de skin. | Etiquetas legibles (`displayName = "Calvo con Capa"`), alto contraste y miniatura nítida. | Componentes accesibles con respuesta táctil inmediata. | **PASS ✅** |
| **CP-05** | **Suite Automatizada KMP** | Ejecución de suite de tests unitarios y linter estático. | `./gradlew :shared:testAndroidHostTest :app:testDebugUnitTest` | 429+ pruebas unitarias en verde y 0 issues de Detekt. | Build exitoso con 0 errores y 0 warnings en CI. | **PASS ✅** |

---

## 2. Entorno de Ejecución
* **Dispositivo de prueba:** Google Pixel 9 Pro XL / MacBook Pro Apple Silicon (M2 Pro, 32GB RAM)
* **SDK:** Android API 36 / JVM OpenJDK 17
* **Versión:** Politécnico Open World 1.0.0.12+dev
