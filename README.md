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


**¿Preguntas o problemas?** Abre un [issue](https://github.com/javier-rocha/telegram-mcp-bot/issues)

**¡Disfruta tu bot inteligente con superpoderes! 🤖✨**
