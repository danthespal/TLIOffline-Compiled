
# TLIOffline

Play **Torchlight: Infinite** on your own computer, with your own game server instead of the official one.

TLIOffline is two small server programs, **LoginServer** and **GameServer**, that run on your PC while you play. They keep your characters on your computer, in your own files.

> **What works today:** signing in, creating and deleting characters, and entering the first zone. Most other parts of the game are still being built, so expect things to be missing.

---

## Contents

1. [What you need](#1-what-you-need)
2. [The files](#2-the-files)
3. [One-time setup](#3-one-time-setup)
4. [Playing](#4-playing)
5. [Settings](#5-settings)
6. [Your characters, logs and backups](#6-your-characters-logs-and-backups)
7. [Troubleshooting](#7-troubleshooting)
8. [Going back to the official servers](#8-going-back-to-the-official-servers)
9. [For developers](#9-for-developers)

---

## 1. What you need

You do **not** need Visual Studio or any programming tools.

| What | Details |
|---|---|
| **Windows 10 or Windows 11, 64-bit** | Older Windows versions are not supported. |
| **Steam** | Installed and signed in. Torchlight: Infinite (free to play) must be in your Steam library. |
| **Microsoft Visual C++ Redistributable (x64)** | A free Microsoft download that many games already installed. Step 3.1 covers it. |
| **An internet connection** | The game still signs you in through Steam and its publisher's account service. Only the game server itself runs on your PC. |
| **About 20 MB of disk space** | For the server programs and your characters. |

---

## 2. The files

Unzip the TLIOffline package to a folder of your own, for example `C:\Games\TLIOffline`. Avoid `C:\Program Files`, because the servers write their files next to themselves. You will see:

| File or folder | What it is |
|---|---|
| `LoginServer.exe` | Handles signing in, the server list and your character list. |
| `GameServer.exe` | Runs the game world once you enter it. |
| `CertTool.exe` | Creates the security certificate the game needs to trust your LoginServer. |
| `libcrypto-4-x64.dll`, `libssl-4-x64.dll` | Encryption libraries the servers need. Keep them next to the `.exe` files. |
| `configs\` | Settings files you can edit (section 5). |
| `data\`, `sql\` | Game data the servers read. Do not change these. |

These are created the first time you run the tools:

| File or folder | Created by | What it is |
|---|---|---|
| `certs\` | CertTool | Your certificates. Never share `server-key.pem`. |
| `databases\` | The servers | Your characters and their progress. |
| `LoginServer.log`, `GameServer.log` | The servers | What the servers did, useful when something goes wrong. |

---

## 3. One-time setup

You only do this once. It takes about ten minutes.

### 3.1 Install the Visual C++ Redistributable

1. Download it from Microsoft: **https://aka.ms/vc14/vc_redist.x64.exe**
2. Run the file and click **Install**. If it says the program is already installed, choose **Repair** or simply close it.

> Without it, Windows says `MSVCP140.dll` or `VCRUNTIME140.dll` was not found when you start a server.

### 3.2 Create and install the certificate (CertTool)

The game only talks to servers it trusts. CertTool makes a certificate for your LoginServer and tells Windows to trust it.

1. Double-click **`CertTool.exe`**.
2. Move with the **arrow keys**, choose with **Enter**, and go back with **Esc**.
3. Choose **Create certificates**, then **Create a new CA and server certificate**. Keep the default settings.
4. Press **Esc** to go back to the main menu, then choose **Install CA into Windows**.
5. Choose **Install for the current user (recommended)**.
6. Windows shows a **Security Warning** about installing a certificate named *TLIOffline Local Root CA*. Click **Yes**.
7. Choose **Overview and checks**. Everything should be green, except the *Hosts file* section, which you fix in the next step.

The certificate is valid for **one year**. When it gets close to expiring, LoginServer warns you. Then open CertTool and choose **Create certificates → Create only a new server certificate (keep the current CA)**.

### 3.3 Point the game at your computer (hosts file)

The game looks for its login server at `version-overseas-torchlight.xdgtw.com`. One line in Windows' *hosts* file sends that name to your own PC instead.

1. Click **Start**, type **Notepad**, right-click **Notepad** and choose **Run as administrator**. Click **Yes**.
2. In Notepad choose **File → Open**, paste this into the file name box and press **Enter**:
   ```
   C:\Windows\System32\drivers\etc\hosts
   ```
   If you see no file, change *Text Documents (\*.txt)* to **All Files (\*.\*)**.
3. Add this line at the very end of the file, on its own line:
   ```
   127.0.0.1 version-overseas-torchlight.xdgtw.com
   ```
4. Choose **File → Save**, then close Notepad.
5. Click **Start**, type **cmd**, open **Command Prompt** and run:
   ```
   ipconfig /flushdns
   ```
6. Open CertTool again and choose **Overview and checks**. The *Hosts file* section should now be green.

> While this line is in your hosts file, the game **cannot reach the official servers**. Section 8 explains how to switch back.

### 3.4 Prepare your game folder

**Use a separate copy of the game.** Steam updates the game it manages, and a newer version may stop working with TLIOffline.

1. Find your game: in Steam, right-click **Torchlight: Infinite → Manage → Browse local files**.
2. Copy the whole **Torchlight Infinite** folder somewhere of your own, for example `C:\Games\Torchlight Infinite (TLIOffline)`.
3. **Check the version.** In your copy, open `UE_game\TorchLight\Saved\PersistentDownloadDir\`. Open the `mount_*.json` file with the highest number in Notepad. The **last** `"PatchVersion"` in it must be **6269408**.
4. Make sure your copy has a file named **`steam_appid.txt`** in its top folder, next to `UE_game`, containing only:
   ```
   1974050
   ```
   If it is missing, create it with Notepad. It lets the game start without going through the Steam library.
5. Copy **`OfflineLauncher.exe`** from your TLIOffline folder into the top folder of your game copy, next to `UE_game`.

Setup is done.

---

## 4. Playing

Do this every time you want to play:

1. **Start Steam** and sign in.
2. In your TLIOffline folder, double-click **`LoginServer.exe`**. Wait until its window says **LoginServer is ready**.
3. Double-click **`GameServer.exe`**. Wait until it says **GameServer is ready**.
4. Leave both windows open while you play. You can minimize them.
5. In your game copy, double-click **`OfflineLauncher.exe`**. It checks both servers, then starts the game:
   ```
   ✔ LoginServer is running.
   ✔ GameServer is running.
   ✔ Torchlight Infinite started
   ```
6. In the game, sign in, choose the server **TLI Offline**, then create a character or pick an existing one, and start.

**Deleting a character:** on the character screen, delete it and type the confirmation word. The game then counts down **one minute**, during which you can still cancel. When the countdown ends, the character is removed for good.

**When you are done:** close the game first, then close the GameServer and LoginServer windows, or press **Ctrl+C** in each.

---

## 5. Settings

Both settings files are in the `configs` folder and can be opened with Notepad. Close the servers before you change them, then start them again. Lines that start with `;` are comments.

### `GameServer_Config.ini`

| Setting | Default | Meaning |
|---|---|---|
| `[world]` `startMapId` | `110000` | The map new characters start in. |
| `[network]` `address` | `127.0.0.1` | Must stay `127.0.0.1`. The servers only accept connections from your own PC. |
| `[network]` `port` | `1005` | The port the game connects to. If you change it, change `[gate] port` in the LoginServer file too. |
| `[log]` `consoleLevel` | `info` | How much the server window shows. |
| `[log]` `fileLevel` | `debug` | How much goes into `GameServer.log`. |

### `LoginServer_Config.ini`

| Setting | Default | Meaning |
|---|---|---|
| `[client]` `patchVersion` | `6269408` | The game version the server is made for. Do not change it. |
| `[gate]` `ip`, `port` | `127.0.0.1`, `1005` | Where LoginServer sends the game. Must match GameServer's `[network]` settings. |
| `[account]` `roleSlots` | `10` | How many characters you can have. |
| `[steam]` `country`, `currency` | `US`, `USD` | Region shown by the in-game store. |
| `[log]` `consoleLevel`, `fileLevel` | `info`, `debug` | As for GameServer, for `LoginServer.log`. |
| `[heroes]` `characterIds` | (list) | The heroes offered when you create a character. |

**Log levels**, from most to least detail: `trace`, `debug`, `info`, `warn`, `error`, `critical`, `off`. Use `trace` only when asked to, because the files then grow quickly.

The servers use these ports on your PC: **443** and **18080** (LoginServer) and **1005** (GameServer). No other program may use them while you play. They only accept connections from your own computer, so they need no firewall access.

---

## 6. Your characters, logs and backups

* **Characters** are stored in `databases\LoginServer.db` and `databases\GameServer.db`.
* **To back up your characters,** close both servers and copy the `databases` folder somewhere safe. To restore, close the servers and copy it back.
* **To start completely fresh,** close both servers and delete the `databases` folder. It is created again, empty, on the next start.
* **Automatic backups:** when an update changes a database, the servers first save a copy in `databases\backups\`.
* **Logs:** `LoginServer.log` and `GameServer.log` sit next to the servers. New lines are added at the end each time, and each start begins with a line like `===== GameServer started =====`. When a log reaches 10 MB, it is renamed to `GameServer.1.log` and so on, keeping the last five. **When you report a problem, include both log files.**

---

## 7. Troubleshooting

When a server cannot start, its window shows the reason and waits for you to press **Enter**. The same text is in its `.log` file.

| What you see | What to do |
|---|---|
| *MSVCP140.dll* or *VCRUNTIME140.dll was not found* | Install the Visual C++ Redistributable (step 3.1). |
| *libssl-4-x64.dll* or *libcrypto-4-x64.dll was not found* | Put the two `.dll` files back next to the `.exe` files. |
| LoginServer: *TLS files are missing … Run CertTool.exe* | Do step 3.2. |
| LoginServer: *TLS certificate has expired* or *expires in N days* | In CertTool choose **Create certificates → Create only a new server certificate (keep the current CA)**. |
| A server stops with *bind* and *Only one usage of each socket address* | Another program already uses port 443, 18080 or 1005. Close it (Skype, VMware, IIS and some VPN tools use 443), or check that the server is not already running in another window. |
| OfflineLauncher: *LoginServer is not running* | Start `LoginServer.exe` first (section 4). |
| OfflineLauncher: *GameServer is not running* | Start `GameServer.exe`. |
| OfflineLauncher: *torchlight_infinite.exe was not found* | Put `OfflineLauncher.exe` in the top folder of your game copy, next to `UE_game`. |
| The game says *Network request timeout. Please try again later.* | LoginServer is not running, the hosts line is missing (step 3.3), or the certificate is not installed (step 3.2). CertTool's **Overview and checks** shows which one. |
| The game shows the official server list, or no **TLI Offline** server | The hosts line is missing or was not saved. Repeat step 3.3, including `ipconfig /flushdns`. |
| The game wants to download an update | Your copy is not at patch 6269408 (step 3.4). TLIOffline only works with that version. |
| Your antivirus blocks a TLIOffline program | The programs are not signed by a publisher, so some antivirus tools are cautious. Allow them, or add the TLIOffline folder as an exception. |

To test the certificate and LoginServer together, start LoginServer, then choose **Test running LoginServer** in CertTool.

---

## 8. Going back to the official servers

1. Open the hosts file as in step 3.3, delete the line `127.0.0.1 version-overseas-torchlight.xdgtw.com`, save, and run `ipconfig /flushdns`.
2. Start the game from Steam as usual.

To put TLIOffline back later, add the line again. If you want to remove the TLIOffline certificate from Windows completely, use CertTool's **Windows certificates (view / uninstall)**.

---
