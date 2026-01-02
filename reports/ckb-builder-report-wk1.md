# Builder Track Weekly Report — Week 1

__Name:__ Victor Okenwa.
__Week Ending:__ Friday 2nd, 2026


This log summarizes my learning journey about __Nervos CKB (Common Knowledge Base)__, including its architecture, data model, and unique features. It's meant as a __knowledge log__ for personal reference and for sharing with the team/community.

## Overview

* __CKB__ is a __Layer 1 blockchain__ designed as a __secure, decentralized, and permanent base layer__.
* Goal: Store __value and data permanently__, providing a foundation for applications and Layer 2 solutions.
* Philosophy: Focus on __security and sustainability__, leaving speed to Layer 2 chains.

CKB is like __land ownership__ — the land itself is permanent and trusted, and buildings (apps/Layer 2 solutions) can be built on top.

---

## About CKBytes & Storage

* __CKByte (CKB)__ is the __native token__.
* __1 CKByte = 1 byte of storage on the blockchain.__
* Storing data __locks CKBytes__, which can be __recovered when data is deleted__.

Paying CKBytes is like __renting land__. You pay to occupy space on the blockchain, but you can reclaim your "land" if you remove your data.

---

## Cells

* __Cells__ are the core data model of CKB.

* Each Cell contains:

  * __Value (CKBytes)__
  * __Optional data__
  * __Scripts (rules)__

* __Immutable:__ You cannot modify a Cell. You must __consume it and create new Cells__ to change value or data.

__Live Cells vs Dead Cells:__

* 🟢 __Live Cell:__ Can be spent
* ⚫ __Dead Cell:__ Already consumed

Cells are like __cash envelopes__. You can't edit an envelope; you must open it and make new ones.

---

## Transactions

* __Transactions__ consume old Cells (inputs) and create new Cells (outputs).
* Rules: __Total inputs ≥ total outputs + fees__
* Every input must pass its __Lock Script__.

Breaking one big candy bar into smaller pieces: giving some to a friend, keeping some, and paying a tiny fee.

---

## Scripts

* __Lock Script:__ Controls __who can spend a Cell__ (ownership).
* __Type Script:__ Optional rules about __how a Cell can be used__ (logic, tokens, NFTs, conditions).

A box with a lock (Lock Script) and printed instructions (Type Script) — e.g., “Only open after 30 days” or “Split contents evenly.”

---

## CKB-VM & Cycles

* __CKB-VM:__ Virtual machine that executes scripts safely.
* __Cycles:__ Measure the computational cost of executing scripts.

__Purpose:__

* Prevent infinite loops
* Limit resource usage
* Ensure deterministic execution

__Real-life analogy:__
A robot checks all rules, counting carefully. Each instruction costs a token (cycle), and if tokens run out, execution stops.

---

## Comparison with Ethereum & Solana

I also tried to compare it with the Ethreum and Solana blockchain and this is what I found out

| Feature      | __CKB__                | __Ethereum__            | __Solana__              |
| ------------ | ---------------------- | ----------------------- | ----------------------- |
| Model        | Cell-based (UTXO)      | Account-based           | Account-based           |
| Storage      | Paid, locked CKBytes   | Mostly free             | Mostly free             |
| Transactions | Consume + create Cells | Modify account balances | Modify account balances |
| Logic        | Scripts per Cell       | Global contracts        | Global contracts        |
| Consensus    | PoW                    | PoS                     | PoS                     |
| Strength     | Security & permanence  | Flexibility             | Speed                   |
| Weakness     | Slower                 | Expensive gas           | Less decentralized      |

---

## In general, I understood that:

1. __CKB = secure, permanent blockchain base layer__
2. __CKBytes__ represent storage ownership
3. __Cells__ are immutable containers of value and data
4. __Transactions__ consume old Cells and create new Cells
5. __Scripts__ enforce rules (Lock Script for ownership, Type Script for logic)
6. __CKB-VM__ safely executes scripts using cycles
7. Nervos prioritizes __long-term security & sustainability__ over speed


## Installation

Running on a windows 11 computer I had several difficulties but first here is a simple solution I used:

1. __Install WSL2 with Ubuntu on Windows 11__  
   - Open PowerShell as Administrator and run:  
     ```
     wsl --install
     ```
   - Restart your computer if prompted.
   - Set Ubuntu as your default distribution (optional):
     ```
     wsl --set-default ubuntu
     ```
   - Launch Ubuntu from the Start Menu and follow the prompts to create a UNIX username and password.

2. __Create a Project Directory in Ubuntu Shell__  
   - After Ubuntu starts, in the shell, create a new project directory:
     ```
     mkdir ~/my-ckb-project
     cd ~/my-ckb-project
     ```

3. __Run IDE on a Remote WSL Window__  
   - Install [Visual Studio Code](https://code.visualstudio.com/) on Windows.
   - Open VS Code and install the "Remote - WSL" extension from the Extensions pane.
   - In VS Code, press `F1` and select `Remote-WSL: New Window`.
   - Open your project folder by navigating to `/home/your-username/my-ckb-project` (replace `your-username` as appropriate).

Now you can edit files in your Ubuntu environment using VS Code, and run commands directly in Ubuntu's shell!

4. __Install Node.js on WSL2 (Ubuntu)__  
   - Node.js is required to use many blockchain tools including CKB tools.
   - __Recommended:__ Use Node Version Manager (nvm) to easily install and switch between Node.js versions.
   - In your Ubuntu shell, run the following commands one at a time:

     ```bash
     # Download and install nvm
     curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
     # Activate nvm in your current shell session
     export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
     [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
     # Install the latest LTS version of Node.js
     nvm install --lts
     # Verify node and npm are installed
     node -v
     npm -v
     ```

     - __If you close and reopen your shell,__ run the three export/activation lines above again or restart the terminal to load nvm automatically.
     - You can now install node.js global packages (like `@offckb/cli`) directly in WSL2 Ubuntu, independent from your Windows node/npm installation. This avoids many Windows/permissions issues.

### Difficulties Faced

1. At first, I ran the OFFCKB installation command `npm install -g @offckb/cli` I got this response:

```bash
PS C:\Users\HomePC> npm install -g @offckb/cli
npm warn cleanup Failed to remove some directories [
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1',
npm warn cleanup     [Error: EBUSY: resource busy or locked, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\secp256k1'] {
npm warn cleanup       errno: -4082,
npm warn cleanup       code: 'EBUSY',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1'
npm warn cleanup     }
npm warn cleanup   ],
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules',
npm warn cleanup     [Error: EBUSY: resource busy or locked, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\secp256k1'] {
npm warn cleanup       errno: -4082,
npm warn cleanup       code: 'EBUSY',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1'
npm warn cleanup     }
npm warn cleanup   ],
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli',
npm warn cleanup     [Error: EBUSY: resource busy or locked, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\secp256k1'] {
npm warn cleanup       errno: -4082,
npm warn cleanup       code: 'EBUSY',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1'
npm warn cleanup     }
npm warn cleanup   ],
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb',
npm warn cleanup     [Error: EBUSY: resource busy or locked, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\secp256k1'] {
npm warn cleanup       errno: -4082,
npm warn cleanup       code: 'EBUSY',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1'
npm warn cleanup     }
npm warn cleanup   ],
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules',
npm warn cleanup     [Error: EBUSY: resource busy or locked, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\secp256k1'] {
npm warn cleanup       errno: -4082,
npm warn cleanup       code: 'EBUSY',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\secp256k1'
npm warn cleanup     }
npm warn cleanup   ],
npm warn cleanup   [
npm warn cleanup     '\\\\?\\C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\@types',
npm warn cleanup     [Error: EPERM: operation not permitted, rmdir 'C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\@types'] {
npm warn cleanup       errno: -4048,
npm warn cleanup       code: 'EPERM',
npm warn cleanup       syscall: 'rmdir',
npm warn cleanup       path: 'C:\\Users\\HomePC\\AppData\\Local\\Volta\\tmp\\image\\packages\\.tmpEa16wk\\node_modules\\@offckb\\cli\\node_modules\\@types'
npm warn cleanup     }
npm warn cleanup   ]
npm warn cleanup ]
npm error code 1
npm error path C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\cpu-features
npm error command failed
npm error command C:\WINDOWS\system32\cmd.exe /d /s /c node buildcheck.js > buildcheck.gypi && node-gyp rebuild
npm error C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\buildcheck\lib\index.js:133
npm error         throw new Error('Unable to detect compiler type');
npm error         ^
npm error
npm error Error: Unable to detect compiler type
npm error     at new BuildEnvironment (C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\buildcheck\lib\index.js:133:15)
npm error     at Object.<anonymous> (C:\Users\HomePC\AppData\Local\Volta\tmp\image\packages\.tmpEa16wk\node_modules\@offckb\cli\node_modules\cpu-features\buildcheck.js:5:12)
npm error     at Module._compile (node:internal/modules/cjs/loader:1692:14)
npm error     at Object..js (node:internal/modules/cjs/loader:1824:10)
npm error     at Module.load (node:internal/modules/cjs/loader:1427:32)
npm error     at Module._load (node:internal/modules/cjs/loader:1250:12)
npm error     at TracingChannel.traceSync (node:diagnostics_channel:322:14)
npm error     at wrapModuleLoad (node:internal/modules/cjs/loader:235:24)
npm error     at Module.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:152:5)
npm error     at node:internal/main/run_main_module:33:47
npm error
npm error Node.js v24.3.0
npm error A complete log of this run can be found in: C:\Users\HomePC\AppData\Local\npm-cache\_logs\2025-12-23T21_50_10_696Z-debug-0.log
```

2. I tried to fix it by running this command `npm install --global --production windows-build-tools` but it failed.

3. I installed [Visual Studio Community Edition](https://visualstudio.microsoft.com/visual-cpp-build-tools/) as recommended for Windows C++ build tools. I installed the following dependencies:

 - MSVC v143 – VS 2022 C++ x64/x86 build tools
- Windows 11 SDK
 -  C++ CMake tools for Windows
-  C++ ATL

1. Restarted my PC for the changes to take effect.

2. Also, installed Python as required by some Node.js native modules. Download Python from the [official website](https://www.python.org/downloads/).
 
 6. Then after I ran `npm install -g @offckb/cli` but it failed again.

 7. I downgraded my Node version from v24 to v20 using volta.

 ```bash
volta uninstall node
node -v
volta install node@20
volta pin node@20
node -v
 ```

 8. Then I reinstalled OFFCKB:

 ```bash
 volta uninstall @offckb/cli || true
npm uninstall -g @offckb/cli || true
rm -rf ~/.local/share/offckb-nodejs
npm cache clean --force
volta install @offckb/cli
```