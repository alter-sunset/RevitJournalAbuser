# RevitJournalAbuser

A small utility library that leverages the Autodesk Revit *Journal* mechanism to execute actions that are not exposed through the official Revit API.

The library provides helper classes for generating Journal scripts, executing them in a separate Revit session, and safely handling temporary resources.

## What is a Revit Journal?

Each Revit session generates a **Journal file** that records every action performed during that session.

These files are primarily intended for debugging. When Revit is launched with a Journal file as a startup argument, it **replays the recorded actions automatically**.

Internally, Journal files use a syntax similar to modified VB scripting.

This library leverages that mechanism to programmatically execute commands that are otherwise inaccessible through the Revit API (for example, retrieving Synchronization history).


## Core Classes

### Journal
Creates and writes a Journal script that will later be executed by Revit.
```c#
using Journal journal = new(scriptPath);
```

### TempDirectory
Creates an isolated temporary working directory (inside the user's `Temp` folder) where Journal files and generated resources are stored.

The directory is automatically cleaned up when disposed.
```c#
public class ReportsTempDirectory: TempDirectory
{
    private string ReportsDirectory => Path.Combine(Dir, "Reports");    
    private IEnumerable<string> Reports => Directory.EnumerateFiles(ReportsDirectory);
    public ReportsTempDirectory (IEnumerable<string> files)
    {
        Directory.CreateDirectory(ReportsDirectory);        
        string[] fileScripts = files
            .Select(file => file.CreatePartitionHistory(ReportsDirectory))
            .ToArray();        
        using Journal journal = new(Script, fileScripts);
    }
}
```

### Abuser
Launches a new Revit process with the generated Journal script.
```c#
using Abuser abuser = new(2023, tempDirectory);
abuser.RunJournalScript();
abuser.WaitForExit();
```

### Scripts
Contains predefined Journal script templates for common tasks.
```c#
string scriptPart = Scripts.CreatePartitionHistory(filePath, reportsDirectory)
```
