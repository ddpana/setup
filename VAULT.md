### Install VAULT
```bash
cd /tmp
wget https://releases.hashicorp.com/vault/0.11.1/vault_0.11.1_linux_amd64.zip
unzip vault_0.11.1_linux_amd64.zip
rm -rf vault_0.11.1_linux_amd64.zip
sudo mv vault /bin/vault
```

### Setup vault

Create configuration file /etc/vault.config
```bash
ui = true

listener "tcp" {
  address     = "127.0.0.1:8200"
  tls_disable = 1
}

storage "file" {
  path = "/opt/vault"
}

telemetry {
  statsd_address = "127.0.0.1:8125"
}
```

Create the data folder
```bash
sudo mkdir /opt/vault/
```

Create the systemd service /etc/systemd/system/vault.service
```bash
[Unit]
Description=Vault Service

[Service]
User=root
ExecStart=/bin/vault server -config /etc/vault.config
ExecStop=/bin/kill -9 $MAINPID
Restart=always
RestartSec=1
StartLimitBurst=0

[Install]
WantedBy=multi-user.target
```

Start the vault server
```bash
sudo systemctl daemon-reload
sudo systemctl enable vault
sudo systemctl start vault
```

Set the vault address in ~/.bashrc , ~/.zshrc
```bash
echo "export VAULT_ADDR='http://127.0.0.1:8200'" >> ~/.zshrc
```

Initialize and configure the vault
```bash
vault init -key-shares=1 -key-threshold=1
```

NOTE the Unseal Keys and Initial root key
```bash
Unseal Key 1: qNsaASDUJkMv2HuZauZSlX5F9hwqNzAVcVXU/mQT2Qs=

Initial Root Token: 2b7afab3-251a-a622-8d16-4449640b8139
```

Unseal the vault
```bash
vault operator unseal qNsaASDUJkMv2HuZauZSlX5F9hwqNzAVcVXU/mQT2Qs=
```

Login with the root Token
```bash
vault login vault operator unseal qNsaASDUJkMv2HuZauZSlX5F9hwqNzAVcVXU/mQT2Qs=
```

Enable LDAP Auth
```bash
vault auth enable ldap
```

Setup LDAP
```bash
vault write auth/ldap/config \
    url="ldap://192.168.5.1" \
    userdn="ou=Users,dc=domain,dc=com" \
    groupdn="ou=Security Groups,dc=domain,dc=com" \
    groupfilter="(&(objectClass=group)(member:1.2.840.113556.1.4.1941:={{.UserDN}}))" \
    groupattr="cn" \
    upndomain="domain.com" \
    insecure_tls=true \
    starttls=false
```

Setup your Policies
```bash
# Need to setup policies according to access
```

Setup Your users and groups
```bash
vault write auth/ldap/groups/devops policies=default
vault write auth/ldap/users/user groups=devops policies=default
vault login -method=ldap username=user
```