# agentic-ai-security

[🇬🇧 Read in English](README.md)

Una skill de Claude que aplica el **marco de seguridad para IA agéntica de ASD/CISA/NSA/NCSC** de forma inline durante el desarrollo de agentes — detectando riesgos de privilegio, comportamiento y estructura en el momento en que se escribe el código, no después.

> Basada en la guía conjunta *"Careful adoption of agentic AI services"* (2026), co-autorada por el Australian Signals Directorate, CISA, NSA, NCSC-UK, NCSC-NZ y el Centro Canadiense de Ciberseguridad.

---

## Qué hace

Cuando estás construyendo un agente basado en LLM en Claude Code, esta skill se activa automáticamente y aplica cinco categorías de riesgo de seguridad a todo lo que se está diseñando o codificando:

| Categoría | Qué detecta |
|---|---|
| **Privilegio** | Scope creep, patrón de confused deputy, suplantación de identidad |
| **Diseño/Config** | Permisos estáticos, segmentación deficiente, decisiones de auth obsoletas |
| **Comportamiento** | Desalineación de objetivos, specification gaming, encadenamiento inesperado de herramientas |
| **Estructural** | Fallos en cascada, inyección via tools, agentes comprometidos, riesgos de componentes externos |
| **Accountability** | Cadenas de decisión opacas, propagación de alucinaciones, brechas en logs |

Cubre el ciclo de vida completo del agente: **Diseño → Desarrollo → Despliegue → Operación**.

---

## Cuándo se activa

La skill se activa automáticamente cuando:

- Diseñás o codificás agentes de IA (basados en LLM)
- Definís herramientas, listas de permitidos o descripciones de tools
- Configurás permisos, scopes o credenciales de agentes
- Construís orquestación multi-agente o cadenas de delegación
- Implementás memoria, pipelines RAG o fuentes de datos externas
- Escribís system prompts para agentes
- Revisás arquitecturas de agentes existentes en busca de problemas de seguridad
- Preguntás sobre: gestión de privilegios · defensa contra prompt injection · human-in-the-loop · identidad de agentes · monitoreo · confused deputy · fallos en cascada

También podés invocarla directamente con `/agentic-ai-security`.

---

## Instalación

### Claude Code

```bash
# Desde el repositorio oficial de skills (una vez publicada)
/install agentic-ai-security

# O manualmente — cloná y copiá al proyecto
git clone https://github.com/YOUR_USERNAME/agentic-ai-security
cp -r agentic-ai-security/.claude/skills/agentic-ai-security TU_PROYECTO/.claude/skills/
```

### Instalación manual (cualquier proyecto)

1. Copiá la carpeta `agentic-ai-security/` dentro de `.claude/skills/` de tu proyecto:

```
tu-proyecto/
└── .claude/
    └── skills/
        └── agentic-ai-security/
            ├── SKILL.md
            └── references/
                ├── risk-taxonomy.md
                └── best-practices-checklist.md
```

2. Claude Code la descubrirá y cargará automáticamente en la próxima sesión.

---

## Estructura de la skill

```
agentic-ai-security/
├── SKILL.md                              # Skill principal — se carga al activarse
└── references/
    ├── risk-taxonomy.md                  # Cinco categorías de riesgo con ejemplos de escenarios
    └── best-practices-checklist.md       # Checklists completos por fase
```

La skill usa **divulgación progresiva**: solo se carga `SKILL.md` en el contexto cuando se activa. Los archivos de referencia se leen bajo demanda, manteniendo el uso de la ventana de contexto al mínimo.

---

## Ejemplo de interacción

**Vos:** Diseñá un sistema multi-agente donde un orquestador delega tareas de investigación a sub-agentes que consultan APIs externas.

**Claude (con la skill activa):**
> Antes de generar el código de orquestación, voy a señalar algunas consideraciones de seguridad del marco de IA agéntica:
>
> **Privilegio**: El orquestador no debería pasar su conjunto completo de credenciales a los sub-agentes. Cada sub-agente necesita su propia identidad con scope limitado — aunque solo viva durante una tarea.
>
> **Estructural**: Si un sub-agente consulta una API externa, esa respuesta fluye de vuelta al contexto del LLM. Esto es una superficie de prompt injection — etiquetá cada respuesta externa con su nivel de confianza antes de que llegue al paso de razonamiento del orquestador.
>
> **Confused deputy**: Si el orquestador actúa sobre los outputs de un sub-agente sin revalidar los permisos del caller original, un usuario de bajo privilegio puede encadenar llamadas para escalar privilegios. Agregá un paso de verificación de principal antes de cualquier acción consecuente.
>
> Acá hay un scaffold con esos controles incorporados: [...]

---

## Marco fuente

Esta skill codifica el framework de seguridad de:

**"Careful adoption of agentic AI services"** (2026)
Co-autorada por: ASD's ACSC (Australia) · CISA (EE.UU.) · NSA (EE.UU.) · NCSC-UK · NCSC-NZ · Centro Canadiense de Ciberseguridad

El framework organiza los riesgos de seguridad de IA agéntica en cinco categorías y provee mejores prácticas a lo largo del ciclo de vida completo del sistema.

Documento completo: [cyber.gov.au](https://www.cyber.gov.au)

---

## Qué cubre y qué no

**Cubre:**
- Sistemas agénticos basados en LLM (agente único y multi-agente)
- Uso de herramientas, RAG, pipelines de memoria
- Gestión de privilegios e identidad
- Riesgos de comportamiento incluyendo prompt injection y specification gaming
- Riesgos estructurales incluyendo componentes externos y fallos en cascada
- Patrones de monitoreo y accountability

**No cubre:**
- Uso de LLM no agéntico (chatbots, APIs de completion sin uso de herramientas)
- Seguridad de infraestructura (red, cloud, hardening de OS)
- Seguridad en entrenamiento y fine-tuning de modelos
- Cumplimiento normativo (GDPR, SOC 2, etc.)

---

## Contribuir

Las contribuciones son bienvenidas. Abrí un issue antes de enviar un PR para cambios significativos. Ver [CONTRIBUTING.md](CONTRIBUTING.md) para más detalles.

---

## Licencia

MIT — ver [LICENSE](LICENSE).

La guía de seguridad fuente es © Commonwealth of Australia 2026, publicada bajo Creative Commons Attribution 4.0 International. Esta skill es una obra derivada.
