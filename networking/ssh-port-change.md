# SSH Port Configuration and Troubleshooting

## Problem

SSH was initially configured to listen on the default TCP port 22

As a troubleshooting exercise, I changed the SSH server configuration to use TCP port 2222 and verified that a client could connect using the new port.

## Initial Configuration

The Ubuntu Server VM was running SSH on TCP port 22.

I verified the listening socket with:

```bash
sudo ss -tlnp | grep -E ':22|:2222'
```

Initial Result:
1. LISTEN 0 4096 0.0.0.0:22 0.0.0.0:* users:(("sshd",...))
2. LISTEN 0 4096 [::]:22 [::]:* users:(("sshd",...))

## Investigation
1. I changed the SSH configuration in: /etc/ssh/sshd_config from #Port 22 to Port 2222
2. I validated the SSH configuration with "sudo sshd -t"
3. No output was returned indicating that the configuration syntax was valid
4. After restarting SSH, port 22 was still listening and port 2222 was not.
5. I checked the SSH socket, and output showed both IPv4 and IPv6 are listening on port 22
6. I inspected socket configuration with "systemctl cat ssh.socket". This showed that systemd was managing the listening sockets.
7. The generated configuration also showed that Ubuntu's sshd-socket-generator had detected the new port configuration in /run/systemd/generator/ssh.socket.d/addresses.conf
```
[Socket]
ListenStream=
ListenStream=0.0.0.0:2222
ListenStream=[::]:2222
```

## Solution
1. Reload Systemd with "sudo systemctl daemon-reload"
2. Restart ssh.socket with "sudo systemctl restart ssh.socket"
3. Verify the listening ports with "sudo ss -tlnp | grep -E ':22|:2222'
4. The final result shows
```
LISTEN 0 4096 0.0.0.0:2222 0.0.0.0:* users:(("sshd",...))
LISTEN 0 4096 [::]:2222 [::]:* users:(("sshd",...))
```
