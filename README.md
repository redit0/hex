# HEX - Advanced Shell Framework for Grey Hack

## 🎮 IMPORTANT: This is a scripting project for the video game Grey Hack 🎮

**HEX is a comprehensive hacking toolkit and bash shell replacement designed exclusively for use within Grey Hack, a hacking simulation game by Loading Home. This is entertainment software created to enhance gameplay experience - not real-world hacking tools.**

---

## What is HEX?

HEX is an advanced command-line shell replacement written in GreyScript (Grey Hack's native scripting language, based on MiniScript) that transforms the in-game terminal experience. It provides a feature-rich, customizable shell environment with powerful automation capabilities for completing game missions and managing in-game systems.

## Core Features

### 🖥️ Shell Replacement
- **Complete Bash Replacement**: Full-featured alternative to the default Grey Hack shell with enhanced functionality
- **Custom Command Pipeline**: Pipe commands together with `|` for complex operations
- **Context Management**: Intelligent context switching between different in-game computers, shells, and file systems
- **Smart Binary Launcher**: Automatically locates and executes in-game binaries with path resolution

### 🎨 Enhanced User Experience
- **Custom ASCII Banner**: Eye-catching HEX logo displayed at startup
- **Color-Coded Interface**: Rich color system for better visual feedback (user privilege levels, file types, status messages)
- **Intelligent Prompt**: Dynamic command prompt showing current user, computer context, and directory path
- **User Detection**: Automatically identifies privilege level (root, guest, or user) with visual indicators

### 🔧 Advanced Utilities

**File Management:**
- Recursive file/folder search with pattern matching and flags (binary, text, folders, permissions)
- UUID generation for unique file operations
- Silent file deletion that does not leave log entries


### 💻 Planned Hacking Tools *(In Development)*

**Exploit Framework:**
- **Overflow Attacks**: Execute memory overflow exploits against vulnerable in-game services
- **Zero-Day Functionality**: Tools for discovering and exploiting vulnerabilities using the zero-day system
- **Brute Force Capabilities**: Automated password and security cracking utilities
- **Exploit Object System**: Type-safe exploit construction and execution framework

**Advanced Features:**
- **Full APT Interface**: Complete package management and library handling system
- **RShell Manager**: Comprehensive remote shell management for controlling compromised in-game computers
- **Proxy Manager**: Full-featured proxy management for routing connections and maintaining anonymity in-game
- **Intelligent Metaxploit Management**: Always scan using your home computer's hardware, even on remote systems, while log entries show proxy IP addresses instead.

### 📦 Architecture

**Modular Design:**
- **Core System**: Foundation classes including Context, Result, and Exploit types
- **Extension System**: Pluggable architecture for adding new commands
- **JSON Support**: Built-in JSON parsing and handling
- **Stack-Based Context**: Manage multiple simultaneous connections and contexts
- **Type Safety**: Custom type system for reliable in-game object management

**Command Processing:**
- Dynamic command registration system
- Result chaining between piped commands
- Graceful error handling with informative messages
- Support for both built-in commands and external binaries

## Technical Implementation

- **Language**: GreyScript (Grey Hack's native scripting language, based on MiniScript)
- **Architecture**: Modular, object-oriented design with type classes
- **Initialization**: Automated startup sequence with root privilege escalation
- **State Management**: Global custom object (`g`) for shared state and utilities
- **Build System**: Organized source structure with separate build output

## Directory Structure

```
hex/
├── src/              # Source files
│   ├── core/         # Core functionality
│   │   ├── types/    # Type definitions (Context, Exploit, Result)
│   │   ├── banner.src
│   │   ├── colors.src
│   │   ├── constants.src
│   │   ├── extensions.src
│   │   ├── functions.src
│   │   ├── init.src
│   │   ├── json.src
│   │   ├── prompt.src
│   │   └── startup.src
│   ├── tools/        # Utility tools
│   │   ├── core.src
│   │   └── stack.src
│   └── hexshell.src  # Main shell loop
├── build/            # Compiled output
└── README.md         # This file
```

## Game Context

Grey Hack is a massively multiplayer hacking simulator game available on Steam. The game features:
- A persistent online world that runs 24/7
- Procedurally generated computer networks
- Real UNIX-based terminal commands
- In-game scripting using GreyScript

HEX is designed to automate common tasks in Grey Hack gameplay, including:
- Mission automation
- Computer infiltration and compromise
- File system navigation and manipulation
- Exploit development and deployment
- Network reconnaissance
- Maintaining persistent access to compromised systems

## About Grey Hack

**Developer**: Loading Home (Victor Cano / Kurouzu)  
**Platform**: Steam (Windows, macOS, Linux)  
**Status**: Early Access  
**Official Website**: [Grey Hack on Steam](https://store.steampowered.com/app/605230/Grey_Hack/)
**Official Discord**: [Grey Hack Official Discord](https://discord.com/invite/greyhack)

---

## ⚠️ Legal Disclaimer

**This software is an in-game tool for Grey Hack, a hacking simulation video game by Loading Home. It contains no real-world hacking capabilities and is purely for entertainment purposes within the game environment. All hacking tools, exploits, and techniques referenced are simulated game mechanics and have no application outside of Grey Hack.**

---

## Author

**Created by**: redit0  
**For**: Grey Hack by Loading Home

---

*HEX - Because even in-game hackers deserve a better shell.*
