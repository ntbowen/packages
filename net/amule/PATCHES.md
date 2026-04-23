# amule Patches

## Upstream Patches (001–006)

| File | Description |
|------|-------------|
| `001_amule-dlp.patch` | aMule DLP (Download Link Protocol) support |
| `002_enable_upnp_cross_compile.patch` | Enable UPnP for cross-compilation |
| `003_gcc_11.patch` | Fix build with GCC 11 |
| `004_allow_to_build_with_autoconf_2.70_and_later.patch` | Fix autoreconf compatibility with autoconf >= 2.70 |
| `005-fix-wxSTRING-MAXLEN-undefined.patch` | Fix `wxSTRING_MAXLEN` undefined error |
| `006-amule-fix-geoip_url.patch` | Fix GeoIP database URL |

## Local Patches (007–009): wxWidgets 3.3 Compatibility

These patches fix build failures caused by breaking changes in wxWidgets 3.3,
which enforces that `_()` translation macros only accept string literals, and
removes implicit `operator+` between `CFormat`/`const char*` and `wxString`.

### `007-fix-wx33-msgid-literals.patch`

**File**: `src/TextClient.cpp`

**Problem**: wxWidgets 3.3 added a `static_assert` to `_()` that rejects non-literal
arguments. Two call sites passed a runtime `wxString` expression into `_()`:

```cpp
// Before (broken with wx 3.3):
Show(_("Processing by hash: " + token + wxT("\n")));
Show(_("Processing by filename: " + token + wxT("\n")));
```

**Fix**: Move the runtime string concatenation outside of `_()`:

```cpp
// After:
Show(_("Processing by hash: ") + token + wxT("\n"));
Show(_("Processing by filename: ") + token + wxT("\n"));
```

---

### `008-fix-wx33-cformat-operator.patch`

**Files**: `src/amule.cpp`, `src/BaseClient.cpp`, `src/ExternalConn.cpp`

**Problem**: wxWidgets 3.3 removed the implicit `operator+` between `CFormat` (or
`const char[]` / `const wchar_t[]`) and `wxString`. Three call sites relied on
this implicit conversion:

```cpp
// amule.cpp — const wchar_t[] + CFormat:
wxString info = wxT(" --- ") + CFormat(...) % new_version + wxT(" ---\n\n");

// BaseClient.cpp — CFormat + const wchar_t[]:
sRet = (CFormat(...) % GetUserName() % GetUserIDHybrid()) + wxT(" ");

// ExternalConn.cpp — const char[] + CFormat:
response->AddTag(CECTag(EC_TAG_STRING,
    wxTRANSLATE("Invalid protocol version.")
    + CFormat(wxT("...")) % proto_version % ...));
```

**Fix**: Add explicit `wxString()` casts to force the correct `operator+` overload:

```cpp
// amule.cpp:
wxString info = wxString(wxT(" --- ")) + wxString(CFormat(...) % new_version) + wxT(" ---\n\n");

// BaseClient.cpp:
sRet = wxString(CFormat(...) % GetUserName() % GetUserIDHybrid()) + wxT(" ");

// ExternalConn.cpp:
response->AddTag(CECTag(EC_TAG_STRING,
    wxString(wxTRANSLATE("Invalid protocol version."))
    + wxString(CFormat(wxT("...")) % proto_version % ...)));
```

---

### `009-fix-missing-makefile-am.patch`

**Files**: `intl/Makefile.am` *(new)*, `src/pixmaps/flags_xpm/Makefile.am` *(new)*

**Problem**: Newer versions of `automake` (run during `autoreconf`) strictly require
a `Makefile.am` to exist for every subdirectory listed in `AC_CONFIG_FILES` and
every `SUBDIRS` entry. The amule 2.3.3 tarball is missing both:

- `intl/Makefile.am` — referenced in `configure.ac:468`
- `src/pixmaps/flags_xpm/Makefile.am` — listed as `SUBDIRS` in `src/pixmaps/Makefile.am`

This caused `automake` to exit with error 1, aborting the entire configure step.

**Fix**: Create minimal stub `Makefile.am` files for both directories.
