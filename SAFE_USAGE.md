# Safe Usage Guide

> **Security warning:** Do not install, build, or run this repository before reviewing its source code, dependencies, Gradle wrappers, and requested Android permissions.

## Audited baseline

This fork was initially reviewed at upstream commit:

`095b71ad93d99ea28d94d97b06385709d354b795`

Review every change made after this commit before using it.

## Before building

- Use an isolated virtual machine, container, or dedicated user account.
- Do not expose personal SSH keys, API tokens, browser data, cryptocurrency wallets, cloud credentials, or unrelated environment variables.
- Do not open the repository in Android Studio before completing the review, because Gradle synchronization may execute build logic automatically.
- Keep GitHub Actions disabled until every workflow and third-party action has been reviewed.
- Verify all `gradle-wrapper.jar` files and Gradle distribution checksums against the official Gradle checksum reference.
- Compare every wrapper against [GRADLE_WRAPPERS.sha256](GRADLE_WRAPPERS.sha256). Stop if any path
  or digest differs.
- Review all Python and Gradle dependencies before downloading them.
- Never build from an unreviewed branch or commit.

## ADB transport and key protection

The audited installation path requires a verified Google ADB executable through
`TV_ADB_BINARY`. In this mode, the tools do not import the legacy `adb-shell`, `rsa`, or
`cryptography` Python packages. Google ADB manages its own pairing and authentication data.

If `TV_ADB_BINARY` is not set, the tools use the compatibility-only Python client. That path is
outside the hardened installation profile and must not be used for a security-sensitive installation.

In legacy mode, the personal system key at `~/.android/adbkey` must not be read automatically. It is available only after explicit opt-in:

`TV_USE_SYSTEM_ADB_KEY=1`

Only enable this option when reusing the system ADB identity is necessary. An ADB private key grants access to every device that previously trusted it and must be treated as a sensitive credential.

Never commit ADB private keys to the repository.

## Start with the minimum scope

Begin with the main launcher only.

Do not install optional add-ons unless their source code and permissions have been reviewed separately. Exercise particular caution with components requesting:

- `WRITE_SECURE_SETTINGS`
- Accessibility service access
- Display-over-other-apps or overlay access
- Device administration privileges
- Broad storage access
- Notification access
- Microphone, camera, location, or nearby-device access

Grant only permissions that are necessary for a specific feature.

## Before modifying a TV

- Record the TV model, Android version, installed packages, and current launcher.
- Back up important settings whenever possible.
- Confirm every package name targeted for disabling or removal.
- Keep the documented restoration procedure available offline.
- Test restoration before making extensive changes.
- Prefer disabling packages before attempting permanent removal.

## Isolated first test

Use a non-critical television or test device first.

Keep the test environment separate from networks and accounts containing sensitive personal or business data. Do not use production credentials during the initial test.

Do not make a production installation until the complete
[pre-install security checklist](PRE_INSTALL_SECURITY.md) is signed off for the exact commit, APK
hash, TV model and selected add-ons.

## After use

- Disable wireless or network ADB.
- Disable USB debugging if it is no longer needed.
- Revoke previously authorized debugging computers from Android developer settings.
- Remove permissions that are no longer required.
- Confirm that the original launcher and disabled packages can be restored.
- Delete repository-specific ADB keys when they are no longer needed.

## Dependency policy

Dependencies must use reviewed, exact versions. Floating version ranges are not acceptable for security-sensitive builds.

Package hashes should be generated and reviewed in an isolated environment before installation. A version pin alone does not prove that a downloaded artifact is trustworthy.

The `rsa` Python package is archived upstream. It remains pinned only for the compatibility-only
legacy `adb-shell` mode, whose package metadata declares it as a dependency. It is forbidden in the
hardened installation profile. The Google ADB path does not import or require it.

## Incident response

If an unexpected network connection, permission request, package modification, or authentication prompt occurs:

1. Stop the process.
2. Disconnect the test device from the network.
3. Preserve logs and the exact commit identifier.
4. Revoke ADB authorization.
5. Restore the device from the documented baseline.
6. Review the relevant source code before trying again.

## Security status

This hardening guide reduces accidental exposure and unsafe use. It does not certify the repository as malware-free or guarantee that every device-specific operation is safe.
