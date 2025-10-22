# 🤖 Telegram MCP Bot

Bot de Telegram inteligente con integración de **Model Context Protocol (MCP)** y **Ollama** para un asistente conversacional con acceso a herramientas externas de diagnóstico y gestión.

![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)
![Docker](https://img.shields.io/badge/Docker-Ready-brightgreen.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

## ✨ Características

- 🤖 **Ollama**: Modelos LLM locales (deepseek-v3.1:671b-cloud por defecto)
- 🔧 **MCP**: Acceso a herramientas externas personalizadas
  - Diagnóstico FTTH (Fibra Óptica)
  - Diagnóstico HFC (Cable Coaxial)
  - Gestión de archivos
- 💬 **Sesiones**: Historial de conversación por usuario (20 mensajes)
- 🔄 **Streaming**: Respuestas en tiempo real opcionales
- 🎭 **Prompts del Sistema**: Modos especializados (telecom, soporte técnico, file manager)
- 🐳 **Docker**: Despliegue containerizado con acceso a MCP locales
- 🔒 **Seguridad**: Usuario no-root, variables de entorno seguras
- 📊 **Health Checks**: Monitoreo automático de disponibilidad

## 📋 Requisitos

- **Docker y Docker Compose** (recomendado) o Python 3.11+
- **Ollama** instalado y corriendo en el host: [ollama.ai](https://ollama.ai)
- **Token de Telegram Bot**: Obtener de [@BotFather](https://t.me/BotFather)
- **Node.js** 18+ (para servidores MCP basados en npm)
- **Python 3.11+** (para servidores MCP personalizados)

## 🚀 Inicio Rápido con Docker

### 1. Clonar el repositorio

```bash
git clone https://github.com/javier-rocha/telegram-mcp-bot.git
cd telegram-mcp-bot
```

### 2. Configurar variables de entorno

```bash
# Copiar archivo de ejemplo
cp .env.example .env

# Editar .env con tus valores
# Windows: notepad .env
# Linux/Mac: nano .env
```

**Variables requeridas en `.env`:**
```bash
TELEGRAM_TOKEN=tu_token_de_botfather
OLLAMA_MODEL=deepseek-v3.1:671b-cloud
OLLAMA_URL=http://host.docker.internal:11434
MCP_SERVERS_PATH=C:/Desarrollos/mcp-servers  # Ruta ABSOLUTA Windows
# MCP_SERVERS_PATH=/home/usuario/mcp-servers  # Ruta ABSOLUTA Linux/Mac
```

### 3. Preparar modelo de Ollama

```bash
# Descargar el modelo configurado
ollama pull deepseek-v3.1:671b-cloud

# Verificar que está disponible
ollama list
```

### 4. Iniciar el bot

```bash
# Linux/Mac: Hacer ejecutables los scripts
chmod +x init_volumes.sh start.sh stop.sh

# Inicializar volúmenes (primera vez)
./init_volumes.sh

# Iniciar bot
./start.sh

# Ver logs en tiempo real
docker-compose logs -f telegram-bot

# Detener bot
./stop.sh
```

**Windows PowerShell:**
```powershell
# Inicializar volúmenes
mkdir data, logs, files -Force

# Iniciar manualmente
docker-compose up -d --build

# Ver logs
docker-compose logs -f telegram-bot

# Detener
docker-compose down
```

## 🖥️ Instalación Local (sin Docker)

### 1. Crear entorno virtual

```bash
python -m venv venv

# Linux/Mac
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 2. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 3. Configurar

```bash
# Copiar y editar configuración
cp .env.example .env
# Editar .env con tu TELEGRAM_TOKEN

# Verificar config.json
cat config.json
```

### 4. Ejecutar

```bash
# Asegurarse que Ollama está corriendo
curl http://localhost:11434/api/tags

# Iniciar bot
python main.py
```

## ⚙️ Configuración Detallada

### Archivo `.env`

Variables de entorno para el despliegue:

```bash
# Token del bot de Telegram (REQUERIDO)
TELEGRAM_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyz

# Modelo de Ollama (opcional, default: deepseek-v3.1:671b-cloud)
OLLAMA_MODEL=deepseek-v3.1:671b-cloud

# URL de Ollama (opcional, default: http://host.docker.internal:11434)
OLLAMA_URL=http://host.docker.internal:11434

# Ruta ABSOLUTA a servidores MCP (REQUERIDO para Docker)
MCP_SERVERS_PATH=C:/Desarrollos/mcp-servers

# Configuración adicional (opcional)
LOG_LEVEL=INFO
MAX_HISTORY=20
```

### Archivo `config.json`

Configuración de parámetros del bot y servidores MCP. Ver ejemplo completo en el proyecto.

**Servidores MCP personalizados:**
```json
{
  "mcp_servers": {
    "ftth-diagnostic": {
      "command": "python",
      "args": ["/mcp-servers/ftth_diagnostic_server.py"],
      "env": {
        "PYTHONPATH": "/mcp-servers",
        "FASTMCP_SUPPRESS_BANNER": "1"
      }
    },
    "file-manager": {
      "command": "npx",
      "args": [
        "@modelcontextprotocol/server-filesystem",
        "/mcp-servers/files/"
      ]
    }
  }
}
```

**System Prompts disponibles:**
- `telecom_system`: Agente especializado en diagnóstico FTTH/HFC
- `general_assistant`: Asistente general con herramientas
- `technical_support`: Especialista en soporte técnico
- `file_manager`: Administrador de archivos

### Token de Telegram

1. Abrir [@BotFather](https://t.me/BotFather) en Telegram
2. Enviar `/newbot` y seguir instrucciones
3. Copiar el token proporcionado
4. Agregar a `.env`: `TELEGRAM_TOKEN=tu_token_aqui`

### Modelos de Ollama

```bash
# Listar modelos disponibles
ollama list

# Descargar modelos recomendados
ollama pull deepseek-v3.1:671b-cloud  # 6.7B - Excelente razonamiento
ollama pull llama3.2                   # 3B - Rápido y eficiente
ollama pull mistral                    # 7B - Balance capacidad/velocidad

# Cambiar modelo en .env
OLLAMA_MODEL=llama3.2
```

## 💬 Comandos del Bot

| Comando | Descripción |
|---------|-------------|
| `/start` | Inicia una nueva sesión y muestra bienvenida |
| `/reset` | Reinicia la conversación actual (limpia historial) |
| `/list_tools` | Muestra todas las herramientas MCP disponibles |
| `/list_prompts` | Lista modos de conversación disponibles |
| `/set_prompt <nombre>` | Cambia el modo de conversación |
| `/stream_on` | Activa respuestas en streaming (tiempo real) |
| `/stream_off` | Desactiva respuestas en streaming |
| `/status` | Muestra estado del sistema (modelo, servidores) |
| `/test_ollama` | Prueba conexión con Ollama |
| `/help` | Muestra ayuda y comandos disponibles |

**Ejemplos de uso:**
```
/set_prompt telecom_system
/stream_on
¿Puedes diagnosticar la ONT con ID 12345?
/list_tools
```

## 📂 Estructura del Proyecto

```
telegram-mcp-bot/
├── 📄 main.py                 # Bot principal con handlers
├── 📄 config_loader.py        # Cargador de configuración
├── 📄 mcp_manager.py          # Gestor de servidores MCP
├── 📄 ollama_client.py        # Cliente Ollama con API
├── 📄 health_check.py         # Health check para Docker
├── ⚙️  config.json             # Configuración MCP y prompts
├── 📋 requirements.txt        # Dependencias Python
├── 🐳 Dockerfile              # Imagen Docker optimizada
├── 🐳 docker-compose.yml      # Orquestación Docker
├── 🔒 .env                    # Variables de entorno (gitignored)
├── 📝 .env.example            # Plantilla de variables
├── 🚫 .dockerignore           # Exclusiones para Docker
├── 🚫 .gitignore              # Archivos excluidos de Git
├── 📖 README.md               # Este archivo
├── 📖 DOCKER.md               # Guía detallada de Docker
├── 📖 CONTRIBUTING.md         # Guía de contribución
├── 📜 LICENSE                 # Licencia MIT
├── 🔧 start.sh                # Script de inicio (Linux/Mac)
├── 🔧 stop.sh                 # Script de parada (Linux/Mac)
├── 🔧 init_volumes.sh         # Inicialización de volúmenes
├── 📁 data/                   # Datos persistentes (volumen)
├── 📁 logs/                   # Logs de aplicación (volumen)
├── 📁 files/                  # Archivos accesibles por MCP (volumen)
└── 📁 tests/                  # Tests unitarios
```

## 🐳 Arquitectura Docker

```
┌─────────────────────┐
│  Telegram Client    │
│  (Cualquier chat)   │
└──────────┬──────────┘
           │ HTTPS
           ▼
┌──────────────────────────────────────────────┐
│   Docker Container: telegram-mcp-bot         │
│   (UID 1000, usuario no-root)                │
│                                              │
│  ┌────────────────────────────────────────┐ │
│  │    main.py (Telegram Bot Handler)     │ │
│  │    - Recibe mensajes                   │ │
│  │    - Gestiona comandos                 │ │
│  │    - Streaming respuestas              │ │
│  └──────────┬─────────────────────────────┘ │
│             │                                │
│  ┌──────────▼─────────────────────────────┐ │
│  │   ollama_client.py                     │ │
│  │   - Chat API                       ────┼─┼──┐
│  │   - Streaming                          │ │  │
│  │   - Tool calling                       │ │  │
│  └──────────┬─────────────────────────────┘ │  │
│             │                                │  │
│  ┌──────────▼─────────────────────────────┐ │  │
│  │   mcp_manager.py                       │ │  │
│  │   ├─ ftth-diagnostic (Python)      ◄───┼─┼──┤
│  │   ├─ hfc-diagnostic (Python)           │ │  │
│  │   └─ file-manager (npx)                │ │  │
│  └────────────────────────────────────────┘ │  │
│                                              │  │
│  Volúmenes montados:                         │  │
│   /app/data     ← ./data                    │  │
│   /app/logs     ← ./logs                    │  │
│   /app/files    ← ./files                   │  │
│   /mcp-servers  ← $MCP_SERVERS_PATH         │  │
└──────────────────────────────────────────────┘  │
                                                  │
                  ┌───────────────────────────────┘
                  │ HTTP
                  ▼
        ┌──────────────────┐
        │  Host Machine    │
        │                  │
        │  Ollama Server   │
        │  :11434          │
        │                  │
        │  Modelos:        │
        │  - deepseek-v3.1 │
        │  - llama3.2      │
        └──────────────────┘
```

### Flujo de Mensajes

1. **Usuario → Telegram → Bot Container**
2. **Bot** recibe mensaje y lo envía a **ollama_client**
3. **ollama_client** llama a **Ollama en host** con herramientas MCP
4. **Ollama** puede decidir usar herramientas → **mcp_manager**
5. **mcp_manager** ejecuta servidor MCP correspondiente
6. **Resultado** vuelve a Ollama → Bot → Usuario

## 🔧 Desarrollo

### Agregar un Nuevo Comando

```python
# En main.py
async def mi_comando(self, update: Update, context: ContextTypes.DEFAULT_TYPE):
    """Descripción del comando"""
    user_id = update.effective_user.id
    await update.message.reply_text("Respuesta del comando")
    logger.info(f"Usuario {user_id} ejecutó mi_comando")

# Registrar en run()
application.add_handler(CommandHandler("mi_comando", self.mi_comando))
```

### Agregar un Nuevo Servidor MCP

**1. Crear servidor Python con FastMCP:**

```python
# mcp-servers/mi_servidor.py
from fastmcp import FastMCP

mcp = FastMCP("Mi Servidor")

@mcp.tool()
def mi_herramienta(parametro: str) -> dict:
    """Descripción de la herramienta"""
    return {"resultado": "ok", "parametro": parametro}

if __name__ == "__main__":
    mcp.run()
```

**2. Agregar a `config.json`:**

```json
{
  "mcp_servers": {
    "mi-servidor": {
      "command": "python",
      "args": ["/mcp-servers/mi_servidor.py"],
      "env": {
        "PYTHONPATH": "/mcp-servers",
        "FASTMCP_SUPPRESS_BANNER": "1"
      }
    }
  }
}
```

**3. Reiniciar bot:**

```bash
docker-compose restart telegram-bot
```

## 🐛 Solución de Problemas

### Bot no responde

```bash
# Verificar logs del contenedor
docker-compose logs telegram-bot | tail -50

# Verificar que el contenedor está corriendo
docker-compose ps

# Reiniciar bot
docker-compose restart telegram-bot
```

### Error de conexión con Ollama

```bash
# Verificar que Ollama está corriendo en el host
curl http://localhost:11434/api/tags

# Verificar conectividad desde contenedor
docker exec -it telegram-mcp-bot ping -c 3 host.docker.internal

# Alternativa: usar IP del host en .env
OLLAMA_URL=http://192.168.1.100:11434
```

### MCP Server no encontrado

```bash
# Verificar que el volumen está montado
docker exec telegram-mcp-bot ls -la /mcp-servers

# Verificar ruta ABSOLUTA en .env
echo $MCP_SERVERS_PATH
# Windows: C:/Desarrollos/mcp-servers
# Linux: /home/usuario/mcp-servers
```

## 📊 Monitoreo

```bash
# Estado de contenedores
docker-compose ps

# Recursos utilizados
docker stats telegram-mcp-bot

# Health check status
docker inspect telegram-mcp-bot --format='{{json .State.Health}}'

# Logs en tiempo real
docker-compose logs -f telegram-bot
```

## 🔒 Seguridad

### Buenas Prácticas Implementadas

- ✅ **Usuario no-root** en Docker (UID 1000)
- ✅ **Variables de entorno** para secretos
- ✅ **Volúmenes read-only** para config.json
- ✅ **.gitignore** para archivos sensibles
- ✅ **Límites de recursos** para prevenir DoS
- ✅ **Health checks** automáticos

### Checklist de Seguridad

- [ ] ❌ **Nunca commitear** `.env` con tokens reales
- [ ] ✅ **Usar `.env.example`** como plantilla
- [ ] ✅ **Permisos restrictivos**: `chmod 600 .env config.json`
- [ ] ✅ **Limitar acceso filesystem** a directorios específicos
- [ ] ✅ **Actualizar dependencias** regularmente
- [ ] ✅ **Revisar logs** periódicamente

## 🚢 Despliegue en Producción

### VPS/Cloud

```bash
# 1. Conectar al servidor
ssh usuario@tu-servidor.com

# 2. Instalar Docker y Ollama
curl -fsSL https://get.docker.com | sh
curl https://ollama.ai/install.sh | sh

# 3. Clonar y configurar
git clone https://github.com/javier-rocha/telegram-mcp-bot.git
cd telegram-mcp-bot
cp .env.example .env
nano .env  # Configurar tokens

# 4. Descargar modelo e iniciar
ollama pull deepseek-v3.1:671b-cloud
docker-compose up -d
```

## 📚 Recursos

- [Documentación Ollama](https://ollama.ai/docs)
- [Model Context Protocol](https://modelcontextprotocol.io)
- [Servidores MCP Oficiales](https://github.com/modelcontextprotocol/servers)
- [FastMCP Documentation](https://github.com/jlowin/fastmcp)
- [python-telegram-bot Docs](https://python-telegram-bot.org)
- [Docker Compose Reference](https://docs.docker.com/compose/)

## 🤝 Contribuir

¡Las contribuciones son bienvenidas! Ver [CONTRIBUTING.md](CONTRIBUTING.md) para detalles.

1. Fork el proyecto
2. Crear rama: `git checkout -b feature/nueva-funcionalidad`
3. Commit: `git commit -am 'Agrega funcionalidad X'`
4. Push: `git push origin feature/nueva-funcionalidad`
5. Abrir Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver archivo [LICENSE](LICENSE) para más detalles.

## 🙏 Agradecimientos

- [Anthropic](https://anthropic.com) por Model Context Protocol
- [Ollama](https://ollama.ai) por hacer LLMs locales accesibles
- Comunidad de [python-telegram-bot](https://github.com/python-telegram-bot)
- [FastMCP](https://github.com/jlowin/fastmcp) por simplificar servidores MCP

---

**¿Preguntas o problemas?** Abre un [issue](https://github.com/javier-rocha/telegram-mcp-bot/issues)

**¡Disfruta tu bot inteligente con superpoderes! 🤖✨**
