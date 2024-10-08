# Changelog

## Unreleased

## 0.12.2 (2023/09/19)

This version bumps Wasmtime to 10.0.2 to include [PR #6372](https://github.com/bytecodealliance/wasmtime/pull/6372).

### `libwasm_runtime.so`
- Dependencies:
  - Upgrading Wasmtime dependency:
    - `wasmtime` from `10.0.1` to `10.0.2`.
  - Updated `cargo.lock` dependencies via `cargo update`.


## 0.12.1 (2023/07/03)

This version bumps Wasmtime to 10.0.1 to include [PR #6348](https://github.com/bytecodealliance/wasmtime/pull/6348) and other improvements such as preliminary support for [WASI preview 2](https://github.com/bytecodealliance/wasmtime/blob/main/RELEASES.md#1000).

### `libwasm_runtime.so`
- Dependencies:
  - Upgrading Wasmtime dependency:
    - `wasmtime` from `7.0.1` to `10.0.1`.
    - `once_cell` to `1.18.0`.
  - Updated `cargo.lock` dependencies via `cargo update`.

## 0.12.0 (2023/05/30)

This version introduces a new Apache directive: `WasmMapCGIFileNames`.
It enables/disables the mapping of `SCRIPT_FILENAME` based on the different `WasmMapDir` instances when `WasmEnableCGI` is enabled.

In addition, it normalizes filename paths for both Windows and Linux styles.

```apache
WasmEnableCGI On
WasmMapCGIFileNames On
WasmMapDir /app C:/myapp/htdocs
```

In the example, `SCRIPT_FILENAME` will store `/app/index.php` instead of the host path `C:/myapp/htdocs/index.php`.
Without this setting, we would also need to provide a `WasmDir` granting access to `C:/myapp/htdocs` as the Wasm module would be trying to access it (or setting `WasmMapDir C:/myapp/htdocs C:/myapp/htdocs`).
            

## 0.11.3 (2023/05/08)

This release fixes a regression issue introduced in [0.11.2](#0112-20230502) when opening directories on Windows. It doesn't affect other platforms. 
The issue is realated to Wasmtime 8.0.1 (see [PR #6348](https://github.com/bytecodealliance/wasmtime/pull/6348)), so downgrading to `7.0.1`.

### `libwasm_runtime.so`
- Dependencies:
  - Downgrading Wasmtime dependency:
    - `wasmtime` from `8.0.1` to `7.0.1`.
  - Updated `cargo.lock` dependencies via `cargo update`.
  

## 0.11.2 (2023/05/02)

This is a security update to bump Wasmtime to 8.0.1 given the CVE published (low severity) and addressed in:
 - [GHSA-ch89-5g45-qwc7 (CVE-2023-30624)](https://github.com/bytecodealliance/wasmtime/security/advisories/GHSA-ch89-5g45-qwc7)


### `libwasm_runtime.so`
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `8.0.1`.
    - `anyhow` to `1.0.71`.
  - Updated `cargo.lock` dependencies via `cargo update`.


## 0.11.1 (2023/03/31)

- Fixes [#40](https://github.com/vmware-labs/mod_wasm/issues/40), where a new thread could not create a new Wasm execution context while another thread was running a Wasm module. This was only measurable if the execution of the Wasm module was long enough in time or if it took longer than expected (i.e.: I/O issues, infinite loop, etc.). Note that CPU-limited Wasm executions are not implemented yet (see [#9](https://github.com/vmware-labs/mod_wasm/issues/9)).

### `libwasm_runtime.so`
- Internal refactoring of `execution_ctx.rs` to address [#40](https://github.com/vmware-labs/mod_wasm/issues/40).
- Updated `cargo.lock` dependencies via `cargo update`.

## 0.11.0 (2023/03/27)

- In this version, Wasm modules can now return any output type via stdout, including binaries with non UTF-8 bytes sequences or `\0` NULL terminators in the middle.
- Also added some minor improvements for error management and building scripts.

### `mod_wasm.so`
- Support for the new `wasm_executionctx_run()` signature in the wasm_runtime C-API.
- Better error management when invoking `ap_scan_script_header_err_strs()`.
- Some improvements in `build.sh` per [#36](https://github.com/vmware-labs/mod_wasm/issues/36).

### `libwasm_runtime.so`
- Now the Wasm module `stdout` is modelled as a `Vec<u8>` instead of a `String`:
  - This prevents an exception when a module emits a non UTF-8 output.
- New signature for `wasm_executionctx_run()` in the C-API now requires buffer and length pointers:
  - This allows NULL terminators (`\0`) in the middle of the output to be managed by C without truncating the string (ie.: requires `fwrite()` instead of `fprintf()`, etc.).
- Better `clean_all` target in Makefile.
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `7.0.0`.
    - `anyhow` to `1.0.70`.
  - Updated `cargo.lock` dependencies via `cargo update`.

### `httpd-mod-wasm` demo container
- Enabling `mod_rewrite`.
- Adding `/php-hello-slim` demo.
- Adding `WasmEnv TMPDIR /tmp` directive to all PHP-related demos.
- Adding stubs for Drupal and Drupal-Zero demos (still WIP).


## 0.10.3 (2023/03/08)

This is a security update to bump Wasmtime to 6.0.1 given the two CVE published (one critical) and addressed in:
 - [GHSA-ff4p-7xrq-q5r8 (CVE-2023-26489)](https://github.com/bytecodealliance/wasmtime/security/advisories/GHSA-ff4p-7xrq-q5r8)
 - [GHSA-xm67-587q-r2vw (CVE-2023-27477)](https://github.com/bytecodealliance/wasmtime/security/advisories/GHSA-xm67-587q-r2vw)


### `libwasm_runtime.so`
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `6.0.1`.
    - `anyhow` to `1.0.69`.
    - `once_cell` to `1.17.1`.
  - Updated `cargo.lock` dependencies via `cargo update`.


## 0.10.2 (2023/01/09)

### `libwasm_runtime.so`
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `4.0.0`.
      - As a consequence, update code to invoke `get_typed_func<Params, Results>()` with only two arguments.
    - `anyhow` to `1.0.66`.
    - `once_cell` to `1.17.0`.
  - Updated `cargo.lock` dependencies via `cargo update`.

## 0.10.1 (2022/12/12)

### `mod_wasm.so`
- All logging is now properly route to `ap_log_error()`, `ap_log_perror()` and `ap_log_rerror()`.
- Apache-2.0 extended license headers.
- Only C89 comments (/* */).

### `libwasm_runtime.so`
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `3.0.1`.
  - Updated `cargo.lock` dependencies via `cargo update`.

## 0.10.0 (2022/11/28)

- In this version, among other improvements, we introduce two major features implementing [#6](https://github.com/vmware-labs/mod_wasm/issues/6), [#7](https://github.com/vmware-labs/mod_wasm/issues/7), and [#16](https://github.com/vmware-labs/mod_wasm/issues/16).
  1. **Wasm multi-module support:**
   
     Now you can specify different Wasm modules to be used in different routes. For instance, now it’s possible with one-single Apache instance to load simultaneously the Wasm builds for the [PHP](https://github.com/vmware-labs/webassembly-language-runtimes/releases) and [Python](https://github.com/tiran/cpython-wasm-test/releases) interpreters (and any other languages that compile to Wasm now or in the future). 

  2. **Shared Wasm modules:** 
   
     Now you can specify different per-route configurations to the same Wasm module. The Wasm binary is loaded in memory only once, and the different configurations are applied to the Wasm module per-HTTP request. Also, now each Wasm execution owns its own `stdout` buffer, so there are no interlocks between executions anymore.


  Combining all together, you can now have a more flexible configuration such as: 

    ```apache
    <Location /wordpress> 
        SetHandler wasm-handler 
        WasmModule /var/www/modules/php7.4.32.wasm 
        WasmDir    /tmp 
        … 
    </Location> 

    <Location /python-app> 
        SetHandler wasm-handler 
        WasmModule /var/www/modules/python3.11.wasm 
        WasmArg    /var/www/python-app/app.py 
        … 
    </Location> 

    <Location /python-app2> 
        SetHandler wasm-handler 
        WasmModule /var/www/modules/python3.11.wasm 
        WasmArg    /var/www/python-app2/app2.py 
        … 
    </Location> 
    ```

### `mod_wasm.so`
- Removed the `WasmRoot` directive. Now, `WasmModule` accepts the full Wasm module *filepath*.
- Removed legacy code:
  - In previous versions, given the only-one Wasm module limitation, `mod_wasm.c` implemented a mechanism to reset the WASI context for each HTTP request. This is not needed anymore.
  - Old code inherited from `mod_example_hooks.c` and not used.

### `libwasm_runtime.so`
- C-API:
  - Reorganized functions and their names to be compliant with the new features and to be more idiomatic (🚨 breaks backwards compatibility 🚨).
  - Removed any kind of logic different from C <---> Rust FFI data transformation.
  - Improved error messages with more information.
- Fixed most suggestions from `cargo clippy -- -W clippy::pedantic`.
- Dependencies:
  - Bump version dependencies:
    - `wasmtime` to `3.0.0`.
    - `once_cell` to `1.16.0`.
  - New dependency:
    - `rand` to `0.8.5`.
  - Updated `cargo.lock` dependencies via `cargo update`.

## 0.8.0 (2022/11/14)

- Updated documentation.
- Added `libxml2-dev` as a dependency for building `mod_wasm.so`.
- Added version check mechanism when building `mod_wasm.so`. Now it checks `libwasm_runtime.so` version is compatible and the minimum required.
  
### `mod_wasm.so`
- Fixed directory hierarchy to be compliant with Apache Server (httpd).
  
### `libwasm_runtime.so`
- Bump version dependencies:
    - `wasmtime` to `2.0.2` (and other related Wasmtime crates).

## 0.7.0 (2022/11/03)

- Added support for sending HTTP Request body into WASI `stdin`.

## 0.6.0 (2022/10/26)

- Fixed build for aarm64.

## 0.5.0 (2022/10/21)

- Bump project version to `0.5.0` since it has been successfully tested with Python and PHP Wasm modules by other developers.

### `libwasm_runtime.so`
- Bump version dependencies:
    - `wasmtime` to `2.0.0`.
    - `anyhow` to `1.0.66`.
    - `once_cell` to `1.15.0`.

## 0.1.0 (2022/10/04)

- Initial `mod_wasm` version!
