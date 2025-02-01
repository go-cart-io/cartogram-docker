# Tablet testing via local network

This document provides step-by-step instructions for setting up tablets for local testing.

## Windows

First, you must allow other devices to access docker. If you use wsl on Windows, you can run the following command in command prompt (may need to run as administrator):

```shell script
netsh advfirewall firewall add rule name="Allowing LAN connections" dir=in action=allow protocol=TCP localport=5001

bash.exe -c "ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'"

netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=5001 connectaddress=<your-docker-ip> connectport=5001
```

Reference: https://stackoverflow.com/questions/61002681/connecting-to-wsl2-server-via-local-network

If you are running the application in development mode, you should allow other computher to access port 5173 (for Windows, you can see https://www.devopinion.com/access-localhost-from-another-computer-on-the-same-network/). If you cannot access 5173, check firewall, e.g., delete rules that block node.js.

You should be able to access the application using `http:\\your-local-ip:5001`

## macOS (and iPad)

Work in progress. Contact @adisidev for help getting set up.

# Tablet testing via USB (Andriod)

Please follow this tutorial https://developer.chrome.com/docs/devtools/remote-debugging/. Then, setup port forwarding for port 5001 and 5173, see https://developer.chrome.com/docs/devtools/remote-debugging/local-server/.
