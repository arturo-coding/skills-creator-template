---
name: creador-de-habilidades
description: Crea o actualiza skills, basado en la detección de patrones repetitivos en las solicitudes del flujo de trabajo o solicitudes explícitas para crear o actualizar una skill.
---

# Creador y Gestor de Habilidades

Esta habilidad capacita al agente para asumir el rol de 'Arquitecto del Sistema'. Le permite expandir y refinar sus propias capacidades formalizando flujos de trabajo repetitivos o correcciones de errores en activos permanentes dentro del directorio .agent/skills/`

## Cuándo usar esta habilidad

- **Detección de Patrones:** Cuando el usuario repite una instrucción técnica más de una vez (ej: "Asegúrate de que los colores de los textos coincidan con Figma" repetido en varios componentes).
- **Corrección Persistente (Update):** Cuando una habilidad existente se ejecuta pero el usuario debe corregir el resultado (ej: "La skill de Figma falló en los márgenes"). El agente debe actualizar la skill para evitar este error futuro.
- **Solicitud Explícita:** Cuando el usuario pide "Crea una skill para..." o "Recuerda cómo hacer esto para la próxima vez".

## Objetivo

Capacitar al agente para actuar como 'Arquitecto del Sistema', permitiéndole formalizar instrucciones efímeras e **intervenciones correctivas recurrentes** en activos permanentes dentro de `.agent/skills/`.

El propósito principal es transformar el **feedback de iteración** (ej: correcciones de estilo, ajustes de parámetros repetidos) en restricciones codificadas, evitando que el agente cometa los mismos errores de implementación en futuras solicitudes.

## Instrucciones

### Fase 1: Validación de Frecuencia y Contexto (El Filtro)

Antes de generar cualquier archivo, debes confirmar que esta acción aporta valor a largo plazo analizando el historial de la conversación.

1.  **Escanear Historial:**
    - Revisa los últimos intercambios del chat. Busca repeticiones semánticas en las solicitudes del usuario o en tus propios errores corregidos.
2.  **Compuerta de Decisión (Decision Gate):**
    - **Caso A (Tarea Puntual):** Si la solicitud es única y no hay una orden explícita de crear una skill -> **DETENERSE**. No actives la lógica de creación. Ejecuta la tarea normalmente sin crear skills.
    - **Caso B (Patrón Detectado):** Si identificas que el usuario ha pedido esto (o corregido este detalle) 2 o más veces -> **PROCEDER**.
    - **Caso C (Orden Explícita):** Si el usuario dice "Crea una skill para esto" -> **PROCEDER**.

### Fase 2: Clasificación de la Acción (Crear o Actualizar)

Una vez validada la necesidad en la Fase 1:

1.  **Verificar Existencia:**
    - Busca en `.agent/skills/` si ya existe una carpeta relacionada con este tópico (ej: si el tema es "figma", busca `create-figma-component` o similares).
2.  **Ramificar:**
    - **Ruta de Creación:** Si NO existe una skill relacionada, inicia la estructura de una nueva skill desde cero.
    - **Ruta de Actualización:** Si YA existe una skill, tu objetivo es refinarla (agregar el caso de borde o la corrección que detectaste en el patrón).

---

### Fase 3: Ejecución y Persistencia

Una vez clasificada la acción en la Fase 2, procede con la manipulación del sistema de archivos según corresponda:

#### A. Ruta de Creación (Nueva Skill)

Si vas a crear una habilidad desde cero, actúa como un "Andamio" (Scaffolder):

1.  **Generar Directorio:**
    - Ejecuta el comando para crear la carpeta anidada obligatoria:
      `mkdir -p .agent/skills/<nombre-kebab-case>/`
      _(Nota: Nunca crees archivos sueltos en la raíz de `.agent/skills/`)_.

2.  **Redactar `SKILL.md`:**
    - Crea el archivo `SKILL.md` dentro de la nueva carpeta.
    - **Frontmatter (Crítico):** Escribe el encabezado YAML al inicio del archivo.
      - `name`: El nombre en kebab-case.
      - `description`: Usa los verbos de acción exactos detectados en el patrón (ej: "Genera", "Valida", "Transforma"). Esto es vital para que el router semántico active la skill en el futuro [1].
    - **Cuerpo:** Transcribe los pasos que has estado ejecutando manualmente en la sección `## Instrucciones`.

3.  **Persistencia:** Guarda el archivo.

#### B. Ruta de Actualización (Refinamiento de Skill)

Si vas a corregir una habilidad existente, actúa con precisión quirúrgica para no romper la lógica previa:

1.  **Lectura de Contexto:**
    - Lee el contenido actual de `.agent/skills/<nombre-existente>/SKILL.md`.

2.  **Inyección de Aprendizaje (Patching):**
    - Localiza la sección `## Restricciones` o `## Casos de Borde`.
    - Si no existe, créala al final del archivo.
    - **Agregar la Corrección:** Inserta una nueva viñeta que prohíba explícitamente el error cometido o imponga la preferencia del usuario.
      - _Ejemplo:_ Si el usuario corrigió "el margen debe ser 16px", escribe: `- REGLA CRÍTICA: Validar que todos los márgenes externos sean exactamente 16px. No usar valores por defecto.`

3.  **Reescritura:** Sobrescribe el archivo `SKILL.md` con el contenido actualizado, manteniendo intacto el Frontmatter original.

#### C. Validación Final (Self-Correction)

- Verifica que la estructura de carpetas sea válida (carpeta contenedora + archivo `SKILL.md`).
- Confirma que el `SKILL.md` tiene el formato YAML válido delimitado por `---` al inicio [2].

## Plantilla Estándar para SKILL.md

```
---

name: { { nombre_de_la_skill } }
description: { { frase_disparadora_con_verbos_activos } }

---

# {{Titulo Legible}}

## Objetivo

{{que_resuelve_esta_skill}}

## Instrucciones

1. {{paso_1}}
2. {{paso_2}}

## Restricciones y Casos de Borde (Aprendidos)

- {{restriccion_1_basada_en_feedback}}
- {{restriccion_2_basada_en_errores_pasados}}
  Ejemplos de Razonamiento (Few-Shot)
  Caso 1: Creación por Patrón Repetitivo
  Contexto: El usuario ha pedido 3 veces "crear un componente react desde este link de Figma" y siempre corrige los colores de las fuentes. Acción del Agente:

1. Detecta patrón: "Conversión Figma -> React con énfasis en Pixel Perfect".
2. Crea .agent/skills/create-figma-component/SKILL.md.
3. En instrucciones incluye: "Usar MCP para extraer metadatos".
4. En restricciones incluye: "CRÍTICO: Validar códigos hex de fuentes contra el diseño original. No usar colores aproximados".
   Caso 2: Actualización por Fallo
   Contexto: El usuario usa create-figma-component, pero se queja: "Los márgenes están mal, el diseño tiene 16px y pusiste 12px". Acción del Agente:
5. Lee .agent/skills/create-figma-component/SKILL.md.
6. Edita la sección Instrucciones.
7. Agrega: "Paso Extra: Realizar una pasada de verificación específica para espaciado (padding/margin). Extraer valores exactos del JSON de Figma, no inferir escalas estándar".
8. Guarda el archivo actualizado.
```

## Aprendizaje por Ejemplos (Few-Shot)

Para entender exactamente qué constituye un "patrón repetitivo" y cómo diferenciar entre crear o actualizar una skill, **debes leer y analizar** el archivo de ejemplos adjunto:

- **Referencia:** `examples/reasoning_traces.md`

Este archivo contiene:

1. **Caso de Creación:** Cómo detectar la intención cuando el usuario corrige estilos (ej. Figma) repetidamente.
2. **Caso de Actualización:** Cómo inyectar nuevas restricciones cuando una skill existente falla en un detalle específico (ej. márgenes).
