# argocd-test-app

## Create test app & project in ArgoCD

```
kustomize build . | kubectl apply -n argocd -f -
```

## Test vault locally

Install the vault cli - https://www.vaultproject.io/downloads
and argocd-vault-plugin cli - https://argocd-vault-plugin.readthedocs.io/en/stable/installation/#installing-locally

Set env vars:
```
export VAULT_ADDR=https://myvault.example.com
export AVP_TYPE=vault
export AVP_AUTH_TYPE=approle
export AVP_ROLE_ID=my-vault-role-id
export AVP_SECRET_ID=my-vault-secret-id 
```

Get the test secret using vault cli (needs to exist in the configured vault instance):

```
vault write auth/approle/login role_id=$AVP_ROLE_ID secret_id=$AVP_SECRET_ID
vault login <token>
vault kv get hybrid-cloud-gateway/testsecret
```

Replace placeholder in secret using argocd-vault-plugin https://argocd-vault-plugin.readthedocs.io/en/stable/howitworks/
```
argocd-vault-plugin generate  ./argocd-test-app/secret-with-vault-placeholder.yaml
```

Replace placeholder with kustomize first:
```
kustomize build ./argocd-test-app/ | argocd-vault-plugin generate -
```

Alternative with kustomize output to file.
```
kustomize build ./argocd-test-app/ > all.yaml && argocd-vault-plugin generate all.yaml
```
