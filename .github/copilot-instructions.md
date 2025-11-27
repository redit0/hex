# HEX - AI Coding Agent Instructions

## Project Overview
HEX is a **GreyScript shell replacement** for the Grey Hack video game, providing enhanced terminal functionality, context management, and hacking automation tools. It's written entirely in GreyScript (Grey Hack's MiniScript-based scripting language) with a modular, object-oriented architecture.

## Core Architecture

### Global State Management
- **`g` (get_custom_object)**: Central global object containing all shared state and utilities
- Structure: `g.colors`, `g.commands`, `g.const`, `g.func`, `g.stack`
- All modules extend this global object rather than using isolated namespaces

### Type System (src/core/types/)
- **`Context`**: Manages shell/computer/file contexts with metadata (user, IPs, libraries)
- **`Result`**: Standard return type with `success`, `output`, and `data` properties
- **`Exploit`**: Framework for Grey Hack exploit object management
- Use `Context.New(source, obj)` and `Result.New(success, data, output)` constructors

### Module Import Pattern
```greyscript
// src/core/init.src orchestrates all imports
import_code("types/context.src")  // Import types first
import_code("functions.src")      // Then core functionality  
import_code("../tools/stack.src") // Then tools
```

### Command Registration System
Commands follow this pattern in `tools/` directory:
```greyscript
g.commands.commandName = {
    "name": "commandName",
    "shortDescription": "Brief description",
    "exec": @doCommandFunction,
    "help": @helpFunction,
    "data": {}
}
```

## Development Patterns

### Pipeline Architecture
- Main shell loop (`hexshell.src`) processes pipe-separated commands: `cmd1 | cmd2 | cmd3`
- Commands pass `Result` objects through the pipeline via `result.data`
- Commands can be built-in (in `g.commands`) or external binaries

### Context Stack Management
- `g.stack`: Manages multiple computer/shell contexts indexed by number
- `g.context.index`: Current active context
- Context switching enables managing multiple compromised systems simultaneously
- Auto-detects user privileges (root/guest/username) and colorizes accordingly

### File Operations
- Use `g.func.find(file, pattern, flags)` for recursive file searching with powerful flag system
- `g.func.silent_delete()` removes files without leaving audit logs (game-specific requirement)  
- Built-in UUID generation for unique temporary file operations

### String Extensions
- Custom string methods: `.trim()`, `.trimStart(text)`, `.trimEnd(text)`
- List extensions: `.max()` for finding maximum values
- Extensions registered via `g.registerExtensions()`

## Color System
- Rich color palette in `g.colors` (b1-b14, r1-r10, g1-g12, etc.)
- Dynamic color functions: `g.r1(text)`, `g.g2(text)`, `g.b3(text)`
- Context-aware coloring for users: root=red, guest=orange, users=green

## Key Workflows

### Adding New Commands
1. Create function in `tools/` directory following the template pattern
2. Register in `g.commands` object with required properties
3. Return `Result.New(success, data, output)` objects
4. Handle help text with dedicated help function

### Context Operations
```greyscript
context = g.stack[g.context.index]  // Get current context
context.SetUser()                   // Auto-detect user privileges
user = context.GetUser(true)        // Get colorized username
```

### Error Handling
- Use `g.error(message)` for consistent red error formatting
- Validate object types before operations: `if obj isa Context then...`
- Return `Result` objects with appropriate success flags

## File Structure
- `src/hexshell.src`: Main shell loop
- `src/core/`: Core framework (types, utilities, UI)
- `src/tools/`: Individual command implementations  
- Import order matters: types → core → tools

## Language Overview: MiniScript & GreyScript

### MiniScript Foundation
MiniScript is a simple, elegant scripting language designed for embedding in applications. GreyScript is Grey Hack's fork of MiniScript with game-specific extensions.

**Core Syntax:**
```greyscript
// Variables and types
name = "string"           // String
count = 42               // Number (all numbers are floats)
active = true            // Boolean (true=1, false=0)
items = [1, 2, 3]        // List
data = {"key": "value"}   // Map (object)

// Functions
myFunction = function(param = "default")
    return param + " processed"
end function

// Control flow
if condition then
    // code
else if other then
    // code
else
    // code
end if

for item in list
    // code
end for

while condition
    // code
end while
```

### GreyScript Extensions
GreyScript adds extensive game-specific intrinsics and objects for Grey Hack:

**Core Intrinsics:**
- `get_shell(user, pass)` - Get shell access, returns shell object or null
- `include_lib(path)` - Import game libraries (.so files), returns lib object
- `import_code(path)` - Import source files (.src), executes code
- `active_user` - Current username string
- `current_path` - Current directory path
- `current_date()` - Current game date/time
- `user_input(prompt, isPassword, anyKey, addToHistory)` - Interactive input
- `get_router(ip)` - Get router object for network operations
- `get_switch(ip)` - Get switch object on local network
- `nslookup(domain)` - DNS lookup, returns IP or "Not found"
- `whois(ip)` - Get domain registration info
- `md5(string)` - Generate MD5 hash
- `is_valid_ip(ip)` - Check if IP address is valid (1/0)
- `is_lan_ip(ip)` - Check if IP is LAN address (1/0)
- `mail_login(user, pass)` - Login to email, returns MetaMail object
- `get_ctf(user, pass, event)` - Get CTF event object
- `bitwise(operator, left, right)` - Bitwise operations

**Computer Object:**
```greyscript
computer.public_ip()             // Public IP address
computer.local_ip()              // Local IP address
computer.network_gateway()       // Gateway IP
computer.active_net_card()       // "WIFI" or "ETHERNET"
computer.File(path)              // Get file object at path
computer.get_ports()             // List of port objects
computer.create_folder(path, name)       // Create directory
computer.touch(path, name)               // Create file
computer.is_network_active()             // Network connection status
computer.show_procs()                    // Running processes string
computer.close_program(pid)              // Close process by PID
computer.create_user(user, pass)         // Add user account
computer.delete_user(user, removeHome)   // Remove user
computer.change_password(user, pass)     // Change user password
computer.groups(user)                    // User's groups
computer.create_group(user, group)       // Add user to group
computer.delete_group(user, group)       // Remove from group
computer.connect_wifi(device, bssid, essid, pass)  // Connect WiFi
computer.connect_ethernet(device, ip, gateway)     // Connect ethernet
computer.wifi_networks(device)           // Available WiFi networks
computer.network_devices()               // Network interface list
computer.reboot(safeMode)                // Reboot computer
computer.get_name()                      // Computer hostname
```

**File Object:**
```greyscript
file.name                        // File name
file.path(symlink)               // Full path (show original if symlink)
file.size                        // File size in bytes
file.get_content()               // File contents (if readable)
file.set_content(text)           // Write file content
file.is_folder()                 // Boolean folder check
file.is_binary()                 // Boolean binary file check
file.is_symlink()                // Boolean symlink check
file.has_permission(perm)        // Check r/w/x permissions
file.owner()                     // File owner
file.group()                     // File group
file.get_files()                 // List files in directory
file.get_folders()               // List subdirectories
file.parent()                    // Parent directory object
file.copy(path, name)            // Copy file
file.move(path, name)            // Move/rename file
file.delete()                    // Delete file
file.chmod(permissions, recursive)       // Change permissions
file.set_owner(owner, recursive)         // Change owner
file.set_group(group, recursive)         // Change group
file.rename(newName)                     // Rename file
file.symlink(path)                       // Create symlink
file.permissions()                       // Permission string (drwxrwxrwx)
file.allow_import()                      // Mark binary as importable
```

**Shell Object:**
```greyscript
shell.host_computer              // Associated computer object
shell.connect_service(ip, port, user, pass, service)  // Connect SSH/FTP
shell.launch(program, args)      // Execute program
shell.build(source, binary, allowImport)    // Compile GreyScript
shell.scp(file, folder, remote, upload)     // Secure copy files
shell.ping(ip)                             // Network ping test
shell.start_terminal()                     // Start interactive terminal
```

**Router Object:**
```greyscript
router.public_ip()               // Public IP
router.local_ip()                // Local IP
router.bssid_name()              // BSSID for WiFi
router.essid_name()              // ESSID for WiFi
router.used_ports()              // List of active ports
router.device_ports(ip)          // Ports for LAN device
router.devices_lan_ip()          // All LAN device IPs
router.ping_port(port)           // Get port object
router.port_info(port)           // Service info for port
router.firewall_rules()          // Firewall rule list
router.kernel_version()          // Kernel router version
```

**Port Object:**
```greyscript
port.port_number                 // Port number
port.get_lan_ip()                // LAN IP behind port
port.is_closed()                 // Is port closed (1/0)
```

**Metaxploit Objects:**
```greyscript
// Metaxploit main library
metax.load(path)                 // Load library, returns MetaLib
metax.scan(metaLib)              // Scan for vulnerabilities
metax.scan_address(metaLib, addr)        // Scan specific memory
metax.net_use(ip, port)          // Get NetSession object
metax.rshell_client(ip, port, name)      // Start reverse shell client
metax.rshell_server()            // Get reverse shell connections
metax.sniffer(saveSource)        // Network packet sniffer

// MetaLib object
metaLib.version                  // Library version
metaLib.lib_name()               // Library name
metaLib.is_patched(getDate)      // Check if patched
metaLib.overflow(addr, zone, args)       // Execute exploit
metaLib.debug_tools(user, pass)  // Get debug library

// NetSession object
netSession.dump_lib()            // Get associated MetaLib
netSession.flood_connection()    // DDoS attack
netSession.get_num_users()       // Number of users
netSession.get_num_conn_gateway()        // Gateway connections
netSession.get_num_portforward()         // Port forwards
netSession.is_any_active_user()          // Any active users
netSession.is_root_active_user()         // Root active

// DebugLibrary object
debugLib.scan()                  // Debug scan
debugLib.payload(zone, file)     // Execute payload
debugLib.apply_patch(path)       // Apply patch
debugLib.unit_testing(lines)     // Run unit tests
```

**Crypto Object:**
```greyscript
crypto.encrypt(file, password)   // Encrypt file
crypto.decrypt(file, password)   // Decrypt file
crypto.is_encrypted(file)        // Check encryption status
crypto.aircrack(capFile)         // Crack WiFi passwords
crypto.airmon(option, device)    // Monitor WiFi interfaces ("start"/"stop")
crypto.aireplay(bssid, essid, maxAcks)   // Inject WiFi frames
crypto.decipher(hash)            // Decrypt MD5 hash
crypto.smtp_user_list(ip, port)  // Get SMTP users
```

**Blockchain & Cryptocurrency:**
```greyscript
// Blockchain library
blockchain.create_wallet(user, pass)     // Create wallet
blockchain.login_wallet(user, pass)      // Login to wallet
blockchain.get_coin(name, user, pass)    // Get coin object
blockchain.get_coin_name(user, pass)     // Get owned coin name
blockchain.delete_coin(name, user, pass) // Delete coin
blockchain.coin_price(name)              // Current coin price
blockchain.amount_mined(name)            // Total mined amount
blockchain.show_history(name)            // Price history

// Wallet object
wallet.get_pin()                 // Get temporary PIN
wallet.list_coins()              // List connected coins
wallet.list_global_coins()       // List all coins
wallet.get_balance(coin)         // Get coin balance
wallet.buy_coin(coin, amount, price, subwallet)     // Buy coins
wallet.sell_coin(coin, amount, price, subwallet)    // Sell coins
wallet.get_pending_trade(coin)   // Get pending trades
wallet.get_global_offers(coin)   // Get all offers
wallet.cancel_pending_trade(coin)        // Cancel trade
wallet.reset_password(newPass)   // Change password
wallet.show_nodes(coin)          // Active miners

// Coin object
coin.get_address()               // Coin address
coin.set_address(addr)           // Set address
coin.get_reward()                // Mining reward
coin.set_reward(amount)          // Set reward
coin.get_cycle_mining()          // Mining cycle time
coin.set_cycle_mining(hours)     // Set cycle time
coin.create_subwallet(id, pin, user, pass)  // Create account
coin.get_subwallet(user)         // Get subwallet
coin.get_subwallets()            // Get all subwallets
coin.transaction(from, to, amount)       // Transfer coins
coin.reset_password(newPass)     // Change password

// SubWallet object
subWallet.get_user()             // Username
subWallet.get_balance()          // Coin balance
subWallet.check_password(pass)   // Verify password
subWallet.mining()               // Start mining
subWallet.last_transaction()     // Last transaction info
subWallet.get_info()             // Get stored info
subWallet.set_info(info)         // Set info
subWallet.wallet_username()      // Parent wallet name
subWallet.delete()               // Delete account
```

**Mail System:**
```greyscript
// MetaMail object
metaMail.fetch()                 // Get mail list
metaMail.read(mailId)            // Read specific mail
metaMail.send(email, subject, msg)       // Send mail
metaMail.delete(mailId)          // Delete mail
```

**Service Management:**
```greyscript
// Service object (HTTP, FTP, SSH, etc.)
service.start_service()          // Start service
service.stop_service()           // Stop service
service.install_service()        // Install service
```

**FTP Objects:**
```greyscript
// FtpShell object (from shell.connect_service with "ftp")
ftpShell.host_computer           // FTP computer object
ftpShell.scp(src, dest, remote, upload)  // Transfer files

// FtpComputer object
ftpComputer.File(path)           // Get FTP file object
ftpComputer.create_folder(path, name)    // Create folder
ftpComputer.get_name()           // Server hostname

// FtpFile object (similar to regular File but for FTP)
ftpFile.name()                   // File name
ftpFile.path()                   // File path
ftpFile.size()                   // File size
ftpFile.is_folder()              // Is directory
ftpFile.is_binary()              // Is binary file
ftpFile.is_symlink()             // Is symlink
ftpFile.owner()                  // File owner
ftpFile.group()                  // File group
ftpFile.permissions()            // Permission string
ftpFile.has_permission(perm)     // Check permissions
ftpFile.get_files()              // List files
ftpFile.get_folders()            // List folders
ftpFile.parent()                 // Parent directory
ftpFile.delete()                 // Delete file
```

**Smart Appliances:**
```greyscript
// SmartAppliance object
smartApp.model()                 // Device model
smartApp.override_settings(power, temp)  // Override settings
smartApp.set_alarm(enable)       // Enable/disable alarm
```

**Traffic & CCTV:**
```greyscript
// TrafficNet object
traffic.camera_link_system()     // Open camera viewer
traffic.locate_vehicle(plate, pass)     // Find vehicle
traffic.get_credentials_info()   // Get NPC info
```

**Package Management:**
```greyscript
// AptClient object
aptClient.search(package)        // Search packages
aptClient.install(package, path) // Install package
aptClient.check_upgrade(file)    // Check for updates
aptClient.update()               // Update package list
aptClient.show(repo)             // Show repo packages
aptClient.add_repo(repo, port)   // Add repository
aptClient.del_repo(repo)         // Remove repository
```

**CTF Events:**
```greyscript
// CtfEvent object
ctfEvent.get_creator_name()      // Event creator
ctfEvent.get_description()       // Event description
ctfEvent.get_template()          // Event template
ctfEvent.get_mail_content()      // Mail content
ctfEvent.player_success()        // Success status
```

**No Classes:** Use maps with `classID` property for type identification:
```greyscript
MyType = {"classID": "MyType", "property": "value"}
MyType.method = function
    // 'self' refers to the instance
    return self.property
end function
instance = new MyType
```

### Language Limitations
- No traditional OOP classes - use map-based objects
- No strict typing - duck typing with `typeof()` checks
- Limited error handling - use return codes and validation
- No modules/namespaces - everything in global scope
- Limited string formatting - use concatenation and custom functions

## Game-Specific Considerations
- **GreyScript limitations**: No classes, limited OOP, MiniScript syntax
- **Grey Hack context**: Shell operates within game's virtual computer network
- **Stealth requirements**: Silent file operations to avoid in-game audit logs
- **Multi-computer management**: Context stack enables managing multiple compromised systems

When extending HEX, maintain the modular architecture, follow the Result pattern for command returns, and ensure proper context management for multi-system operations.