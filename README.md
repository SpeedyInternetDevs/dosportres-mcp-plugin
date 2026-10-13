# Plugin dosportres para asistentes de IA

Conecta Claude, Antigravity, Gemini, Codex, OpenCode, Kimi y cualquier cliente MCP al ERP dosportres
(`https://dosportres.com.ec/mcp/v1`). Cada persona entra con **su** usuario de iDempiere: la IA ve y
hace solo lo que permite el rol que elige al conectar.

Una sola carpeta sirve a todos los clientes:

| Archivo | Cliente |
|---|---|
| `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `.mcp.json` | Claude Code |
| `plugin.json`, `mcp_config.json` | Antigravity (`agy`) |
| `gemini-extension.json` | Gemini CLI |
| `.kimi-plugin/plugin.json` | Kimi |
| `skills/dosportres-erp/SKILL.md`, `AGENTS.md` | Instrucciones de uso (Claude, Antigravity, Kimi / Gemini) |

## Instalar

El repositorio es privado: hace falta acceso a `SpeedyInternetDevs/dosportres-mcp-plugin` en GitHub.

**Claude Code**
```bash
claude plugin marketplace add SpeedyInternetDevs/dosportres-mcp-plugin
claude plugin install dosportres@dosportres
```

**Claude web y Claude Desktop** (no usan plugins): *Ajustes → Connectors → Add custom connector* →
`https://dosportres.com.ec/mcp/v1`.

**Antigravity** (instala desde una carpeta local)
```bash
git clone https://github.com/SpeedyInternetDevs/dosportres-mcp-plugin.git
agy plugin install ./dosportres-mcp-plugin
```

**Gemini CLI**
```bash
gemini extensions install https://github.com/SpeedyInternetDevs/dosportres-mcp-plugin.git
```

**Codex**
```bash
codex mcp add dosportres --url https://dosportres.com.ec/mcp/v1
```

**OpenCode**: en `~/.config/opencode/opencode.json`
```json
{ "mcp": { "dosportres": { "type": "remote", "url": "https://dosportres.com.ec/mcp/v1" } } }
```
y luego `opencode mcp auth dosportres`.

**Kimi**: instalar esta carpeta como plugin. El formato de servidor remoto de Kimi no se ha verificado
con el cliente; si no conecta, agregue el servidor HTTP `https://dosportres.com.ec/mcp/v1` en su
configuración de MCP.

**Cualquier otro cliente MCP**: servidor HTTP (*streamable HTTP*) en `https://dosportres.com.ec/mcp/v1`,
con OAuth 2.1 y registro dinámico.

## Primera conexión

El cliente abre el navegador en dosportres: entre con su usuario y clave de iDempiere, elija empresa y
rol, y pulse **Permitir acceso**. La IA nunca recibe la clave.

## Seguridad: aprobar las escrituras

`create_record` y `update_record` crean o modifican borradores en el ERP. Configure su cliente para que
**pida aprobación** antes de ejecutarlas y revise los argumentos, sobre todo `confirmed_defaults`:

- **Claude Code**: en `~/.claude/settings.json`,
  `"permissions": { "ask": ["mcp__plugin_dosportres_dosportres__create_record", "mcp__plugin_dosportres_dosportres__update_record"] }`.
- **Antigravity y demás**: no las agregue a listas de autoaprobación ni elija "permitir siempre".

## Requisitos en el servidor

En dosportres (*System Configurator*), `MCP_REDIRECT_HOSTS` debe incluir el host de retorno de cada
cliente web: `claude.ai` (Claude web y Desktop) y `antigravity.google` (Antigravity). Los clientes que
vuelven por `localhost` (Claude Code, Codex, Gemini, OpenCode) no necesitan entrada.
