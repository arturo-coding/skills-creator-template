oda Skill debe vivir dentro de su propia carpeta dedicada. Una estructura completa ("Level 5" o de composición) se ve así:
nombre-de-tu-skill/ <-- Carpeta raíz de la Skill
│
├── SKILL.md <-- OBLIGATORIO: El cerebro (definición e instrucciones)
│
├── scripts/ <-- OPCIONAL: Lógica ejecutable
│ ├── run.py
│ └── util.sh
│
├── resources/ (o references/) <-- OPCIONAL: Conocimiento estático
│ ├── plantilla.txt
│ └── documentacion_api.md
│
├── examples/ <-- OPCIONAL: Aprendizaje "Few-Shot"
│ ├── input.json
│ └── output.py
│
└── assets/ <-- OPCIONAL: Imágenes o logos

---

2. Desglose de los Componentes
   A. El Archivo SKILL.md (El Cerebro)
   Es el único archivo estrictamente obligatorio. Actúa como la interfaz entre el humano y el agente. Se divide en dos partes:
1. YAML Frontmatter (Encabezado de Metadatos): Esta sección es crítica porque el agente la indexa para el "enrutamiento semántico". Define cuándo activar la habilidad.
   ◦ name: Identificador único (minúsculas y guiones).
   ◦ description: La "frase disparadora". Debe contener verbos precisos para que el agente sepa que esta skill coincide con la intención del usuario.
1. Cuerpo Markdown (Instrucciones): Contiene la lógica en lenguaje natural que se inyecta en el contexto del agente una vez activada la skill. Debe incluir:
   ◦ Goal (Objetivo): Qué se va a lograr.
   ◦ Instructions (Instrucciones): Pasos lógicos a seguir.
   ◦ Constraints (Restricciones): Qué no hacer (ej. "No ejecutar DELETE").
   B. La Carpeta scripts/ (Ejecución)
   Permite al agente realizar acciones reales en el sistema que son difíciles para un LLM (como cálculos complejos o ejecución de binarios).
   • Contenido: Scripts en Python, Bash, Node.js, etc.
   • Uso: El SKILL.md debe instruir al agente para ejecutar estos scripts.
   ◦ Ejemplo: Una skill database-schema-validator usa un script validate_schema.py para verificar matemáticamente si una tabla SQL tiene una clave primaria, en lugar de que la IA "adivine".
   C. La Carpeta resources/ (Conocimiento Estático)
   Se usa para descargar texto pesado que no debería estar consumiendo memoria constantemente en el prompt principal.
   • Contenido: Plantillas de texto, encabezados de licencias, documentación de APIs.
   • Uso: El agente lee estos archivos solo cuando es necesario.
   ◦ Ejemplo: Una skill license-header-adder lee un archivo HEADER_TEMPLATE.txt de esta carpeta para pegarlo en nuevos archivos de código.
   D. La Carpeta examples/ (Aprendizaje por Ejemplo)
   Se utiliza para enseñar al agente mediante patrones de "Input -> Output" (Few-Shot Learning), lo cual es más efectivo que dar instrucciones abstractas para transformaciones de datos.
   • Contenido: Pares de archivos que muestran el "antes" y el "después".
   ◦ Ejemplo: Para enseñar a convertir JSON a Pydantic, colocas un input_data.json y su correspondiente output_model.py perfecto en esta carpeta.
1. Ubicación de la Estructura (Scope)
   Dependiendo de dónde crees esta estructura de carpetas, la Skill tendrá un alcance diferente:
   • Alcance Global (Usuario): ~/.gemini/antigravity/skills/
   ◦ Uso: Herramientas personales disponibles en todos tus proyectos (ej. formateadores, linters personales).
   • Alcance del Workspace (Proyecto): <raíz-del-proyecto>/.agent/skills/
   ◦ Uso: Habilidades específicas del repositorio que viajan con el código (ej. scripts de despliegue específicos de esa app).
