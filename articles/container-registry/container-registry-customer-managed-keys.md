---
title: Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta
description: Dowiedz się więcej na temat szyfrowania w usłudze Azure Container Registry oraz sposobu szyfrowania rejestru Premium za pomocą klucza zarządzanego przez klienta, który jest przechowywany w Azure Key Vault
ms.topic: article
ms.date: 09/30/2020
ms.custom: ''
ms.openlocfilehash: 6eaae5266277a6a65c7cecaa761b75e3a41ebe87
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940671"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta

Gdy przechowujesz obrazy i inne artefakty w rejestrze kontenerów platformy Azure, platforma Azure automatycznie szyfruje zawartość rejestru przy użyciu [kluczy zarządzanych przez usługę](../security/fundamentals/encryption-models.md). Można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu klucza tworzonego i zarządzanego w Azure Key Vault (klucz zarządzany przez klienta). Ten artykuł przeprowadzi Cię przez kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta jest obsługiwane przez integrację z [Azure Key Vault](../key-vault/general/overview.md). Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy. Za pomocą Azure Key Vault można również prześledzić użycie klucza.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).


## <a name="things-to-know"></a>Co należy wiedzieć

* Klucz zarządzany przez klienta można obecnie włączyć tylko podczas tworzenia rejestru. Po włączeniu klucza należy skonfigurować tożsamość zarządzaną *przypisaną przez użytkownika* , aby uzyskać dostęp do magazynu kluczy.
* Po włączeniu szyfrowania z kluczem zarządzanym przez klienta w rejestrze nie można wyłączyć szyfrowania.  
* [Zaufanie zawartości](container-registry-content-trust.md) nie jest obecnie obsługiwane w rejestrze zaszyfrowanym przy użyciu klucza zarządzanego przez klienta.
* W rejestrze zaszyfrowanym kluczem zarządzanym przez klienta uruchamianie dzienników dla [zadań ACR](container-registry-tasks-overview.md) jest obecnie zachowywane przez zaledwie 24 godziny. Jeśli zachodzi potrzeba przechowywania dzienników przez dłuższy czas, zobacz Wskazówki dotyczące [eksportowania i przechowywania dzienników uruchamiania zadań](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Jeśli dostęp do magazynu kluczy platformy Azure jest ograniczony przy użyciu sieci wirtualnej z [zaporą Key Vault](../key-vault/general/network-security.md), wymagane są dodatkowe czynności konfiguracyjne. Po utworzeniu rejestru i włączeniu klucza zarządzanego przez klienta należy skonfigurować dostęp do klucza przy użyciu tożsamości zarządzanej *przypisanej przez system* do rejestru i skonfigurować rejestr w celu obejścia zapory Key Vaultej. Wykonaj kroki opisane w tym artykule, aby włączyć szyfrowanie z kluczem zarządzanym przez klienta, a następnie zobacz Wskazówki dotyczące [scenariusza zaawansowanego: Key Vault zaporze](#advanced-scenario-key-vault-firewall) w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Włącz klucz zarządzany przez klienta — interfejs wiersza polecenia

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom polecenie [AZ Group Create][az-group-create] , aby utworzyć grupę zasobów służącą do tworzenia magazynu kluczy, rejestru kontenerów i innych wymaganych zasobów.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) za pomocą polecenia [AZ Identity Create][az-identity-create] . Ta tożsamość będzie używana przez rejestr w celu uzyskania dostępu do usługi Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

W danych wyjściowych polecenia Zwróć uwagę na następujące wartości: `id` i `principalId` . Te wartości są potrzebne w dalszych krokach w celu skonfigurowania dostępu rejestru do magazynu kluczy.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Dla wygody należy przechowywać te wartości w zmiennych środowiskowych:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Utwórz magazyn kluczy za pomocą [AZ Key magazynu Create][az-keyvault-create] , aby przechowywać klucz zarządzany przez klienta na potrzeby szyfrowania rejestru.

Aby zapobiec utracie danych spowodowanym przez przypadkowe Usuwanie klucza lub magazynu kluczy, należy włączyć następujące ustawienia: **nietrwałe usuwanie** i **przeczyszczanie**. Poniższy przykład zawiera parametry dla tych ustawień:

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

> [!NOTE]
> Począwszy od interfejsu wiersza polecenia platformy Azure w wersji 2,2, `az keyvault create` domyślnie włącza opcję usuwania nietrwałego.

Aby skorzystać z dalszych kroków, Pobierz identyfikator zasobu magazynu kluczy:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Włącz dostęp do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp. W poniższym poleceniu AZ webstore [Set-Policy][az-keyvault-set-policy] należy przekazać Identyfikator podmiotu zabezpieczeń, który został utworzony wcześniej w zmiennej środowiskowej. Ustaw uprawnienia do **pobierania**, **unwrapKey**i **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Alternatywnie możesz przypisać uprawnienia do tożsamości w celu uzyskania dostępu do magazynu kluczy za pomocą [usługi Azure RBAC dla Key Vault](../key-vault/general/rbac-guide.md) (wersja zapoznawcza). Na przykład Przypisz rolę szyfrowania Key Vault usług kryptograficznych do tożsamości za pomocą polecenia [AZ role Assign Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Utwórz klucz i Pobierz identyfikator klucza

Uruchom polecenie [AZ Key magazynu klucz Create][az-keyvault-key-create] , aby utworzyć klucz w magazynie kluczy.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

W danych wyjściowych polecenia Zanotuj identyfikator klucza, `kid` . Ten identyfikator jest używany w następnym kroku:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Dla wygody należy zapisać tę wartość w zmiennej środowiskowej:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Tworzenie rejestru z kluczem zarządzanym przez klienta

Uruchom polecenie [AZ ACR Create][az-acr-create] , aby utworzyć rejestr w warstwie usługi Premium i włączyć klucz zarządzany przez klienta. Przekaż Identyfikator podmiotu zabezpieczeń tożsamości zarządzanej i identyfikator klucza przechowywane wcześniej w zmiennych środowiskowych:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Pokaż stan szyfrowania

Aby zobaczyć, czy szyfrowanie rejestru z kluczem zarządzanym przez klienta jest włączone, uruchom polecenie [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

Dane wyjściowe są podobne do następujących:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Włącz klucz zarządzany przez klienta — Portal

### <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w Azure Portal. Aby uzyskać instrukcje, zobacz [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Nazwa tożsamości jest używana w dalszych krokach.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../key-vault/secrets/quick-create-portal.md).

Podczas tworzenia magazynu kluczy dla klucza zarządzanego przez klienta na karcie **podstawowe** Włącz następujące ustawienia ochrony: **usuwanie nietrwałe** i **Ochrona przed czyszczeniem**. Te ustawienia pomagają zapobiec utracie danych spowodowanym przez przypadkowe usunięcie klucza lub magazynu kluczy.

![Utwórz magazyn kluczy w Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="enable-key-vault-access"></a>Włącz dostęp do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp.

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **zasady dostępu > + Dodaj zasady dostępu**.
1. Wybierz pozycję **uprawnienia klucza**i wybierz pozycję **Pobierz**, **Odpakuj klucz**i **Zawijaj klucz**.
1. Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika.  
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz**.

![Utwórz zasady dostępu do magazynu kluczy](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

 Alternatywnie możesz przypisać uprawnienia do tożsamości w celu uzyskania dostępu do magazynu kluczy za pomocą [usługi Azure RBAC dla Key Vault](../key-vault/general/rbac-guide.md) (wersja zapoznawcza). Na przykład Przypisz rolę szyfrowania Key Vault usług kryptograficznych do tożsamości.

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Kontrola dostępu (IAM)**  >  **i Dodaj**  >  **Dodawanie przypisania roli**.
1. W oknie **Dodawanie przypisania roli** :
    1. Wybierz rolę **Key Vault szyfrowania usług kryptograficznych (wersja zapoznawcza)** . 
    1. Przypisz dostęp do **tożsamości zarządzanej przypisanej przez użytkownika**.
    1. Wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika, a następnie wybierz pozycję **Zapisz**.

### <a name="create-key"></a>Utwórz klucz

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **klucze**.
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź unikatową nazwę klucza.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Utwórz**.
1. Po utworzeniu wybierz klucz i zanotuj bieżącą wersję klucza.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Wybierz pozycję **Utwórz**  >  **kontenery**zasobów  >  **Container Registry**.
1. Na karcie **podstawy** wybierz lub Utwórz grupę zasobów, a następnie wprowadź nazwę rejestru. W obszarze **jednostka SKU**wybierz pozycję **Premium**.
1. Na karcie **szyfrowanie** w **kluczu zarządzanym przez klienta**wybierz pozycję **włączone**.
1. W obszarze **tożsamość**Wybierz utworzoną tożsamość zarządzaną.
1. W obszarze **szyfrowanie**wybierz pozycję **Wybierz z Key Vault**.
1. W oknie **Wybieranie klucza z Azure Key Vault** wybierz magazyn kluczy, klucz i wersję utworzoną w poprzedniej sekcji.
1. Na karcie **szyfrowanie** wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz pozycję **Utwórz** , aby wdrożyć wystąpienie rejestru.

![Tworzenie rejestru kontenerów w witrynie Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Aby wyświetlić stan szyfrowania rejestru w portalu, przejdź do rejestru. W obszarze **Ustawienia**wybierz pozycję  **szyfrowanie**.

## <a name="enable-customer-managed-key---template"></a>Włączanie szablonu klucza zarządzanego przez klienta

Można również użyć szablonu Menedżer zasobów, aby utworzyć rejestr i włączyć szyfrowanie z kluczem zarządzanym przez klienta.

Poniższy szablon umożliwia utworzenie nowego rejestru kontenerów i zarządzanej tożsamości przypisanej przez użytkownika. Skopiuj poniższą zawartość do nowego pliku i Zapisz go przy użyciu nazwy pliku, takiej jak `CMKtemplate.json` .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Wykonaj kroki opisane w poprzednich sekcjach, aby utworzyć następujące zasoby:

* Magazyn kluczy identyfikowany według nazwy
* Klucz magazynu kluczy identyfikowany przez identyfikator klucza

Uruchom następujące polecenie [AZ Group Deployment Create][az-group-deployment-create] , aby utworzyć rejestr przy użyciu poprzedniego pliku szablonu. Gdzie to wskazane, podaj nową nazwę rejestru i nazwę tożsamości zarządzanej, a także nazwę i identyfikator klucza magazynu kluczy.

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Pokaż stan szyfrowania

Aby wyświetlić stan szyfrowania rejestru, uruchom polecenie [AZ ACR Encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Korzystanie z rejestru

Po włączeniu klucza zarządzanego przez klienta w rejestrze można wykonać te same operacje rejestru, które są wykonywane w rejestrze, który nie jest szyfrowany przy użyciu klucza zarządzanego przez klienta. Można na przykład przeprowadzić uwierzytelnianie przy użyciu rejestru i wypychania obrazów platformy Docker. Zobacz przykładowe polecenia w [wypychaniu i ściąganiu obrazu](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Obróć klucz

Obróć klucz zarządzany przez klienta używany do szyfrowania rejestru zgodnie z zasadami zgodności. Utwórz nowy klucz lub zaktualizuj wersję klucza, a następnie zaktualizuj rejestr, aby szyfrować dane przy użyciu klucza. Te kroki można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure lub w portalu.

Podczas obracania klucza zwykle określana jest ta sama tożsamość użyta podczas tworzenia rejestru. Opcjonalnie można skonfigurować nową tożsamość przypisaną przez użytkownika na potrzeby dostępu do klucza lub włączyć i określić tożsamość przypisaną przez system do rejestru.

> [!NOTE]
> Upewnij się, że wymagany jest [dostęp do magazynu kluczy](#enable-key-vault-access) dla tożsamości skonfigurowanej na potrzeby dostępu do klucza.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą polecenia [AZ Key magazynu][az-keyvault-key] można utworzyć klucze magazynu kluczy lub zarządzać nimi. Na przykład, aby utworzyć nową wersję lub klucz klucza, uruchom polecenie [AZ Key magazynu klucz Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

Następnie uruchom polecenie [AZ ACR Encryption Obróć-Key][az-acr-encryption-rotate-key] , przekazując nowy identyfikator klucza i tożsamość, którą chcesz skonfigurować:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Portal

Użyj ustawień **szyfrowania** rejestru, aby zaktualizować wersję klucza, klucz, Magazyn kluczy lub ustawienia tożsamości używane dla klucza zarządzanego przez klienta.

Na przykład w celu wygenerowania i skonfigurowania nowej wersji klucza:

1. W portalu przejdź do rejestru.
1. W obszarze **Ustawienia**wybierz pozycję **szyfrowanie**  >  **Zmień klucz**.
1. Wybierz **klucz SELECT**

    ![Obróć klucz w Azure Portal](./media/container-registry-customer-managed-keys/rotate-key.png)
1. W oknie **Wybieranie klucza z Azure Key Vault** wybierz wcześniej skonfigurowany Magazyn kluczy i klucz, a następnie w polu **wersja**wybierz pozycję **Utwórz nowy**.
1. W oknie **Tworzenie klucza** wybierz pozycję **Generuj**, a następnie **Utwórz**.
1. Ukończ wybór klucza i wybierz pozycję **Zapisz**.

## <a name="revoke-key"></a>Odwołaj klucz

Odwołaj zarządzany przez klienta klucz szyfrowania, zmieniając zasady dostępu lub uprawnienia w magazynie kluczy lub usuwając klucz. Na przykład za pomocą polecenia [AZ Registry Delete-Policy][az-keyvault-delete-policy] Zmień zasady dostępu zarządzanej tożsamości używanej przez rejestr:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odwołanie klucza skutecznie blokuje dostęp do wszystkich danych rejestru, ponieważ rejestr nie może uzyskać dostępu do klucza szyfrowania. Jeśli dostęp do klucza jest włączony lub usunięty klucz zostanie przywrócony, rejestr wybierze klucz, aby można było ponownie uzyskać dostęp do zaszyfrowanych danych rejestru.

## <a name="advanced-scenario-key-vault-firewall"></a>Scenariusz zaawansowany: Key Vault zapory

Jeśli magazyn kluczy platformy Azure został wdrożony w sieci wirtualnej za pomocą zapory Key Vault, wykonaj następujące dodatkowe kroki po włączeniu szyfrowania klucza zarządzanego przez klienta w rejestrze.

1. Konfigurowanie szyfrowania rejestru do korzystania z tożsamości przypisanej do systemu przez rejestr
1. Aby obejść Zaporę Key Vault, Włącz rejestr
1. Obróć klucz zarządzany przez klienta

### <a name="configure-system-assigned-identity"></a>Konfigurowanie tożsamości przypisanej do systemu

Można skonfigurować tożsamość zarządzaną przez system do rejestru, aby uzyskać dostęp do magazynu kluczy dla kluczy szyfrowania. Jeśli nie znasz różnych zarządzanych tożsamości dla zasobów platformy Azure, zapoznaj się z [omówieniem](../active-directory/managed-identities-azure-resources/overview.md).

Aby włączyć tożsamość przypisaną przez system do rejestru w portalu:

1. W portalu przejdź do rejestru.
1. Wybierz pozycję **Ustawienia**  >   **tożsamość**.
1. W obszarze **przypisano system** **Ustaw wartość** **na włączone**. Wybierz pozycję **Zapisz**.
1. Skopiuj **Identyfikator obiektu** tożsamości.

Aby udzielić tożsamości dostępu do magazynu kluczy:

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **zasady dostępu > + Dodaj zasady dostępu**.
1. Wybierz pozycję **uprawnienia klucza**i wybierz pozycję **Pobierz**, **Odpakuj klucz**i **Zawijaj klucz**.
1. Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wyszukaj identyfikator obiektu zarządzanej tożsamości przypisanej do systemu lub nazwę rejestru.  
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz**.

Aby zaktualizować ustawienia szyfrowania rejestru w celu użycia tożsamości:

1. W portalu przejdź do rejestru.
1. W obszarze **Ustawienia**wybierz pozycję **szyfrowanie**  >  **Zmień klucz**.
1. W obszarze **tożsamość**wybierz pozycję **przypisana przez system**i wybierz pozycję **Zapisz**.

### <a name="enable-key-vault-bypass"></a>Włącz obejście magazynu kluczy

Aby uzyskać dostęp do magazynu kluczy skonfigurowanego za pomocą zapory Key Vault, rejestr musi ominąć zaporę. Upewnij się, że magazyn kluczy jest skonfigurowany tak, aby zezwalać na dostęp przez dowolną [zaufaną usługę](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry jest jedną z zaufanych usług.

1. W portalu przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **Sieć**.
1. Potwierdź, zaktualizuj lub Dodaj ustawienia sieci wirtualnej. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](../key-vault/general/network-security.md).
1. W obszarze **Zezwól usługom zaufanym firmy Microsoft na ominięcie tej zapory**wybierz pozycję **tak**. 

### <a name="rotate-the-customer-managed-key"></a>Obróć klucz zarządzany przez klienta

Po wykonaniu powyższych kroków Obróć klucz na nowy klucz w magazynie kluczy za zaporą. Aby uzyskać instrukcje, zobacz [Obróć klucz](#rotate-key) w tym artykule.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="removing-user-assigned-identity"></a>Usuwanie tożsamości przypisanej przez użytkownika

W przypadku próby usunięcia tożsamości przypisanej przez użytkownika z rejestru, który jest używany do szyfrowania, może zostać wyświetlony komunikat o błędzie podobny do:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Nie będzie też można zmienić (obrócić) klucza szyfrowania. W przypadku wystąpienia tego problemu należy najpierw ponownie przypisać tożsamość przy użyciu identyfikatora GUID wyświetlanego w komunikacie o błędzie. Na przykład:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Następnie po zmianie klucza i przypisaniu innej tożsamości można usunąć oryginalną tożsamość przypisaną przez użytkownika.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [szyfrowaniu na platformie Azure](../security/fundamentals/encryption-atrest.md).
* Dowiedz się więcej o zasadach dostępu i sposobach [zabezpieczania dostępu do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
