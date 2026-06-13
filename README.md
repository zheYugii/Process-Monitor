# Process-Monitor

**Dynamic analysis tool – hook, dump, and dissect any Windows process.**  
*Makes Themida packing look like someone who tried to obfuscate Python code with Scratch tutorials.*

## What it does

- **Injects `Monitor.dll`** into any running or freshly launched process.
- **Hooks critical APIs**: `VirtualAlloc`, `VirtualAllocEx`, `connect`, `send`, `recv`, `WSASend`, `WSARecv`, `RegSetValueExW`, `CreateServiceW`, `CreateProcessW/A`, `ExitProcess`.
- **Automatically decrypts PyArmor** – hooks `__pyarmor__` and dumps decrypted strings from the stack.
- **Forces a full memory dump** of all readable private regions after 50ms – no transient data escapes.
- **Ultra‑fast scanner** – scans every 1ms (private memory only, no wasted cycles).
- **Logs webhook URLs** (Discord, Telegram) from network traffic and memory strings.
- **Detects packers**: UPX, VMProtect, Themida.
- **Outputs everything to `C:\HookAnalysis\`** – JSON logs, memory dumps, extracted strings, and a canary file.

## Usage

1. Place `hooker.exe` and `Monitor.dll` in the **same directory**.
2. Run `hooker.exe` (as Administrator for best results).
3. Enter the target process name (e.g., `notepad.exe`) or the full path to an executable.
4. The tool will:
   - If the process is running: inject the DLL immediately.
   - If not running: launch the process **suspended**, inject the DLL, then resume.
5. Press **`q`** at any time to create `C:\hooker_stop.txt` – the DLL will detect this file and cleanly stop all hooks.
6. Browse `C:\HookAnalysis` for:
   - `analysis_log.json` – timestamped events with callstacks.
   - `Leaked-Strings.txt` – all extracted ASCII/UTF‑16 strings.
   - `memory_dumps\*.bin` – suspicious or high‑entropy regions.
   - `injected_here.txt` – confirms the DLL loaded successfully.

## Requirements

- Windows 7 or later (x64).
- Administrator privileges (recommended for full process access).
- Visual C++ Redistributable (if missing, the injector will still run, but hooks may fail).

## Why "makes Themida look like Scratch obfuscation"?

Themida is a commercial software protector that many malware authors use. This tool:
- Hooks `VirtualAlloc` to catch unpacked code the moment it's allocated.
- Dumps entire private memory regions every 1ms – even short‑lived decrypted payloads.
- Injects before the main thread runs (using `CREATE_SUSPENDED`), so it sees everything from the very first instruction.
The result: Themida's "advanced" protection crumbles under this level of coverage.

## Notes

- The DLL **does not** install any persistence; stop the injected process or press `q` to clean up.
- All outputs are **unfiltered and raw** – you get everything the malware does.
- For PyArmor, the hook attempts to decrypt obfuscated code dynamically; not guaranteed to work on all versions, but effective on most.

## License

Same as the original Process-HookerV2 – GPL‑3.0 (inherited from Detours and pyinstxtractor).

---

**Drop the DLL, run the injector, and watch the malware confess.**
