# Download and Set Up the POP Binary
```
curl -L -o pop "https://dl.pipecdn.app/v0.2.4/pop"
chmod +x pop
mkdir download_cache
```
# Running POP Node as a Systemd Service

```
sudo useradd -r -m -s /sbin/nologin pop-svc-user -d /home/pop-svc-user 2>/dev/null || true
sudo mkdir -p /opt/pop /var/lib/pop /var/cache/pop/download_cache
```

```
sudo mv -f ~/pop /opt/pop/
sudo chmod +x /opt/pop/pop 2>/dev/null || true
```
If you get an error, ignore it.

```
sudo mv -f ~/node_info.json /var/lib/pop/ 2>/dev/null || true
```

```
sudo chown -R pop-svc-user:pop-svc-user /var/lib/pop
sudo chown -R pop-svc-user:pop-svc-user /var/cache/pop
sudo chown -R pop-svc-user:pop-svc-user /opt/pop
```

# Create a Systemd Service File

important Modify pop.service: Customize the parameters ```such as --ram, --max-disk, --SOL wallet address``` based on your preferences

``` sudo tee /etc/systemd/system/pop.service << 'EOF'
[Unit]
Description=Pipe POP Node Service
After=network.target
Wants=network-online.target

[Service]
User=pop-svc-user
Group=pop-svc-user
ExecStart=/opt/pop/pop \
    --ram=how many use ram  \
    --pubKey SOL wallet address \
    --max-disk how many use disk \
    --cache-dir /var/cache/pop/download_cache \
    --no-prompt
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=pop-node
WorkingDirectory=/var/lib/pop

[Install]
WantedBy=multi-user.target
EOF
```



```
ln -sf /var/lib/pop/node_info.json ~/node_info.json
grep -q "alias pop='cd /var/lib/pop && /opt/pop/pop'" ~/.bashrc || echo "alias pop='cd /var/lib/pop && /opt/pop/pop'" >> ~/.bashrc && source ~/.bashrc
```

```
echo "alias pop='/opt/pop/pop'" >> ~/.bashrc
source ~/.bashrc
```
```
sudo systemctl daemon-reload
sudo systemd-analyze verify pop.service && sudo systemctl enable pop.service && sudo systemctl start pop.service
```

# Verify the Service Status

```
sudo systemctl status pop
```

# View Metrics

```
./pop --status #or /opt/pop/pop --status
```
# Verifying if POP is listening on the correct port

```
ss -tulnp | grep 8003
```

#  Checking Logs for Errors

```
journalctl -u pop.service -f
```
# Generate a Referral Code

```
/opt/pop/pop --gen-referral-route
```









