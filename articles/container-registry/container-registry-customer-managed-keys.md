---
title: Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta
description: Dowiedz się więcej na temat szyfrowania w usłudze Azure Container Registry oraz sposobu szyfrowania rejestru Premium za pomocą klucza zarządzanego przez klienta, który jest przechowywany w Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: aad9419fdb139ff615bfe07075be78a2ca4ee4ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489076"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta

Gdy przechowujesz obrazy i inne artefakty w rejestrze kontenerów platformy Azure, platforma Azure automatycznie szyfruje zawartość rejestru przy użyciu [kluczy zarządzanych przez usługę](../security/fundamentals/encryption-models.md). Można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu klucza tworzonego i zarządzanego w Azure Key Vault (klucz zarządzany przez klienta). Ten artykuł przeprowadzi Cię przez kroki przy użyciu interfejsu wiersza polecenia platformy Azure, Azure Portal lub szablonu Menedżer zasobów.

Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta jest obsługiwane za pomocą integracji z [Azure Key Vault](../key-vault/general/overview.md): 

* Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy. 
* Za pomocą Azure Key Vault można również prześledzić użycie klucza.
* Azure Container Registry obsługuje automatyczne obracanie kluczy szyfrowania rejestru, gdy w programie Azure Key Vault dostępna jest nowa wersja klucza. Możesz również ręcznie obrócić klucze szyfrowania rejestru.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).


## <a name="things-to-know"></a>Co należy wiedzieć

* Klucz zarządzany przez klienta można obecnie włączyć tylko podczas tworzenia rejestru. Po włączeniu klucza należy skonfigurować tożsamość zarządzaną *przypisaną przez użytkownika* , aby uzyskać dostęp do magazynu kluczy.
* Po włączeniu szyfrowania z kluczem zarządzanym przez klienta w rejestrze nie można wyłączyć szyfrowania.  
* Azure Container Registry obsługuje tylko klucze modułu HSM RSA lub RSA. Klucze krzywej eliptyczna nie są obecnie obsługiwane.
* [Zaufanie zawartości](container-registry-content-trust.md) nie jest obecnie obsługiwane w rejestrze zaszyfrowanym przy użyciu klucza zarządzanego przez klienta.
* W rejestrze zaszyfrowanym kluczem zarządzanym przez klienta uruchamianie dzienników dla [zadań ACR](container-registry-tasks-overview.md) jest obecnie zachowywane przez zaledwie 24 godziny. Jeśli zachodzi potrzeba przechowywania dzienników przez dłuższy czas, zobacz Wskazówki dotyczące [eksportowania i przechowywania dzienników uruchamiania zadań](container-registry-tasks-logs.md#alternative-log-storage).


> [!IMPORTANT]
> Jeśli planujesz przechowywanie klucza szyfrowania rejestru w istniejącym magazynie kluczy platformy Azure, który odmawia dostępu publicznego i zezwala tylko na prywatny punkt końcowy lub wybrane sieci wirtualne, wymagane są dodatkowe czynności konfiguracyjne. Zapoznaj się z [scenariuszem zaawansowanym: Key Vault zapory](#advanced-scenario-key-vault-firewall) w tym artykule.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatyczna lub ręczna aktualizacja wersji kluczowych

Ważnym zagadnieniem dotyczącym zabezpieczeń rejestru szyfrowanych za pomocą klucza zarządzanego przez klienta jest częstotliwość aktualizowania (obracania) klucza szyfrowania. Organizacja może mieć zasady zgodności, które wymagają regularnego aktualizowania [wersji](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) kluczy przechowywanych w Azure Key Vault, gdy są używane jako klucze zarządzane przez klienta. 

Podczas konfigurowania szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta dostępne są dwie opcje aktualizacji wersji klucza używanej do szyfrowania:

* **Automatycznie Aktualizuj wersję klucza** — aby automatycznie zaktualizować klucz zarządzany przez klienta, gdy nowa wersja jest dostępna w Azure Key Vault, Pomiń wersję klucza po włączeniu szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Gdy rejestr jest szyfrowany przy użyciu klucza bez wersji, program Azure Container Registry regularnie sprawdza Magazyn kluczy pod kątem nowej wersji klucza i aktualizuje klucz zarządzany przez klienta w ciągu 1 godziny. Azure Container Registry automatycznie używa najnowszej wersji klucza.

* **Ręcznie zaktualizuj wersję klucza** — aby użyć określonej wersji klucza do szyfrowania rejestru, określ tę wersję klucza po włączeniu szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Gdy rejestr jest szyfrowany z określoną wersją klucza, Azure Container Registry używa tej wersji do szyfrowania do momentu ręcznego obrócenia klucza zarządzanego przez klienta.

Aby uzyskać szczegółowe informacje, zobacz [Wybieranie identyfikatora klucza z lub bez wersji klucza](#choose-key-id-with-or-without-key-version) i [zaktualizuj wersję klucza](#update-key-version)w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej lub Azure Cloud Shell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

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

Domyślnie ustawienie **usuwanie nietrwałe** jest automatycznie włączane w nowym magazynie kluczy. Aby zapobiec utracie danych spowodowanej przypadkowym usunięciem klucza lub magazynem kluczy, należy również włączyć ustawienie **Ochrona przed czyszczeniem** .

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Aby skorzystać z dalszych kroków, Pobierz identyfikator zasobu magazynu kluczy:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Włącz dostęp do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp. W poniższym poleceniu AZ webstore [Set-Policy][az-keyvault-set-policy] należy przekazać Identyfikator podmiotu zabezpieczeń, który został utworzony wcześniej w zmiennej środowiskowej. Ustaw uprawnienia do **pobierania**, **unwrapKey** i **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Alternatywnie można przypisywać uprawnienia do tożsamości w celu uzyskania dostępu do magazynu kluczy za pomocą [usługi Azure RBAC dla Key Vault](../key-vault/general/rbac-guide.md) . Na przykład Przypisz rolę szyfrowania Key Vault usług kryptograficznych do tożsamości za pomocą polecenia [AZ role Assign Create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
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
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Wybierz identyfikator klucza z lub bez wersji klucza

Dla wygody należy przechowywać wybrany przez Ciebie format identyfikatora klucza w zmiennej środowiskowej $keyID. Identyfikator klucza można użyć z wersją lub kluczem bez wersji.

#### <a name="manual-key-rotation---key-id-with-version"></a>Ręczny obrót klucza — identyfikator klucza z wersją

W przypadku korzystania z programu do szyfrowania rejestru z kluczem zarządzanym przez klienta ten klucz umożliwia ręczne obracanie kluczy w Azure Container Registry.

W tym przykładzie jest przechowywana `kid` Właściwość klucza:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatyczne rotacja kluczy — identyfikator klucza pomijający wersję 

W przypadku użycia do szyfrowania rejestru z kluczem zarządzanym przez klienta ten klucz włącza automatyczne rotację kluczy w przypadku wykrycia nowej wersji klucza w Azure Key Vault.

Ten przykład usuwa wersję z `kid` właściwości klucza:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Tworzenie rejestru z kluczem zarządzanym przez klienta

Uruchom polecenie [AZ ACR Create][az-acr-create] , aby utworzyć rejestr w warstwie usługi Premium i włączyć klucz zarządzany przez klienta. Przekaż identyfikator tożsamości zarządzanej i identyfikator klucza przechowywane wcześniej w zmiennych środowiskowych:

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

W zależności od klucza używanego do szyfrowania rejestru dane wyjściowe są podobne do:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Włącz klucz zarządzany przez klienta — Portal

### <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w Azure Portal. Aby uzyskać instrukcje, zobacz [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Nazwa tożsamości jest używana w dalszych krokach.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Utwórz tożsamość przypisaną przez użytkownika w Azure Portal":::

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki Start: Tworzenie magazynu kluczy przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).

Podczas tworzenia magazynu kluczy dla klucza zarządzanego przez klienta na karcie **podstawy** Włącz ustawienie **Ochrona przed czyszczeniem** . To ustawienie pozwala zapobiec utracie danych spowodowanym przez przypadkowe usunięcie klucza lub magazynu kluczy.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Utwórz magazyn kluczy w Azure Portal":::

### <a name="enable-key-vault-access"></a>Włącz dostęp do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp.

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **zasady dostępu > + Dodaj zasady dostępu**.
1. Wybierz pozycję **uprawnienia klucza** i wybierz pozycję **Pobierz**, **Odpakuj klucz** i **Zawijaj klucz**.
1. W obszarze **Wybierz podmiot zabezpieczeń** wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika.  
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Utwórz zasady dostępu do magazynu kluczy":::

Alternatywnie można przypisywać uprawnienia do tożsamości w celu uzyskania dostępu do magazynu kluczy za pomocą [usługi Azure RBAC dla Key Vault](../key-vault/general/rbac-guide.md) . Na przykład Przypisz rolę szyfrowania Key Vault usług kryptograficznych do tożsamości.

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Kontrola dostępu (IAM)**  >  **i Dodaj**  >  **Dodawanie przypisania roli**.
1. W oknie **Dodawanie przypisania roli** :
    1. Wybierz **Key Vault rolę użytkownika szyfrowania usługi kryptograficznej** . 
    1. Przypisz dostęp do **tożsamości zarządzanej przypisanej przez użytkownika**.
    1. Wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika, a następnie wybierz pozycję **Zapisz**.

### <a name="create-key-optional"></a>Utwórz klucz (opcjonalnie)

Opcjonalnie można utworzyć klucz w magazynie kluczy, który będzie używany do szyfrowania rejestru. Wykonaj te kroki, jeśli chcesz wybrać określoną wersję klucza jako klucz zarządzany przez klienta. 

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **klucze**.
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź unikatową nazwę klucza.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Utwórz**.
1. Po utworzeniu wybierz klucz, a następnie wybierz bieżącą wersję. Skopiuj **Identyfikator klucza** dla wersji klucza.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Wybierz pozycję **Utwórz**  >  **kontenery** zasobów  >  **Container Registry**.
1. Na karcie **podstawy** wybierz lub Utwórz grupę zasobów, a następnie wprowadź nazwę rejestru. W obszarze **jednostka SKU** wybierz pozycję **Premium**.
1. Na karcie **szyfrowanie** w **kluczu zarządzanym przez klienta** wybierz pozycję **włączone**.
1. W obszarze **tożsamość** Wybierz utworzoną tożsamość zarządzaną.
1. W obszarze **szyfrowanie** wybierz jedną z następujących opcji:
    * Wybierz pozycję **Wybierz z Key Vault** i wybierz istniejący magazyn kluczy i klucz lub **Utwórz nowy**. Wybrany klucz nie jest w wersji i włącza automatyczne obracanie kluczy.
    * Wybierz pozycję **Wprowadź identyfikator URI klucza** i podaj bezpośrednio identyfikator klucza. Możesz podać identyfikator URI klucza z prawidłową wersją (dla klucza, który musi być obrócony ręcznie) lub identyfikator URI klucza bez wersji (co umożliwia automatyczne obracanie kluczy). 
1. Na karcie **szyfrowanie** wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz pozycję **Utwórz** , aby wdrożyć wystąpienie rejestru.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Utwórz zaszyfrowany rejestr w Azure Portal":::

Aby wyświetlić stan szyfrowania rejestru w portalu, przejdź do rejestru. W obszarze **Ustawienia** wybierz pozycję  **szyfrowanie**.

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

Uruchom następujące polecenie [AZ Deployment Group Create][az-deployment-group-create] , aby utworzyć rejestr przy użyciu poprzedniego pliku szablonu. Gdzie to wskazane, podaj nową nazwę rejestru i nazwę tożsamości zarządzanej, a także nazwę i identyfikator klucza magazynu kluczy.

```bash
az deployment group create \
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

Zaktualizuj wersję klucza w Azure Key Vault lub Utwórz nowy klucz, a następnie zaktualizuj rejestr, aby szyfrować dane przy użyciu klucza. Te kroki można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure lub w portalu.

Podczas obracania klucza zwykle określana jest ta sama tożsamość użyta podczas tworzenia rejestru. Opcjonalnie można skonfigurować nową tożsamość przypisaną przez użytkownika na potrzeby dostępu do klucza lub włączyć i określić tożsamość przypisaną przez system do rejestru.

> [!NOTE]
> Upewnij się, że wymagany jest [dostęp do magazynu kluczy](#enable-key-vault-access) dla tożsamości skonfigurowanej na potrzeby dostępu do klucza.

### <a name="update-key-version"></a>Zaktualizuj wersję klucza

Typowym scenariuszem jest zaktualizowanie wersji klucza używanego jako klucz zarządzany przez klienta. W zależności od sposobu skonfigurowania szyfrowania rejestru klucz zarządzany przez klienta w Azure Container Registry jest automatycznie aktualizowany lub należy go ręcznie zaktualizować.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Za pomocą polecenia [AZ Key magazynu][az-keyvault-key] można utworzyć klucze magazynu kluczy lub zarządzać nimi. Aby utworzyć nową wersję klucza, uruchom polecenie [AZ Key magazynu klucz Create][az-keyvault-key-create] :

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Następny krok zależy od konfiguracji szyfrowania rejestru:

* Jeśli rejestr jest skonfigurowany do wykrywania aktualizacji wersji klucza, klucz zarządzany przez klienta zostanie automatycznie zaktualizowany w ciągu 1 godziny.

* Jeśli rejestr jest skonfigurowany tak, aby wymagał ręcznej aktualizacji dla nowej wersji klucza, uruchom polecenie [AZ ACR Encryption Obróć-Key][az-acr-encryption-rotate-key] , przekazując nowy identyfikator klucza i tożsamość, którą chcesz skonfigurować:

Aby ręcznie zaktualizować wersję klucza zarządzanego przez klienta:

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

> [!TIP]
> Po uruchomieniu można `az acr encryption rotate-key` przekazać identyfikator klucza z wersją lub identyfikator klucza bez wersji. Jeśli używasz identyfikatora klucza bez wersji, rejestr zostanie następnie skonfigurowany do automatycznego wykrywania aktualizacji wersji klucza.

### <a name="portal"></a>Portal

Użyj ustawień **szyfrowania** rejestru, aby zaktualizować Magazyn kluczy, klucz lub ustawienia tożsamości używane dla klucza zarządzanego przez klienta.

Na przykład, aby skonfigurować nowy klucz:

1. W portalu przejdź do rejestru.
1. W obszarze **Ustawienia** wybierz pozycję **szyfrowanie**  >  **Zmień klucz**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Obróć klucz w Azure Portal":::
1. W obszarze **szyfrowanie** wybierz jedną z następujących opcji:
    * Wybierz pozycję **Wybierz z Key Vault** i wybierz istniejący magazyn kluczy i klucz lub **Utwórz nowy**. Wybrany klucz nie jest w wersji i włącza automatyczne obracanie kluczy.
    * Wybierz pozycję **Wprowadź identyfikator URI klucza** i podaj bezpośrednio identyfikator klucza. Możesz podać identyfikator URI klucza z prawidłową wersją (dla klucza, który musi być obrócony ręcznie) lub identyfikator URI klucza bez wersji (co umożliwia automatyczne obracanie kluczy).
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

Możesz chcieć przechowywać klucz szyfrowania przy użyciu istniejącego magazynu kluczy platformy Azure skonfigurowanego za pomocą [zapory Key Vault](../key-vault/general/network-security.md), co uniemożliwia dostęp publiczny i zezwala tylko na prywatny punkt końcowy lub wybrane sieci wirtualne. 

W tym scenariuszu należy najpierw utworzyć nową tożsamość przypisaną przez użytkownika, Magazyn kluczy i rejestr kontenerów zaszyfrowanej za pomocą klucza zarządzanego przez klienta przy użyciu [interfejsu wiersza polecenia platformy Azure](#enable-customer-managed-key---cli), [portalu](#enable-customer-managed-key---portal)lub [szablonu](#enable-customer-managed-key---template). Szczegółowe instrukcje znajdują się w poprzednich sekcjach tego artykułu.
   > [!NOTE]
   > Nowy magazyn kluczy zostanie wdrożony poza zaporą. Jest on używany tymczasowo tylko do przechowywania klucza zarządzanego przez klienta.

Po utworzeniu rejestru wykonaj następujące czynności. Szczegóły znajdują się w poniższych sekcjach.

1. Włącz tożsamość przypisaną przez system do rejestru.
1. Przyznaj przypisane do systemu uprawnienia do uzyskiwania dostępu do kluczy w magazynie kluczy ograniczonym do zapory Key Vault.
1. Upewnij się, że Zapora Key Vault zezwala na obejście przez usługi zaufane. Obecnie usługa Azure Container Registry może ominąć zaporę tylko wtedy, gdy jest używana tożsamość zarządzana przez system. 
1. Obróć klucz zarządzany przez klienta, wybierając go w magazynie kluczy ograniczonym za pomocą zapory Key Vault.
1. Gdy nie jest już potrzebne, można usunąć magazyn kluczy utworzony poza zaporą.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Krok 1. Włączanie tożsamości przypisanej do systemu w rejestrze

1. W portalu przejdź do rejestru.
1. Wybierz pozycję **Ustawienia**  >   **tożsamość**.
1. W obszarze **przypisano system** **Ustaw wartość** **na włączone**. Wybierz pozycję **Zapisz**.
1. Skopiuj **Identyfikator obiektu** tożsamości.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Krok 2. przyznanie tożsamości przypisanej systemowi dostępu do magazynu kluczy

1. W portalu przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **zasady dostępu > + Dodaj zasady dostępu**.
1. Wybierz pozycję **uprawnienia klucza** i wybierz pozycję **Pobierz**, **Odpakuj klucz** i **Zawijaj klucz**.
1. Wybierz **pozycję Wybierz podmiot zabezpieczeń** i wyszukaj identyfikator obiektu zarządzanej tożsamości przypisanej do systemu lub nazwę rejestru.  
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz**.

### <a name="step-3---enable-key-vault-bypass"></a>Krok 3 — Włączanie obejścia magazynu kluczy

Aby uzyskać dostęp do magazynu kluczy skonfigurowanego za pomocą zapory Key Vault, rejestr musi ominąć zaporę. Upewnij się, że magazyn kluczy jest skonfigurowany tak, aby zezwalać na dostęp przez dowolną [zaufaną usługę](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry jest jedną z zaufanych usług.

1. W portalu przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia**  >  **Sieć**.
1. Potwierdź, zaktualizuj lub Dodaj ustawienia sieci wirtualnej. Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](../key-vault/general/network-security.md).
1. W obszarze **Zezwól usługom zaufanym firmy Microsoft na ominięcie tej zapory** wybierz pozycję **tak**. 

### <a name="step-4---rotate-the-customer-managed-key"></a>Krok 4 — obracanie klucza zarządzanego przez klienta

Po wykonaniu powyższych kroków Obróć do klucza, który jest przechowywany w magazynie kluczy za zaporą.

1. W portalu przejdź do rejestru.
1. W obszarze **Ustawienia** wybierz pozycję **szyfrowanie**  >  **Zmień klucz**.
1. W obszarze **tożsamość** wybierz pozycję **przypisano system**.
1. Wybierz pozycję **Wybierz z Key Vault** i wybierz nazwę magazynu kluczy znajdującego się za zaporą.
1. Wybierz istniejący klucz lub **Utwórz nowy**. Wybrany klucz nie jest w wersji i włącza automatyczne obracanie kluczy.
1. Ukończ wybór klucza i wybierz pozycję **Zapisz**.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="removing-managed-identity"></a>Usuwanie tożsamości zarządzanej


W przypadku próby usunięcia tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu z rejestru, który jest używany do konfigurowania szyfrowania, może zostać wyświetlony komunikat o błędzie podobny do:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Nie będzie też można zmienić (obrócić) klucza szyfrowania. Kroki rozwiązania zależą od typu tożsamości używanej do szyfrowania.

**Tożsamość przypisana przez użytkownika**

Jeśli ten problem występuje w przypadku tożsamości przypisanej przez użytkownika, należy najpierw ponownie przypisać tożsamość przy użyciu identyfikatora GUID wyświetlanego w komunikacie o błędzie. Na przykład:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Następnie po zmianie klucza i przypisaniu innej tożsamości można usunąć oryginalną tożsamość przypisaną przez użytkownika.

**Tożsamość przypisana przez system**

Jeśli ten problem występuje w przypadku tożsamości przypisanej do systemu, [Utwórz bilet pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/) w celu uzyskania pomocy w celu przywrócenia tożsamości.


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
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
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
