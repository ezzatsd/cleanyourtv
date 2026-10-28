# Use a verified Google ADB connection

The Google ADB backend is the preferred mode for security-sensitive use. When
`TV_ADB_BINARY` is set, `tools/tv.py` does not import or require the legacy
`adb-shell`, `rsa`, or `cryptography` Python packages.

Obtain Android SDK Platform-Tools from the official Android Developers source and
verify the download before use. On the TV, enable Wireless debugging. Pair with
the address, port, and code from **Pair device with pairing code**, then connect
to the separate address and port on the main Wireless debugging screen:

```bash
adb pair <tv-ip>:<pairing-port>
adb connect <tv-ip>:<connection-port>
export TV_ADB_BINARY=/absolute/path/to/adb
export TV_HOST=<tv-ip>:<connection-port>
python3 -I tools/tv.py --probe
```

Google ADB manages pairing and authentication; the tools neither read its private
key nor implement TLS. Keep the main screen's current connection port: it can
change when debugging is toggled or the TV restarts. `TV_HOST` and `TV_PORT`
have their usual meaning. IPv6 endpoints use `[address]:port`. The executable
may also be a verified name on PATH, such as `adb`.

The backend requires an already connected, authorized transport. Every operation
selects that exact endpoint with `adb -s`; it never falls back to another TV or
runs `adb tcpip` to change the TV's debugging mode. Its `close()` leaves the
shared Google ADB connection and server running, so other tools keep working.

The setting is inherited by `tools/disable.sh`, `tools/restore.sh`,
`tools/privacy.py`, `tools/theme.sh`, and `launcher/build-and-install.sh`;
their existing safety checks still run. Back up first and follow `PROMPT.md`
before changing a TV. For example:

```bash
python3 -I tools/tv.py --screenshot shots/before.png
```

## Legacy Python client

If `TV_ADB_BINARY` is not set, the tools fall back to `adb-shell`. This
compatibility mode uses the packages in `tools/requirements.txt`.
`adb-shell==0.4.4` declares the archived `rsa` package as a dependency, even
though this fork uses its `CryptographySigner` instead of `PythonRSASigner`.

Avoid the legacy mode unless it has been reviewed and tested in an isolated
environment. Do not remove the explicit `rsa` pin while `adb-shell` remains:
otherwise its archived transitive dependency would be resolved without an exact
version.

When finished, switch Wireless debugging and any other enabled debugging modes
off on the TV and revoke the authorizations. Check the noted connection port with
`python3 tools/adb-closed.py <tv-ip> <connection-port>` before rebooting, then
check that debugging remains off after the reboot. Pairing codes, keys, and
screenshots must remain private.
