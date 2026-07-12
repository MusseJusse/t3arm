# Running T3 Code on this headless ARM server

This fork runs T3 Code as the `t3arm.service` systemd service on this ARM
machine. The service runs as the `musse` user from `/home/musse`, uses the local
`t3arm` launcher, and starts the checked-out server with `t3 serve`.

The service binds to the machine's Tailscale address (`100.112.252.123`) on
port `3773`, uses `/home/musse/.t3` for T3 Code state, and waits for Tailscale
to be available before starting. These values describe this machine exactly;
they should be changed when installing the unit elsewhere.

The `t3 serve` command starts the server without trying to open a browser and
prints the headless pairing details needed to connect from another device.

This is useful on a headless development machine because systemd starts T3 Code
at boot, restarts it after an unexpected failure, and makes its output
available through the journal. A remote browser can then be used as the
interface while the coding agents and projects stay on the server.

## Install

This repository includes the live unit at [`deploy/t3code.service`](../../deploy/t3code.service).
Install it on this machine with:

```bash
sudo install -o root -g root -m 644 deploy/t3code.service /etc/systemd/system/t3arm.service
```

Start the service and enable it at boot:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now t3arm.service
```

## Inspect the service

Check its state and follow its logs with:

```bash
systemctl status t3arm.service
journalctl --unit t3arm.service --follow
```

The startup output contains the headless pairing information. Keep the server
behind an authenticated private network, SSH tunnel, or a properly configured
reverse proxy; do not expose an unauthenticated development server directly to
the public internet.

To stop or disable it:

```bash
sudo systemctl disable --now t3arm.service
```
