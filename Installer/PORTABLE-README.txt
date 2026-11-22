Sandboxie-Plus x64 — local portable build
==========================================

This folder contains a locally modified, self-compiled Sandboxie-Plus build.

Local build change
------------------
The driver build is configured to skip Sandboxie-Plus Supporter Certificate
entitlement validation and enables the Plus capability flags:
  - enhanced-security box types
  - box encryption/protection
  - advanced network features
  - isolated desktops

This does NOT disable Windows kernel-driver signing enforcement. It also
does NOT disable the separate Sandboxie process-image signature checks.
The build does not create or impersonate a supporter certificate.

Portable use
------------
1. Extract the entire ZIP to a writable folder.
2. Run SandMan.exe. On first launch, portable mode may ask for administrator
   permission to create/start the Sandboxie services and driver.
3. The Sandboxie.ini and Sandboxie-Plus.ini files in this folder make the
   configuration local to this directory.

Driver signing
--------------
The GitHub Actions build does not sign the kernel driver. On normal Windows
installations Windows may refuse to load an unsigned driver. For local
development/testing only, the upstream build notes describe Windows Test
Mode and the `bcdedit /set testsigning on` command (administrator console
and reboot required). Do not disable Windows driver-signing protections on
a machine used for sensitive work.

Scope and licensing
-------------------
This local override is intended for private, non-commercial use. The
repository's LICENSE.Plus applies to the Sandboxie-Plus UI and sets
additional conditions for distribution of self-compiled binaries.
LICENSE.Classic applies to GPLv3 components. Keep all copyright and license
notices. Read both files before sharing or publishing this build.
