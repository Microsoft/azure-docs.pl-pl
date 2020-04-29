---
title: Szyfrowanie — w spoczynku z kluczami zarządzanymi przez klienta
description: Dowiedz się więcej na temat szyfrowania w usłudze Azure Container Registry oraz sposobu szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta przechowywanego w Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461756"
---
# <a name="encryption-using-customer-managed-keys"></a>Szyfrowanie przy użyciu kluczy zarządzanych przez klienta

Gdy przechowujesz obrazy i inne artefakty w rejestrze kontenerów platformy Azure, platforma Azure automatycznie szyfruje zawartość rejestru przy użyciu [kluczy zarządzanych przez usługę](../security/fundamentals/encryption-atrest.md#data-encryption-models). Można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu klucza tworzonego i zarządzanego w Azure Key Vault. Ten artykuł przeprowadzi Cię przez kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta jest obsługiwane przez integrację z [Azure Key Vault](../key-vault/general/overview.md). Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy szyfrowania. Za pomocą Azure Key Vault można również prześledzić użycie klucza.

Ta funkcja jest dostępna w warstwie usługi kontenera **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry SKU](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations) mają zastosowanie. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>
   
## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej 

* Tę funkcję można obecnie włączyć tylko podczas tworzenia rejestru.
* Po włączeniu klucza zarządzanego przez klienta w rejestrze nie można go wyłączyć.
* [Zaufanie zawartości](container-registry-content-trust.md) nie jest obecnie obsługiwane w rejestrze zaszyfrowanym przy użyciu klucza zarządzanego przez klienta.
* W rejestrze zaszyfrowanym kluczem zarządzanym przez klienta uruchamianie dzienników dla [zadań ACR](container-registry-tasks-overview.md) jest obecnie zachowywane przez zaledwie 24 godziny. Jeśli zachodzi potrzeba przechowywania dzienników przez dłuższy czas, zobacz Wskazówki dotyczące [eksportowania i przechowywania dzienników uruchamiania zadań](container-registry-tasks-logs.md#alternative-log-storage).

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

W danych wyjściowych polecenia Zwróć uwagę na następujące wartości: `id` i. `principalId` Te wartości są potrzebne w dalszych krokach w celu skonfigurowania dostępu rejestru do magazynu kluczy.

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

Aby zapobiec utracie danych spowodowanym przez przypadkowe Usuwanie klucza lub magazynu kluczy, należy włączyć następujące ustawienia: **trwałe usuwanie** i **przeczyszczanie ochrony**. Poniższy przykład zawiera parametry dla tych ustawień: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Dodawanie zasad dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp. W poniższym poleceniu AZ webstore [Set-Policy][az-keyvault-set-policy] należy przekazać Identyfikator podmiotu zabezpieczeń, który został utworzony wcześniej w zmiennej środowiskowej. Ustaw uprawnienia do **pobierania**, **unwrapKey**i **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Utwórz klucz i Pobierz identyfikator klucza

Uruchom polecenie [AZ Key magazynu klucz Create][az-keyvault-key-create] , aby utworzyć klucz w magazynie kluczy.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

W danych wyjściowych polecenia Zanotuj identyfikator klucza, `kid`. Ten identyfikator jest używany w następnym kroku:

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
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Tworzenie rejestru z kluczem zarządzanym przez klienta

Uruchom polecenie [AZ ACR Create][az-acr-create] , aby utworzyć rejestr i włączyć klucz zarządzany przez klienta. Przekaż Identyfikator podmiotu zabezpieczeń tożsamości zarządzanej i identyfikator klucza przechowywane wcześniej w zmiennych środowiskowych:

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

## <a name="enable-customer-managed-key---portal"></a>Włącz klucz zarządzany przez klienta — Portal

### <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tworzenie [tożsamości zarządzanej przypisanej przez użytkownika dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w Azure Portal. Aby uzyskać instrukcje, zobacz [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Zanotuj **nazwę zasobu** zarządzanej tożsamości. Ta nazwa jest potrzebna w dalszych krokach.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w Azure Portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki Start: Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](../key-vault/secrets/quick-create-portal.md).

Podczas tworzenia magazynu kluczy dla klucza zarządzanego przez klienta na karcie **podstawowe** należy włączyć następujące ustawienia ochrony: **usuwanie nietrwałe** i **ochronę przed przeczyszczaniem**. Te ustawienia pomagają zapobiec utracie danych spowodowanym przez przypadkowe usunięcie klucza lub magazynu kluczy.

![Utwórz magazyn kluczy w Azure Portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Dodawanie zasad dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp.

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia** > **zasady dostępu > + Dodaj zasady dostępu**.
1. Wybierz pozycję **uprawnienia klucza**i wybierz pozycję **Pobierz**, **Odpakuj klucz**i **Zawijaj klucz**.
1. Wybierz pozycję **Wybierz podmiot zabezpieczeń** i wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika.  
1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz**.

![Utwórz zasady dostępu do magazynu kluczy](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Utwórz klucz

1. Przejdź do magazynu kluczy.
1. Wybierz pozycję **Ustawienia** > **klucze**.
1. Wybierz pozycję **+ Generuj/Importuj** i wprowadź unikatową nazwę klucza.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Utwórz**.
1. Po utworzeniu wybierz klucz i zanotuj bieżącą wersję klucza.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Wybierz **pozycję Utwórz** > **kontenery** > zasobów**Container Registry**.
1. Na karcie **podstawy** wybierz lub Utwórz grupę zasobów, a następnie wprowadź nazwę rejestru. W obszarze **jednostka SKU**wybierz pozycję **Premium**.
1. Na karcie **szyfrowanie** w **kluczu zarządzanym przez klienta**wybierz pozycję **włączone**.
1. W obszarze **tożsamość**Wybierz utworzoną tożsamość zarządzaną.
1. W polu **klucz szyfrowania**wybierz pozycję **Wybierz z Key Vault**.
1. W oknie **Wybieranie klucza z Azure Key Vault** wybierz magazyn kluczy, klucz i wersję utworzoną w poprzedniej sekcji.
1. Na karcie **szyfrowanie** wybierz pozycję **Przegląd + Utwórz**.
1. Wybierz pozycję **Utwórz** , aby wdrożyć wystąpienie rejestru.

![Tworzenie rejestru kontenerów w witrynie Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Aby wyświetlić stan szyfrowania rejestru w portalu, przejdź do rejestru. W obszarze **Ustawienia**wybierz pozycję **szyfrowanie (wersja zapoznawcza)**.

## <a name="enable-customer-managed-key---template"></a>Włączanie szablonu klucza zarządzanego przez klienta

Można również użyć szablonu Menedżer zasobów, aby utworzyć rejestr i włączyć szyfrowanie z kluczem zarządzanym przez klienta. 

Poniższy szablon umożliwia utworzenie nowego rejestru kontenerów i zarządzanej tożsamości przypisanej przez użytkownika. Skopiuj poniższą zawartość do nowego pliku i Zapisz go przy użyciu nazwy pliku, `CMKtemplate.json`takiej jak.

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

Aby wyświetlić stan szyfrowania rejestru, uruchom polecenie [AZ ACR Encryption show-status] [AZ-ACR-Encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Korzystanie z rejestru

Po włączeniu rejestru do szyfrowania danych przy użyciu klucza zarządzanego przez klienta można wykonać te same operacje rejestru, które są wykonywane w rejestrze, który nie jest szyfrowany przy użyciu klucza zarządzanego przez klienta. Można na przykład przeprowadzić uwierzytelnianie przy użyciu rejestru i wypychania obrazów platformy Docker. Zobacz przykładowe polecenia w [wypychaniu i ściąganiu obrazu](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Obróć klucz

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Utwórz nowy klucz, a następnie zaktualizuj rejestr, aby szyfrować dane przy użyciu nowego klucza. Te kroki można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure lub w portalu.

Na przykład uruchom polecenie [AZ Key magazynu klucz Create][az-keyvault-key-create] , aby utworzyć nowy klucz:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Następnie uruchom polecenie [AZ ACR Encryption Obróć-Key][az-acr-encryption-rotate-key] , przekazując nowy identyfikator klucza i Identyfikator podmiotu zabezpieczeń, który został wcześniej skonfigurowany:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Odwołaj klucz

Odwołaj zarządzany przez klienta klucz szyfrowania, zmieniając zasady dostępu w magazynie kluczy lub usuwając klucz. Na przykład za pomocą polecenia [AZ Registry Delete-Policy][az-keyvault-delete-policy] Zmień zasady dostępu zarządzanej tożsamości używanej przez rejestr. Przykład:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odwołanie klucza skutecznie blokuje dostęp do wszystkich danych rejestru, ponieważ rejestr nie może uzyskać dostępu do klucza szyfrowania. Jeśli dostęp do klucza jest włączony lub usunięty klucz zostanie przywrócony, rejestr wybierze klucz, aby można było ponownie uzyskać dostęp do zaszyfrowanych danych rejestru.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [szyfrowaniu na platformie Azure](../security/fundamentals/encryption-atrest.md).
* Dowiedz się więcej o zasadach dostępu i sposobach [zabezpieczania dostępu do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).
* Aby przekazać opinię na temat kluczy zarządzanych przez klienta Azure Container Registry, odwiedź [witrynę usługi GitHub ACR](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
