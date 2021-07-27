# Setup Hashicorp Vault Server on Docker and a Getting Started CLI Guide

The following repo just outlines the steps required to deploy vault on your local machine for testing in vault.

Setting up the Vault Server
Create the directory structure:

```
touch docker-compose.yml
mkdir -p volumes/{config,file,logs}
```

Populate the vault config vault.json. (As you can see the config is local, in the next couple of posts, I will show how to persist this config to Amazon S3)

```
cat > volumes/config/vault.json << EOF
{
  "backend": {
    "file": {
      "path": "/vault/file"
    }
  },
  "listener": {
    "tcp":{
      "address": "0.0.0.0:8200",
      "tls_disable": 1
    }
  },
  "ui": true
}
EOF
```

Populate the docker-compose.yml:

```
cat > docker-compose.yml << EOF
version: '2'
services:
  vault:
    image: vault
    container_name: vault
    ports:
      - "8200:8200"
    restart: always
    volumes:
      - ./volumes/logs:/vault/logs
      - ./volumes/file:/vault/file
      - ./volumes/config:/vault/config
    cap_add:
      - IPC_LOCK
    entrypoint: vault server -config=/vault/config/vault.json
EOF
```

Start the Vault Server:

```
docker-compose up
```

Install Vault on your local machine so you can interact with it.

I am using mac, so I will be using brew:

```
brew install vault
```

Set environment variables:

```
export VAULT_ADDR='http://127.0.0.1:8200'
```

Initialize the Vault Cluster:
Initialize new vault cluster with 6 key shares:

```
vault operator init -key-shares=1 -key-threshold=1
```
