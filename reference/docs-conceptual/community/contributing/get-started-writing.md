---
description: This article is an overview of how to get started as a contributor to the PowerShell documentation.
ms.date: 03/30/2025
title: Get started contributing to PowerShell documentation
---
# Get started contributing to PowerShell documentation

This article is an overview of how to get started as a contributor to the PowerShell documentation.

## PowerShell-Docs structure

There are three categories of content in the [PowerShell-Docs][1] repository:

- reference content
- conceptual content
- metadata and configuration files

### Reference content

The reference content is the PowerShell cmdlet reference for the cmdlets that ship in PowerShell.
The cmdlet [reference][2] is collected in versioned folders (like 5.1, 7.4, 7.5, and 7.6), which
contain the reference for the modules that ship with PowerShell. This content is also used to create
the help information displayed by the `Get-Help` cmdlet.

### Conceptual content

The [conceptual documentation][3] isn't organized by version. All articles are displayed for every
version of PowerShell.

> [!NOTE]
> Anytime a conceptual article is added, removed, or renamed, the TOC must be updated.
> Any deleted or renamed files must be redirected.

### Metadata files

This project contains several types of metadata files. The metadata files control the behavior of
our build tools and the publishing system. Only PowerShell-Docs maintainers and approved
contributors are allowed to change these files. If you think that a meta file should be changed,
open an issue to discuss the needed changes.

Meta files in the root of the repository

- `.*` - configuration files in the root of the repository
- `*.md` - Project documentation in the root of the repository
- `*.yml` - Project documentation in the root of the repository
- `.devcontainer/*` - devcontainer configuration files
- `.github/**/*` - GitHub templates, actions, and other meta files
- `.vscode/**/*` - VS Code extension configurations
- `assets/*` - contains downloadable files linked in the documentation
- `redir/*` - contain redirection mapping files
- `tests/*` - test tools used by the build system
- `tools/*` - other tools used by the build system

Meta files in the documentation set

- `reference/**/*.json` - docset configuration files
- `reference/**/*.yml` - TOC and other structured content files
- `reference/bread/*` - breadcrumb navigation configuration
- `reference/includes/*` - markdown include files
- `reference/mapping/*` - version mapping configuration
- `reference/**/media/**` - image files used in documentation
- `reference/module/*` - Module Browser page configuration

## Creating new articles

A GitHub issue must be created for any new document you want to contribute. Check for existing
issues to make sure you're not duplicating efforts. Assigned issues are considered to be
`in progress`. If you wish to collaborate on an issue, contact the person assigned to the issue.

Similar to the PowerShell [RFC process][4], create an issue before you write the content. The issue
ensures you don't waste time and effort on work that gets rejected by the PowerShell-Docs team. The
issue allows us to consult with you on the scope of the content and where it fits in the PowerShell
documentation. All articles must be included in the Table of Contents (TOC). The proposed TOC
location should be included in the issue discussion.

> [!NOTE]
> The publishing system autogenerates the TOC for reference content. You don't have to update
> the TOC.

## Updating existing articles

Where applicable, cmdlet reference articles are duplicated across all versions of PowerShell
maintained in this repository. When reporting an issue about a cmdlet reference or an `About_`
article, list the versions of the article that have the problem.

Apply the appropriate change to each version of the file.

## Localized content

The PowerShell documentation is written in English and translated into 17 other languages. The
English content is stored in the GitHub repository named `MicrosoftDocs/PowerShell-Docs`. Issues
found in the translated content should be submitted to this repository.

All translations start from the English content first. We use both human and machine translation.

| Translation method  |                              Languages                               |
| ------------------- | -------------------------------------------------------------------- |
| Human translation   | de-DE, es-ES, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, ru-RU, zh-CN, zh-TW |
| Machine translation | cs-CZ, hu-HU, nl-NL, pl-PL, pt-PT, sv-SE, tr-TR                      |

The content translated by machine translation might not always result in correct word choices and
grammar. If you find an error in translation for any language, rather than in the technical details
of the article, open an issue explaining why you think the translation is wrong.

Some translation issues can be fixed by changing the English source files. However, some issues can
require updates to our internal translation system. For those cases, we must submit the issue to our
internal localization team for review and response.

## Next steps

There are two common ways of submitting changes in GitHub. Both methods are described in the central
Contributor's Guide:

1. You can make [quick edits to existing documents][5] in the GitHub web interface.
1. Use the [full GitHub workflow][6] for adding new articles, updating multiple files, or other
   large changes.

Before starting any changes, you should create a fork of the PowerShell-Docs repository. The changes
should be made in a working branch in your copy of the PowerShell-Docs. If you're using the **quick
edit** method in GitHub, these steps are handled for you. If you're using the **full GitHub
workflow**, you must be set up to [work locally][7].

Both methods end with the creation of a Pull Request (PR). For more information and best practices,
see [Submitting a pull request][8].

<!--link refs-->
[1]: https://github.com/MicrosoftDocs/PowerShell-Docs
[2]: https://github.com/MicrosoftDocs/PowerShell-Docs/tree/main/reference
[3]: https://github.com/MicrosoftDocs/PowerShell-Docs/tree/main/reference/docs-conceptual
[4]: https://github.com/PowerShell/powershell-rfc/blob/master/RFC0000-RFC-Process.md
[5]: /contribute/content/how-to-write-quick-edits
[6]: /contribute/how-to-write-workflows-major#making-your-changes
[7]: /contribute/get-started-setup-local#fork-the-repository
[8]: pull-requests.md
