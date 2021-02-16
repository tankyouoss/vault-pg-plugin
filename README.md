# Custom vault postgres plugin

This is the exact same code as current vault postgres database plugin but only with an updated version of lib pq.
This allow to support inline ssl certificate for postgres.
This will become deprecated as soon as vault upgrades to the latest version of lib pq (> 1.9).

Versioning policy : plugin_version = vault_version

## Usage
```
# Load the custom plugin
vault write sys/plugins/catalog/database/pg-database-plugin \
    sha256="<SHA_256_OF_PLUGIN>" \
    command="pg-database-plugin"

# Enable database secret
vault secrets enable database

# Export certificates and key
export CLIENT_CERT=$(cat client-cert.pem)
export CLIENT_KEY=$(cat  client-key.pem)
export SERVER_CA=$(cat server-ca.pem)

# Write database config
vault write database/config/my-db \
    plugin_name=pg-database-plugin \
    allowed_roles="test" \
    connection_url="user='{{username}}' password='{{password}}' database='postgres' host='<DB_HOST>' port='<DB_PORT>' sslmode='verify-ca' sslinline='true' sslcert='${CLIENT_CERT}' sslkey='${CLIENT_KEY}' sslrootcert='${SERVER_CA}'" \
    username="<USERNAME>" \
    password="<PASSWORD>"
    
# Rotate current user password
vault write -force database/rotate-root/my-db
```