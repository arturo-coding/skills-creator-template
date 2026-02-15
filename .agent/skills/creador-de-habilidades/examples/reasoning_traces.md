# Ejemplos de Razonamiento del Agente

Utiliza estos trazas de pensamiento como guía para tomar decisiones.

## Caso 1: Creación por Patrón Repetitivo (Detection -> Creation)

**Contexto del Usuario:**
El usuario ha pedido 3 veces "crear un componente react desde este link de Figma" y en las últimas 2 iteraciones ha tenido que corregir manualmente los colores de las fuentes.

**Razonamiento del Agente:**

1.  _Observación:_ Detecto una repetición en la intención "Figma a React" y una corrección recurrente sobre "Pixel Perfect / Colores".
2.  _Decisión:_ No existe una skill para esto. Debo crearla para automatizar la corrección.
3.  _Acción (Filesystem):_
    - Crear directorio: `.agent/skills/create-figma-component/`
    - Crear `SKILL.md` con:
      - Descripción: "Genera componentes React desde Figma asegurando coincidencia Pixel Perfect de colores."
      - Restricción Crítica: "Validar códigos hex de fuentes contra el diseño original. No usar colores aproximados."

---

## Caso 2: Actualización por Fallo (Feedback -> Update)

**Contexto del Usuario:**
El usuario invoca la skill `create-figma-component`, pero comenta: "Los márgenes están mal, el diseño tiene 16px y pusiste 12px".

**Razonamiento del Agente:**

1.  _Observación:_ La skill existe y se usó, pero el resultado falló en una regla de negocio específica (márgenes).
2.  _Decisión:_ Debo refinar la skill existente para evitar este error en el futuro.
3.  _Acción (Filesystem):_
    - Leer `.agent/skills/create-figma-component/SKILL.md`.
    - Localizar sección `## Restricciones`.
    - Añadir: "- REGLA: Realizar una pasada de verificación específica para espaciado (padding/margin). Extraer valores exactos del JSON de Figma."
    - Guardar archivo actualizado.
