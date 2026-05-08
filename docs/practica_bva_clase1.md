# Práctica de Boundary Value Analysis — Clase 1

> **Curso:** Calidad de Software y Pruebas Automatizadas
> **Módulo:** 2 — Test Architect Agent
> **Clase:** 1 (Técnicas de diseño de pruebas caja negra)
> **Técnica que aplica:** Boundary Value Analysis (BVA-2)
> **Tiempo estimado:** 25 minutos (15 min trabajo individual + 10 min puesta en común)
> **Modalidad:** trabajo individual seguido de auditoría grupal
> **Pre-requisito:** los estudiantes ya hicieron la práctica de Equivalence Partitioning sobre el mismo caso (validación de contraseña Katary360)

---

## 1. Para el docente — contexto y propósito

Esta práctica se aplica **inmediatamente después de la presentación teórica de BVA**, y deliberadamente reutiliza el caso de la práctica anterior de EP (campo "contraseña" de Katary360). Su valor pedagógico está en hacer **explícita la sinergia EP+BVA** en un solo caso continuo.

**Triple objetivo pedagógico:**

1. **Aplicar BVA-2:** que cada estudiante identifique correctamente los 4 valores frontera para la dimensión de longitud.
2. **Reconocer la sinergia con EP:** que el estudiante vea cómo los valores BVA "regalan" cobertura de varias clases inválidas de EP.
3. **Identificar clases huérfanas:** que el estudiante distinga qué clases de EP siguen requiriendo casos separados porque BVA no las cubre (composición, espacios, vacío).

Este último punto es **el más rico pedagógicamente**: enseña que BVA y EP son técnicas complementarias, no alternativas, y que ningún equipo serio aplica una sin la otra.

**Por qué reutilizar el caso de EP:** continuidad. El estudiante ya dominó el dominio del problema, ya tiene su tabla de 9 clases. Ahora aplica una segunda técnica encima. Pedagógicamente esto modela cómo se trabaja en la realidad: las técnicas se acumulan sobre el mismo problema, no se aplican en paralelo a problemas distintos.

---

## 2. Enunciado para entregar a los estudiantes

> ### Práctica BVA — Refinamiento de la validación del campo "contraseña"
>
> **Contexto:** estás en el mismo caso que en la práctica anterior de EP (campo "contraseña" del registro de Katary360). Recuerda las reglas:
>
> - **Longitud:** entre 8 y 64 caracteres inclusive.
> - **Composición:** al menos 1 mayúscula, 1 minúscula, 1 número, 1 carácter especial (`@ # $ % & * ! ?`).
> - **Restricción:** no puede contener espacios.
> - **Obligatorio.**
>
> En la práctica anterior identificaste 9 clases de equivalencia. Ahora vas a **refinar tu suite de pruebas aplicando BVA**.
>
> **Tu tarea, en tres partes:**
>
> **Parte 1 — Identificar dimensiones donde BVA aplica.**
> El campo "contraseña" tiene varias reglas. Identifica cuáles tienen "bordes" (es decir, son medibles en una escala con mínimo y máximo) y cuáles son reglas booleanas o categóricas (sin bordes naturales).
>
> **Parte 2 — Aplicar BVA-2 a la(s) dimensión(es) ordenable(s).**
> Lista los valores específicos a probar siguiendo BVA-2. Para cada valor, da un **ejemplo concreto de contraseña** que cumpla todas las demás reglas (composición, sin espacios) y solo varíe en la dimensión que estás probando.
>
> **Parte 3 — Mapear cobertura.**
> Construye una tabla con dos columnas:
> - **Clases de EP cubiertas automáticamente por tus valores BVA** (de las 9 clases originales).
> - **Clases de EP que siguen requiriendo casos separados** (las que BVA no toca).
>
> **Tiempo:** 15 minutos.

---

## 3. Solución modelo (para el docente)

### Parte 1 — Análisis de dimensiones

| Regla | ¿Tiene bordes? | ¿BVA aplica? |
|---|---|---|
| Longitud entre 8 y 64 caracteres | Sí (escala numérica con min/max) | **Sí** |
| Al menos 1 mayúscula | No (booleana: tiene o no tiene) | No |
| Al menos 1 minúscula | No (booleana) | No |
| Al menos 1 número | No (booleana) | No |
| Al menos 1 carácter especial | No (booleana) | No |
| Sin espacios | No (booleana) | No |
| Obligatorio | No (booleana: existe o no) | No |

**Conclusión:** BVA aplica solo a la dimensión de longitud. Las demás reglas son booleanas y no tienen "bordes" — la prueba es directa: cumple o no cumple.

**Mensaje pedagógico:** *"BVA aplica a dimensiones ordenables (rangos numéricos, longitudes, tamaños, fechas). NO aplica a reglas booleanas o categóricas. Por eso aquí solo va sobre longitud."*

### Parte 2 — Aplicación de BVA-2 a longitud

Bordes del rango [8, 64]:
- Borde inferior: 8 (incluido en válido), 7 (justo afuera, inválido).
- Borde superior: 64 (incluido en válido), 65 (justo afuera, inválido).

| # | Longitud | Ejemplo concreto (cumpliendo demás reglas) | Validez |
|---|---|---|---|
| 1 | 7 chars | `Pa1234!` | Inválido (longitud menor a 8) |
| 2 | 8 chars | `Pa12345!` | Válido (borde inferior) |
| 3 | 64 chars | `Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!` (16 repeticiones del patrón "Pa1!") | Válido (borde superior) |
| 4 | 65 chars | El anterior + un carácter más, ej: `Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!Pa1!X` | Inválido (longitud mayor a 64) |

**Total: 4 valores BVA-2.**

**Punto pedagógico:** cada ejemplo BVA debe cumplir **todas las demás reglas** para que el resultado del test sea atribuible específicamente al borde de longitud. Si pones una contraseña de 7 caracteres sin mayúscula, no sabes si el sistema la rechazó por longitud o por composición. Aislar variables es ingeniería pura.

### Parte 3 — Mapeo de cobertura

**Clases de EP cubiertas automáticamente por los 4 valores BVA:**

| Valor BVA | Clase de EP cubierta |
|---|---|
| 8 chars (`Pa12345!`) | Clase válida (parcialmente, en su borde inferior) |
| 64 chars | Clase válida (parcialmente, en su borde superior) |
| 7 chars (`Pa1234!`) | Inválida: longitud menor a 8 |
| 65 chars | Inválida: longitud mayor a 64 |

**Clases de EP que siguen requiriendo casos separados (BVA no las cubre):**

| Clase de EP | Por qué BVA no la cubre | Ejemplo de caso adicional |
|---|---|---|
| Sin letra mayúscula | Es regla booleana, no tiene borde | `pass1234!` (8+ chars, sin mayúscula) |
| Sin letra minúscula | Booleana | `PASS1234!` |
| Sin número | Booleana | `Password!` |
| Sin carácter especial | Booleana | `Password1` |
| Contiene espacios | Booleana | `Pass 1234!` |
| Campo vacío (longitud 0) | BVA prueba con 7-65 chars **con contenido**, no con vacío | `""` |

**Total de casos finales del campo "contraseña" con EP+BVA aplicados:**

- 4 valores BVA (cubren la clase válida y dos inválidas de longitud)
- 6 valores EP adicionales (cubren las clases inválidas que BVA no toca)
- **Total: 10 casos de prueba**

Compárese con los 9 casos que se hubieran hecho con EP solo (1 representante por clase). La diferencia: con EP+BVA invertimos un caso adicional pero **escogimos los valores precisos donde los bugs son más probables**, en vez de un valor aleatorio del medio.

---

## 4. Rúbrica de evaluación (sobre 100 puntos)

| Criterio | Puntos | Detalle |
|---|---|---|
| **Identificación correcta de dimensiones BVA** | 20 | Distingue que solo longitud tiene bordes; identifica que las demás reglas son booleanas y BVA no aplica. Confundir esto: −10 puntos. |
| **Valores BVA-2 correctamente identificados** | 25 | Lista los 4 valores (7, 8, 64, 65 caracteres). Cada valor faltante o erróneo: −7 puntos. |
| **Ejemplos concretos válidos** | 15 | Cada ejemplo cumple las demás reglas (composición, sin espacios) para aislar la variable de longitud. Ejemplos que rompen otras reglas: −5 puntos cada uno. |
| **Cobertura BVA→EP correctamente mapeada** | 20 | Identifica las 4 clases de EP cubiertas por los valores BVA (1 válida + 2 inválidas de rango = 3 clases distintas, más la válida en sus dos bordes). |
| **Identificación de clases huérfanas** | 20 | Lista las 6 clases que requieren casos separados (4 de composición + espacios + vacío). Cada clase faltante: −3 puntos. |

**Escalas:**
- **≥ 90 puntos:** excelente — domina la sinergia EP+BVA.
- **70 a 89 puntos:** aprobado — comprende la técnica con detalles a refinar.
- **50 a 69 puntos:** reprobado — necesita repaso.
- **< 50 puntos:** confunde técnicas, requiere repaso individual.

---

## 5. Puesta en común (10 minutos finales)

**Dinámica recomendada:**

1. El docente proyecta la solución de un voluntario.
2. Auditoría grupal con dos preguntas guía:
   - *"¿Identificó correctamente que BVA solo aplica a longitud, o trató de aplicarla a las reglas booleanas?"*
   - *"En el mapeo de cobertura, ¿quedó clara cuál fue la economía de casos por la sinergia EP+BVA?"*
3. El docente cierra con la solución modelo y enfatiza el mensaje clave.

**Mensaje pedagógico clave para el cierre** (decir literalmente):

> *"En este ejercicio invertimos 1 caso adicional respecto a EP solo (10 vs 9), pero ese caso adicional vale oro porque escogió los valores donde los bugs ocurren empíricamente. Esa es la diferencia entre testing que pasa y testing que encuentra bugs. La IA del Módulo 2 va a hacer este mismo razonamiento porque se lo vamos a forzar en el prompt — sin nuestra intervención, generaría un valor cualquiera del medio. Como ingenieros de calidad, somos quienes le damos la disciplina al modelo."*

---

## 6. Errores típicos que vas a ver en clase (anticípate)

1. **Aplicar BVA a reglas booleanas** (~30%). Algún estudiante intentará "aplicar BVA" a "al menos 1 mayúscula" tratando de pensar en bordes. Penalización en "Identificación correcta de dimensiones BVA".
2. **Olvidar que el ejemplo BVA debe cumplir las demás reglas** (~25%). Ejemplos como `1234567` (7 chars, sin mayúscula ni especial) — penaliza porque mezcla variables.
3. **Listar negativos o "valores fuera del rango" como clases no cubiertas** (~20%). Es el error que JuanCa cometió en la verificación oral: confundir "valores del rango cubierto" con "todos los valores que se podrían probar".
4. **Olvidar que vacío es caso aparte** (~15%). Asumen que vacío está implícito en "longitud menor a 8" y no lo separan.
5. **Confundir BVA-2 con BVA-3** (~10%). Estudiantes que leyeron extra-curricularmente y aplican 6 valores en vez de 4 sin justificar.

---

## 7. Conexión con el resto del curso

- **Cierre de Clase 1 — técnicas de caja negra:** después de esta práctica los estudiantes habrán aplicado EP y BVA al mismo caso. La siguiente técnica (Decision Tables) la verán en el último bloque de Clase 1. Esa secuencia construye una imagen mental coherente: las técnicas de caja negra son herramientas complementarias que se eligen según la naturaleza del problema (rangos vs combinaciones de reglas).
- **Módulo 2 — el agente:** los prompts del Agente 2 forzarán explícitamente la aplicación de BVA. Los estudiantes auditarán los escenarios Gherkin que el agente produzca: si para un AC con un rango el agente devuelve solo un caso del medio, sabrán que el prompt necesita refinarse.
- **Módulo 4 — Functional Tester:** los escenarios BVA generados en Módulo 2 serán ejecutados como tests Playwright. Los estudiantes verán si los casos que el agente diseñó realmente atraparon bugs reales en producción.

---

*Documento vivo: actualizar después de la primera aplicación con observaciones del aula.*
