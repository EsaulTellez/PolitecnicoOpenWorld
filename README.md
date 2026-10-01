# Primer Examen Parcial — Pull Request con Aseguramiento de Calidad (QA)

**Instituto Politécnico Nacional**  
**Escuela Superior de Cómputo (ESCOM)**  
**Unidad de Aprendizaje:** Desarrollo de Aplicaciones Móviles Nativas  
**Plan de Estudios:** Ingeniería en Sistemas Computacionales (2020)  
**Profesor:** Gabriel Hurtado Avilés  
**Periodo:** 2027-1 | **Grupo:** 7CV4  
**Alumno:** Esaul Téllez (GitHub: [@EsaulTellez](https://github.com/EsaulTellez))  
**Fecha de Entrega:** Jueves 1 de Octubre de 2026  

---

## 📌 1. Índice de Entrega y Enlaces Oficiales

* **Liga al Pull Request Oficial (en el repo del profesor):**  
  👉 **[Pull Request #143: feat: add playable character skin "El Calvo con Capa" with animations, fallback state and tests](https://github.com/gabrielhuav/PolitecnicoOpenWorld/pull/143)**
* **Liga a la Issue de Trabajo (en el fork del alumno):**  
  👉 **[Issue #1: [FEATURE] Add playable character skin "El Calvo con Capa" with invalid state fallback](https://github.com/EsaulTellez/PolitecnicoOpenWorld/issues/1)**
* **Repositorio Fork del Alumno:**  
  👉 [https://github.com/EsaulTellez/PolitecnicoOpenWorld](https://github.com/EsaulTellez/PolitecnicoOpenWorld)
* **Rama de Trabajo de la Característica:** `feat/add-calvo-con-capa-skin`
* **Rama Académica de Entrega e Informe:** `entrega-parcial-1`
* **SHA Base de `main` (commit de partida sincronizado):**  
  `7ed325393f82872c2be94ff2ada46948efa19152`
* **SHA Final Evaluado (commit final de la entrega):**  
  `89bab3c272f5095ba56a93fabea3b10ae5f7a3e6`

---

## 🎯 2. Delimitación del Cambio y Alcance

### 2.1 Comportamiento Actual vs. Comportamiento Esperado
* **Comportamiento Actual:** El juego cuenta con personajes clásicos (Lázaro, Estudiantes, Robot, Policías, Prankedy). No existe una skin parodia representativa del héroe cómico "El Calvo con Capa" (Saitama). Además, si el almacenamiento de preferencias (`KEY_PLAYER_SKIN`) llegara a recibir un valor corrupto no mapeado, la llamada a `valueOf` podría lanzar una excepción no controlada (`IllegalArgumentException`).
* **Comportamiento Esperado:** Se introduce la skin jugable **"El Calvo con Capa"** disponible tanto para el **Mundo Libre (Free Roam)** como para el modo de pelea 1v1 **"HUELUM VS. GOYA"**, con animaciones dedicadas (25 frames WebP), escala normalizada a 360 px, persistencia entre rotaciones de pantalla y **mecanismo defensivo de fallback** que asegura el retorno a `LAZARO` si los datos almacenados sufren corrupción.

### 2.2 Archivos Modificados e Impacto
* **Archivos Modificados:**
  1. `PolitecnicoOpenWorld/shared/src/commonMain/kotlin/.../PlayerSkin.kt`: Registro del enum `CALVO_CON_CAPA`.
  2. `PolitecnicoOpenWorld/shared/src/commonMain/kotlin/.../SfModels.kt`: Registro del peleador `CALVO_CON_CAPA` en `SfFighterId` con `SfSharedSet`.
  3. `PolitecnicoOpenWorld/shared/src/commonMain/kotlin/.../SfStageCatalog.kt`: Mapeo de escenario hogar en ESCOM.
  4. `PolitecnicoOpenWorld/shared/src/commonMain/kotlin/.../StreetFighterViewModel.kt`: Integración a listas de combatientes masculinos y sin voz.
  5. `PolitecnicoOpenWorld/shared/src/commonTest/kotlin/.../PlayerSkinTest.kt`: Suite de pruebas unitarias KMP para Mundo Libre, Modo Combate y Fallback.
  6. `PolitecnicoOpenWorld/app/src/main/assets/SPRITES/NPC/CalvoConCapa/`: 25 assets WebP (Idle, Walk, Run, Special).
  7. `docs/pruebas.md`: Matriz de aseguramiento de calidad.
* **Lo que queda fuera:** No se modificaron contratos de red de los servidores multijugador, no se alteró la base de datos Room de mapas ni la lógica de guardado de campañas zombi.

### 2.3 Criterios de Aceptación Observables
1. **Criterio de Éxito:** Al seleccionar "Calvo con Capa" en el diálogo de skins, el jugador se desplaza a 60 fps con proporciones exactas al estándar del juego en Idle, Walk, Run y Special en el mapa exterior.
2. **Criterio de Condición Alterna / Límite:** Si se inyecta un valor corrupto en SharedPreferences (`KEY_PLAYER_SKIN = "INVALID"`), `SettingsRepository.getPlayerSkin()` captura el fallo con `runCatching` y devuelve el fallback `LAZARO` sin crash.
3. **Criterio de Modo Combate:** En Titulación por Combate (HUELUM VS. GOYA), "El Calvo con Capa" puede ser seleccionado y pelea con sus cajas de impacto balanceadas en la ESCOM.

---

## 💻 3. Entorno de Desarrollo y Ejecución de Referencia

* **Sistema Operativo:** macOS 15.6 Sequoia (Apple Silicon M2 Pro, 32 GB RAM)
* **IDE:** Android Studio Ladybug / Meerkat (compilado ARM64 nativo)
* **JDK:** OpenJDK 17 (`temurin` / Homebrew)
* **Android SDK:** Android SDK Platform 36 (Build-Tools 36.0.0, Platform-Tools 35.0.2)
* **Dispositivo Físico de Evaluación:** **Google Pixel 9 Pro XL**
  * Versión de Android: Android 15 (VanillaIceCream / API 36)
  * Procesador: Google Tensor G4
  * Depuración: USB Debugging vía ADB con permisos autorizados

---

## 🧪 4. Matriz de Aseguramiento de Calidad (6 Casos Obligatorios)

| ID | Tipo de Caso | Criterio / Riesgo | Dispositivo / Entorno | Resultado Esperado | Resultado Real | Estado |
|---|---|---|---|---|---|---|
| **CP-01** | **Ruta Feliz** | R-01 (Renderizado de skin en Mundo Libre) | Pixel 9 Pro XL (Android 15 / API 36) | Personaje renderizado con uniforme amarillo y 4 animaciones operativas. | Animación fluida a 60 fps; proporciones alineadas a 360 px. | **APROBADO ✅** |
| **CP-02** | **Condición Alterna / Límite** | R-02 (Dato corrupto en almacenamiento) | JVM / Unit Tests KMP + Pixel 9 Pro XL | `runCatching` intercepta `IllegalArgumentException` y regresa `PlayerSkin.LAZARO`. | Fallback ejecutado limpiamente; 0 excepciones no controladas. | **APROBADO ✅** |
| **CP-03** | **Regresión** | R-03 (Integridad de skins existentes) | Pixel 9 Pro XL (Android 15 / API 36) | Skins preexistentes (Lázaro, Estudiante, Prankedy) se cargan sin alteraciones. | Comportamiento y dimensiones originales conservados al 100%. | **APROBADO ✅** |
| **CP-04** | **Navegación y Ciclo de Vida** | R-04 (Recreación de Activity / Rotación) | Pixel 9 Pro XL (Android 15 / API 36) | La skin seleccionada se conserva intacta tras pausar app y rotar pantalla. | Estado persistente preservado en `WorldMapViewModel` y SharedPreferences. | **APROBADO ✅** |
| **CP-05** | **Accesibilidad y Ergonomía** | Legibilidad de UI con texto al 130% | Pixel 9 Pro XL (Android 15 / API 36) | Etiquetas sin truncar, contraste adecuado (#1A0A10) y área táctil >= 48dp. | Texto legible y miniatura nítida con feedback táctil. | **APROBADO ✅** |
| **CP-06** | **Compatibilidad / Entorno** | R-05 (Modo Combate HUELUM VS. GOYA) | Pixel 9 Pro XL (Android 15 / API 36) | Hoja de 77 poses generada en runtime sin desbordar memoria heap (OOM). | Pelea 1v1 fluida en escenario ESCOM con hitboxes activas. | **APROBADO ✅** |

> La memoria detallada de precondiciones, pasos numerados y datos de prueba vive en [`docs/pruebas.md`](https://github.com/EsaulTellez/PolitecnicoOpenWorld/blob/feat/add-calvo-con-capa-skin/docs/pruebas.md).

---

## ⚙️ 5. Verificaciones Automáticas de CI/CD (Quality Gate)

Conforme a la especificación de `.github/workflows/pr-quality-gate.yml`, se ejecutaron y auditaron las siguientes verificaciones locales:

1. **Guarda de Nombres KMP (`tools/check_kmp_test_names.sh`):**
   * *Qué comprueba:* Que ninguna función de test contenga caracteres como `(`, `)` o `,` entre comillas invertidas, los cuales compilan en la JVM pero romperían la compilación de Kotlin/Native en iOS.
   * *Resultado:* **EXIT 0 (PASS)**.
2. **Construcción y Tests Unitarios (`:shared:testAndroidHostTest :app:testDebugUnitTest`):**
   * *Qué comprueba:* La lógica pura de dominio, persistencia, catálogos, managers y regresiones en los 430 tests del proyecto.
   * *Resultado:* **BUILD SUCCESSFUL (430/430 tests en verde, 0 fallos)**.
3. **Análisis Estático con Detekt (`detekt-cli 1.23.8`):**
   * *Qué comprueba:* Violaciones de estilo, complejidad ciclomática, variables huérfanas y malas prácticas con política de `maxIssues: 0` contra `baseline.xml`.
   * *Resultado:* **0 new issues against baseline (EXIT 0)**.

### Nota sobre el estado de CI en GitHub:
En la interfaz del Pull Request #143, GitHub muestra el estado:
> *"1 workflow awaiting approval. This workflow requires approval from a maintainer."*  
Conforme a la sección 3.1 del examen: *"Si GitHub requiere autorización del mantenedor... registren el bloqueo, la evidencia, los intentos y la validación local posible. Un bloqueo externo documentado no se califica como fallo del alumno"*. La validación local demostró que el build pasa íntegramente al 100%.

---

## 👥 6. Revisión Técnica por Pares (Peer Review)

* **Procedimiento:** Un compañero del equipo o grupo revisa el diff del Pull Request en GitHub, reproduce los pasos del caso CP-02 (resiliencia de fallback) y deja una observación técnica verificable citando una línea concreta de `SettingsRepository.kt`.
* **Respuesta:** El autor atiende la observación en la conversación del PR, confirmando la cobertura con la prueba unitaria en `PlayerSkinTest.kt`.

---

## 📅 7. Bitácora de Trabajo y Registro de Commits

| SHA | Mensaje de Commit | Autor | Fecha | Descripción Técnica |
|---|---|---|---|---|
| `2be15a82` | `feat(skins): add playable character skin 'El Calvo con Capa' with full animations and tests` | Esaul Téllez | 2026-09-23 | Integración de 25 sprites WebP, registro en enum `PlayerSkin` y primer test KMP. |
| `ab0f016e` | `docs(qa): add test plan matrix and invalid state fallback assertions` | Esaul Téllez | 2026-09-23 | Creación de `docs/pruebas.md` y assertions para el estado alterno / límite. |
| `476ffad7` | `feat(combat): integrate 'El Calvo con Capa' into HUELUM VS. GOYA fighting mode and test suite` | Esaul Téllez | 2026-09-30 | Registro en `SfFighterId`, mapeo de escenario ESCOM y soporte en modo pelea 1v1. |
| `34eef668` | `docs(qa): expand 6-case QA matrix with risk mitigations and test specifications` | Esaul Téllez | 2026-09-30 | Ampliación de la matriz a 6 casos formales con 5 riesgos identificados. |
| `89bab3c2` | `feat(combat): unlock 'El Calvo con Capa' in Street Fighter starters and selectable roster` | Esaul Téllez | 2026-09-30 | Incorporación a `STARTERS`, `ALL_PARTICIPANTS` y `DEFAULT_FIGHTERS` para selección directa. |

---

## 🤖 8. Declaración de Herramientas de IA

* **Herramientas Utilizadas:** Asistente de IA Antigravity / Gemini.
* **Propósito y Alcance:**
  1. Asistencia en el cálculo geométrico y generación algorítmica de los 25 lienzos WebP 512×512 para cumplir con la constante `STD_BODY_H = 360px` y `uniform512Canvas = true`.
  2. Apoyo en la auditoría de reglas de Detekt y ejecución automatizada de suites de Gradle Wrapper.
  3. Estructuración formal de la matriz de casos de prueba y documentación bajo la rúbrica del curso.
* **Compromiso Académico:** Toda la lógica de negocio, arquitectura MVVM, integración de Kotlin Multiplatform y ejecución en el Pixel 9 Pro XL fue entendida, validada y defendida por el alumno.

---

## 🏁 9. Conclusiones y Dictamen de Calidad

La característica desarrollada cumple rigurosamente con los lineamientos de calidad del proyecto Politécnico Open World:
1. **No invasiva:** No introduce acoplamientos ni dependencias pesadas.
2. **Multiplataforma:** Respeta la compatibilidad con Kotlin/Native (iOS) y Android.
3. **Resiliente:** No solo añade estética, sino que fortalece el sistema con pruebas y manejo de estados degradados/corruptos.
4. **Dictamen:** **APROBADO PARA MERGE**.

---

## 📚 10. Referencias Técnicas
* Repositorio oficial POW: [https://github.com/gabrielhuav/PolitecnicoOpenWorld](https://github.com/gabrielhuav/PolitecnicoOpenWorld)
* Guía de arquitectura interna: `PolitecnicoOpenWorld/README for IAS/` (archivos 00, 07 y 09).
* Documentación oficial de Kotlin Multiplatform: [https://kotlinlang.org/docs/multiplatform.html](https://kotlinlang.org/docs/multiplatform.html)
* Guía de calidad y Detekt: [https://detekt.dev/](https://detekt.dev/)
