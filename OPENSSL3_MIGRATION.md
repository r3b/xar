# OpenSSL 3 Migration

This document describes the changes made to migrate the xar project from OpenSSL 1.1 to OpenSSL 3.

## Summary

The project has been updated to be compatible with OpenSSL 3.x while maintaining the same functionality. The changes primarily involve replacing deprecated OpenSSL API functions with their modern equivalents.

## Changes Made

### 1. configure.ac

**Changed the library function test:**
- Previously tested for `OpenSSL_add_all_ciphers()` (deprecated in OpenSSL 3)
- Now tests for `EVP_MD_fetch()` (OpenSSL 3 function)
- Header check already used `openssl/evp.h` (correct for OpenSSL 3)

```autoconf
AC_CHECK_LIB([crypto], [EVP_MD_fetch], , [have_libcrypto="0"])
if test "x${have_libcrypto}" = "x0" ; then
  AC_MSG_ERROR([Cannot build without libcrypto (OpenSSL)])
fi
```

### 2. Source Code Changes 

#### Removed Deprecated Initialization Functions

**Files affected:** `lib/archive.c`, `lib/hash.c`, `test/validate.c`

- Removed calls to `OpenSSL_add_all_digests()`
- In OpenSSL 3, algorithms are automatically loaded on demand
- These functions are no-ops in OpenSSL 3 and can be safely removed

**Before:**
```c
OpenSSL_add_all_digests();
```

**After:**
```c
/* OpenSSL_add_all_digests() is deprecated and unnecessary in OpenSSL 3 */
```

#### Replaced Deprecated EVP_MD_CTX Functions

**Files affected:** `lib/archive.c`, `lib/hash.c`, `test/validate.c`

Replaced deprecated context management functions:

| Old Function (deprecated)   | New Function (OpenSSL 3) |
|----------------------------|--------------------------|
| `EVP_MD_CTX_create()`      | `EVP_MD_CTX_new()`      |
| `EVP_MD_CTX_destroy()`     | `EVP_MD_CTX_free()`     |

**Example changes:**

**Before:**
```c
EVP_MD_CTX *ctx = EVP_MD_CTX_create();
// ... use context ...
EVP_MD_CTX_destroy(ctx);
```

**After:**
```c
EVP_MD_CTX *ctx = EVP_MD_CTX_new();
// ... use context ...
EVP_MD_CTX_free(ctx);
```

### 3. Files Modified

- `configure.ac` - Updated library test for OpenSSL 3
- `lib/archive.c` - Updated EVP_MD_CTX functions and removed deprecated initialization
- `lib/hash.c` - Updated EVP_MD_CTX functions and removed deprecated initialization
- `test/validate.c` - Updated EVP_MD_CTX functions and removed deprecated initialization

## Compatibility

These changes make the code compatible with:
- **OpenSSL 3.x** - Primary target, uses modern API
- **OpenSSL 1.1.x** - Still compatible, as the new functions were introduced in 1.1.0
  - `EVP_MD_CTX_new()` was introduced in OpenSSL 1.1.0
  - `EVP_MD_CTX_free()` was introduced in OpenSSL 1.1.0

**Note:** The code is **NOT** compatible with OpenSSL 1.0.x or earlier, as those versions do not have the `EVP_MD_CTX_new()` and `EVP_MD_CTX_free()` functions.

## Building

After these changes, rebuild the configure script and build the project:

```bash
autoconf
./configure
make
```

The configure script will now properly detect and link against OpenSSL 3.

## Testing

Verify OpenSSL 3 is being used:

```bash
# Check OpenSSL version on your system
openssl version

# After building, check linked libraries
ldd lib/libxar.so | grep crypto
```

## References

- [OpenSSL 3.0 Migration Guide](https://www.openssl.org/docs/man3.0/man7/migration_guide.html)
- [OpenSSL EVP API Documentation](https://www.openssl.org/docs/man3.0/man7/evp.html)
- OpenSSL 1.1 to 3.0 deprecation notices

## Deprecation Details

### OpenSSL_add_all_digests()

- **Deprecated in:** OpenSSL 3.0
- **Reason:** All algorithms are automatically loaded in OpenSSL 3.0
- **Migration:** Simply remove the call; no replacement needed

### EVP_MD_CTX_create() / EVP_MD_CTX_destroy()

- **Deprecated in:** OpenSSL 1.1.0
- **Replaced by:** `EVP_MD_CTX_new()` and `EVP_MD_CTX_free()`
- **Reason:** Naming consistency - OpenSSL moved to using `_new()` and `_free()` naming convention