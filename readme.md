# 👁️‍🗨️ Stealth Keylogger –

IMPORTANT: This tool is intended for authorized security testing and educational purposes only. Unauthorized use is illegal.

---

## 1. Introduction

This is a Windows keylogger that disguises itself as a normal system information tool. While it displays real‑time data about your computer (CPU, RAM, disk space, uptime), it secretly records every keystroke in the background. The logs are encrypted so only someone with the correct key can read them.

The program can be set to start automatically when Windows boots, and the window cannot be closed – it only minimizes, so the keylogger keeps running even if the user tries to exit. This makes it useful for security training, penetration testing, and understanding how such tools work.

---

## Screenshots

![System Information Window](main-window.png)

![Decrypted Logs](logs-decrypted.png)

---

## 2. Current Features

- Genuine‑looking Interface – Shows real system information (CPU, RAM, disk, uptime) with a modern acrylic glass effect on Windows 10/11.
- Stealth – The window cannot be closed; the user can only minimize it. The keylogger continues to run in the background.
- Robust Keylogging – Uses a low‑level keyboard hook (SetWindowsHookEx) with automatic fallbacks (pynput and polling) to ensure it works even if the hook is blocked.
- Encrypted Logs – Keystrokes are saved with a timestamp and the active window title, then encrypted with AES‑256‑GCM. The encryption key is derived from the machine’s unique ID, so logs from one computer cannot be read on another.
- Log Rotation – Prevents the log file from growing too large. Old logs are rotated and compressed automatically.
- Persistence – Optional: installs a Registry Run key and a scheduled task so the keylogger starts again after a reboot. (Requires administrator rights.)
- Single Instance – Only one copy of the program can run at a time.
- Uninstall Switch – A simple command‑line option removes all persistence entries.

---

## 3. Requirements

- Windows 7, 8, 10, or 11
- Python 3.6 or higher (if running the script directly)
- Administrator privileges are only needed for installing persistence

---

## 4. Installation

### Option A – Run from source (requires Python)

1. Save the keylogger.py script in a folder.
2. Open a Command Prompt in that folder.
3. Install the required libraries:
   ```bash
   pip install -r requirements.txt
   ```
   (The requirements.txt file contains pywin32, pycryptodome, and pynput.)
4. Run the script:
   ```bash
   python keylogger.py
   ```
   A window titled “System Information” will appear, and the keylogger will start.

### Option B – Create a standalone executable

1. Follow steps 1‑3 above.
2. Install PyInstaller:
   ```bash
   pip install pyinstaller
   ```
3. Build the executable:
   ```bash
   pyinstaller --onefile --noconsole --name "SystemInfo.exe" keylogger.py
   ```
4. The file SystemInfo.exe will be in the dist folder. You can copy it to any Windows computer (Python not needed) and double‑click to run.

---

## 5. Usage

- After starting, the System Information window opens and displays live stats. It looks like a normal tool.
- The keylogger is now active. Every key you press is captured and saved.
- You can minimize the window, but closing it (clicking the X) will only minimize it – the program stays in the background.
- To stop the keylogger, you must end the process via Task Manager or use the uninstall option (if you previously enabled persistence).

---

## 6. Viewing Logs

Logs are stored in:
   ```bash
   %APPDATA%\Microsoft\Windows\logs\keystrokes.enc
   ```
They are encrypted. To read them, you need the decryption function.

download the small script decrypt.py next to keylogger.py.

Then run:
   ```bash
   python decrypt.py "%APPDATA%\Microsoft\Windows\logs\keystrokes.enc
   ```

The output will look like:
   ```bash
   2025-03-28 14:23:05 | Notepad - Untitled | Hello
   2025-03-28 14:23:06 | Notepad - Untitled | [ENTER]
   ```

If you compiled the executable, you will need to reuse the Crypto class from the source code – copy it into a separate Python script and run it with the same key derivation method.

---

## 7. Persistence (Auto‑start after reboot)

If you want the keylogger to survive a restart, edit the script and change the line in the CONFIG dictionary from "persistence": False to "persistence": True. Then run the script as administrator (right‑click Command Prompt → Run as administrator, then python keylogger.py). The program will:
- Add a Registry Run key (HKCU\Software\Microsoft\Windows\CurrentVersion\Run)
- Create a scheduled task (daily at 09:00)

After that, the keylogger will start automatically every time you log in.

Important: This requires administrator rights. The script will show a UAC prompt to ask for permission.

---

## 8. Uninstalling

To remove all persistence entries, run the script (or the compiled EXE) with the --uninstall switch:
```bash
   python keylogger.py --uninstall
```
or
```bash
   SystemInfo.exe --uninstall
```

This will delete the Registry key and the scheduled task. It does not delete the log files – you can delete them manually from %APPDATA%\Microsoft\Windows\logs if you wish.

---

## 9. Defense Against This Tool

- Detection: Security software can detect hooks, unusual scheduled tasks, and registry entries. Monitor for processes with unexpected names.
- Prevention: Keep your system updated, use an EDR (Endpoint Detection and Response) solution, and limit user privileges – a standard user cannot install persistence system‑wide.
- Response: If you suspect a keylogger, check Task Manager for suspicious processes, look at the startup folder, and examine scheduled tasks.

---

## 10. Future Scope & Add‑on Features

The current version is a stable, educational keylogger. Possible improvements (not yet implemented) include:

- Remote C2 (Command & Control) – Send logs to a server over HTTPS or SMTP.
- Clipboard Logging – Capture copied text.
- Screenshots – Take periodic screenshots.
- Process Injection – Hide the keylogger inside a legitimate process.
- Stealthier Hooks – Use kernel‑level drivers or alternative hooking techniques.
- Configurable via external file – Allow editing settings without recompiling.
- Tray Icon – Minimize the window to the system tray for even more stealth.

These features would make the tool more advanced and closer to real‑world malware, but they also increase risk. Use them only in controlled environments.

---

## ⚠️ Educational Purpose & Compliance Notice

This repository is intended **strictly for educational and authorized security research**. The code demonstrates common malware techniques (keylogging, persistence, encryption) to help defenders understand how such tools work and how to detect them.

### 🧪 Intended Use

- Run this software **only** in isolated environments you own (e.g., a virtual machine) or have explicit written permission to test.
- Use the code to learn about:
  - Low‑level keyboard hooks
  - Registry and scheduled task persistence
  - Log encryption and rotation
  - Defensive countermeasures

### 🚫 Prohibited Use

- **Do not** deploy this tool on any system without the owner’s consent.
- **Do not** use it for actual surveillance, espionage, or any malicious purpose.
- **Do not** distribute compiled binaries that could be misused.

### 📜 GitHub Terms of Service Compliance

- This repository contains **source code only** – no pre‑compiled executables are provided.
- The code is presented as a learning resource and does **not** encourage or facilitate illegal activities.
- By using this repository, you agree to abide by all applicable laws and GitHub’s [Acceptable Use Policies](https://docs.github.com/en/site-policy/acceptable-use-policies/github-acceptable-use-policies).

### ⚖️ Legal Disclaimer

The author(s) are not responsible for any misuse of this software. Unauthorized use may violate local, state, or federal laws, including the Computer Fraud and Abuse Act (CFAA) in the United States and similar laws in other jurisdictions. You assume full responsibility for your actions when using this code.