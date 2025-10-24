# BlockCore-SDK

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![.NET](https://img.shields.io/badge/.NET-9.0-purple.svg)](https://dotnet.microsoft.com/)
[![Status](https://img.shields.io/badge/Status-In%20Development-yellow.svg)]()

**Official developer toolkit for building engine-level extensions for BlockCore.**

BlockCore-SDK provides CLI tools, schemas, templates, and documentation for creating plugins, resource packs, data packs, and shader extensions that work across all BlockCore-based games.

---

## 🎯 What is BlockCore-SDK?

BlockCore-SDK is the **dev-time toolkit** for extending the BlockCore engine. It helps you:

- **Scaffold** plugins and packs with official templates
- **Validate** your extensions against engine versions
- **Package** distributable `.zip` files with correct manifests
- **Test** compatibility across BlockCore versions

**Think of it as**: The official "mod tools" for BlockCore engine itself.

---

## 🚀 Quick Start

### Installation

```bash
# Install globally via dotnet tool
dotnet tool install -g BlockCore.SDK

# Or download standalone binary
# Windows: blockcore-sdk-win-x64.zip
# Linux: blockcore-sdk-linux-x64.tar.gz
# macOS: blockcore-sdk-osx-x64.tar.gz
```

### Create Your First Plugin

```bash
# Scaffold a new Lua plugin
blockcore new plugin MyPlugin --lang lua

# Validate against engine version
blockcore validate ./MyPlugin --engine ^0.5

# Package for distribution
blockcore pack ./MyPlugin --out MyPlugin-1.0.0.zip
```

---

## 📦 What Can You Build?

### 1. Engine Plugins
Extend engine behavior with scripted logic:
- Custom commands
- Event handlers (onTick, onBlockPlace, etc.)
- Gameplay systems

**Example**: Teleport plugin, custom worldgen rules

### 2. Resource Packs
Override visual and audio assets:
- Textures
- Models
- Sounds
- UI layouts
- Localization strings

**Example**: HD texture pack, custom UI theme

### 3. Data Packs
Define data-driven content:
- Block definitions
- Item definitions
- Recipe configurations
- Biome settings

**Example**: New block types, custom biomes

### 4. Shader Packs
Custom rendering profiles:
- Material definitions
- Shader parameters
- Post-processing effects

**Example**: Toon shading, cinematic mode

---

## 📚 Documentation

- **🎓 Learning Path**: [Comprehensive guides](docs/learning/README.md) for developers transitioning from enterprise development to SDK development
- [Planning & Roadmap](docs/planning/overall.md) - SDK vision and roadmap
- [v0.1 Development Plan](docs/planning/v0.1.md) - Current milestone
- [CLI Reference](docs/cli/README.md) - Complete command reference
- [Plugin API Reference](docs/api/README.md) - Plugin development API
- [Pack Format Guide](docs/packs/README.md) - Resource pack specifications

---

## 🛠️ CLI Commands

| Command | Description |
|---------|-------------|
| `blockcore new <type> <name>` | Scaffold plugin, pack, or extension |
| `blockcore validate <path>` | Validate manifests and schemas |
| `blockcore pack <path>` | Create distributable `.zip` |
| `blockcore install <zip>` | Install to local game |
| `blockcore dev --watch` | Auto-repack on file changes |

### Examples

```bash
# Create a Lua plugin
blockcore new plugin HomeTP --lang lua

# Create a resource pack
blockcore new resource HiResPack

# Create a data pack
blockcore new data CustomBlocks

# Validate against specific engine version
blockcore validate ./HomeTP --engine ^0.6

# Package for release
blockcore pack ./HomeTP --out HomeTP-1.0.0.zip

# Install to MineWorld for testing
blockcore install HomeTP-1.0.0.zip --game-path ~/Games/MineWorld
```

---

## 📋 Extension Types

### Plugin Manifest

```json
{
  "id": "author:plugin_name",
  "name": "My Plugin",
  "version": "1.0.0",
  "type": "plugin",
  "engine": "^0.6",
  "entry": "main.lua",
  "side": "server",
  "permissions": ["commands.register", "world.read"],
  "capabilities": ["chat", "events.tick"]
}
```

### Resource Pack Manifest

```json
{
  "id": "author:pack_name",
  "name": "My Resource Pack",
  "version": "1.0.0",
  "type": "resource",
  "engine": "^0.4",
  "capabilities": ["textures", "models", "sounds"]
}
```

---

## 🎓 Tutorials

- [Creating Your First Plugin](docs/tutorials/first-plugin.md) - Step-by-step plugin creation
- [Building a Resource Pack](docs/tutorials/resource-pack.md) - Complete resource pack guide
- [Data Pack Best Practices](docs/tutorials/data-pack-best-practices.md) - Data pack guidelines
- [Shader Pack Development](docs/tutorials/shader-pack.md) - Custom shader development

---

## 🌟 Example Extensions

- **Teleport Plugin** - `/home` and `/sethome` commands
- **HD Texture Pack** - High-resolution block textures
- **New Ores Data Pack** - Custom ore generation
- **Toon Shader Pack** - Cell-shaded rendering

*(All examples coming with v0.1 release)*

---

## 🤝 Contributing

We welcome contributions to the SDK toolkit itself!

### Development Setup

```bash
# Clone the SDK repository
git clone https://github.com/DandelionBold/BlockCore-SDK.git
cd BlockCore-SDK

# Build the CLI tool
dotnet build

# Run tests
dotnet test

# Install locally for testing
dotnet pack
dotnet tool install --global --add-source ./nupkg BlockCore.SDK
```

---

## 🔗 Related Projects

- [**BlockCore**](https://github.com/DandelionBold/BlockCore) - The voxel engine
- [**MineWorld**](https://github.com/DandelionBold/MineWorld) - Reference game built on BlockCore
- [**MineWorld-SDK**](https://github.com/DandelionBold/MineWorld-SDK) - Game-specific modding toolkit

---

## 📄 License

BlockCore-SDK is licensed under [MIT License](LICENSE).

Example assets and templates are licensed under [CC BY 4.0](LICENSE-CC-BY-4.0).

---

## 🌟 Related Projects

### Core Engine
- **[BlockCore](https://github.com/DandelionBold/BlockCore)** - Game engine that BlockCore-SDK extends
  - Plugin architecture and APIs
  - Rendering and physics systems
  - [Engine Documentation](https://github.com/DandelionBold/BlockCore/blob/main/docs/learning/README.md)

### Game Implementation
- **[MineWorld](https://github.com/DandelionBold/MineWorld)** - Reference game built on BlockCore
  - Real-world plugin examples
  - Game-specific extensions
  - [Game Documentation](https://github.com/DandelionBold/MineWorld/blob/main/docs/learning/README.md)

### Game-Specific Tools
- **[MineWorld-SDK](https://github.com/DandelionBold/MineWorld-SDK)** - Modding toolkit for MineWorld
  - Game content creation tools
  - Mod templates and examples
  - [Modding Guide](https://github.com/DandelionBold/MineWorld-SDK/blob/main/docs/learning/README.md)

---

## 💬 Community

- GitHub Issues: Bug reports and feature requests
- Discussions: SDK questions and feedback

---

**Build extensions. Power games. Welcome to BlockCore-SDK.**

