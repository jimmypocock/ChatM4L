# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Project Overview

**ChatM4L** - An AI-powered chat assistant for Ableton Live via Max for Live. Users interact with a chat interface inside Ableton to control their session using natural language.

### What It Does
- Natural language control of Ableton Live
- Create MIDI clips with patterns (drums, melodies, chords)
- Adjust track parameters (volume, pan, mute, solo)
- Control device parameters on any plugin
- Fire/stop clips, manage arrangement
- Context-aware - sees track name, devices, clips, parameters

### Example Interactions
- "Create a hip hop drum beat in clip 1"
- "Turn up the reverb on the synth"
- "Mute this track and solo the bass"
- "Add a 4-bar chord progression in C minor"

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Ableton Live                              │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                    ChatM4L.amxd                          │    │
│  │  ┌─────────┐    ┌─────────┐    ┌──────────────────┐    │    │
│  │  │  jsui   │◄──►│   v8    │◄──►│   node.script    │    │    │
│  │  │chat-ui.js│    │bridge.js│    │   main.js        │    │    │
│  │  └─────────┘    └─────────┘    └────────┬─────────┘    │    │
│  │       ▲              │                   │              │    │
│  │       │              ▼                   ▼              │    │
│  │  ┌─────────┐    ┌─────────┐    ┌──────────────────┐    │    │
│  │  │textedit │    │Live API │    │  Claude/OpenAI   │    │    │
│  │  │ (input) │    │ (OSC)   │    │    (via API)     │    │    │
│  │  └─────────┘    └─────────┘    └──────────────────┘    │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | File | Purpose |
|-----------|------|---------|
| **jsui** | `chat-ui.js` | Renders chat UI, handles display, scrolling, sidebar buttons |
| **v8** | `code/bridge.js` | Routes messages, executes Live API commands, manages state |
| **node.script** | `code/ai/main.js` | Handles AI provider communication, config, sessions |
| **textedit** | (Max object) | Text input field for user messages |

## Directory Structure

```
ChatM4L/
├── ChatM4L.amxd           # Main Max for Live device (frozen for release)
├── ChatM4L-v0.0.0.amxd    # Versioned release copy
├── chat-ui.js             # jsui component - full UI rendering
├── code/
│   ├── bridge.js          # v8 script - message routing & Live API
│   └── ai/
│       ├── main.js        # node.script - AI communication
│       ├── config.js      # Configuration management
│       ├── client.js      # AI provider client abstraction
│       ├── sessions.js    # Session/conversation persistence
│       ├── commands/      # Slash command handlers
│       │   ├── index.js
│       │   ├── help.js
│       │   ├── status.js
│       │   ├── newchat.js
│       │   ├── reload.js
│       │   ├── createconfig.js
│       │   ├── openconfig.js
│       │   ├── skill.js
│       │   └── skills.js
│       ├── defaults/      # Default config templates
│       │   ├── config.json
│       │   ├── core/
│       │   │   ├── system.md
│       │   │   └── user.md
│       │   └── skills/
│       │       ├── drums.md
│       │       ├── mixing.md
│       │       └── sound-design.md
│       ├── package.json
│       └── node_modules/  # Dependencies (not in git)
├── site/                  # Landing page website (TODO)
├── README.md
├── LICENSE
├── CLAUDE.md              # This file
└── .gitignore
```

## User Configuration

Config is stored in `~/Library/Application Support/ChatM4L/`:

```
config/
├── core/
│   ├── system.md          # System prompt (how AI behaves)
│   └── user.md            # User profile (genres, preferences)
├── skills/                # Skill definitions (expertise modes)
│   ├── drums.md
│   ├── mixing.md
│   └── sound-design.md
└── config.json            # Provider settings & API keys
```

### config.json Structure
```json
{
  "provider": "anthropic",
  "model": "claude-sonnet-4-20250514",
  "apiKey": "sk-ant-..."
}
```

Supported providers: `anthropic`, `openai`, `google`, `ollama`

## Key Technical Details

### Message Flow

1. User types in textedit → sends to v8 via `route text` → `tosymbol`
2. v8 (`bridge.js`) receives message, gets track context from Live API
3. v8 sends `{message, context}` to node.script
4. node.script (`main.js`) sends to AI provider, gets response
5. Response contains `{response, commands[]}`
6. v8 executes commands via Live API, sends response to jsui
7. jsui (`chat-ui.js`) displays message in chat

### Live API Access (via v8/bridge.js)

```javascript
// Get track info
const track = new LiveAPI("this_device canonical_parent");
track.get("name");
track.get("devices");

// Set parameters
const param = new LiveAPI("this_device canonical_parent devices 0 parameters 3");
param.set("value", 0.5);

// Create clips
const slot = new LiveAPI("this_device canonical_parent clip_slots 0");
slot.call("create_clip", 4);  // 4 bars
```

### Command Execution

AI returns commands in this format:
```json
{
  "response": "Created a drum pattern!",
  "commands": [
    {"action": "add_notes", "clip_slot": 0, "notes": [...], "length": 4},
    {"action": "set_volume", "value": 0.85},
    {"action": "fire_clip", "clip_slot": 0}
  ]
}
```

Supported actions: `set_parameter`, `set_volume`, `set_pan`, `set_mute`, `set_solo`, `add_notes`, `clear_clip`, `fire_clip`, `stop_clip`

### Slash Commands

| Command | Handler | Description |
|---------|---------|-------------|
| `/help` | help.js | Show available commands |
| `/status` | status.js | Show current provider/model |
| `/newchat` | newchat.js | Clear conversation history |
| `/reload` | reload.js | Reload configuration |
| `/createconfig` | createconfig.js | Create initial config files |
| `/openconfig` | openconfig.js | Show config folder path |
| `/skills` | skills.js | List available skills |
| `/<skill>` | skill.js | Apply a skill (e.g., `/drums`) |

### Skills System

Skills are markdown files that get injected into the system prompt:
- One-shot by default (apply to next message only)
- Can be persisted with `/skill <name>`
- Located in user config `skills/` folder

## Development Workflow

### Working on the Device

1. Open `ChatM4L.amxd` in Ableton (from this repo folder)
2. Click the device to open Max editor
3. Edit JS files - they auto-reload with `@autowatch 1`
4. Test changes in Ableton
5. When done, freeze (snowflake) and save

### Freezing for Release

1. Click snowflake icon in Max toolbar (turns blue)
2. File → Save (Cmd+S)
3. File size should increase significantly (includes dependencies)
4. Copy to `ChatM4L-vX.X.X.amxd` for versioned release

### Polishing the UI (Presentation Mode)

Users should see "Presentation view" (clean UI), not "Patcher view" (the wiring).

1. **Mark UI elements for presentation**: Select each UI element (textedit, buttons), open Inspector (Cmd+I), check "Include in Presentation"
2. **Arrange the presentation**: Go to View → Presentation (or Cmd+Opt+E) to switch to Presentation view. Drag/resize your UI elements to look nice.
3. **Make it open in Presentation by default**: Go to View → Patcher Inspector, check "Open in Presentation"
4. **Set the device width**: View → Set Device Width to control how wide it appears in Ableton

Now when users load the device, they'll only see the clean UI - no wiring visible.

### Creating a Release

```bash
# Tag and create GitHub release
gh release create v0.1.0 ./ChatM4L-v0.1.0.amxd \
  --title "ChatM4L v0.1.0" \
  --notes "Release notes here"
```

## M4L Constraints

- **Device height is fixed at 169 pixels** (width is adjustable)
- jsui uses mgraphics for rendering (Cairo-like API)
- `mgraphics` functions only work inside `paint()` callback
- node.script runs in separate process, communicates via messages
- v8 has access to Live API, node.script does not

## Known Issues / TODO

- [ ] "Thinking..." indicator not fully working
- [ ] Response time is slow (~1 minute) - needs optimization
- [ ] Streaming responses not implemented
- [ ] Error handling could be improved
- [ ] No undo for AI-executed commands

## Performance Optimization Ideas

1. **Reduce context size** - Send less track/device info
2. **Use faster model** - claude-haiku for quick tasks
3. **Implement streaming** - Show response as it arrives
4. **Cache track context** - Don't re-fetch on every message
5. **Batch commands** - Execute multiple commands efficiently

## Links

- **Repo**: https://github.com/jimmypocock/ChatM4L
- **Releases**: https://github.com/jimmypocock/ChatM4L/releases
- **Website**: https://chatm4l.com

## Dependencies

- Node.js (bundled with Max)
- `@anthropic-ai/sdk` - Anthropic API client
- Ableton Live 11+ with Max for Live

## Testing

Currently manual testing only:
1. Load device in Ableton
2. Test chat functionality
3. Test slash commands
4. Test AI responses and command execution
5. Test frozen device in fresh project

## File Naming Conventions

- `ChatM4L.amxd` - Main device (always latest frozen)
- `ChatM4L-vX.X.X.amxd` - Versioned releases
- Semantic versioning: MAJOR.MINOR.PATCH
