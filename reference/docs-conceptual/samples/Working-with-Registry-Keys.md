---
description: This article discusses how to deal with registry keys using PowerShell.
ms.date: 12/08/2022
title: Working with registry keys
---
# Working with registry keys

> This sample only applies to Windows platforms.

Because registry keys are items on PowerShell drives, working with them is very similar to working
with files and folders. One critical difference is that every item on a registry-based PowerShell
drive is a container, just like a folder on a file system drive. However, registry entries and their
associated values are properties of the items, not distinct items.

## Listing all subkeys of a registry key

You can show all items directly within a registry key using `Get-ChildItem`. Add the optional
**Force** parameter to display hidden or system items. For example, this command displays the items
directly within PowerShell drive `HKCU:`, which corresponds to the `HKEY_CURRENT_USER` registry
hive:

```powershell
Get-ChildItem -Path HKCU:\ | Select-Object Name
```

```Output
   Hive: Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER

Name
----
HKEY_CURRENT_USER\AppEvents
HKEY_CURRENT_USER\Console
HKEY_CURRENT_USER\Control Panel
HKEY_CURRENT_USER\DirectShow
HKEY_CURRENT_USER\dummy
HKEY_CURRENT_USER\Environment
HKEY_CURRENT_USER\EUDC
HKEY_CURRENT_USER\Keyboard Layout
HKEY_CURRENT_USER\MediaFoundation
HKEY_CURRENT_USER\Microsoft
HKEY_CURRENT_USER\Network
HKEY_CURRENT_USER\Printers
HKEY_CURRENT_USER\Software
HKEY_CURRENT_USER\System
HKEY_CURRENT_USER\Uninstall
HKEY_CURRENT_USER\WXP
HKEY_CURRENT_USER\Volatile Environment
```

These are the top-level keys visible under `HKEY_CURRENT_USER` in the Registry Editor
(`regedit.exe`).

You can also specify this registry path by specifying the Registry provider's name, followed by
`::`. The Registry provider's full name is `Microsoft.PowerShell.Core\Registry`, but this can be
shortened to just `Registry`. Any of the following commands will list the contents directly under
`HKCU:`.

```powershell
Get-ChildItem -Path Registry::HKEY_CURRENT_USER
Get-ChildItem -Path Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER
Get-ChildItem -Path Registry::HKCU
Get-ChildItem -Path Microsoft.PowerShell.Core\Registry::HKCU
Get-ChildItem HKCU:
```

These commands list only the directly contained items, much like using `DIR` in `cmd.exe` or `ls`
in a Unix shell. To show contained items, you need to specify the **Recurse** parameter. To list all
registry keys in `HKCU:`, use the following command.

```powershell
Get-ChildItem -Path HKCU:\ -Recurse
```

`Get-ChildItem` can perform complex filtering capabilities through its **Path**, **Filter**,
**Include**, and **Exclude** parameters, but those parameters are typically based only on name. You
can perform complex filtering based on other properties of items using the `Where-Object` cmdlet.
The following command finds all keys within `HKCU:\Software` that have no more than one subkey and
also have exactly four values:

```powershell
Get-ChildItem -Path HKCU:\Software -Recurse |
    Where-Object {($_.SubKeyCount -le 1) -and ($_.ValueCount -eq 4) }
```

## Copying keys

Copying is done with `Copy-Item`. The following example copies the `CurrentVersion` subkey of
`HKLM:\SOFTWARE\Microsoft\Windows\` and all of its properties to `HKCU:\`.

```powershell
Copy-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion' -Destination HKCU:
```

If you examine this new key in the registry editor or using `Get-ChildItem`, you notice that you
don't have copies of the contained subkeys in the new location. In order to copy all of the contents
of a container, you need to specify the **Recurse** parameter. To make the preceding copy command
recursive, you would use this command:

```powershell
Copy-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion' -Destination HKCU: -Recurse
```

You can still use other tools you already have available to perform filesystem copies. Any registry
editing tools—including `reg.exe`, `regini.exe`, `regedit.exe`, and COM objects that support
registry editing, such as **WScript.Shell** and WMI's **StdRegProv** class can be used from within
PowerShell.

## Creating keys

Creating new keys in the registry is simpler than creating a new item in a file system. Because all
registry keys are containers, you don't need to specify the item type. Just provide an explicit
path, such as:

```powershell
New-Item -Path HKCU:\Software_DeleteMe
```

You can also use a provider-based path to specify a key:

```powershell
New-Item -Path Registry::HKCU\Software_DeleteMe
```

## Deleting keys

Deleting items is essentially the same for all providers. The following commands silently remove
items:

```powershell
Remove-Item -Path HKCU:\Software_DeleteMe
Remove-Item -Path 'HKCU:\key with spaces in the name'
```

## Removing all keys under a specific key

You can remove contained items using `Remove-Item`, but you will be prompted to confirm the removal
if the item contains anything else. For example, if we attempt to delete the `HKCU:\CurrentVersion`
subkey we created, we see this:

```powershell
Remove-Item -Path HKCU:\CurrentVersion
```

```Output
Confirm
The item at HKCU:\CurrentVersion\AdminDebug has children and the Recurse
parameter was not specified. If you continue, all children will be removed with
the item. Are you sure you want to continue?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

To delete contained items without prompting, specify the **Recurse** parameter:

```powershell
Remove-Item -Path HKCU:\CurrentVersion -Recurse
```

If you wanted to remove all items within `HKCU:\CurrentVersion` but not `HKCU:\CurrentVersion`
itself, you could instead use:

```powershell
Remove-Item -Path HKCU:\CurrentVersion\* -Recurse
```
