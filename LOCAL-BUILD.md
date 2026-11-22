# Local Sandboxie-Plus build

Modified source date: **2026-11-22**.

This fork adds a local build-time switch in
`Sandboxie/core/drv/local_build_config.h`.

```c
#define SBIE_LOCAL_NO_SUPPORTER_CERT 1
```

With the switch enabled, `KphValidateCertificate()` skips the Supporter
Certificate parser/entitlement checks and sets the four Plus feature flags
(`opt_sec`, `opt_enc`, `opt_net`, `opt_desk`) for this local build. The
certificate type remains unset; no fake certificate is generated.

The override is deliberately limited to Supporter Certificate feature
gating. It does not change Windows kernel driver signing, the custom
process-image signature validation in `MyIsCallerSigned()`, nor the
commercial-use certificate gate in the setup wizard. The setup wizard
still requires an appropriate business certificate when "business use"
is selected.


## Fix for MSB/LNK1104: missing NtCRT inputs

The upstream `.gitignore` contains broad `x86/`, `x64/`, and `*.obj` rules.
Sandboxie's `Sandboxie/common/NtCRT/` tree contains checked-in linker inputs,
not build output: two `ntdll.def` files and prebuilt `.obj` files for Win32,
x64, ARM64, and ARM64EC. This fork explicitly re-includes those inputs.

When applying this overlay to an existing fork, make sure the complete tree is
committed (changing `.gitignore` alone does not add files that Git previously
ignored):

```powershell
Test-Path .\Sandboxie\common\NtCRT\x64\ntdll.def
Test-Path .\Sandboxie\common\NtCRT\x86\ntdll.def
git add .gitignore Sandboxie/common/NtCRT/
git status --short
git commit -m "Track required NtCRT linker inputs"
git push
```

The CI preflight checks every NtCRT `.def` and `.obj` input referenced by
`Sandboxie/core/dll/SboxDll.vcxproj` before the Win32 and architecture builds.
If a required input is omitted from a fork commit, the workflow reports the
missing file before invoking MSBuild.

## GitHub Actions

The existing `CI` workflow still builds the project. A dependent
`Package_Portable_x64` job now downloads its `Sandboxie_x64` artifact,
adds the portable INI files and license notices, then uploads
`Sandboxie-Plus-x64-portable` with a SHA-256 checksum.

After pushing this fork to GitHub, open **Actions → CI**. The portable ZIP
appears as a separate artifact after the x64 build succeeds.

## Important limitations

- The workflow packages the build; it does not sign the kernel driver.
  Windows may block the unsigned driver unless it is properly signed or the
  machine is intentionally configured for driver test-signing.
- The source archive was statically inspected and patched here, but this
  environment does not have the Windows SDK/WDK/MSVC toolchain, so the
  binaries have not been compiled or run in this session.
- The root `LICENSE.Plus` is a custom license for the Plus UI, not GPLv3.
  It permits private, non-commercial use of self-compiled binaries and
  places conditions on public distribution. See the license text included
  in the repository and in the portable package.

The Win32 core build also requires the checked-in NtCRT inputs under
`Sandboxie/common/NtCRT/x86/` (`ntdll.def` and four prebuilt `.obj` files).
The root `.gitignore` explicitly re-includes these files; commit them to the
fork so the GitHub Actions checkout receives them.

