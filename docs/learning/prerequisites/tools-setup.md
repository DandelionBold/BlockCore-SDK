# Tools & Setup - Development Environment

Setting up your development environment for BlockCore-SDK development.

## 🛠️ Required Tools

### 1. .NET 9 SDK
**Purpose**: C# runtime and development tools

**Download**: https://dotnet.microsoft.com/download/dotnet/9.0

**Verify Installation**:
```bash
dotnet --version
# Should show: 9.0.x
```

### 2. IDE (Choose One)

#### Visual Studio 2022 (Recommended)
**Download**: https://visualstudio.microsoft.com/vs/
**Required Workloads**:
- .NET desktop development
- ASP.NET and web development (for web-based tools)

**Pros**: Best C# support, integrated debugging, IntelliSense
**Cons**: Windows only, large download

#### JetBrains Rider
**Download**: https://www.jetbrains.com/rider/
**Required Plugins**: None (C# support built-in)

**Pros**: Cross-platform, excellent C# support, lightweight
**Cons**: Paid (free trial available)

#### Visual Studio Code
**Download**: https://code.visualstudio.com/
**Required Extensions**:
- C# Dev Kit
- C# Extensions
- GitLens
- PowerShell (for Windows)

**Pros**: Free, lightweight, cross-platform
**Cons**: Less integrated than full IDEs

### 3. Git
**Purpose**: Version control

**Download**: https://git-scm.com/

**Verify Installation**:
```bash
git --version
# Should show: git version 2.x.x
```

### 4. BlockCore Engine
**Purpose**: Engine dependency for validation

**Installation**: Will be added as NuGet package dependency

## 🔧 CLI Development Tools

### 1. Command Line Parsing
**System.CommandLine**: Microsoft's CLI framework
```bash
dotnet add package System.CommandLine
```

**CommandLineParser**: Alternative CLI framework
```bash
dotnet add package CommandLineParser
```

### 2. Template Engines
**Handlebars.Net**: Template processing
```bash
dotnet add package Handlebars.Net
```

**RazorEngine**: Razor template engine
```bash
dotnet add package RazorEngine
```

### 3. JSON Processing
**System.Text.Json**: Modern JSON handling
```bash
dotnet add package System.Text.Json
```

**Newtonsoft.Json**: Legacy JSON library
```bash
dotnet add package Newtonsoft.Json
```

**JsonSchema.Net**: JSON Schema validation
```bash
dotnet add package JsonSchema.Net
```

## 🎨 Additional Tools

### 1. Package Creation
**SharpZipLib**: ZIP file creation
```bash
dotnet add package SharpZipLib
```

**System.IO.Compression**: Built-in compression
```csharp
using System.IO.Compression;
```

### 2. Cross-Platform Utilities
**System.Runtime.InteropServices**: Platform detection
```csharp
using System.Runtime.InteropServices;
```

**System.Environment**: Environment information
```csharp
using System.Environment;
```

### 3. Testing Frameworks
**xUnit**: Unit testing
```bash
dotnet add package xunit
dotnet add package xunit.runner.visualstudio
```

**Moq**: Mocking framework
```bash
dotnet add package Moq
```

## 🔧 Development Environment Setup

### 1. Project Structure
```
BlockCore-SDK/
├─ src/
│   ├─ BlockCore-SDK.CLI/
│   ├─ BlockCore-SDK.Templates/
│   ├─ BlockCore-SDK.Validation/
│   ├─ BlockCore-SDK.Packaging/
│   └─ BlockCore-SDK.Installation/
├─ tests/
├─ templates/
├─ docs/
└─ BlockCore-SDK.sln
```

### 2. CLI Project Setup
```csharp
// Program.cs
using System.CommandLine;

var rootCommand = new RootCommand("BlockCore SDK CLI Tool");

var newCommand = new Command("new", "Create new project");
var typeArgument = new Argument<string>("type", "Project type");
var nameArgument = new Argument<string>("name", "Project name");

newCommand.AddArgument(typeArgument);
newCommand.AddArgument(nameArgument);
newCommand.SetHandler((type, name) => {
    Console.WriteLine($"Creating {type} project: {name}");
}, typeArgument, nameArgument);

rootCommand.AddCommand(newCommand);
return await rootCommand.InvokeAsync(args);
```

### 3. Template System Setup
```csharp
// TemplateEngine.cs
using HandlebarsDotNet;

public class TemplateEngine {
    private readonly IHandlebars handlebars;
    
    public TemplateEngine() {
        handlebars = Handlebars.Create();
    }
    
    public string ProcessTemplate(string template, object data) {
        var compiledTemplate = handlebars.Compile(template);
        return compiledTemplate(data);
    }
}
```

## 🌐 Cross-Platform Considerations

### Windows Development
- **Shell**: PowerShell, cmd.exe
- **Path Separator**: `\`
- **Package Manager**: Chocolatey
- **CLI Tools**: Windows Terminal

### Linux Development
- **Shell**: Bash, Zsh
- **Path Separator**: `/`
- **Package Manager**: apt, yum, pacman
- **CLI Tools**: Terminal emulator

### macOS Development
- **Shell**: Zsh, Bash
- **Path Separator**: `/`
- **Package Manager**: Homebrew
- **CLI Tools**: Terminal.app

## 📚 Additional Resources

### 1. CLI Design Guidelines
- **Microsoft CLI Guidelines**: https://docs.microsoft.com/en-us/dotnet/standard/commandline/
- **Command Line Interface Guidelines**: https://clig.dev/
- **Unix Philosophy**: https://en.wikipedia.org/wiki/Unix_philosophy

### 2. Template Engines
- **Handlebars Documentation**: https://handlebarsjs.com/
- **Razor Syntax**: https://docs.microsoft.com/en-us/aspnet/core/mvc/views/razor
- **Mustache Templates**: https://mustache.github.io/

### 3. JSON Schema
- **JSON Schema Specification**: https://json-schema.org/
- **Schema Validation**: https://json-schema.org/understanding-json-schema/
- **Online Validator**: https://www.jsonschemavalidator.net/

## 🚀 Verification Steps

### 1. Test .NET Installation
```bash
dotnet new console -n TestApp
cd TestApp
dotnet run
# Should print: Hello, World!
```

### 2. Test CLI Framework
```csharp
// Create simple CLI app
dotnet new console -n TestCLI
cd TestCLI
dotnet add package System.CommandLine
```

### 3. Test Template Engine
```csharp
// Test Handlebars
dotnet add package Handlebars.Net
// Create simple template test
```

### 4. Test Cross-Platform
```bash
# Test on different platforms
dotnet build
dotnet run -- --help
```

## 🎯 Next Steps

1. **Install Required Tools**: .NET 9 SDK, IDE, Git
2. **Set up CLI Project**: Create basic CLI structure
3. **Add Dependencies**: Install required NuGet packages
4. **Test Environment**: Verify everything works
5. **Start Learning**: [v0.1 Learning Guide](v0.1/what-you-will-learn.md)

## 🆘 Troubleshooting

### Common Issues

**"dotnet command not found"**:
- Add .NET SDK to PATH
- Restart terminal/IDE

**"Package restore failed"**:
- Check internet connection
- Clear NuGet cache: `dotnet nuget locals all --clear`

**"CLI not responding"**:
- Check command syntax
- Verify argument parsing
- Test with --help flag

**"Template processing failed"**:
- Check template syntax
- Verify data binding
- Test with simple template

---

**Ready to start coding? Head to [v0.1 Learning Guide](v0.1/what-you-will-learn.md)!**
