Ingeniería de Inteligencia Artificial – EP2
Agente Inteligente RAG con Memoria, Planificación y Toma de Decisiones – Banco Andino
🧩 A. Diseño e Implementación del Agente (IE1, IE2)

En esta segunda entrega (EP2) desarrollé un agente funcional completo, mejorando la versión inicial del proyecto (EP1).
El nuevo sistema integra herramientas de consulta, escritura y razonamiento dentro de un flujo automatizado que replica un entorno organizacional.

El agente fue construido con FastAPI, LangChain, FAISS y HuggingFace Embeddings, integrados de forma modular para asegurar escalabilidad y compatibilidad técnica.
Su propósito es asistir al Banco Andino respondiendo consultas comunes basadas en la normativa CMF y los documentos internos del banco.

Las tres herramientas principales son:

 search_docs → Recupera información semántica desde el índice FAISS.

 reason_policy → Evalúa si la pregunta puede responderse o si debe derivarse.

 write_note → Registra notas y evidencias de interacción en formato JSONL.

Cada herramienta funciona de forma autónoma, pero orquestada dentro del flujo del agente.

💾 B. Configuración de Memoria y Recuperación de Contexto (IE3, IE4)

Para esta etapa configuré dos tipos de memoria:

🧠 Memoria corta (ShortMemory): guarda las últimas 10 interacciones entre el usuario y el agente, permitiendo mantener coherencia en conversaciones prolongadas.

💽 Memoria larga (FAISS): actúa como una base vectorial donde se almacenan embeddings de documentos internos, que permiten recuperación semántica del contexto.

Gracias a esta estructura, el agente puede recordar temas tratados previamente y ofrecer respuestas consistentes, incluso en flujos prolongados.

🧭 C. Planificación y Toma de Decisiones (IE5, IE6)

Agregué una clase llamada TaskPlanner, que organiza las etapas del flujo de ejecución de manera ordenada y priorizada.
La secuencia del plan es la siguiente:

plan = ["seguridad", "recuperar_ctx", "razonar", "responder", "registrar"]


Esto permite que el agente planifique sus acciones antes de responder.
Por ejemplo:

Primero revisa si la consulta contiene datos sensibles.

Luego recupera el contexto desde FAISS.

Aplica razonamiento con reason_policy.

Genera la respuesta solo si las condiciones son seguras.

Finalmente, registra la evidencia con write_note.

Este proceso demuestra toma de decisiones adaptativa, ya que el agente puede modificar su comportamiento según el tipo de consulta o el contexto.

📘 D. Documentación Técnica y Orquestación (IE7, IE8)

Para documentar el diseño y la orquestación del sistema, incluí diagramas Mermaid dentro de la carpeta /docs.
Estos diagramas muestran cómo se comunican los módulos principales:

🔹 Diagrama de Orquestación
graph TD
    U[Usuario] --> A[FastAPI /consultar]
    A --> P[TaskPlanner]
    P --> S[Seguridad]
    S --> C{¿Contiene datos sensibles?}
    C -- Sí --> D[Derivar a ejecutivo]
    C -- No --> R[VectorStore FAISS]
    R --> L[LLM (ChatOpenAI)]
    L --> W[Registrar nota JSONL]
    L --> O[Enviar respuesta al usuario]

🔹 Flujo de Tareas del Agente
sequenceDiagram
    participant U as Usuario
    participant API as FastAPI
    participant PL as Planner
    participant VS as VectorStore
    participant LLM as LLM RAG
    U->>API: POST /consultar (pregunta)
    API->>PL: plan(pregunta)
    PL->>API: [seguridad, recuperar_ctx, razonar, responder, registrar]
    API->>VS: tool_search_docs()
    API->>API: tool_reason_policy()
    API->>LLM: RetrievalQA(query)
    LLM-->>API: Respuesta fundamentada + fuentes
    API->>API: tool_write_note()
    API-->>U: respuesta + trazas + evidencias


Estos diagramas explican claramente la interacción entre FastAPI, el planificador, la memoria y el modelo LLM, cumpliendo con el requisito de documentar la orquestación de los componentes.

🧩 E. Redacción Técnica (IE10)

Todo el proyecto fue documentado con lenguaje técnico claro y coherente, usando nombres descriptivos en las clases y funciones.
Los comentarios en el código explican cada paso del flujo del agente, lo que facilita la comprensión por parte de evaluadores o compañeros de equipo.
Además, se respetaron las buenas prácticas de seguridad, evitando publicar tokens o claves dentro del código.

🔄 F. Ejemplos de Flujo y Evidencias (IE9)

El proyecto incluye endpoints funcionales que permiten probar directamente las capacidades del agente:

Método	Ruta	Descripción
GET	/salud	Verifica si el agente está inicializado correctamente.
POST	/consultar	Realiza una consulta y ejecuta el flujo completo del agente.
GET	/memoria/corto	Muestra las últimas interacciones almacenadas.
POST	/nota	Registra manualmente una nota operativa.
 
En la carpeta /data se generan automáticamente los archivos:

notas_operacionales.jsonl → guarda las notas escritas por el agente.

traces_ep2.log → registra las trazas del proceso interno (planificación y razonamiento).

Estos elementos sirven como evidencia del comportamiento real y adaptativo del sistema.

G. Referencias Técnicas (APA)

FastAPI. (2025). FastAPI Framework Documentation. Recuperado de https://fastapi.tiangolo.com

LangChain. (2025). LangChain Framework – Agents & RAG Documentation. Recuperado de https://python.langchain.com

HuggingFace. (2025). Sentence Transformers: all-MiniLM-L6-v2. Recuperado de https://huggingface.co/sentence-transformers

FAISS. (2025). Facebook AI Similarity Search. Recuperado de https://faiss.ai

OpenAI. (2025). Chat Models API Reference. Recuperado de https://platform.openai.com/docs
