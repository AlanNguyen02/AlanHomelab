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
```
1. LISTEN 0 4096 0.0.0.0:22 0.0.0.0:* users:(("sshd",...))
2. LISTEN 0 4096 [::]:22 [::]:* users:(("sshd",...))
```

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
5. Port 22 was no longer listening

## Verification
1. From a Windows PC, I established a new SSH connection using "ssh -p 2222 "VM""
2. Connection succeeded
3. Confirms that SSH was moved from TCP port 22 to TCP port 2222

## What I Learned
1. SSH normally uses TCP port 22
2. A service can be running without necessarily listening on the port I expect
3. ss can be used to determine which ports are listening
4. systemctl can be used to inspect services and sockets
5. Modern Ubuntu can use systemd socket activation for SSH
6. sshd_config and the systemd SSH socket can interact when determining the listening port
7. Configuration changes should be verified rather than assumed to taken effect
8. Keeping an existing SSH session open while changing the SSH port prevents accidentally locking myself out before the new connection is tested.

## Troubleshooting Method
The general troubleshooting process was:

1. Check whether the SSH service is running.
2. Check which port is actually listening.
3. Inspect the relevant configuration.
4. Identify which component controls the listening socket.
5. Apply the configuration change.
6. Reload/restart the appropriate component.
7. Verify the listening port.
8. Test the connection from the client.

## Result
SSH was successfully reconfigured from TCP port 22 to TCP port 2222, and remote access was verified from a Windows client.
