# BlockCore-SDK - Overall Vision & Roadmap

**Document Version**: 1.0  
**Last Updated**: 2025-10-18  
**Status**: Living Document

---

## Executive Summary

**BlockCore-SDK** is the official developer toolkit for creating engine-level extensions for BlockCore. It provides CLI tools, JSON schemas, templates, and documentation that enable developers to build plugins, resource packs, data packs, and shaders that work across all BlockCore-based games (MineWorld, future games).

---

## 1. Vision & Philosophy

### 1.1 Mission Statement

**"Make extending BlockCore a joy, not a journey into documentation hell."**

### 1.2 Guiding Principles

1. **Developer Experience First** - Fast scaffolding, clear errors, helpful docs
2. **Safety & Validation** - Catch errors at dev-time, not runtime
3. **Cross-Game Compatible** - Extensions work on any BlockCore game
4. **Standards-Based** - JSON schemas, semantic versioning, capability flags
5. **Open & Extensible** - Templates and schemas are modifiable

---

## 2. Strategic Goals (2025-2030)

### 2.1 Year 1 (2025)

- ✅ **v0.1-v0.3**: CLI foundation, schemas, basic templates
- 🎯 **v1.0**: Complete toolkit with validation, packaging, installation

**Success Metrics**:
- 50+ plugins/packs created using SDK
- <5 minutes from `new` to working extension
- 0 validation false-positives

### 2.2 Year 2-3 (2026-2027)

- 🎯 **v1.x**: Advanced features (signing, registry, marketplace hooks)
- 🎯 **v2.0**: IDE integrations (VS Code extension, language servers)

**Success Metrics**:
- 500+ extensions using SDK
- Featured in game mod marketplaces
- Community-contributed templates

---

## 3. Artifact Types Supported

### 3.1 Engine Plugins

**Purpose**: Extend BlockCore logic via scripting

**Languages**: Lua (v0.1), JS (v2.0+)

**Example Use Cases**:
- Custom commands (`/home`, `/warp`)
- Event handlers (onTick, onBlockPlace)
- Gameplay systems (economy, permissions)

**Output**: `.zip` with `manifest.json` + scripts

### 3.2 Resource Packs

**Purpose**: Override visual/audio assets

**Contents**:
- Textures (`textures/**/*.png`)
- Models (`models/**/*.json`)
- Sounds (`sounds/**/*.ogg`)
- UI layouts (`ui/**/*.json`)
- Localization (`lang/*.json`)

**Output**: `.zip` with `manifest.json` + assets

### 3.3 Data Packs

**Purpose**: Define data-driven content

**Contents**:
- Block definitions (`data/blocks/*.json`)
- Item definitions (`data/items/*.json`)
- Recipes (`data/recipes/*.json`)
- Biome configs (`data/biomes/*.json`)
- Features (`data/features/*.json`)

**Output**: `.zip` with `manifest.json` + data files

### 3.4 Shader Packs

**Purpose**: Custom rendering profiles

**Contents**:
- Material definitions (`materials/*.json`)
- Shader parameters (`profiles/*.json`)
- Capability requirements

**Output**: `.zip` with `manifest.json` + shader configs

---

## 4. Capability Roadmap

### v0.1 - Foundation (2-3 months)

**Focus**: Prove the CLI concept

- [ ] `blockcore new` command (scaffold projects)
- [ ] Basic manifest schema (id, version, engine, type)
- [ ] One template: `plugin-lua`
- [ ] Project structure conventions

**Deliverable**: Scaffold a Lua plugin in <2 minutes

---

### v0.2 - Validation (2-3 months)

**Focus**: Catch errors early

- [ ] `blockcore validate` command
- [ ] Full schema validation (all manifest fields)
- [ ] Engine version compatibility checks
- [ ] Capability validation
- [ ] Clear error messages with fixes

**Deliverable**: Validate extensions against BlockCore v0.x

---

### v0.3 - Development Flow (2-3 months)

**Focus**: Fast iteration

- [ ] `blockcore pack` command (create `.zip`)
- [ ] `blockcore install` command (install to game)
- [ ] `blockcore dev --watch` (auto-repack on changes)
- [ ] Hot-reload support for supported types
- [ ] Better templates (resource, data, shader)

**Deliverable**: Edit → save → instantly testable workflow

---

### v0.4 - Distribution (2-3 months)

**Focus**: Publish extensions

- [ ] `blockcore sign` command (optional signing)
- [ ] Content hash generation (`packset.lock`)
- [ ] Publish to registry hooks
- [ ] Dependency resolution
- [ ] Version update checking

**Deliverable**: One-command publish to community registry

---

### v1.0 - Stable Release (Polishing)

**Focus**: Production-ready toolkit

- [ ] CLI interface freeze (v1.x backward compatible)
- [ ] Complete documentation (tutorials, API ref)
- [ ] Example extensions (10+ showcases)
- [ ] Integration tests with BlockCore v1.0
- [ ] Multi-language support (CLI messages)

**Success Criteria**:
- 100+ extensions using SDK
- <10 support questions per month (good docs)
- 0 critical bugs for 6 months

---

### v2.0+ - Advanced Tooling (Future)

- [ ] VS Code extension (syntax highlighting, IntelliSense)
- [ ] Language server for Lua plugin APIs
- [ ] GUI wizard for non-technical creators
- [ ] Asset pipeline tools (texture optimization, model conversion)
- [ ] Automated testing framework for extensions

---

## 5. Technical Architecture

### 5.1 CLI Tool Design

```
blockcore (CLI)
  ├─ Commands/
  │   ├─ NewCommand.cs         # Scaffold projects
  │   ├─ ValidateCommand.cs    # Schema validation
  │   ├─ PackCommand.cs        # Create zips
  │   ├─ InstallCommand.cs     # Install to game
  │   └─ DevCommand.cs         # Watch mode
  ├─ Schemas/
  │   ├─ manifest.schema.json
  │   ├─ plugin.schema.json
  │   ├─ resource.schema.json
  │   ├─ data.schema.json
  │   └─ shader.schema.json
  ├─ Templates/
  │   ├─ plugin-lua/
  │   ├─ plugin-js/
  │   ├─ resource-pack/
  │   ├─ data-pack/
  │   └─ shader-pack/
  └─ Utils/
      ├─ SchemaValidator.cs
      ├─ ZipBuilder.cs
      └─ VersionChecker.cs
```

### 5.2 Manifest Format (Core)

```json
{
  "$schema": "https://blockcore.dev/schemas/manifest.v1.json",
  "id": "author:extension_name",
  "name": "Human-Readable Name",
  "version": "1.0.0",
  "type": "plugin|resource|data|shader",
  "engine": "^0.6",
  "description": "Short description",
  "author": "Your Name",
  "license": "MIT",
  "repository": "https://github.com/...",
  "dependencies": {
    "other:extension": "^1.0"
  }
}
```

### 5.3 Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **CLI Tool** | C# .NET 9 | Command-line interface |
| **Schema Validation** | Json.NET + Json.Schema | Validate manifests |
| **Templates** | Scriban / RazorLight | Project scaffolding |
| **Packaging** | SharpZipLib | Create `.zip` files |
| **Version Parsing** | Semver library | Semantic versioning |

---

## 6. Integration with Ecosystem

### 6.1 Relationship with BlockCore

```
BlockCore (Engine)
    ↓ defines plugin/pack contracts
BlockCore-SDK (Toolkit)
    ↓ scaffolds & validates
Extensions (.zip files)
    ↓ loaded by
BlockCore Runtime
```

**Contract**: SDK mirrors BlockCore's public APIs in schemas; breaking changes in engine require SDK update.

### 6.2 Relationship with MineWorld

```
MineWorld (Game)
    ↓ uses BlockCore
BlockCore-SDK
    ↓ creates extensions that work on
MineWorld (and any BlockCore game)
```

**Note**: MineWorld-SDK extends BlockCore-SDK with game-specific templates/schemas.

### 6.3 Version Compatibility Matrix

| SDK Version | BlockCore Engine | Notes |
|-------------|------------------|-------|
| v0.1 | v0.1-v0.3 | Foundation |
| v0.2 | v0.4-v0.6 | Validation added |
| v1.0 | v1.0+ | Stable, API frozen |

---

## 7. Developer Experience Goals

### 7.1 Time Budgets

| Task | Target Time | How Measured |
|------|------------|--------------|
| Install SDK | < 1 minute | `dotnet tool install` |
| Scaffold plugin | < 2 minutes | `new` to working template |
| Validate extension | < 5 seconds | `validate` command |
| Package extension | < 10 seconds | `pack` command |
| Install to game | < 5 seconds | `install` command |

### 7.2 Error Message Quality

**Bad Example**:
```
Error: Validation failed
```

**Good Example**:
```
Error: Invalid manifest in ./MyPlugin/manifest.json

Line 5: Missing required field "engine"
Expected: Engine version range (e.g., "^0.5")

Fix: Add the following to your manifest:
  "engine": "^0.6"

Learn more: https://docs.blockcore.dev/sdk/manifest#engine
```

---

## 8. Community & Content Strategy

### 8.1 Example Extensions (Bundled with SDK)

- **plugin-home**: Teleport home plugin (Lua)
- **pack-hires**: High-resolution texture pack
- **data-ores**: Custom ore data pack
- **shader-toon**: Toon shading shader pack

All examples:
- MIT licensed
- Heavily commented
- Pass validation
- Work on latest BlockCore

### 8.2 Documentation Strategy

**v0.1**:
- CLI reference (all commands + flags)
- Getting Started (5-minute tutorial)
- Manifest format reference

**v1.0**:
- Complete API reference (all schemas)
- Advanced tutorials (10+ use cases)
- Video tutorials (YouTube)
- Troubleshooting guide

**v2.0**:
- Interactive learning (in-browser editor)
- Community cookbook (patterns & recipes)

---

## 9. Risk Management

| Risk | Impact | Mitigation |
|------|--------|-----------|
| **BlockCore API churn** | High | Version schemas separately; deprecation warnings |
| **Schema complexity** | Medium | Start simple; add incrementally |
| **Template maintenance** | Medium | Automated tests for all templates |
| **Breaking CLI changes** | High | SemVer strictly; freeze v1.0 interface |

---

## 10. Success Metrics

### v1.0 Targets

**Quantitative**:
- 100+ extensions created with SDK
- 50+ community templates
- <1% validation false-positives
- 95% uptime for schema hosting

**Qualitative**:
- "Easiest mod tools I've used" - user feedback
- Featured in game development communities
- Active contributor community

---

## 11. Open Questions

- [ ] **JS Runtime**: Add JS plugin support v1.x or wait for v2.0?
- [ ] **Marketplace**: Build official registry or integrate with existing?
- [ ] **Signing**: Required for v1.0 or optional?
- [ ] **IDE Integrations**: VS Code first or multi-IDE?

---

## 12. Conclusion

BlockCore-SDK is the **bridge between engine capabilities and creator imagination**. Success comes from:

1. **Fast & Friendly** - Scaffold → validate → test in minutes
2. **Safe & Reliable** - Catch errors before runtime
3. **Well-Documented** - Every feature has examples
4. **Community-Driven** - Open templates, open schemas

**The SDK is as important as the engine—it unlocks the ecosystem.**

---

## Appendix: Related Documents

- [v0.1 Plan](v0.1.md) - Detailed implementation plan
- [BlockCore Overall](https://github.com/DandelionBold/BlockCore/blob/main/docs/planning/overall.md)

---

**Last Reviewed**: 2025-10-18  
**Next Review**: After v0.1 completion

