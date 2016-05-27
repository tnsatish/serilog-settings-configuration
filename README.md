# Serilog.Settings.Configuration [![Build status](https://ci.appveyor.com/api/projects/status/r2bgfimd9ocr61px/branch/master?svg=true)](https://ci.appveyor.com/project/serilog/serilog-settings-configuration/branch/master)

A Serilog settings provider that reads from _Microsoft.Extensions.Configuration_, .NET Core's `appsettings.json` file.

Configuration is read from the `Serilog` section.

```json
{
  "Serilog": {
    "Using":  ["Serilog.Sinks.Literate"],
    "MinimumLevel": "Debug",
    "WriteTo": [
      { "Name": "LiterateConsole" },
      { "Name": "File", "Args": { "path": "%TEMP%\\Logs\\serilog-configuration-sample.txt" } }
    ],
    "Enrich": ["FromLogContext", "WithMachineName", "WithThreadId"],
    "Properties": {
		"Application": "Sample"
    }
  }
}
```

This example relies on the _Serilog.Sinks.Literate_, _Serilog.Sinks.File_, _Serilog.Enrichers.Environment_ and _Serilog.Sinks.Thread_ packages also being installed.

After installing this package, use `ReadFrom.Configuration()` and pass an `IConfiguration` object.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var configuration = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .Build();

        var logger = new LoggerConfiguration()
            .ReadFrom.Configuration(configuration)
            .CreateLogger();

        logger.Information("Hello, world!");
    }
}
```

The `WriteTo` and `Enrich` sections support the same syntax, for example the following is valid if no arguments are needed by the sinks:

```json
"WriteTo": ["LiterateConsole", "DiagnosticTrace"]
```

Or alternatively, the long-form (`"Name":` ...) sytax from the first example can be used when arguments need to be supplied.

(This package implements a convention using `DependencyContext` to find any package with `Serilog` anywhere in the name and pulls configuration methods from it, so the `Using` example above is redundant.)

### .NET 4.x

To use this package in .NET 4.x applications, add `preserveCompilationContext` to `buildOptions` in _project.json_.

```json
"net4.6": {
   "buildOptions": {
     "preserveCompilationContext": true
   }
},
```