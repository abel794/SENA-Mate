SENA-Mate API

Versión: 1.0
Archivo principal: main.py
Objetivo: API para generar respuestas de IA y checklists de Pull Requests, con trazas (prints) para depuración y validaciones.

1️⃣ Requisitos

Python ≥ 3.10

pip

.env con variable HF_TOKEN (token Hugging Face o OpenAI compatible con router HF)

Opcional: variable CHAT_MODEL para elegir modelo (por defecto "openai/gpt-oss-20b")

2️⃣ Instalación

Clonar o descargar el repositorio:

git clone <URL_DEL_REPOSITORIO>
cd SENA-Mate


Crear entorno virtual:

python -m venv .venv


Activar entorno:

Windows:

.venv\Scripts\activate


Linux / macOS:

source .venv/bin/activate


Instalar dependencias:

pip install fastapi uvicorn pydantic openai python-dotenv

3️⃣ Estructura de carpetas
SENA-Mate/
├── main.py                  # Código principal de la API
├── prompts/
│   └── prompt.json          # Prompt inicial y few_shots
├── .env                     # Variables de entorno (HF_TOKEN)
└── README.md

4️⃣ Configuración del .env
HF_TOKEN=TU_TOKEN_AQUI
CHAT_MODEL=openai/gpt-oss-20b  # Opcional


⚠️ Nota de seguridad: No compartas tu HF_TOKEN públicamente.

5️⃣ Uso de la API
Iniciar servidor
uvicorn main:app --reload --port 8000

Endpoints
GET /

Retorna mensaje de salud de la API.

{
  "message": "SENA-Mate API funcionando (prompt.json integrado)"
}

POST /ask

Valida entrada con Pydantic (question mínima de 5 caracteres)

Llama al modelo configurado (CHAT_MODEL)

Devuelve respuesta de IA y metadatos

Ejemplo request:

{
  "question": "¿Qué es Python?"
}


Ejemplo response:

{
  "question": "¿Qué es Python?",
  "answer": "Python es un lenguaje de programación de alto nivel...",
  "prompt_used": "Eres un asistente que ayuda con tareas técnicas...",
  "source": "openai/gpt-oss-20b"
}

POST /hf_ask

Similar a /ask, enfocado en Hugging Face

Incluye trazas y logs detallados

POST /prepPR

Valida entrada (title y description)

Genera checklist combinando base + coincidencias en few_shots del prompt.json

Devuelve JSON listo para frontend

Ejemplo request:

{
  "title": "Agregar autenticación JWT",
  "description": "Implementar login seguro con tokens JWT para usuarios"
}


Ejemplo response:

{
  "title": "Agregar autenticación JWT",
  "description": "Implementar login seguro con tokens JWT para usuarios",
  "checklist": [
    "Título claro y descriptivo",
    "Descripción con contexto",
    "Código probado y funcional",
    "Documentación actualizada",
    "Cumple con estándares del proyecto"
  ],
  "matched_templates": [],
  "prompt_used": "Eres un asistente que ayuda con tareas técnicas...",
  "source": "prompt.json"
}

6️⃣ Logs y depuración

Cada endpoint imprime trazas con print:

Entradas recibidas

Keywords extraídas

Templates evaluados

Checklist final

Errores y excepciones

Esto permite seguimiento paso a paso en desarrollo.

7️⃣ Seguridad

No se imprime HF_TOKEN completo.

Validaciones de entradas con Pydantic (min_length, campos requeridos).

Manejo de errores con HTTPException.

8️⃣ Integración Frontend

Desde React o cualquier frontend:

Usar fetch o axios para llamar endpoints

Mostrar answer o checklist según endpoint

Ejemplo básico con fetch:

const response = await fetch("http://127.0.0.1:8000/ask", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ question: "¿Qué es Python?" })
});
const data = await response.json();
console.log(data.answer);

9️⃣ Documentación automática

FastAPI genera documentación OpenAPI:

http://127.0.0.1:8000/docs

http://127.0.0.1:8000/redoc

10️⃣ Notas finales

Mantener prompts/prompt.json actualizado con few_shots para mejorar resultados de checklist.

Ajustar CHAT_MODEL según disponibilidad y necesidades.

call_chat_model centraliza la llamada al modelo y maneja logs y errores.


# 📚 SENA-Mate API (Backend)

SENA-Mate API es un asistente virtual diseñado para aprendices e instructores del SENA.  
Funciona con **FastAPI** y un modelo de IA alojado en **Hugging Face** (o cualquier otro proveedor compatible con la API de OpenAI).  
Permite hacer preguntas y recibir respuestas inteligentes, así como generar checklists para Pull Requests.

---

## 📂 Estructura del proyecto

SENA-Mate/
├── main.py # Backend con FastAPI
├── prompts/
│ └── prompt.json # Archivo con el prompt y ejemplos
├── .env # Variables de entorno (no subir a GitHub)
└── README.md # Documentación

yaml
Copiar
Editar

---

## 🛠 1. Requisitos previos

Antes de empezar, asegúrate de tener instalado:

- [Python 3.9 o superior](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installation/)
- [Hugging Face account](https://huggingface.co/) (para obtener el **HF_TOKEN** gratuito)

---

## 🚀 2. Instalación

1️⃣ **Clonar el repositorio**

```bash
git clone https://github.com/TU-USUARIO/SENA-Mate.git
cd SENA-Mate
2️⃣ Crear un entorno virtual

bash
Copiar
Editar
python -m venv .venv
3️⃣ Activar el entorno

En Windows:

bash
Copiar
Editar
.venv\Scripts\activate
En Linux / Mac:

bash
Copiar
Editar
source .venv/bin/activate
4️⃣ Instalar dependencias

bash
Copiar
Editar
pip install fastapi uvicorn pydantic openai python-dotenv
🔑 3. Configuración de variables de entorno
Crear un archivo .env en la carpeta raíz del proyecto:

ini
Copiar
Editar
HF_TOKEN=tu_token_de_huggingface
CHAT_MODEL=openai/gpt-oss-20b
Nota:

HF_TOKEN es tu clave de Hugging Face (puedes generarla en huggingface.co/settings/tokens).

CHAT_MODEL es el modelo que quieres usar.

📄 4. Archivo prompt.json
Ubicado en prompts/prompt.json
Aquí defines la personalidad del asistente y ejemplos de cómo debe responder.

Ejemplo:

json
Copiar
Editar
{
    "system": "Eres SENA-Mate, un asistente virtual para aprendices e instructores del SENA. Genera checklists claros, rutas de aprendizaje y retroalimentación breve.",
    "few_shots": [
        {
            "question": "¿Qué es Python?",
            "answer": "Es un lenguaje de programación muy usado en ciencia de datos y desarrollo web."
        },
        {
            "question": "Dame una frase del día",
            "answer": "El éxito es la suma de pequeños esfuerzos repetidos día tras día."
        }
    ]
}
🖥 5. Ejecución del servidor
bash
Copiar
Editar
uvicorn main:app --reload --port 8000
Esto levantará el backend en:

cpp
Copiar
Editar
http://127.0.0.1:8000
📌 6. Endpoints disponibles
1️⃣ /hf_ask (POST)
Descripción: Recibe una pregunta y devuelve una respuesta de la IA.

Ejemplo de request:

json
Copiar
Editar
{
    "question": "hola dime una frase del dia"
}
Ejemplo de response:

json
Copiar
Editar
{
    "answer": "La perseverancia es la clave del éxito.",
    "confidence": 0.95,
    "source": "huggingface"
}
2️⃣ /prepPR (POST)
Descripción: Genera un checklist para un Pull Request.

Ejemplo de request:

json
Copiar
Editar
{
    "title": "Mejora de login",
    "description": "Se añadió validación de usuario y manejo de errores."
}
Ejemplo de response:

json
Copiar
Editar
{
    "checklist": [
        "Validar credenciales correctamente",
        "Probar recuperación de contraseña",
        "Verificar que los mensajes de error sean claros"
    ]
}
⚠ 7. Manejo de errores y validaciones
Validación de entradas con Pydantic (asegura que question, title, description sean strings válidos).

HTTPException para devolver errores claros en formato JSON.

Logs en consola (print) para mostrar:

Preguntas recibidas.

Respuestas generadas.

Errores detectados.

🔗 8. Integración con el Frontend
Puedes conectar este backend a cualquier frontend (React, Vue, Angular o incluso HTML simple).

Ejemplo en React con fetch:

javascript
Copiar
Editar
async function preguntar() {
  const resp = await fetch("http://127.0.0.1:8000/hf_ask", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ question: "Hola, dame una frase del día" })
  });
  const data = await resp.json();
  console.log(data.answer);
}
🧪 9. Probar en Swagger UI
FastAPI genera documentación automática en:

arduino
Copiar
Editar
http://127.0.0.1:8000/docs
Ahí puedes enviar peticiones de prueba sin necesidad de Postman.

📜 10. Notas finales
No subas el archivo .env a GitHub.

Puedes cambiar el modelo de IA modificando CHAT_MODEL en .env.

Si quieres usar OpenAI, instala openai y cambia la inicialización en main.py.

👨‍💻 Autor
Abel Moreno
Brajhan medina
Sebastian lizcano

Proyecto de aprendizaje para SENA