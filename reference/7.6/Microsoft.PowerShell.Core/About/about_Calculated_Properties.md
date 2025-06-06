---
description: PowerShell provides the ability to dynamically add new properties and alter the formatting of objects output to the pipeline.
Locale: en-US
ms.date: 09/03/2024
online version: https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_calculated_properties?view=powershell-7.6&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about_Calculated_Properties
---
# about_Calculated_Properties

## Short description

PowerShell provides the ability to dynamically add new properties and alter
the formatting of objects output to the pipeline.

## Long description

Several PowerShell cmdlets transform, group, or process input objects into
output objects using parameters that allow the addition of new properties to
those output objects. You can use these parameters to generate new, calculated
properties on output objects based on the values of input objects. The
calculated property is defined by a [hashtable][03] containing key-value pairs
that specify the name of the new property, an expression to calculate the
value, and optional formatting information.

## Supported cmdlets

The following cmdlets support calculated property values for the **Property**
parameter. The `Format-*` cmdlets also support calculated values for the
**GroupBy** parameter.

The following list itemizes the cmdlets that support calculated properties and
the key-value pairs that each cmdlet supports.

- `Compare-Object`
  - `Expression`

- `ConvertTo-Html`
  - `Name`/`Label` - optional (added in PowerShell 6.x)
  - `Expression`
  - `Width` - optional
  - `Alignment` - optional

- `Format-Custom`
  - `Expression`
  - `Depth` - optional

- `Format-List`
  - `Name`/`Label` - optional
  - `Expression`
  - `FormatString` - optional

  This same set of key-value pairs also apply to calculated property values
  passed to the **GroupBy** parameter for all `Format-*` cmdlets.

- `Format-Table`
  - `Name`/`Label` - optional
  - `Expression`
  - `FormatString` - optional
  - `Width` - optional
  - `Alignment` - optional

- `Format-Wide`
  - `Expression`
  - `FormatString` - optional

- `Group-Object`
  - `Expression`

- `Measure-Object`
  - Only supports a script block for the expression, not a hashtable.
  - Not supported in PowerShell 5.1 and older.

- `Select-Object`
  - `Name`/`Label` - optional
  - `Expression`

- `Sort-Object`
  - `Expression`
  - `Ascending`/`Descending` - optional

> [!NOTE]
> The value of the `Expression` can be a script block instead of a
> hashtable. For more information, see the [Notes][02] section.

## Hashtable key definitions

- `Name`/`Label` - Specifies the name of the property being created. You can
  use `Name` or its alias, `Label`, interchangeably.
- `Expression` - A string or script block used to calculate the value of the
  new property. If the `Expression` is a string, the value is interpreted as a
  property name on the input object. This is a shorter option than
  `Expression = { $_.<PropertyName> }`.
- `Alignment` - Used by cmdlets that produce tabular output to define how the
  values are displayed in a column. The value must be `'Left'`, `'Center'`, or
  `'Right'`.
- `FormatString` - Specifies a format string that defines how the value is
  formatted for output. For more information about format strings, see
  [Format types in .NET][01].
- `Width` - Specifies the maximum width column in a table when the value is
  displayed. The value must be greater than `0`.
- `Depth` - The **Depth** parameter of `Format-Custom` specifies the depth of
  expansion for all properties. The `Depth` key allows you to specify the
  depth of expansion per property.
- `Ascending` / `Descending` - Allows you to specify the order of sorting for
  one or more properties. These are boolean values.

You don't need to spell out the hashtable keys as long as the specified name
prefix is unambiguous. For example, you can use `n` instead of `Name` and `e`
instead of `Expression`.

## Examples

### Compare-Object

With calculated properties, you can control how the properties of the input
objects are compared. In this example, rather than comparing the values
directly, the values are compared to the result of the arithmetic operation
(modulus of 2).

```powershell
Compare-Object @{p=1} @{p=2} -Property @{ Expression = { $_.p % 2 } }
```

```Output
 $_.p % 2  SideIndicator
---------- -------------
         0 =>
         1 <=
```

### ConvertTo-Html

`ConvertTo-Html` can convert a collection of objects to an HTML table.
Calculated properties allow you to control how the table is presented.

```powershell
Get-Alias |
  ConvertTo-Html Name,
                 Definition,
                 @{
                    Name='ParameterCount'
                    Expr={$_.Parameters.Keys.Count}
                    Align='Center'
                 } |
    Out-File .\aliases.htm -Force
```

This example creates an HTML table containing a list of PowerShell aliases and
the number parameters for each aliased command. The values of
**ParameterCount** column are centered.

### Format-Custom

`Format-Custom` provides a custom view of an object in a format similar to a
class definition. More complex objects can contain members that are deeply
nested with complex types. The **Depth** parameter of `Format-Custom` specifies
the depth of expansion for all properties. The `Depth` key allows you to
specify the depth of expansion per property.

In this example, the `Depth` key simplifies the custom output for the
`Get-Date` cmdlet. `Get-Date` returns a **DateTime** object. The **Date**
property of this object is also a **DateTime** object, so the object is nested.

```powershell
Get-Date | Format-Custom @{Expr={$_.Date};Depth=1},TimeOfDay
```

```Output
class DateTime
{
  $_.Date =
    class DateTime
    {
      Date = 8/7/2020 12:00:00 AM
      Day = 7
      DayOfWeek = Friday
      DayOfYear = 220
      Hour = 0
      Kind = Local
      Millisecond = 0
      Minute = 0
      Month = 8
      Second = 0
      Ticks = 637323552000000000
      TimeOfDay = 00:00:00
      Year = 2020
      DateTime = Friday, August 07, 2020 12:00:00 AM
    }
  TimeOfDay =
    class TimeSpan
    {
      Ticks = 435031592302
      Days = 0
      Hours = 12
      Milliseconds = 159
      Minutes = 5
      Seconds = 3
      TotalDays = 0.503508787386574
      TotalHours = 12.0842108972778
      TotalMilliseconds = 43503159.2302
      TotalMinutes = 725.052653836667
      TotalSeconds = 43503.1592302
    }
}
```

### Format-List

In this example, we use calculated properties to change the name and format of
the output from `Get-ChildItem`.

```powershell
Get-ChildItem *.json -File |
  Format-List FullName,
              @{
                 Name='Modified'
                 Expression={$_.LastWriteTime}
                 FormatString='O'
              },
              @{
                 Name='Size'
                 Expression={$_.Length/1KB}
                 FormatString='N2'
              }
```

```Output
FullName : C:\Git\PS-Docs\PowerShell-Docs\.markdownlint.json
Modified : 2020-07-23T10:26:28.4092457-07:00
Size     : 2.40

FullName : C:\Git\PS-Docs\PowerShell-Docs\.openpublishing.publish.config.json
Modified : 2020-07-23T10:26:28.4092457-07:00
Size     : 2.25

FullName : C:\Git\PS-Docs\PowerShell-Docs\.openpublishing.redirection.json
Modified : 2020-07-27T13:05:24.3887629-07:00
Size     : 324.60
```

### Format-Table

In this example, the calculated property adds a **Type** property used to
classify the files by the content type.

```powershell
Get-ChildItem -File |
  Sort-Object Extension |
    Format-Table Name, Length -GroupBy @{
      Name='Type'
      Expression={
        switch ($_.Extension) {
          '.md'   {'Content'}
          ''      {'Metacontent'}
          '.ps1'  {'Automation'}
          '.yml'  {'Automation'}
          default {'Configuration'}
        }
      }
    }
```

```Output
   Type: Metacontent

Name              Length
----              ------
ThirdPartyNotices   1229
LICENSE-CODE        1106
LICENSE            19047

   Type: Configuration

Name                                Length
----                                ------
.editorconfig                          183
.gitattributes                         419
.gitignore                             228
.markdownlint.json                    2456
.openpublishing.publish.config.json   2306
.openpublishing.redirection.json    332394
.localization-config                   232

   Type: Content

Name            Length
----            ------
README.md         3355
CONTRIBUTING.md    247

   Type: Automation

Name                      Length
----                      ------
.openpublishing.build.ps1    796
build.ps1                   7495
ci.yml                       645
ci-steps.yml                2035
daily.yml                   1271
```

### Format-Wide

The `Format-Wide` cmdlet allows you to display the value of one property for
objects in a collection as a multi-column list.

For this example, we want to see the filename and the size (in kilobytes) as a
wide listing. Since `Format-Wide` doesn't display more than one property, we
use a calculated property to combine the value of two properties into a single
value.

```powershell
Get-ChildItem -File |
  Format-Wide -Property @{e={'{0} ({1:N2}kb)' -f $_.Name,($_.Length/1kb)}}
```

```Output
.editorconfig (0.18kb)                          .gitattributes (0.41kb)
.gitignore (0.22kb)                             .localization-config (0.23kb)
.markdownlint.json (2.40kb)                     .openpublishing.build.ps1 (0.78kb)
.openpublishing.publish.config.json (2.25kb)    .openpublishing.redirection.json (324.60kb)
build.ps1 (7.32kb)                              ci.yml (0.63kb)
ci-steps.yml (1.99kb)                           CONTRIBUTING.md (0.24kb)
daily.yml (1.24kb)                              LICENSE (18.60kb)
LICENSE-CODE (1.08kb)                           README.md (3.28kb)
ThirdPartyNotices (1.20kb)
```

### Group-Object

The `Group-Object` cmdlet displays objects in groups based on the value of a
specified property. In this example, the calculated property counts the number
of files of each content type.

```powershell
Get-ChildItem -File |
  Sort-Object Extension |
    Group-Object -NoElement -Property @{
      Expression={
        switch ($_.Extension) {
          '.md'   {'Content'}
          ''      {'Metacontent'}
          '.ps1'  {'Automation'}
          '.yml'  {'Automation'}
          default {'Configuration'}
        }
      }
    }
```

```Output
Count Name
----- ----
    5 Automation
    7 Configuration
    2 Content
    3 Metacontent
```

### Measure-Object

The `Measure-Object` cmdlet calculates the numeric properties of objects. In
this example, we use a calculated property to get the count of the numbers
between 1 and 10 that are evenly divisible by 3.

The script block returns `$true` if the number is divisible by 3 and `$false`
for all other numbers. The **Sum** operation treats `$true` values as `1` and
`$false` values as `0`.

```powershell
1..10 | Measure-Object -Property {($_ % 3) -eq 0} -Sum
```

```Output
Count             : 10
Average           :
Sum               : 3
Maximum           :
Minimum           :
StandardDeviation :
Property          : ($_ % 3) -eq 0
```

> [!NOTE]
> Unlike the other cmdlets, `Measure-Object` doesn't accept a hashtable for
> calculated properties. You must use a script block.

### Select-Object

You can use calculated properties to add additional members to the objects
output with the `Select-Object` cmdlet. In this example, we're listing the
PowerShell aliases that begin with the letter `C`. Using `Select-Object`, we
output the alias, the cmdlet it's mapped to, and a count for the number of
parameters defined for the cmdlet. Using a calculated property, we can create
the **ParameterCount** property.

```powershell
$aliases = Get-Alias c* |
  Select-Object Name,
                Definition,
                @{
                    Name='ParameterCount'
                    Expr={$_.Parameters.Keys.Count}
                }
$aliases | Get-Member
$aliases
```

```Output
   TypeName: Selected.System.Management.Automation.AliasInfo

Name           MemberType   Definition
----           ----------   ----------
Equals         Method       bool Equals(System.Object obj)
GetHashCode    Method       int GetHashCode()
GetType        Method       type GetType()
ToString       Method       string ToString()
Definition     NoteProperty string Definition=Get-Content
Name           NoteProperty string Name=cat
ParameterCount NoteProperty System.Int32 ParameterCount=21

Name    Definition         ParameterCount
----    ----------         --------------
cat     Get-Content                    21
cd      Set-Location                   15
cdd     Push-MyLocation                 1
chdir   Set-Location                   15
clc     Clear-Content                  20
clear   Clear-Host                      0
clhy    Clear-History                  17
cli     Clear-Item                     20
clp     Clear-ItemProperty             22
cls     Clear-Host                      0
clv     Clear-Variable                 19
cnsn    Connect-PSSession              29
compare Compare-Object                 20
copy    Copy-Item                      24
cp      Copy-Item                      24
cpi     Copy-Item                      24
cpp     Copy-ItemProperty              23
cvpa    Convert-Path                   13
```

### Sort-Object

Using the calculated properties, you can sort data in different orders per
property. This example sorts data from a CSV file in ascending order by
**Date**. But within each date, it sorts the rows in descending order by
**UnitsSold**.

```powershell
Import-Csv C:\temp\sales-data.csv |
  Sort-Object Date, @{Expr={$_.UnitsSold}; Desc=$true}, Salesperson  |
    Select-Object Date, Salesperson, UnitsSold
```

```Output
Date       Salesperson UnitsSold
----       ----------- ---------
2020-08-01 Sally       3
2020-08-01 Anne        2
2020-08-01 Fred        1
2020-08-02 Anne        6
2020-08-02 Fred        2
2020-08-02 Sally       0
2020-08-03 Anne        5
2020-08-03 Sally       3
2020-08-03 Fred        1
2020-08-04 Anne        2
2020-08-04 Fred        2
2020-08-04 Sally       2
```

## Notes

- You may specify the expression script block _directly_, as an argument,
  rather than specifying it as the `Expression` entry in a hashtable. For
  example:

  ```powershell
  '1', '10', '2' | Sort-Object { [int] $_ }
  ```

  This example is convenient for cmdlets that don't require (or support)
  naming a property via the `Name` key, such as `Sort-Object`, `Group-Object`,
  and `Measure-Object`.

  For cmdlets that support naming the property, the script block is converted
  to a string and used as the name of the property in the output.

- `Expression` script blocks run in _child_ scopes, meaning that the caller's
  variables can't be directly modified.

- Pipeline logic is applied to the output from `Expression` script blocks. This
  means that outputting a single-element array causes that array to be
  unwrapped.

- For most cmdlets, errors inside expression script blocks are quietly ignored.
  For `Sort-Object`, statement-terminating and script-terminating errors are
  _output_ but they don't terminate the statement.

## See also

- [about_Hash_Tables][03]
- [ConvertTo-Html][05]
- [Format-Custom][06]
- [Format-List][07]
- [Format-Table][08]
- [Format-Wide][09]
- [Compare-Object][04]
- [Group-Object][10]
- [Measure-Object][11]
- [Select-Object][12]
- [Sort-Object][13]
- [Format types in .NET][01]

<!-- link references -->
[01]: /dotnet/standard/base-types/formatting-types
[02]: #notes
[03]: about_hash_tables.md
[04]: xref:Microsoft.PowerShell.Utility.Compare-Object
[05]: xref:Microsoft.PowerShell.Utility.ConvertTo-Html
[06]: xref:Microsoft.PowerShell.Utility.Format-Custom
[07]: xref:Microsoft.PowerShell.Utility.Format-List
[08]: xref:Microsoft.PowerShell.Utility.Format-Table
[09]: xref:Microsoft.PowerShell.Utility.Format-Wide
[10]: xref:Microsoft.PowerShell.Utility.Group-Object
[11]: xref:Microsoft.PowerShell.Utility.Measure-Object
[12]: xref:Microsoft.PowerShell.Utility.Select-Object
[13]: xref:Microsoft.PowerShell.Utility.Sort-Object
