# Student-Optimized Repository Analysis

This document analyzes the nanobot repository and identifies which parts can be **removed** to create a leaner, more focused version for students. The goal is to reduce cognitive load while preserving everything students need.

**Student usage profile**: Discord (primary chat platform) + CLI (local interface) + skill creation workflow.

## Current Repository Size

| Component | Files | Lines |
|-----------|-------|-------|
| Agent core (`agent/`) | 16 | 3,247 |
| Chat channels (`channels/`) | 15 | 6,626 |
| LLM providers (`providers/`) | 8 | 1,859 |
| Other modules (bus, session, config, cron, heartbeat, security, cli, utils) | 21 | 2,933 |
| Skills (`skills/`) | ~20 | ~1,770 |
| Bridge (`bridge/`) | 6 | 464 |
| Tests (`tests/`) | 45 | 9,946 |
| Documentation (markdown) | 5 | 2,131 |
| Infrastructure (Dockerfile, CI, pyproject.toml) | ~5 | ~220 |
| **Total** | **~141** | **~29,196** |

---

## What to Remove

### 1. Chat Channels — Remove 10 of 14 files (saves ~5,855 lines source)

The repository has 12 platform-specific channel implementations. Students only need Discord and the supporting infrastructure. The rest are platform-specific integration code that adds bulk without educational value.

**Remove:**

| File | Lines | Reason |
|------|-------|--------|
| `channels/feishu.py` | 1,244 | China-specific platform (Lark), complex rich-text formatting |
| `channels/mochat.py` | 946 | Niche platform, large implementation |
| `channels/telegram.py` | 816 | Redundant — Discord covers the same patterns |
| `channels/matrix.py` | 739 | E2EE complexity, niche federated protocol |
| `channels/dingtalk.py` | 580 | China-specific enterprise messaging |
| `channels/email.py` | 445 | IMAP/SMTP protocol complexity, not chat-native |
| `channels/wecom.py` | 370 | China-specific enterprise (WeChat Work) |
| `channels/slack.py` | 343 | Redundant with Discord (similar bot patterns) |
| `channels/whatsapp.py` | 188 | Requires separate Node.js bridge to function |
| `channels/qq.py` | 183 | China-specific platform |

**Keep:**

| File | Lines | Why |
|------|-------|-----|
| `channels/discord.py` | 395 | Primary student communication platform |
| `channels/base.py` | 139 | Abstract base class — required infrastructure |
| `channels/manager.py` | 161 | Channel lifecycle management — required infrastructure |
| `channels/registry.py` | 71 | Plugin discovery — required infrastructure |
| `channels/__init__.py` | 6 | Package init |

### 2. Chat Channel Tests — Remove tests for removed channels (saves ~3,606 lines)

**Remove:**

| Test File | Lines | Corresponds to |
|-----------|-------|---------------|
| `test_matrix_channel.py` | 1,318 | matrix.py |
| `test_telegram_channel.py` | 737 | telegram.py |
| `test_feishu_reply.py` | 435 | feishu.py |
| `test_email_channel.py` | 368 | email.py |
| `test_channel_plugins.py` | 228 | Plugin system (reduced scope) |
| `test_dingtalk_channel.py` | 213 | dingtalk.py |
| `test_slack_channel.py` | 147 | slack.py |
| `test_feishu_tool_hint_code_block.py` | 138 | feishu.py |
| `test_qq_channel.py` | 125 | qq.py |
| `test_feishu_table_split.py` | 104 | feishu.py |
| `test_feishu_post_content.py` | 65 | feishu.py |
| `test_feishu_markdown_rendering.py` | 57 | feishu.py |

**Keep:** `test_base_channel.py` (25 lines) — tests the base class that Discord depends on.

### 3. WhatsApp Bridge — Remove entirely (saves ~464 lines)

**Remove:** The entire `bridge/` directory.

| File | Lines |
|------|-------|
| `bridge/src/whatsapp.ts` | 239 |
| `bridge/src/server.ts` | 129 |
| `bridge/src/index.ts` | 51 |
| `bridge/src/types.d.ts` | 3 |
| `bridge/package.json` | 26 |
| `bridge/tsconfig.json` | 16 |

**Reason:** This is a separate Node.js/TypeScript application in a different language from the rest of the codebase. It only exists to support WhatsApp, which is being removed. No educational value for understanding the Python agent framework.

### 4. Provider Implementations — Remove 2 specialized providers (saves ~529 lines source)

**Remove:**

| File | Lines | Reason |
|------|-------|--------|
| `providers/openai_codex_provider.py` | 317 | OAuth device-code flow complexity, specialized use case |
| `providers/azure_openai_provider.py` | 212 | Enterprise Azure variant, not student-facing |

**Keep:**

| File | Lines | Why |
|------|-------|-----|
| `providers/base.py` | 280 | LLM API abstraction — core educational value |
| `providers/litellm_provider.py` | 355 | Multi-provider routing, caching, streaming — key AI/ML concepts |
| `providers/registry.py` | 523 | Provider ecosystem — how models are discovered and configured |
| `providers/custom_provider.py` | 78 | Shows how to add any OpenAI-compatible endpoint |
| `providers/transcription.py` | 64 | Audio transcription (Whisper) — multimodal AI |
| `providers/__init__.py` | 30 | Package init |

### 5. Provider Tests — Remove tests for removed providers (saves ~411 lines)

| Test File | Lines | Corresponds to |
|-----------|-------|---------------|
| `test_azure_openai_provider.py` | 398 | azure_openai_provider.py |
| `test_custom_provider.py` | 13 | Keep — but minimal |

### 6. Documentation & Misc — Remove non-essential docs (saves ~258 lines)

**Remove:**

| File | Lines | Reason |
|------|-------|--------|
| `docs/CHANNEL_PLUGIN_GUIDE.md` | 254 | No longer relevant with reduced channel set |
| `COMMUNICATION.md` | 4 | Not needed for student use |

**Keep:**

| File | Lines | Why |
|------|-------|-----|
| `README.md` | 1,488 | Main documentation — essential reference |
| `CONTRIBUTING.md` | 122 | Useful if students contribute |
| `SECURITY.md` | 263 | AI safety awareness |

### 7. Demo Assets — Remove `case/` directory

The `case/` directory contains demo GIFs and screenshots. These are marketing/showcase material, not educational content. Students learn by using the tool, not by viewing screenshots.

---

## What to Keep — Everything Students Need

### Core Agent System (3,247 lines) — Keep All

This is the educational heart of the repository.

| File | Lines | What Students Learn |
|------|-------|-------------------|
| `agent/loop.py` | 510 | The ReAct agent loop — how AI agents work end-to-end |
| `agent/memory.py` | 357 | Memory consolidation, token management, context windows |
| `agent/subagent.py` | 235 | Multi-agent architectures, background task delegation |
| `agent/skills.py` | 228 | Dynamic capability loading, teaching agents via markdown |
| `agent/context.py` | 195 | Prompt engineering — how system prompts are assembled |
| `agent/tools/filesystem.py` | 381 | Tool implementation: file operations with safety bounds |
| `agent/tools/web.py` | 338 | RAG patterns: web search + retrieval |
| `agent/tools/cron.py` | 199 | Scheduled agent actions |
| `agent/tools/mcp.py` | 184 | Model Context Protocol — emerging industry standard |
| `agent/tools/shell.py` | 183 | Command execution with security guards |
| `agent/tools/base.py` | 181 | Tool abstraction, JSON Schema, function calling interface |
| `agent/tools/message.py` | 109 | Agent-to-channel communication |
| `agent/tools/registry.py` | 70 | Dynamic tool registration pattern |
| `agent/tools/spawn.py` | 63 | Subagent spawning tool |

### CLI (1,134 lines) — Keep All

Students use the CLI directly for local interaction. `cli/commands.py` covers onboarding, agent messaging, gateway management, and provider/channel status.

### Skill Creator & Skills (~1,770 lines) — Keep All

The skill creation workflow is a key student activity. Keep all skills including:
- `skill-creator/` — scripts for init, validate, package
- `weather/`, `github/`, `cron/`, `memory/`, `summarize/`, `tmux/`, `clawhub/`

### Supporting Infrastructure — Keep All

| Module | Lines | Why |
|--------|-------|-----|
| `session/` | 247 | Conversation persistence — students need working sessions |
| `config/` | 420 | Configuration — required for setup and customization |
| `cron/` | 441 | Scheduled tasks — enables proactive agent behavior |
| `heartbeat/` | 190 | Periodic wake-up — demonstrates autonomous agent patterns |
| `security/` | 105 | SSRF + injection prevention — AI safety in practice |
| `bus/` | 88 | Message routing — clean async architecture |
| `utils/` | 308 | Shared helpers |

### Build & Deploy — Keep All

Dockerfile, docker-compose.yml, pyproject.toml, `.github/workflows/ci.yml` — students benefit from understanding how AI systems are packaged and deployed.

### Tests for Kept Components (~5,929 lines) — Keep

All tests that exercise kept components remain. Key educational tests:
- `test_consolidate_offset.py` (619) — memory consolidation behavior
- `test_memory_consolidation_types.py` (478) — memory system types
- `test_tool_validation.py` (408) — tool parameter validation
- `test_filesystem_tools.py` (364) — file tool safety
- `test_heartbeat_service.py` (289) — proactive agent tests
- `test_mcp_tool.py` (282) — MCP protocol tests
- `test_security_network.py` (101) — SSRF protection tests
- `test_exec_security.py` (69) — command injection tests

---

## Summary

| Category | Before | Removed | After |
|----------|--------|---------|-------|
| Channel source | 6,626 | 5,855 | 771 |
| Channel tests | 3,935 | 3,606 | 329 |
| Bridge | 464 | 464 | 0 |
| Provider source | 1,859 | 529 | 1,330 |
| Provider tests | 411 | 398 | 13 |
| Docs/misc | 2,131 | 258 | 1,873 |
| Demo assets (`case/`) | varies | all | 0 |
| Other source | 6,180 | 0 | 6,180 |
| Other tests | 6,011 | 0 | 6,011 |
| Skills | 1,770 | 0 | 1,770 |
| Infrastructure | ~220 | 0 | ~220 |
| **Total** | **~29,607** | **~11,110** | **~18,497** |

**~37% reduction** in total codebase size, while preserving 100% of the functionality students will use (Discord, CLI, skill creation, all agent capabilities).

---

## Optional Further Reductions

If an even leaner version is desired:

1. **Trim `providers/registry.py`** (523 lines): Remove provider specs for services students won't use. Keep only OpenAI, Anthropic, DeepSeek, Groq, and Gemini. Estimated savings: ~200 lines.

2. **Simplify `agent/tools/web.py`** (338 lines): Reduce from 5 search providers (Brave, Tavily, Jina, SearXNG, DuckDuckGo) to 2. Estimated savings: ~100 lines.

3. **Trim `cli/commands.py`** (1,133 lines): Remove multi-instance management and OAuth login flows. Keep basic `agent`, `gateway`, `onboard`, `status`. Estimated savings: ~300 lines.

4. **Reduce test verbosity**: Some test files have extensive fixture setup that could be simplified. Potential savings: ~500 lines across multiple files.

These optional reductions would bring the total down to approximately **~17,400 lines** (~41% reduction from original).
