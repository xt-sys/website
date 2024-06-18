+++
title = 'Developing ExectOS'
date = 2024-06-18T16:36:47+02:00
+++
We leave you free to choose your working environment, but we also recommend working with VS Code, or VS Codium. Both are
free to use, open-source IDEs, compatible with Windows, Linux, and macOS operating systems. They falls under the same
category as Atom, Sublime, and Text Wrangler, but they also has more advanced features. While the first one is am original
code editor from the Microsoft&copy;, VS Codium is essentially a telemetry-free, open-source version of VS Code.

### Recommended Extensions
 * **[Clangd](https://open-vsx.org/extension/llvm-vs-code-extensions/vscode-clangd)**: helps developers write,
   understand and improve C/C++ code by providing code completion, compile errors and warnings, hover information and
   inlay hints, include management, code formatting and more.
 * **[CMake](https://open-vsx.org/extension/twxs/cmake)**: extension provides support for CMake in Visual Studio Code.
 * **[Codeium](https://open-vsx.org/extension/Codeium/codeium)**: modern coding superpower, a free code acceleration
   toolkit built on cutting edge AI technology. Currently, Codeium provides autocomplete, chat, and search capabilities
   in 70+ languages, with lightning fast speeds and state-of-the-art suggestion quality.
 * **[GNU Assembler Language Support](https://open-vsx.org/extension/basdp/language-gas-x86)**: extension provides
   language support for x86 and x86_64 assembly language in AT&T dialect.
 * **[Markdown Preview Enhanced](https://open-vsx.org/extension/shd101wyy/markdown-preview-enhanced)**: extension that
   provides many useful functionalities such as automatic scroll sync, math typesetting, mermaid, PlantUML, pandoc,
   PDF export, code chunk, presentation writer, etc.

### Getting Started with VS Code
Opening a project in Visual Studio Code is straightforward, especially that you can find `ExectOS.code-workspace` file
in project's GIT repository. Once you checkout the source code, you can open the workspace file directly from the command
line:
```
code ExectOS.code-workspace
```
Alternatively, you can start VS Code and use the menu options:
 * Open Visual Studio Code.
 * Go to File > Open Workspace from File...
 * Navigate to the directory containing your cloned repository.
 * Select the ExectOS.code-workspace file and click Open.

VS Code will load everything defined in the workspace file, including folder structure and settings specific to the
ExectOS project. You're now ready to start working on the project.
