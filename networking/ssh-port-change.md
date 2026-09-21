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

##Investigation
1. I changed the SSH configuration in:
2. /etc/ssh/sshd_config
3. from #Port 22 to Port 2222

