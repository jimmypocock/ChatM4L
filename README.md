# ChatM4L

AI-powered chat assistant for Ableton Live via Max for Live.

## Features

- Natural language control of Ableton Live
- Create MIDI clips, adjust parameters, control transport
- Context-aware - understands your track, devices, and clips
- Multi-provider support (Anthropic, OpenAI, Google, Ollama)
- Skills system for specialized expertise (drums, mixing, sound design)

## Installation

1. Download the latest release from [Releases](https://github.com/jimmypocock/ChatM4L/releases)
2. Place `ChatM4L.amxd` in your Ableton User Library
3. Drag onto any MIDI track
4. Run `/createconfig` to set up your API key

## Configuration

Config is stored in `~/Library/Application Support/ChatM4L/`

```
config/
├── core/
│   ├── system.md    # System prompt
│   └── user.md      # Your profile/preferences
├── skills/          # Skill definitions
└── config.json      # Provider settings
```

## Commands

| Command | Description |
|---------|-------------|
| `/help` | Show help |
| `/status` | Show current status |
| `/newchat` | Clear conversation |
| `/reload` | Reload config |
| `/createconfig` | Create initial config |
| `/<skill>` | Apply a skill (e.g., `/drums`) |

## Skills Examples

Skills add specialized expertise to the AI. Activate one and it stays active until you switch or turn it off.

**Drum Programming** (`/drums`)
```
"Give me an 80s ballad beat"
"Make it more syncopated"
"Add ghost notes to the snare"
"What hi-hat pattern would work for house?"
```

**Mixing** (`/mixing`)
```
"This sounds muddy, how do I fix it?"
"Help me set up sidechain compression"
"What frequencies should I cut for clarity?"
"My mix sounds flat, what's missing?"
```

**Sound Design** (`/sound-design`)
```
"How do I make this pad more evolving?"
"Create a pluck sound from scratch"
"Make this bass more aggressive"
"What modulation would add movement?"
```

Use `/skill off` to return to the general assistant.

## Requirements

- Ableton Live 11+ with Max for Live
- API key from Anthropic, OpenAI, or Google (or local Ollama)

## Development

```bash
# Clone the repo
git clone https://github.com/jimmypocock/ChatM4L.git

# Install node dependencies
cd ChatM4L/code/ai
npm install
```

## License

MIT
