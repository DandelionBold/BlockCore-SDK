# Troubleshooting - Common Issues and Solutions

Solutions to common problems encountered during BlockCore-SDK development.

## 🚨 Common Issues

### 1. CLI Command Issues

#### "Command not found"
**Symptoms**: CLI tool doesn't recognize command
**Causes**: 
- Command not registered
- Typo in command name
- Command not implemented
- Help system not updated

**Solutions**:
```csharp
// Ensure command is registered
public class CommandRegistry {
    private Dictionary<string, ICommand> commands;
    
    public void RegisterCommand(ICommand command) {
        commands[command.Name] = command;
    }
    
    public ICommand GetCommand(string name) {
        if (commands.ContainsKey(name)) {
            return commands[name];
        }
        throw new CommandNotFoundException($"Command '{name}' not found");
    }
}

// Update help system
public class HelpGenerator {
    public string GenerateHelp() {
        var help = new StringBuilder();
        help.AppendLine("Available commands:");
        
        foreach (var command in commands.Values) {
            help.AppendLine($"  {command.Name} - {command.Description}");
        }
        
        return help.ToString();
    }
}
```

#### "Invalid arguments"
**Symptoms**: Command fails with argument errors
**Causes**:
- Missing required arguments
- Invalid argument values
- Incorrect argument order
- Type conversion errors

**Solutions**:
```csharp
// Validate arguments before processing
public class ArgumentValidator {
    public ValidationResult Validate(string[] args, CommandDefinition definition) {
        var errors = new List<string>();
        
        // Check required arguments
        for (int i = 0; i < definition.RequiredArguments.Count; i++) {
            if (i >= args.Length) {
                errors.Add($"Missing required argument: {definition.RequiredArguments[i].Name}");
            }
        }
        
        // Validate argument types
        for (int i = 0; i < args.Length && i < definition.Arguments.Count; i++) {
            var arg = args[i];
            var definition = definition.Arguments[i];
            
            if (!definition.Validate(arg)) {
                errors.Add($"Invalid value for {definition.Name}: {arg}");
            }
        }
        
        return new ValidationResult(errors);
    }
}
```

### 2. Template System Issues

#### "Template not found"
**Symptoms**: Template processing fails
**Causes**:
- Template file missing
- Incorrect template path
- Template not registered
- File system permissions

**Solutions**:
```csharp
// Check template existence
public class TemplateManager {
    private Dictionary<string, string> templates;
    
    public string GetTemplate(string name) {
        if (templates.ContainsKey(name)) {
            return templates[name];
        }
        
        // Try to load from file system
        var templatePath = Path.Combine("templates", $"{name}.hbs");
        if (File.Exists(templatePath)) {
            var content = File.ReadAllText(templatePath);
            templates[name] = content;
            return content;
        }
        
        throw new TemplateNotFoundException($"Template '{name}' not found");
    }
}
```

#### "Variable substitution failed"
**Symptoms**: Template variables not replaced
**Causes**:
- Missing variable values
- Incorrect variable names
- Template syntax errors
- Variable type mismatches

**Solutions**:
```csharp
// Safe variable substitution
public class TemplateProcessor {
    public string ProcessTemplate(string template, Dictionary<string, object> variables) {
        var result = template;
        
        foreach (var variable in variables) {
            var placeholder = $"{{{{{variable.Key}}}}}";
            var value = variable.Value?.ToString() ?? "";
            
            if (result.Contains(placeholder)) {
                result = result.Replace(placeholder, value);
            } else {
                LogWarning($"Variable '{variable.Key}' not found in template");
            }
        }
        
        // Check for unresolved placeholders
        var unresolved = Regex.Matches(result, @"\{\{(\w+)\}\}");
        foreach (Match match in unresolved) {
            LogWarning($"Unresolved placeholder: {match.Value}");
        }
        
        return result;
    }
}
```

### 3. Validation Issues

#### "Schema validation failed"
**Symptoms**: Project validation fails
**Causes**:
- Invalid JSON format
- Missing required fields
- Incorrect data types
- Schema version mismatch

**Solutions**:
```csharp
// Comprehensive validation
public class ProjectValidator {
    public ValidationResult ValidateProject(string projectPath) {
        var errors = new List<string>();
        
        try {
            // Check project structure
            if (!Directory.Exists(projectPath)) {
                errors.Add("Project directory does not exist");
                return new ValidationResult(errors);
            }
            
            // Validate manifest
            var manifestPath = Path.Combine(projectPath, "manifest.json");
            if (!File.Exists(manifestPath)) {
                errors.Add("Manifest file not found");
            } else {
                var manifestErrors = ValidateManifest(manifestPath);
                errors.AddRange(manifestErrors);
            }
            
            // Validate project structure
            var structureErrors = ValidateStructure(projectPath);
            errors.AddRange(structureErrors);
            
        } catch (Exception ex) {
            errors.Add($"Validation error: {ex.Message}");
        }
        
        return new ValidationResult(errors);
    }
}
```

#### "Validation rules not working"
**Symptoms**: Custom validation rules not applied
**Causes**:
- Rules not registered
- Rule logic errors
- Data format issues
- Rule execution order

**Solutions**:
```csharp
// Rule registration and execution
public class ValidationEngine {
    private List<IValidationRule> rules;
    
    public void RegisterRule(IValidationRule rule) {
        rules.Add(rule);
        LogInfo($"Registered validation rule: {rule.Name}");
    }
    
    public ValidationResult Validate(object data) {
        var errors = new List<string>();
        
        foreach (var rule in rules) {
            try {
                var result = rule.Validate(data);
                if (!result.IsValid) {
                    errors.AddRange(result.Errors);
                }
            } catch (Exception ex) {
                errors.Add($"Rule '{rule.Name}' failed: {ex.Message}");
            }
        }
        
        return new ValidationResult(errors);
    }
}
```

### 4. Packaging Issues

#### "Package creation failed"
**Symptoms**: Package building fails
**Causes**:
- File access permissions
- Disk space issues
- Invalid file paths
- Compression errors

**Solutions**:
```csharp
// Safe package creation
public class PackageBuilder {
    public void CreatePackage(string sourcePath, string outputPath) {
        try {
            // Check disk space
            var drive = new DriveInfo(Path.GetPathRoot(outputPath));
            if (drive.AvailableFreeSpace < 100 * 1024 * 1024) { // 100MB
                throw new InsufficientDiskSpaceException("Not enough disk space");
            }
            
            // Create output directory
            var outputDir = Path.GetDirectoryName(outputPath);
            if (!Directory.Exists(outputDir)) {
                Directory.CreateDirectory(outputDir);
            }
            
            // Build package
            using (var archive = ZipFile.Open(outputPath, ZipArchiveMode.Create)) {
                AddFilesToArchive(archive, sourcePath);
            }
            
        } catch (Exception ex) {
            LogError($"Package creation failed: {ex.Message}");
            throw;
        }
    }
}
```

#### "Package installation failed"
**Symptoms**: Package installation fails
**Causes**:
- Corrupted package file
- Insufficient permissions
- Disk space issues
- Dependency conflicts

**Solutions**:
```csharp
// Safe package installation
public class PackageInstaller {
    public InstallationResult InstallPackage(string packagePath, string installPath) {
        try {
            // Validate package
            if (!File.Exists(packagePath)) {
                return InstallationResult.Failure("Package file not found");
            }
            
            // Check package integrity
            var manifest = ExtractManifest(packagePath);
            if (manifest == null) {
                return InstallationResult.Failure("Invalid package format");
            }
            
            // Check disk space
            var drive = new DriveInfo(Path.GetPathRoot(installPath));
            if (drive.AvailableFreeSpace < 50 * 1024 * 1024) { // 50MB
                return InstallationResult.Failure("Insufficient disk space");
            }
            
            // Install package
            var installDir = Path.Combine(installPath, manifest.Name);
            ExtractPackage(packagePath, installDir);
            RegisterPackage(manifest);
            
            return InstallationResult.Success("Package installed successfully");
            
        } catch (Exception ex) {
            return InstallationResult.Failure($"Installation failed: {ex.Message}");
        }
    }
}
```

### 5. Cross-Platform Issues

#### "Path separator issues"
**Symptoms**: File paths not working on different platforms
**Causes**:
- Hardcoded path separators
- Platform-specific path formats
- Case sensitivity differences
- Path length limitations

**Solutions**:
```csharp
// Cross-platform path handling
public class PathHelper {
    public static string NormalizePath(string path) {
        return Path.GetFullPath(path);
    }
    
    public static string CombinePaths(params string[] paths) {
        return Path.Combine(paths);
    }
    
    public static bool IsValidPath(string path) {
        try {
            Path.GetFullPath(path);
            return true;
        } catch {
            return false;
        }
    }
    
    public static string GetRelativePath(string fromPath, string toPath) {
        var fromUri = new Uri(fromPath);
        var toUri = new Uri(toPath);
        return fromUri.MakeRelativeUri(toUri).ToString();
    }
}
```

#### "Platform-specific behavior"
**Symptoms**: Different behavior on different platforms
**Causes**:
- Platform-specific APIs
- Different file system behaviors
- Environment variable differences
- Permission model differences

**Solutions**:
```csharp
// Platform detection and handling
public class PlatformHelper {
    public static bool IsWindows => RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
    public static bool IsLinux => RuntimeInformation.IsOSPlatform(OSPlatform.Linux);
    public static bool IsMacOS => RuntimeInformation.IsOSPlatform(OSPlatform.OSX);
    
    public static string GetConfigPath() {
        var home = Environment.GetFolderPath(Environment.SpecialFolder.UserProfile);
        
        if (IsWindows) {
            return Path.Combine(home, "AppData", "Local", "BlockCore-SDK");
        } else {
            return Path.Combine(home, ".blockcore-sdk");
        }
    }
    
    public static string GetExecutableExtension() {
        return IsWindows ? ".exe" : "";
    }
}
```

## 🔧 Debugging Techniques

### 1. CLI Debugging

#### Command Execution Logging
```csharp
public class CommandLogger {
    private List<string> commandLog = new List<string>();
    
    public void LogCommand(string command, string[] args) {
        var logEntry = $"{DateTime.Now:HH:mm:ss.fff}: {command} {string.Join(" ", args)}";
        commandLog.Add(logEntry);
        
        if (commandLog.Count > 1000) {
            commandLog.RemoveAt(0);
        }
    }
    
    public void PrintCommandLog() {
        foreach (var entry in commandLog) {
            Console.WriteLine(entry);
        }
    }
}
```

#### Argument Parsing Debug
```csharp
public class ArgumentDebugger {
    public void DebugArguments(string[] args) {
        Console.WriteLine($"Raw arguments: {string.Join(" ", args)}");
        Console.WriteLine($"Argument count: {args.Length}");
        
        for (int i = 0; i < args.Length; i++) {
            Console.WriteLine($"  [{i}] = '{args[i]}'");
        }
    }
}
```

### 2. Template Debugging

#### Template Processing Debug
```csharp
public class TemplateDebugger {
    public void DebugTemplate(string templateName, Dictionary<string, object> variables) {
        Console.WriteLine($"Processing template: {templateName}");
        Console.WriteLine("Variables:");
        
        foreach (var variable in variables) {
            Console.WriteLine($"  {variable.Key} = {variable.Value}");
        }
        
        var template = GetTemplate(templateName);
        Console.WriteLine($"Template content:\n{template}");
    }
}
```

### 3. Validation Debugging

#### Validation Rule Debug
```csharp
public class ValidationDebugger {
    public void DebugValidation(object data, List<IValidationRule> rules) {
        Console.WriteLine("Validation Debug:");
        Console.WriteLine($"Data type: {data.GetType().Name}");
        
        foreach (var rule in rules) {
            Console.WriteLine($"Testing rule: {rule.Name}");
            var result = rule.Validate(data);
            Console.WriteLine($"  Result: {(result.IsValid ? "PASS" : "FAIL")}");
            
            if (!result.IsValid) {
                foreach (var error in result.Errors) {
                    Console.WriteLine($"    Error: {error}");
                }
            }
        }
    }
}
```

## 🚀 Performance Optimization

### 1. CLI Performance

#### Command Execution Optimization
```csharp
public class CommandOptimizer {
    private Dictionary<string, ICommand> commandCache;
    
    public ICommand GetCommand(string name) {
        if (!commandCache.ContainsKey(name)) {
            commandCache[name] = CreateCommand(name);
        }
        return commandCache[name];
    }
    
    private ICommand CreateCommand(string name) {
        // Lazy command creation
        return commandFactory.CreateCommand(name);
    }
}
```

#### Template Caching
```csharp
public class TemplateCache {
    private Dictionary<string, string> templateCache;
    private Dictionary<string, DateTime> templateTimestamps;
    
    public string GetTemplate(string name) {
        if (!templateCache.ContainsKey(name)) {
            LoadTemplate(name);
        }
        
        // Check if template file has been modified
        var templatePath = GetTemplatePath(name);
        var lastWrite = File.GetLastWriteTime(templatePath);
        
        if (templateTimestamps[name] < lastWrite) {
            LoadTemplate(name);
        }
        
        return templateCache[name];
    }
}
```

### 2. Memory Optimization

#### Resource Management
```csharp
public class ResourceManager : IDisposable {
    private List<IDisposable> resources;
    private bool disposed = false;
    
    public void AddResource(IDisposable resource) {
        resources.Add(resource);
    }
    
    public void Dispose() {
        if (!disposed) {
            foreach (var resource in resources) {
                resource?.Dispose();
            }
            resources.Clear();
            disposed = true;
        }
    }
}
```

## 🎯 Best Practices

### 1. Error Handling
- Always wrap CLI operations in try-catch blocks
- Provide meaningful error messages
- Log errors with context information
- Use appropriate exit codes

### 2. User Experience
- Provide clear help and usage information
- Use consistent command syntax
- Give helpful error messages
- Support both short and long options

### 3. Performance
- Cache frequently used data
- Use lazy loading for expensive operations
- Optimize file I/O operations
- Monitor memory usage

### 4. Testing
- Test on all target platforms
- Use automated testing for CLI commands
- Test error conditions and edge cases
- Validate cross-platform compatibility

---

**Still having issues? Check the [CLI Patterns](cli-patterns.md) for architectural solutions!**
