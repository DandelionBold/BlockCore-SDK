# CLI Patterns - Common CLI Design Patterns

Essential CLI design patterns for BlockCore-SDK development, explained for enterprise developers.

## 🛠️ Core CLI Patterns

### 1. Command Pattern
**Purpose**: Encapsulate operations as objects for execution

**Enterprise Analogy**: Like command pattern in enterprise apps, but for CLI

**Structure**:
```csharp
public interface ICommand {
    string Name { get; }
    string Description { get; }
    void Execute(string[] args);
}

public class NewCommand : ICommand {
    public string Name => "new";
    public string Description => "Create new project";
    
    public void Execute(string[] args) {
        var projectType = args[0];
        var projectName = args[1];
        CreateProject(projectType, projectName);
    }
}
```

**Benefits**:
- Consistent command interface
- Easy to add new commands
- Testable command logic
- Centralized command management

### 2. Factory Pattern
**Purpose**: Create commands without specifying exact classes

**Enterprise Analogy**: Like factory pattern for object creation

**Structure**:
```csharp
public class CommandFactory {
    private Dictionary<string, Func<ICommand>> creators;
    
    public ICommand CreateCommand(string name) {
        if (creators.ContainsKey(name)) {
            return creators[name]();
        }
        throw new ArgumentException($"Unknown command: {name}");
    }
    
    public void RegisterCommand(string name, Func<ICommand> creator) {
        creators[name] = creator;
    }
}
```

**Benefits**:
- Flexible command creation
- Easy to extend with new commands
- Centralized command registration
- Lazy command instantiation

### 3. Builder Pattern
**Purpose**: Construct complex objects step by step

**Enterprise Analogy**: Like configuration builders

**Structure**:
```csharp
public class ProjectBuilder {
    private Project project;
    
    public ProjectBuilder SetName(string name) {
        project.Name = name;
        return this;
    }
    
    public ProjectBuilder SetType(ProjectType type) {
        project.Type = type;
        return this;
    }
    
    public ProjectBuilder AddTemplate(string template) {
        project.Templates.Add(template);
        return this;
    }
    
    public Project Build() {
        return project;
    }
}
```

**Benefits**:
- Clear object construction
- Flexible parameter handling
- Fluent interface
- Immutable final objects

## 🎯 Argument Processing Patterns

### 4. Argument Parser Pattern
**Purpose**: Parse and validate command-line arguments

**Enterprise Analogy**: Like request parsing in web APIs

**Structure**:
```csharp
public class ArgumentParser {
    public ParsedArguments Parse(string[] args) {
        var result = new ParsedArguments();
        
        for (int i = 0; i < args.Length; i++) {
            if (args[i].StartsWith("--")) {
                // Long option
                var option = args[i].Substring(2);
                var value = i + 1 < args.Length ? args[i + 1] : null;
                result.Options[option] = value;
                if (value != null) i++; // Skip value
            } else if (args[i].StartsWith("-")) {
                // Short option
                var option = args[i].Substring(1);
                result.Options[option] = "true";
            } else {
                // Positional argument
                result.Arguments.Add(args[i]);
            }
        }
        
        return result;
    }
}
```

**Benefits**:
- Consistent argument parsing
- Support for different argument formats
- Validation and error handling
- Clear argument structure

### 5. Option Validation Pattern
**Purpose**: Validate command-line options and arguments

**Enterprise Analogy**: Like input validation in web forms

**Structure**:
```csharp
public class OptionValidator {
    private Dictionary<string, OptionDefinition> options;
    
    public ValidationResult Validate(ParsedArguments args) {
        var errors = new List<string>();
        
        foreach (var option in args.Options) {
            if (!options.ContainsKey(option.Key)) {
                errors.Add($"Unknown option: {option.Key}");
            } else {
                var definition = options[option.Key];
                if (!definition.Validate(option.Value)) {
                    errors.Add($"Invalid value for {option.Key}: {option.Value}");
                }
            }
        }
        
        return new ValidationResult(errors);
    }
}
```

**Benefits**:
- Centralized validation logic
- Clear error messages
- Reusable validation rules
- Type-safe option handling

## 🔧 Template Processing Patterns

### 6. Template Engine Pattern
**Purpose**: Process templates with variable substitution

**Enterprise Analogy**: Like code generators or scaffolding tools

**Structure**:
```csharp
public class TemplateEngine {
    private Dictionary<string, string> templates;
    
    public string ProcessTemplate(string templateName, Dictionary<string, object> variables) {
        if (!templates.ContainsKey(templateName)) {
            throw new ArgumentException($"Template not found: {templateName}");
        }
        
        var template = templates[templateName];
        return ProcessTemplate(template, variables);
    }
    
    private string ProcessTemplate(string template, Dictionary<string, object> variables) {
        var result = template;
        
        foreach (var variable in variables) {
            var placeholder = $"{{{{{variable.Key}}}}}";
            result = result.Replace(placeholder, variable.Value?.ToString() ?? "");
        }
        
        return result;
    }
}
```

**Benefits**:
- Flexible template processing
- Variable substitution
- Template inheritance
- Error handling

### 7. File Generator Pattern
**Purpose**: Generate files from templates

**Enterprise Analogy**: Like document generation systems

**Structure**:
```csharp
public class FileGenerator {
    private TemplateEngine templateEngine;
    
    public void GenerateFile(string templateName, string outputPath, Dictionary<string, object> variables) {
        var content = templateEngine.ProcessTemplate(templateName, variables);
        
        var directory = Path.GetDirectoryName(outputPath);
        if (!string.IsNullOrEmpty(directory)) {
            Directory.CreateDirectory(directory);
        }
        
        File.WriteAllText(outputPath, content);
    }
    
    public void GenerateProject(ProjectTemplate template, string outputPath, ProjectConfig config) {
        foreach (var fileTemplate in template.Files) {
            var filePath = Path.Combine(outputPath, fileTemplate.Path);
            GenerateFile(fileTemplate.TemplateName, filePath, config.Variables);
        }
    }
}
```

**Benefits**:
- Automated file generation
- Consistent project structure
- Template-based content
- Directory management

## 📦 Package Management Patterns

### 8. Package Builder Pattern
**Purpose**: Create distributable packages

**Enterprise Analogy**: Like build systems or deployment packages

**Structure**:
```csharp
public class PackageBuilder {
    private string packagePath;
    private List<string> files;
    private PackageManifest manifest;
    
    public PackageBuilder SetManifest(PackageManifest manifest) {
        this.manifest = manifest;
        return this;
    }
    
    public PackageBuilder AddFile(string filePath) {
        files.Add(filePath);
        return this;
    }
    
    public PackageBuilder AddDirectory(string directoryPath) {
        var directoryFiles = Directory.GetFiles(directoryPath, "*", SearchOption.AllDirectories);
        files.AddRange(directoryFiles);
        return this;
    }
    
    public void Build(string outputPath) {
        using (var archive = ZipFile.Open(outputPath, ZipArchiveMode.Create)) {
            // Add manifest
            var manifestEntry = archive.CreateEntry("manifest.json");
            using (var stream = manifestEntry.Open()) {
                JsonSerializer.Serialize(stream, manifest);
            }
            
            // Add files
            foreach (var file in files) {
                archive.CreateEntryFromFile(file, Path.GetFileName(file));
            }
        }
    }
}
```

**Benefits**:
- Consistent package creation
- Manifest generation
- File inclusion/exclusion
- Compression and optimization

### 9. Package Installer Pattern
**Purpose**: Install packages to local environment

**Enterprise Analogy**: Like software installation or deployment

**Structure**:
```csharp
public class PackageInstaller {
    private string installPath;
    
    public InstallationResult Install(string packagePath) {
        try {
            var manifest = ExtractManifest(packagePath);
            ValidatePackage(manifest);
            
            var installDir = Path.Combine(installPath, manifest.Name);
            Directory.CreateDirectory(installDir);
            
            ExtractFiles(packagePath, installDir);
            RegisterPackage(manifest);
            
            return new InstallationResult(true, "Package installed successfully");
        } catch (Exception ex) {
            return new InstallationResult(false, ex.Message);
        }
    }
    
    private PackageManifest ExtractManifest(string packagePath) {
        using (var archive = ZipFile.OpenRead(packagePath)) {
            var manifestEntry = archive.GetEntry("manifest.json");
            using (var stream = manifestEntry.Open()) {
                return JsonSerializer.Deserialize<PackageManifest>(stream);
            }
        }
    }
}
```

**Benefits**:
- Safe package installation
- Validation and error handling
- Package registration
- Rollback capabilities

## 🔍 Validation Patterns

### 10. Schema Validation Pattern
**Purpose**: Validate data against defined schemas

**Enterprise Analogy**: Like data validation in enterprise systems

**Structure**:
```csharp
public class SchemaValidator {
    private Dictionary<string, JsonSchema> schemas;
    
    public ValidationResult Validate(string data, string schemaName) {
        if (!schemas.ContainsKey(schemaName)) {
            throw new ArgumentException($"Schema not found: {schemaName}");
        }
        
        var schema = schemas[schemaName];
        var document = JsonDocument.Parse(data);
        
        var errors = new List<ValidationError>();
        ValidateDocument(document.RootElement, schema, errors);
        
        return new ValidationResult(errors);
    }
    
    private void ValidateDocument(JsonElement element, JsonSchema schema, List<ValidationError> errors) {
        // Implement schema validation logic
        foreach (var property in schema.Properties) {
            if (!element.TryGetProperty(property.Key, out var propElement)) {
                if (property.Value.Required) {
                    errors.Add(new ValidationError($"Missing required property: {property.Key}"));
                }
            } else {
                ValidateProperty(propElement, property.Value, errors);
            }
        }
    }
}
```

**Benefits**:
- Centralized validation logic
- Reusable validation rules
- Clear error reporting
- Schema evolution support

### 11. Rule Engine Pattern
**Purpose**: Apply custom validation rules

**Enterprise Analogy**: Like business rule engines

**Structure**:
```csharp
public interface IValidationRule {
    string Name { get; }
    ValidationResult Validate(object data);
}

public class ProjectNameRule : IValidationRule {
    public string Name => "ProjectName";
    
    public ValidationResult Validate(object data) {
        var project = data as Project;
        if (project == null) return ValidationResult.Success;
        
        if (string.IsNullOrEmpty(project.Name)) {
            return ValidationResult.Failure("Project name is required");
        }
        
        if (!Regex.IsMatch(project.Name, @"^[a-zA-Z][a-zA-Z0-9_-]*$")) {
            return ValidationResult.Failure("Project name must start with letter and contain only letters, numbers, hyphens, and underscores");
        }
        
        return ValidationResult.Success;
    }
}

public class RuleEngine {
    private List<IValidationRule> rules;
    
    public ValidationResult Validate(object data) {
        var errors = new List<string>();
        
        foreach (var rule in rules) {
            var result = rule.Validate(data);
            if (!result.IsValid) {
                errors.AddRange(result.Errors);
            }
        }
        
        return new ValidationResult(errors);
    }
}
```

**Benefits**:
- Flexible validation rules
- Easy to add new rules
- Reusable rule components
- Clear rule separation

## 🎯 When to Use Each Pattern

### Core Patterns (Always Use)
- **Command Pattern**: Every CLI needs commands
- **Factory Pattern**: For flexible command creation
- **Builder Pattern**: For complex object construction

### Argument Patterns (Use for Input)
- **Argument Parser**: For command-line argument processing
- **Option Validation**: For input validation

### Template Patterns (Use for Generation)
- **Template Engine**: For content generation
- **File Generator**: For project scaffolding

### Package Patterns (Use for Distribution)
- **Package Builder**: For creating distributable packages
- **Package Installer**: For local installation

### Validation Patterns (Use for Quality)
- **Schema Validation**: For data validation
- **Rule Engine**: For custom validation rules

## 🚀 Implementation Tips

### Start Simple
- Begin with basic command structure
- Add complexity gradually
- Test with real users

### Focus on User Experience
- Prioritize ease of use
- Provide clear error messages
- Include comprehensive help

### Learn by Using
- Use existing CLI tools
- Understand what makes them good
- Apply lessons to your own tools

### Ask Questions
- Join developer communities
- Read CLI documentation
- Watch tooling tutorials

---

**Need help with specific patterns? Check the [Troubleshooting](troubleshooting.md) guide!**
