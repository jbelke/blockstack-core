# Blockstack API

You can read the API documentation and try out API calls at: https://core.blockstack.org

In general, all documentation is in the [docs/](https://github.com/blockstack/blockstack-core/tree/api/docs) directory.

Instructions for deploying your own node are [here](https://github.com/blockstack/blockstack-core/tree/api/docs/install-api.md).

When the user clicks “login” in an application, the app should redirect the user to this endpoint. If the user already has an account, they will be redirected along with requested data. If the user doesn’t have an account, the user will be presented with each of the app’s requested permissions, then will satisfy or deny them. The dashboard will then redirect the user back with a JWT. The response JWT contains a signature and an API token that the app can use for future authorization of endpoints.

Each application specifies in advance which family of API calls it will need to make to function properly.  This list is passed along to the dashboard endpoint when creating an application account.  The account-creation page shows this list of API endpoints and what they do, and allows the user to line-item approve or deny them.  The list is stored by the API server in the local account structure, and the list is given to the application as part of the session JWT.  The API server will NACK requests to endpoints in API families absent from the session JWT. 

## Administrative API

### Node

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Ping the node | GET /v1/node/ping | - | Requires pre-shared secret in the `Authorization:` header |
| - | - | - | - |
| Get the node's config | GET /v1/node/config | - | Requires pre-shared secret in the `Authorization:` header. Returns a dict with the config file |
| Set one or more config fields in a config section | POST /v1/node/config/{section}?{key}={value} | - | Requires pre-shared secret in the `Authorization:` header. |
| Delete a config field | DELETE /v1/node/config/{section}/{key} | - | Requires pre-shared secret in the `Authorization:` header. |
| Delete a config section | DELETE /v1/node/config/{section} | - | Requires pre-shared secret in the `Authorization:` header. |
| - | - | - | - |
| Get registrar state | GET /v1/node/registrar/state | - | Requires pre-shared secret in the `Authorization:` header. |
| - | - | - | - |
| Initialize a driver (DANGEROUS) | POST /v1/node/drivers | - | Takes the following query strings: `driver={name of driver}`, `index={0 or 1}`, and `force={0 or 1}` (DANGEROUS) |
| List driver-specific fields to set | GET /v1/node/drivers/{driver_name}/settings | - | - |
| Set driver-specific fields | POST /v1/node/drivers/{driver_name}/settings | - | Takes a JSON object with `key=value` pairs |

### Wallet

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Get wallet payment address | GET /v1/wallet/payment_address | wallet_read | - |
| Get wallet owner address | GET /v1/wallet/owner_address | wallet_read | - |
| Get wallet data public key | GET /v1/wallet/data_pubkey | wallet_read | - |
| - | - | - | - |
| Set the wallet | PUT /v1/wallet/keys | - | Requires a pre-shared secret in the `Authorization:` header |
| Get the wallet | GET /v1/wallet/keys | - | Requires a pre-shared secret in the `Authorization:` header |
| - | - | - | - |
| Get the wallet balance | GET /v1/wallet/balance | wallet_read | - |
| Withdraw funds from the wallet | POST /v1/wallet/balance | wallet_write | Payload: `{'address': str, 'amount': int, 'min_confs': int, 'tx_only':  bool} |
| - | - | - | - |
| Change wallet password | PUT /v1/wallet/password | wallet_write | Payload: `{'password': ..., 'new_password': ...}`|

### Authorization

| Method | API Call | API family | Notes |
| ------------- | ------------- | ------------- | ------------- |
| Create an authorization token | GET /v1/auth?authRequest={authRequestToken} | - | Requires a pre-shared secret in the `Authorization:` header. |

TODO: authRequestToken format

## Naming API

### Names

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Get all names | GET /v1/names?page=xxx | names | The `page=xxx` is required (xxx is an integer) | 
| Register name | POST /v1/names | register | Payload: {"name": NAME} | 
| Get name info | GET /v1/names/{name} | names | - | 
| Get name history | GET /v1/names/{name}/history | names | - | 
| Get historical zone file | GET /names/{name}/zonefile/{zoneFileHash} | zonefiles | - | 
| Revoke name | DELETE /v1/names/{name} | revoke | - | 
| Transfer name | PUT /v1/names/{name}/owner | transfer | Payload: {"owner": OWNER } | 
| Set zone file | PUT /v1/names/{name}/zonefile | update | Payload: {"zonefile": ZONE_FILE } | 
| Set zone file hash | PUT /v1/names/{name}/zonefile | update | Payload: {"zonefile_hash": ZONE_FILE_HASH } | 

### Addresses

| Method | API Call | API family | Notes |
| ------------- | ------------- | ------------- | ------------- |
| Get names owned by address | GET /v1/addresses/{address} | names | - |

### Namespaces

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Get all namespaces | GET /v1/namespaces | namespaces | - | 
| Get namespace names | GET /v1/namespaces/{tld}/names?page=xxx | namespaces | The `page=xxx` is required (xxx is an integer) |

### Prices

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Get namespace price | GET /v1/prices/namespaces/{tld} | prices | May return a warning if the wallet does not have enough funds | 
| Get name price | GET /v1/prices/names/{name} | prices | May return a warning if the wallet does not have enough funds | 


### Blockchains

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Get block operations | GET /v1/blockchains/{blockchainName}/block/{blockHeight} | blockchains | - | 
| Get raw name history | GET /v1/blockchains/{blockchainName}/names/{nameID}/history | blockchains |  - | 
| Get consensus hash | GET /v1/blockchains/{blockchainName}/consensusHash | blockchains | - | 
| Get pending transactions | GET /v1/blockchains/{blockchainName}/pending | blockchains | - |

## Identity API
NOT IMPLEMENTED YET

### Datastores

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Create store for this session | POST /v1/stores | store_write | Creates a datastore for the application indicated by the session |
| Get store metadata | GET /v1/stores/{storeID} | - | - | 
| Delete store | DELETE /v1/stores/{storeID} | store_write | Deletes all files and directories in the store as well |
| - | - | - | - |
| Get inode info (stat) | GET /v1/stores/{storeID}/inodes?path={path} | - | - | 
| - | - | - | - |
| Get directory files (ls) | GET /v1/stores/{storeID}/directories?path={path} | - | Returns structured inode data | 
| Create directory (mkdir) | POST /v1/stores/{storeID}/directories?path={path} | store_write | Only works on the datastore for the application indicated by the session | 
| Delete directory (rmdir) | DELETE /v1/stores/{storeID}/directories?path={path} | store_write | Only works on the datastore for the application indicated by the session | 
| - | - | - | - |
| Get file data (cat) | GET /v1/stores/{storeID}/files?path={path} | -f | Returns `application/octet-stream` data | 
| Create file | POST /v1/stores/{storeID}/files?path={path} | store_write | Uploads `application/octet-stream` raw file data.  Only works on the datastore for the application indicated by the session. | 
| Update file | PUT /v1/stores/{storeID}/files?path={path} | store_write | Uploads `application/octet-stream` raw file data.  Only works on the datastore for the application indicated by the session. | 
| Delete file (rm) | DELETE /v1/stores/{storeID}/files?path={path} | store_write | Only works on the datastore for the application indicated by the session | 


### Collections
NOT IMPLEMENTED YET; STUBS ONLY

| Method  | API Call | API family | Notes | 
| ------------- | ------------- | ------------- | ------------- |
| Create collection | POST /v1/collections | collection_admin | NOT IMPLEMENTED | 
| Get all collection items | GET /v1/collections/{collectionID} | collection_read | NOT IMPLEMENTED | 
| Create collection item | POST /v1/collections/{collectionID} | collection_write | NOT IMPLEMENTED | 
| Get collection item | GET /v1/{collectionID}/{itemID} | collection_read | NOT IMPLEMENTED | 
