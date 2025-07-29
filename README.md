

````markdown
# 🕹️ touchHLE with Windows 7 & Vista SP2 Support  
> Build guide using patched Rust (1.88.0) to run touchHLE for legacy Windows systems

---

## 📦 What is this?

This guide shows how to build [touchHLE](https://github.com/touchHLE/touchHLE) on **Windows 7** and **Windows Vista SP2** using a **patched version of Rust 1.88.0** that restores compatibility with these platforms.

---

## ⚙️ Prerequisites

- Visual Studio 2022 with C++ components  
- Python (for Rust build scripts)  
- Git   

the build was done on Windows 11, it should work on Windows 10 too

---

## 🛠️ How to Build

### 1. Install Visual Studio 2022

- Make sure **C++ Development Tools** are installed.
- Recommended setup: **VS2022 + Windows 11 SDK (26100)**

---

### 2. Open the proper command prompt

Launch:  
```bash
x64 Native Tools Command Prompt for VS2022
````

---

### 3. Clone Rust (with submodules)

```bash
git clone https://github.com/rust-lang/rust.git --recurse-submodules
cd rust
git checkout 1.88.0
```

---

### 4. Apply Windows 7 / Vista Patches

1. Download patches from this repo:
   👉 [https://github.com/e3kskoy7wqk/Firefox-for-windows-7](https://github.com/e3kskoy7wqk/Firefox-for-windows-7)

2. Files you need:

   * `1.88.0win7.diff` — for Windows 7 support
   * (Optional) `1.88.0vista.diff` — for Vista support

3. Copy the patch files to the Rust directory and apply them:

```bash
git apply --verbose --ignore-space-change 1.88.0win7.diff
git apply --verbose --ignore-space-change 1.88.0vista.diff  # optional
```

---

### 5. Build Patched Rust

```bash
python x.py build --target i686-pc-windows-msvc,x86_64-pc-windows-msvc
python x.py build library --target i686-pc-windows-msvc,x86_64-pc-windows-msvc
```

After building, your custom Rust binaries will be located in:

```
build\stage1\bin\
```

---

### 6. Build Cargo (Required for touchHLE)

Although Cargo doesn't use patched Rust code, it's **required** to build `touchHLE`.
Since you're using a custom Rust toolchain, you'll need to build Cargo using it.

#### Step-by-step:

1. **Temporarily copy the default Cargo from `stage0` to `stage1`**, so you can build a new version:

   ```bash
   copy build\stage0\bin\cargo.exe build\stage1\bin\
   ```

2. **Use this temporary Cargo to build a new, patched Cargo**:

   ```bash
   cargo build --release
   ```

   This will produce `cargo.exe` in `target\release`.

3. **Replace the temporary Cargo in `stage1` with the newly built one**:

   ```bash
   copy /Y target\release\cargo.exe build\stage1\bin\
   ```

4. *(Optional)*: Also replace the old Cargo in `stage0`, if needed:

   ```bash
   copy /Y target\release\cargo.exe build\stage0\bin\
   ```

Now you have both `cargo` and `rustc` in `stage1\bin\` using your patched setup.

---

### 7. Build touchHLE

```bash
git clone https://github.com/touchHLE/touchHLE.git --recurse-submodules
cd touchHLE
cargo build --release
```

After building, the `touchHLE` binary will be available in `target\release\`.

---

## Final Notes

* ✅ Make sure **VS2022 Redistributable** is installed — the emulator will crash at startup without it.
* 🪟 Vista users must have the **Platform Update** installed.
* 🚀 This setup allows you to run `touchHLE` natively on Windows 7 or Vista SP2!

---

## Credits

* Rust patch by: [@e3kskoy7wqk](https://github.com/e3kskoy7wqk)
* touchHLE: [github.com/touchHLE/touchHLE](https://github.com/touchHLE/touchHLE)

---

⭐ If this helped you, consider starring the original projects!

```
