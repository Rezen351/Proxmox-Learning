# Serveo SSH Tunneling

## Simple Configuration
To expose a local server to the internet using Serveo:
```bash
ssh -R 80:localhost:80 serveo.net
```

## Add Custom Domain
To expose your local server with a custom domain:
```bash
ssh -R your-domain.com:80:localhost:80 serveo.net
```
Replace `your-domain.com` with your desired domain.

## Run in Background with Custom Domain or Port
To run Serveo SSH in the background and expose a different port (e.g., 8080):
```bash
autossh -M 0 -R 80:localhost:8080 serveo.net
```

## Auto-Run Serveo SSH at Boot
To automatically start Serveo SSH at system boot and run it in the background:

1. **Create a Script**
   Create a shell script to configure the SSH tunnel.
   ```bash
   sudo nano /usr/local/bin/serveo_tunnel.sh
   ```
   Add the following lines:
   ```bash
   #!/bin/bash
   autossh -M 0 -R 80:localhost:8080 serveo.net
   ```
   Save and close the file (Ctrl+O, Enter, Ctrl+X).

2. **Make the Script Executable**
   ```bash
   sudo chmod +x /usr/local/bin/serveo_tunnel.sh
   ```

3. **Create a Systemd Service**
   Create a new service file for systemd.
   ```bash
   sudo nano /etc/systemd/system/serveo_tunnel.service
   ```
   Add the following lines:
   ```ini
   [Unit]
   Description=Serveo SSH Tunnel
   After=network.target

   [Service]
   ExecStart=/usr/local/bin/serveo_tunnel.sh
   Restart=always
   User=$(whoami)

   [Install]
   WantedBy=multi-user.target
   ```
   Save and close the file.

4. **Enable and Start the Service**
   Enable the service to start at boot:
   ```bash
   sudo systemctl enable serveo_tunnel.service
   ```
   Start the service immediately:
   ```bash
   sudo systemctl start serveo_tunnel.service
   ```

5. **Verify the Service**
   Check the status of the service to ensure it is running:
   ```bash
   sudo systemctl status serveo_tunnel.service
   ```

Your Serveo SSH tunnel is now set up to run in the background and will automatically start at system boot.
