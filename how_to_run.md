# How to Run & Use This Project

## 1. How to Compile the Project

Run these commands from the project root directory (`d:\LSM_Tree_KV_Store`):

### Set Environment PATH (Windows PowerShell)
```powershell
$env:Path = "D:\Development_Software\cmake_files\bin;D:\Development_Software\msys2_files\ucrt64\bin;" + $env:Path
```

### Configure CMake
```powershell
cmake -S . -B build -G Ninja
```

### Build Executables
```powershell
cmake --build build
```

---

## 2. Executable Files & Folder Location

All compiled executable files are generated inside the **`build/`** directory:

- **`build/lsm_cli.exe`** — Interactive Command-Line Interface for demonstrating the storage engine live.
- **`build/lsm_tests.exe`** — Automated unit and integration test runner (25 test cases).
- **`build/lsm_benchmark.exe`** — Benchmark suite for performance and overhead measurements.

---

## 3. How to Run the Executables

### Run the Interactive CLI Demo
```powershell
.\build\lsm_cli.exe .\demo_db
```
*(You can pass any folder path like `.\demo_db` or `.\my_database`. If omitted, defaults to `./demo_db`)*

### Run Automated Tests
```powershell
.\build\lsm_tests.exe
```

Run specific test modules:
```powershell
.\build\lsm_tests.exe MemTable
.\build\lsm_tests.exe WAL
.\build\lsm_tests.exe SSTable
.\build\lsm_tests.exe Compaction
.\build\lsm_tests.exe Integration
```

### Run Benchmarks
```powershell
.\build\lsm_benchmark.exe
```

---

## 4. Supported Interactive CLI Commands

Inside `lsm_cli.exe`, the prompt is `annany_lsm>`.

### Data Manipulation Commands
- **`PUT <key> <value>`**
  - Inserts or updates a key-value pair. Supports values with spaces.
  - *Example:* `PUT user123 Annany Sharma`
- **`GET <key>`**
  - Retrieves the value associated with `<key>`.
  - *Example:* `GET user123`
  - *Returns:* `Annany Sharma` or `NOT_FOUND`
- **`DELETE <key>`**
  - Marks `<key>` as deleted by writing a tombstone.
  - *Example:* `DELETE user123`

### Engine Control & Maintenance Commands
- **`FLUSH`**
  - Manually flushes the in-memory MemTable to an immutable SSTable file on disk (`sst_XXXXXX.sst`) and resets `wal.log`.
  - *Example:* `FLUSH`
- **`COMPACT`**
  - Merges all active SSTables on disk into a single SSTable, resolves duplicate keys (newer wins), purges tombstones, and deletes old SSTables.
  - *Example:* `COMPACT`
- **`STATS`**
  - Displays current database directory, active SSTable list, and approximate MemTable size in bytes.
  - *Example:* `STATS`

### Utility & Exit Commands
- **`HELP`**
  - Displays the list of all supported commands with short explanations.
- **`EXIT`** or **`QUIT`**
  - Safely closes the database and exits the interactive shell.

---

## 5. Important Tips & Notes

- **Persistent Recovery Demonstration:**
  1. Open CLI: `.\build\lsm_cli.exe .\demo_db`
  2. Perform `PUT key1 value1`
  3. Exit CLI with `EXIT` (do not call `FLUSH`)
  4. Reopen CLI: `.\build\lsm_cli.exe .\demo_db`
  5. Perform `GET key1` $\to$ Returns `value1` (recovered from `wal.log`).

- **Disk File Verification:**
  While the CLI is running, inspect the database folder (`.\demo_db`) in a separate terminal:
  - `wal.log` — Write-Ahead Log
  - `manifest.txt` — List of active SSTable files
  - `sst_XXXXXX.sst` — Flushed SSTable files
