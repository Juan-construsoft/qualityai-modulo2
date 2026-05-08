# Práctica de Decision Tables — Clase 1

> **Curso:** Calidad de Software y Pruebas Automatizadas
> **Módulo:** 2 — Test Architect Agent
> **Clase:** 1 (Técnicas de diseño de pruebas caja negra)
> **Técnica que aplica:** Decision Tables (incluyendo reducción con "don't care")
> **Tiempo estimado:** 30 minutos (20 min trabajo individual + 10 min puesta en común)
> **Modalidad:** trabajo individual seguido de auditoría grupal
> **Pre-requisito:** los estudiantes ya completaron las prácticas de EP y BVA

---

## 1. Para el docente — contexto y propósito

Esta práctica se aplica **al final del bloque conceptual de Clase 1**, después de las prácticas de EP y BVA. Cierra la trilogía de técnicas de caja negra. Su valor pedagógico está en mostrar que **Decision Tables resuelve un tipo de problema que EP y BVA no abordan**: las reglas combinatorias entre múltiples condiciones de entrada.

**Por qué un caso fresco** (en vez de continuar con la contraseña):

El caso de la contraseña EP+BVA tiene reglas mayoritariamente booleanas que actúan independientemente — Decision Tables no aporta valor adicional ahí. Para que los estudiantes vean Decision Tables en acción real, necesitan un caso donde **el resultado del sistema dependa de la combinación de varias condiciones**, no de cada una aislada.

**Triple objetivo pedagógico:**

1. **Construir una tabla completa correctamente:** identificar condiciones/acciones, generar las 2^N combinaciones.
2. **Mapear acciones según reglas de negocio:** sin inventar resultados; si una combinación no está en el requerimiento, es hallazgo.
3. **Aplicar reducción iterativa con "don't care":** identificar todos los colapsos posibles, no solo el más obvio.

---

## 2. Enunciado para entregar a los estudiantes

> ### Práctica Decision Tables — Aprobación de horas extra en Katary
>
> **Contexto:** Katary tiene un sistema automático de aprobación de horas extra para sus colaboradores. Las reglas que el área de Recursos Humanos definió son:
>
> 1. Si el colaborador es **de planta** Y ha trabajado **8 horas o más** en el día Y el **jefe directo aprobó** la solicitud: **AUTORIZADAS Y PAGADAS AL 150%** (con recargo nocturno).
>
> 2. Si el colaborador es de planta Y ha trabajado **menos de 8 horas** en el día (sale temprano y regresa) Y el jefe directo aprobó: **AUTORIZADAS Y PAGADAS AL 100%** (sin recargo, son horas regulares de complemento).
>
> 3. Si el colaborador es de planta pero el jefe directo **NO aprobó**: **NO AUTORIZADAS** (sin importar las horas trabajadas).
>
> 4. Si el colaborador **NO es de planta** (es contratista o externo): **NO AUTORIZADAS** sin importar las demás condiciones (los contratistas se rigen por sus propios contratos).
>
> **Tu tarea, aplicando Decision Tables en sus 4 pasos:**
>
> **Paso 1 — Identifica condiciones y acciones del sistema.**
>
> **Paso 2 — Construye la tabla completa con todas las combinaciones binarias.** No te preocupes por las acciones todavía, solo enumera las combinaciones. Pista: 3 condiciones binarias = 8 combinaciones.
>
> **Paso 3 — Llena las acciones según las 4 reglas del negocio.** Si encuentras alguna combinación que el requerimiento NO especifica, márcala como "GAP DE REQUERIMIENTO" — es un hallazgo de calidad para devolver al analista.
>
> **Paso 4 — Reduce la tabla aplicando "don't care".** Aplica la reducción **iterativamente**: después de cada colapso, releé la tabla y busca nuevos colapsos hasta que no haya más posibles.
>
> **Cierre:** lista los **casos de prueba finales** derivados de la tabla reducida.
>
> **Tiempo:** 20 minutos.

---

## 3. Solución modelo (para el docente)

### Paso 1 — Condiciones y acciones

**Condiciones:**
- C1: ¿Es colaborador de planta? (Sí / No)
- C2: ¿Trabajó 8 horas o más? (Sí / No)
- C3: ¿Jefe directo aprobó? (Sí / No)

**Acciones posibles:**
- Pagadas al 150%
- Pagadas al 100%
- No autorizadas

### Paso 2 — Tabla completa (8 combinaciones)

| Regla | C1: De planta | C2: >=8 horas | C3: Jefe aprobó | Acción |
|---|---|---|---|---|
| R1 | Sí | Sí | Sí | ? |
| R2 | Sí | Sí | No | ? |
| R3 | Sí | No | Sí | ? |
| R4 | Sí | No | No | ? |
| R5 | No | Sí | Sí | ? |
| R6 | No | Sí | No | ? |
| R7 | No | No | Sí | ? |
| R8 | No | No | No | ? |

### Paso 3 — Acciones mapeadas

| Regla | C1 | C2 | C3 | Acción | Aplicó regla |
|---|---|---|---|---|---|
| R1 | Sí | Sí | Sí | **Pagadas 150%** | Regla 1 |
| R2 | Sí | Sí | No | **No autorizadas** | Regla 3 |
| R3 | Sí | No | Sí | **Pagadas 100%** | Regla 2 |
| R4 | Sí | No | No | **No autorizadas** | Regla 3 |
| R5 | No | Sí | Sí | **No autorizadas** | Regla 4 |
| R6 | No | Sí | No | **No autorizadas** | Regla 4 |
| R7 | No | No | Sí | **No autorizadas** | Regla 4 |
| R8 | No | No | No | **No autorizadas** | Regla 4 |

**Sin gaps de requerimiento** — las 4 reglas del negocio cubren las 8 combinaciones.

### Paso 4 — Reducción iterativa con "don't care"

**Primera iteración — colapso grande:** R5, R6, R7, R8 todos producen "No autorizadas" cuando C1=No, sin importar C2 ni C3. Se colapsan en una sola regla.

→ RR4: **No, -, -, No autorizadas**

**Segunda iteración — colapso menor:** R2 y R4 producen "No autorizadas" cuando C1=Sí y C3=No, sin importar C2. Se colapsan.

→ RR3: **Sí, -, No, No autorizadas**

**Tercera iteración:** R1 y R3 difieren en acción (150% vs 100%) — no colapsan.

**Tabla reducida final:**

| Regla | C1: De planta | C2: >=8 horas | C3: Jefe aprobó | Acción |
|---|---|---|---|---|
| RR1 | Sí | Sí | Sí | Pagadas 150% |
| RR2 | Sí | No | Sí | Pagadas 100% |
| RR3 | Sí | **-** | No | No autorizadas |
| RR4 | **No** | **-** | **-** | No autorizadas |

**De 8 reglas pasamos a 4.** Reducción del 50% con dos colapsos: uno grande (RR4) y uno menor (RR3).

### Casos de prueba finales

| TC | C1: De planta | C2: >=8 horas | C3: Jefe aprobó | Resultado esperado |
|---|---|---|---|---|
| TC1 | Sí (planta) | Sí (10 horas) | Sí (aprobó) | Pagadas 150% |
| TC2 | Sí (planta) | No (5 horas) | Sí (aprobó) | Pagadas 100% |
| TC3 | Sí (planta) | Cualquiera (escoger un valor para ejecución) | No (rechazó) | No autorizadas |
| TC4 | No (contratista) | Cualquiera | Cualquiera | No autorizadas |

**4 casos de prueba cubren las 8 combinaciones lógicas posibles.**

---

## 4. Rúbrica de evaluación (sobre 100 puntos)

| Criterio | Puntos | Detalle |
|---|---|---|
| **Identificación correcta de condiciones y acciones** | 15 | 3 condiciones binarias y 3 acciones bien identificadas. Confundir condiciones con acciones: −5 puntos. |
| **Tabla completa con las 8 combinaciones** | 20 | Las 8 combinaciones presentes y únicas. Cada combinación faltante o duplicada: −3 puntos. |
| **Mapeo correcto de acciones** | 25 | Las 8 acciones correctamente derivadas de las 4 reglas. Cada acción mal mapeada: −4 puntos. |
| **Reducción iterativa completa** | 30 | Identifica los DOS colapsos (RR4 grande, RR3 menor). Solo el grande: −15 puntos. Olvidar reducir: −25 puntos. Reducir mal (perder cobertura): −20 puntos. |
| **Casos de prueba derivados** | 10 | Los 4 casos finales correctamente listados desde la tabla reducida con resultado esperado. |

**Escalas:**
- **≥ 90 puntos:** excelente — domina Decision Tables incluyendo reducción iterativa.
- **70 a 89 puntos:** aprobado — comprende la técnica con detalles a refinar.
- **50 a 69 puntos:** reprobado — necesita repaso, especialmente la reducción.
- **< 50 puntos:** confunde la técnica, requiere repaso individual.

---

## 5. Puesta en común (10 minutos finales)

**Dinámica recomendada:**

1. El docente proyecta la solución de un voluntario.
2. Auditoría grupal con tres preguntas guía:
   - *"¿Identificó correctamente las 3 condiciones y las 3 acciones?"*
   - *"¿Mapeó las 8 acciones de manera consistente con las reglas del negocio?"*
   - *"¿Aplicó reducción iterativa o solo encontró el colapso grande y se quedó ahí?"*
3. El docente cierra con la solución modelo y enfatiza el mensaje clave de la **reducción iterativa**.

**Mensaje pedagógico clave para el cierre** (decir literalmente):

> *"Decision Tables es la técnica que más detecta gaps de requerimientos en proyectos reales. Cuando la construyes y una celda queda en blanco porque el negocio no especificó qué hacer, NO inventes el resultado — es un hallazgo de calidad que devuelves al analista. Esa devolución es la conexión más importante con el Módulo 1: el agente del Módulo 2 va a hacer este mismo razonamiento, y cuando detecte gaps, los va a marcar para que el analista humano los resuelva. Esa es la cadena de calidad de QualityAI."*

---

## 6. Errores típicos que vas a ver en clase (anticípate)

1. **Reducción incompleta** (~35%). Identifican el colapso grande (todos los "No, -, -") pero olvidan el colapso menor (R2+R4 con C2 indistinto). Es exactamente el error que un buen docente anticipa: les pides la "reducción iterativa" pero algunos paran al primer colapso.

2. **Mapeo incorrecto de acciones** (~25%). Confunden las reglas, especialmente cuando hay condiciones que parecen contradecirse. La regla 3 ("jefe NO aprobó: no autorizadas") y la regla 4 ("no es de planta: no autorizadas") tienen el mismo resultado pero por causas distintas — algunos estudiantes intentan separarlas y se confunden.

3. **Inventar resultados sin gaps** (~15%). Cuando el requerimiento es claro, no hay gaps. Pero si hubiera gaps, algunos estudiantes "rellenan" con su intuición en lugar de marcar el gap. Esa tentación se modela en este caso (que NO tiene gaps reales) pero hay que explicárselo: en este caso no hay gaps, pero en otros sí los habrá.

4. **Casos de prueba sin valores concretos** (~15%). Listan los TC con `-` sin escoger valores específicos para ejecución. Recordatorio: el `-` es para diseño abstracto; al ejecutar se escoge cualquier valor concreto.

5. **Confundir Decision Tables con EP/BVA** (~10%). Algunos intentan aplicar EP a las condiciones (no es incorrecto pero se sale del foco). Decision Tables es sobre combinaciones; EP es sobre valores dentro de un campo.

---

## 7. Conexión con el resto del curso

- **Cierre conceptual de Clase 1:** los estudiantes ya aplicaron las 3 técnicas de caja negra al final de Clase 1 (EP en contraseña, BVA en contraseña, Decision Tables en horas extra). Tienen una imagen mental completa de cuándo aplicar cada una. Mensaje de cierre: *"EP para campos, BVA para bordes de campos, Decision Tables para combinaciones de campos. Las tres juntas son la caja de herramientas básica de testing funcional."*

- **Módulo 2 — el agente:** los prompts del Agente 2 forzarán Decision Tables explícitamente cuando los AC del Contract A tengan reglas combinatorias. Sin Decision Tables explícito, el LLM tiende a generar escenarios solo para los caminos "principales" y olvida combinaciones — exactamente el gap que esta técnica cierra.

- **Conexión con el Módulo 1:** los gaps detectados por Decision Tables (combinaciones sin acción especificada en el requerimiento) se devuelven al analista del Módulo 1. Esto cierra el ciclo: el Módulo 2 alimenta de retorno al Módulo 1 con hallazgos de calidad. La trazabilidad entre módulos es lo que hace al pipeline una herramienta CMMI-compatible.

- **Auditoría de IA en Clase 4:** cuando los estudiantes auditen las salidas del agente, verán si éste aplicó Decision Tables o solo cubrió el camino feliz. La habilidad de detectar combinaciones faltantes en el output del LLM se entrena precisamente con prácticas como esta.

---

*Documento vivo: actualizar después de la primera aplicación con observaciones reales del aula.*
