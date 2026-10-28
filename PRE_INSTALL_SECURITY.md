# Pre-install security checklist

This checklist is a release gate, not a promise that an installation is risk-free. Complete it for
the exact commit and television that will be used. If any required item is false or unknown, stop.

## Source and review

- [ ] Record the full 40-character commit SHA. Do not build from a moving branch or tag.
- [ ] Review every diff after the last audited commit.
- [ ] Confirm that the repository has no unexpected executable, archive, APK, AAB, native library,
      editor task, devcontainer, Git hook, or new workflow.
- [ ] Confirm that `main` is protected: pull request required, at least one approval, force-push and
      branch deletion blocked.
- [ ] Confirm that GitHub Actions remains disabled, or review every enabled workflow, action SHA,
      permission and secret before it runs.

## Supply chain

- [ ] Obtain Android SDK Platform-Tools only from Android Developers.
- [ ] Verify its published checksum before setting `TV_ADB_BINARY`.
- [ ] Do not install `tools/requirements.txt` for the hardened path. The legacy `adb-shell` and
      archived `rsa` packages are outside this profile.
- [ ] Verify every `gradle-wrapper.jar` against `GRADLE_WRAPPERS.sha256`.
- [ ] Verify every `distributionSha256Sum` against Gradle's official checksum page.
- [ ] Review every Gradle repository, plugin and dependency version before allowing network access.
- [ ] Build twice in fresh isolated environments and compare the unsigned APK contents. Investigate
      every unexplained difference before signing.

## Build and signing

- [ ] Build in a disposable VM or dedicated account with no SSH keys, browser profile, wallet,
      cloud credentials, unrelated environment variables or pre-existing ADB identity.
- [ ] Keep the signing key outside the repository and outside the build workspace.
- [ ] Use a dedicated signing identity, never Android's shared debug key, for a retained installation.
- [ ] Record the signed APK SHA-256 and signing-certificate fingerprint.
- [ ] Verify both values again immediately before installation.

## TV and permissions

- [ ] Use a non-critical test TV first, on an isolated or guest network and without personal accounts.
- [ ] Record model, firmware, Android version, current Home component, installed packages and disabled
      packages before the first change.
- [ ] Save the restoration commands offline and test restoration before the production TV is changed.
- [ ] Review each selected add-on separately. Do not grant accessibility, overlay,
      `WRITE_SECURE_SETTINGS`, device administration, microphone, camera, location, storage or
      notification access unless the exact feature needs it.
- [ ] Prefer disabling packages; do not uninstall system packages.
- [ ] Compare observed outbound domains with the domains documented for the selected features.

## Close-out

- [ ] Confirm the expected Home app and essential TV inputs, remote, keyboard, Play services and store.
- [ ] Disable wireless/network ADB and USB debugging unless explicitly required.
- [ ] Revoke debugging authorizations and verify the noted ADB port and port 5555 are closed.
- [ ] Remove temporary APKs, logs and credentials from the build environment.
- [ ] Store the commit SHA, APK hash, certificate fingerprint, permission decisions and restoration
      result with the audit record.

