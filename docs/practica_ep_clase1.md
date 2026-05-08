# Práctica de Equivalence Partitioning — Clase 1

> **Curso:** Calidad de Software y Pruebas Automatizadas
> **Módulo:** 2 — Test Architect Agent
> **Clase:** 1 (Técnicas de diseño de pruebas caja negra)
> **Técnica que aplica:** Equivalence Partitioning
> **Tiempo estimado:** 30 minutos (20 min trabajo individual + 10 min puesta en común)
> **Modalidad:** trabajo individual seguido de auditoría grupal

---

## 1. Para el docente — contexto y propósito

Esta práctica se aplica **después de la presentación teórica de EP** y **antes de pasar a Boundary Value Analysis**. Su objetivo doble:

1. **Verificar comprensión:** confirmar que cada estudiante puede aplicar el método de los 4 pasos sobre un caso real.
2. **Entrenar el músculo de la auditoría:** la puesta en común al final entrena a los estudiantes a evaluar trabajo ajeno aplicando criterios técnicos. Esta habilidad es exactamente la que necesitarán cuando auditen las salidas del agente de IA en clases posteriores.

**Por qué este caso específico:** un campo de contraseña con 8 reglas violables independientemente fuerza el pensamiento de "una clase por regla". Si un estudiante agrupa "todas las inválidas" en una clase, su tabla tendrá 2 clases en vez de 9 — el error es evidente y corregible en clase.

---

## 2. Enunciado para entregar a los estudiantes

> ### Práctica EP — Validación del campo "contraseña" en Katary360
>
> **Contexto:** estás diseñando los casos de prueba para el campo "contraseña" del formulario de registro del sistema Katary360. El equipo de seguridad definió las siguientes reglas:
>
> - **Longitud:** entre 8 y 64 caracteres inclusive.
> - **Composición obligatoria:** debe contener al menos 1 letra mayúscula, 1 letra minúscula, 1 número y 1 carácter especial (de este conjunto: `@ # $ % & * ! ?`).
> - **Restricción adicional:** no puede contener espacios.
> - **Obligatoriedad:** el campo es requerido (no puede estar vacío).
>
> **Tu tarea:**
>
> Aplica la técnica de **Equivalence Partitioning** siguiendo el método de los 4 pasos visto en clase. Entrega una tabla con las siguientes columnas:
>
> | # | Tipo (Válida / Inválida) | Descripción de la clase | Ejemplo concreto de valor |
> |---|---|---|---|
>
> **Restricciones de la entrega:**
>
> - Cada clase debe corresponder a UNA regla violable independiente. No agrupes múltiples reglas en una sola clase.
> - El ejemplo concreto debe pertenecer realmente a la clase declarada (verificable por inspección).
> - Aplica los 4 pasos del método con disciplina: identifica el dominio → identifica clases válidas → identifica clases inválidas → diseña un caso por clase.
>
> **Tiempo:** 20 minutos.

---

## 3. Solución modelo (para el docente)

### 1 clase válida

| # | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| 1 | Válida | Cadena de 8-64 caracteres con al menos 1 mayúscula, 1 minúscula, 1 número, 1 carácter especial del conjunto permitido, sin espacios | `Pass123!` |

### 8 clases inválidas (una por regla violable)

| # | Tipo | Descripción | Ejemplo |
|---|---|---|---|
| 2 | Inválida | Longitud menor a 8 caracteres | `Pa1!` |
| 3 | Inválida | Longitud mayor a 64 caracteres | `Pass123!Pass123!Pass123!Pass123!Pass123!Pass123!Pass123!Pass123!Extra` |
| 4 | Inválida | Sin letra mayúscula | `pass123!` |
| 5 | Inválida | Sin letra minúscula | `PASS123!` |
| 6 | Inválida | Sin número | `Password!` |
| 7 | Inválida | Sin carácter especial del conjunto | `Password1` |
| 8 | Inválida | Contiene espacios | `Pass 123!` |
| 9 | Inválida | Campo vacío / nulo | `""` |

**Total: 9 clases.**

### Notas de la solución modelo

- Las clases 4, 5, 6 y 7 (composición) son **independientes entre sí** porque cada una viola una regla distinta. El sistema podría dar mensajes de error específicos: *"falta mayúscula"*, *"falta minúscula"*, etc. Por eso son clases separadas.
- La clase 9 (vacío) es **distinta de la clase 2 (menor a 8)**: aunque una cadena vacía técnicamente tiene longitud 0 que es menor a 8, el sistema generalmente da un mensaje específico para campo requerido (*"campo obligatorio"*) versus longitud (*"mínimo 8 caracteres"*). Por eso se separan.
- Se puede aceptar como correcta una solución con 8 clases si el estudiante fusiona vacío con longitud-menor-a-8 con justificación explícita. La capacidad de **justificar la decisión de fusionar o separar** es parte de la habilidad evaluada.

---

## 4. Rúbrica de evaluación (sobre 100 puntos)

| Criterio | Puntos | Detalle |
|---|---|---|
| **Identificación de clase válida** | 15 | Define UNA clase válida que contiene **todas** las reglas combinadas (no solo algunas). Ejemplo concreto que verdaderamente las cumple todas. |
| **Cobertura de clases inválidas** | 40 | Identifica al menos 6 de las 8 clases inválidas. Cada clase faltante a partir de 8: −5 puntos. Identificar las 8: 40 puntos. Identificar 7: 35. Identificar 6: 30. Identificar 5 o menos: 0-25 puntos. |
| **Separación correcta de reglas** | 20 | Cada clase corresponde a UNA regla violable. Penalización si agrupa reglas independientes en una sola clase ("composición incorrecta" como una sola clase = −10 puntos). |
| **Ejemplos válidos y representativos** | 15 | Cada ejemplo realmente pertenece a la clase declarada. Verificable por inspección. Cada ejemplo erróneo: −2 puntos. |
| **Lectura precisa del requerimiento** | 10 | No comete errores de interpretación (no convierte "entre 8 y 64" en "exactamente 8" o similar). Conserva las reglas tal como se enuncian. |

**Escalas:**
- **≥ 90 puntos:** excelente — el estudiante domina EP a nivel profesional.
- **70 a 89 puntos:** aprobado — comprende la técnica con detalles a refinar.
- **50 a 69 puntos:** reprobado — necesita repaso del prework + acompañamiento.
- **< 50 puntos:** no comprendió el método — repaso completo individual obligatorio antes de seguir.

---

## 5. Puesta en común (10 minutos finales)

**Dinámica recomendada:**

1. El docente pide un voluntario que comparta su solución (proyecta su tabla en pantalla).
2. La clase **audita en conjunto** la solución del voluntario aplicando estas preguntas:
   - ¿Identificó la clase válida correctamente?
   - ¿Cuántas clases inválidas listó? ¿Faltó alguna?
   - ¿Cada clase corresponde a UNA regla, o agrupó reglas?
   - ¿Los ejemplos pertenecen realmente a sus clases?
3. El docente cierra con la **solución modelo de 9 clases** y comenta los pitfalls que vio al recorrer las soluciones del grupo.

**Mensaje pedagógico clave para el cierre** (decir literalmente al cerrar la práctica):

> *"Acabamos de hacer dos cosas en una. La primera, aplicar Equivalence Partitioning a un caso real. La segunda, y más importante, **auditar la solución de un compañero** aplicando criterios técnicos, no opinión. Ese ejercicio de auditoría es exactamente lo que vamos a hacer en las próximas clases sobre las salidas del agente de IA. La diferencia entre un operador de prompts y un ingeniero de calidad asistido por IA es justo esta: la capacidad de mirar lo que el LLM produce y decir con criterio si está bien, qué le falta, y qué le sobra."*

---

## 6. Errores típicos que vas a ver en clase (anticípate)

Los errores más comunes que los estudiantes cometerán, listados en orden de frecuencia esperada:

1. **Olvidar la clase válida** (~30% de los estudiantes). Listan solo inválidas. Penalización en el criterio "Identificación de clase válida".
2. **Agrupar reglas de composición** (~25%). Ponen "no cumple complejidad" como una sola clase en vez de cuatro. Penalización en "Separación correcta".
3. **Olvidar el caso de espacios** (~20%). La regla "no puede contener espacios" se les pasa porque parece menor. Penalización en "Cobertura".
4. **Definir mal la clase válida** (~15%). La definen incompleta (ej: solo "8-64 caracteres" sin mencionar composición ni espacios). Penalización en "Identificación de clase válida".
5. **Ejemplos no verificables** (~10%). Dan ejemplos que dicen pertenecer a una clase pero realmente pertenecen a otra. Penalización en "Ejemplos representativos".

Tener este mapa de errores anticipado te permite proyectar autoridad pedagógica en clase: *"Voy a apostar que cinco de ustedes olvidaron la clase válida y cuatro agruparon las reglas de composición..."* — y cuando aciertes, se les graba la lección.

---

## 7. Conexión con el resto del curso

Esta práctica conecta con tres puntos posteriores del módulo:

- **Próxima técnica (BVA):** una vez que el estudiante tiene las 9 clases, BVA le dirá **qué valores específicos** elegir dentro de cada clase (en lugar del valor representativo, los valores en los bordes). EP define las clases; BVA refina los valores.
- **Decision Tables:** cuando hay múltiples campos con sus propias clases que se combinan en reglas de negocio, ya no basta EP por campo aislado — entran las tablas de decisión.
- **Auditoría del agente (Clase 4):** el estudiante usará exactamente esta misma técnica para auditar las salidas Gherkin que el agente de IA produzca. Si el agente le devuelve solo 3 escenarios para un campo con 9 clases identificables, el estudiante lo detectará y mejorará el prompt o el RAG.

---

*Documento vivo: actualizar después de la primera aplicación con observaciones reales del aula.*
