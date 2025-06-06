---
description: Function
Locale: en-US
ms.date: 03/06/2025
online version: https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_function_provider?view=powershell-7.4&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about_Function_Provider
---
# about_Function_Provider

## Provider name

Function

## Drives

`Function:`

## Capabilities

**ShouldProcess**

## Short description

Provides access to the functions defined in PowerShell.

## Detailed description

The PowerShell **Function** provider lets you get, add, change, clear, and
delete the functions and filters in PowerShell.

A function is a named block of code that performs an action. When you type the
function name, the code in the function runs. A filter is a named block of code
that establishes conditions for an action. You can type the name of the filter
in place of the condition, such as in a `Where-Object` command.

The **Function** drive is a flat namespace that contains only the function
and filter objects. Neither functions nor filters have child items.

The **Function** provider supports the following cmdlets, which are covered
in this article.

- [Get-Location][01]
- [Set-Location][02]
- [Get-Item][03]
- [New-Item][04]
- [Remove-Item][05]
- [Clear-Item][06]

## Types exposed by this provider

Each function is an instance of the
[System.Management.Automation.FunctionInfo][07] class. Each filter is an
instance of the [System.Management.Automation.FilterInfo][08] class.

## Navigating the Function drive

The **Function** provider exposes its data store in the `Function:` drive. To
work with functions, you can change your location to the `Function:` drive
(`Set-Location Function:`). Or, you can work from another PowerShell drive. To
reference a function from another location, use the drive name (`Function:`) in
the path.

```powershell
Set-Location Function:
```

To return to a file system drive, type the drive name. For example, type:

```powershell
Set-Location C:
```

You can also work with the **Function** provider from any other PowerShell
drive. To reference a function from another location, use the drive name
`Function:` in the path.

> [!NOTE]
> PowerShell uses aliases to allow you a familiar way to work with provider
> paths. Commands such as `dir` and `ls` are now aliases for
> [Get-ChildItem][09], `cd` is an alias for [Set-Location][10] and `pwd` is an
> alias for [Get-Location][01].

## Getting functions

This command gets the list of all the functions in the current session. You can
use this command from any PowerShell drive.

```powershell
Get-ChildItem -Path Function:
```

The Function provider has no containers, so the above command has the
same effect when used with `Get-ChildItem`.

```powershell
Get-ChildItem -Path Function:
```

You can retrieve a function's definition by accessing the **Definition**
property, as shown below.

```powershell
(Get-Item -Path Function:more).Definition
```

You can also retrieve a function's definition using its provider path prefixed
by the dollar sign (`$`).

```powershell
$Function:more
```

To retrieve the definition for a function that has a dash (`-`) in the name,
wrap the value after the dollar sign in curly braces.

```powershell
${Function:Clear-Host}
```

### Getting selected functions

This command gets the `man` function from the `Function:` drive. It uses the
`Get-Item` cmdlet to get the function. The pipeline operator (`|`) sends the
result to `Format-Table`. The `-Wrap` parameter directs text that does not fit
on the line onto the next line. The `-AutoSize` parameter resizes the table
columns to accommodate the text.

```powershell
Get-Item -Path man | Format-Table -Wrap -AutoSize
```

### Working with Function provider paths

These commands both get the function named `C:`. The first command can be used
in any drive. The second command is used in the `Function:` drive. Because the
name ends in a colon, which is the syntax for a drive, you must qualify the
path with the drive name. Within the `Function:` drive, you can use either
format. In the second command, the dot (`.`) represents the current location.

```
PS C:\> Get-Item -Path Function:c:
PS Function:\> Get-Item -Path .\c:
```

## Creating a function

This command uses the `New-Item` cmdlet to create a function called `Win32:`.
The expression in braces is the script block that is represented by the
function name.

```powershell
New-Item -Path Function:Win32: -Value {Set-Location C:\Windows\System32}
```

### Scopes

Just like variables, functions belong to a specific scope. When you create a
function, it is available only in the scope in which it was created. To make a
function available, use a scope modifier when you create the function. For more
information, see [about_Scopes][15].

The following example uses the `Global:` scope modifier to create a function in
the global scope.

```powershell
function New-Function {
    param(
        [string] $Name,
        [scriptblock] $Script
    )

    $lp = "Function:\Global:$($Name)"
    Set-Item -LiteralPath $lp -Value $Script -PassThru -Force
}

New-Function -Name 'Win32:' -Script { Set-Location C:\Windows\System32 }
```

Without the `Global:` scope modifier, the function would be created in the
local scope. When `New-Function` exits the newly created function would no
longer exist.

## Deleting a function

This command deletes the `more` function from the current session.

```powershell
Remove-Item Function:more
```

## Changing a function

This command uses the `Set-Item` cmdlet to change the `prompt` function so that
it displays the time before the path.

```powershell
Set-Item -Path Function:prompt -Value {
  'PS '+ (Get-Date -Format t) + " " + (Get-Location) + '> '
  }
```

### Rename a function

This command uses the `Rename-Item` cmdlet to change the name of the `help`
function to `gh`.

```powershell
Rename-Item -Path Function:help -NewName gh
```

## Copying a function

This command copies the `prompt` function to `oldPrompt`, effectively creating
a new name for the script block that is associated with the prompt function.
You can use this to save the original prompt function if you plan to change it.
The **Options** property of the new function has a value of `None`. To change
the value of the **Options** property, use `Set-Item`.

```powershell
Copy-Item -Path Function:prompt -Destination Function:oldPrompt
```

## Dynamic parameters

Dynamic parameters are cmdlet parameters that are added by a PowerShell
provider and are available only when the cmdlet is being used in the
provider-enabled drive.

### Options <[System.Management.Automation.ScopedItemOptions]>

Determines the value of the **Options** property of a function.

- `None`: No options. `None` is the default.
- `Constant`: The function cannot be deleted, and its properties cannot be
  changed. `Constant` is available only when you are creating a function.
  You cannot change the option of an existing function to `Constant`.
- `Private`: The function is visible only in the current scope
- (not in child scopes).
- `ReadOnly`: The properties of the function cannot be changed except by
   using the `-Force` parameter. You can use `Remove-Item` to delete the
   function.
- `AllScope`: The function is copied to any new scopes that are created.

### Cmdlets supported

- [New-Item][04]

- [Set-Item][11]

## Using the pipeline

Provider cmdlets accept pipeline input. You can use the pipeline to simplify
task by sending provider data from one cmdlet to another provider cmdlet.
To read more about how to use the pipeline with provider cmdlets, see the
cmdlet references provided throughout this article.

## Getting help

Beginning in Windows PowerShell 3.0, you can get customized help topics for
provider cmdlets that explain how those cmdlets behave in a file system drive.

To get the help topics that are customized for the file system drive, run a
[Get-Help][12] command in a file system drive or use the `-Path` parameter of
[Get-Help][12] to specify a file system drive.

```powershell
Get-Help Get-ChildItem
```

```powershell
Get-Help Get-ChildItem -Path Function:
```

## See also

- [about_Functions][13]
- [about_Providers][14]

<!-- link references -->
[01]: xref:Microsoft.PowerShell.Management.Get-Location
[02]: xref:Microsoft.PowerShell.Management.Set-Location
[03]: xref:Microsoft.PowerShell.Management.Get-Item
[04]: xref:Microsoft.PowerShell.Management.New-Item
[05]: xref:Microsoft.PowerShell.Management.Remove-Item
[06]: xref:Microsoft.PowerShell.Management.Clear-Item
[07]: /dotnet/api/system.management.automation.functioninfo
[08]: /dotnet/api/system.management.automation.filterinfo
[09]: xref:Microsoft.PowerShell.Management.Get-ChildItem
[10]: xref:Microsoft.PowerShell.Management.Set-Location
[11]: xref:Microsoft.PowerShell.Management.Set-Item
[12]: xref:Microsoft.PowerShell.Core.Get-Help
[13]: about_Functions.md
[14]: about_Providers.md
[15]: about_Scopes.md
