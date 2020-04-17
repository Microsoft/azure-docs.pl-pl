---
title: Szyfrowanie w spoczynku za pomocą kluczy zarządzanych przez klienta
description: Dowiedz się więcej o szyfrowaniu w pozostałej części rejestru kontenerów platformy Azure i o tym, jak zaszyfrować rejestr za pomocą klucza zarządzanego przez klienta przechowywanego w usłudze Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461756"
---
# <a name="encryption-using-customer-managed-keys"></a>Szyfrowanie przy użyciu kluczy zarządzanych przez klienta

Podczas przechowywania obrazów i innych artefaktów w rejestrze kontenerów platformy Azure automatycznie szyfruje zawartość rejestru w pozostałej części za pomocą [kluczy zarządzanych przez usługę.](../security/fundamentals/encryption-atrest.md#data-encryption-models) Szyfrowanie domyślne można uzupełnić dodatkową warstwą szyfrowania przy użyciu klucza, który można utworzyć i zarządzać w usłudze Azure Key Vault. W tym artykule otrzymasz od kroków przy użyciu interfejsu wiersza polecenia platformy Azure i witryny Azure portal.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta jest obsługiwane przez integrację z [usługą Azure Key Vault](../key-vault/general/overview.md). Można utworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy szyfrowania. Za pomocą usługi Azure Key Vault można również przeprowadzić inspekcję użycia kluczy.

Ta funkcja jest dostępna w warstwie usługi rejestru kontenerów **premium.** Aby uzyskać informacje na temat warstw i limitów usług rejestru, zobacz [Jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md).

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>
   
## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej 

* Obecnie tę funkcję można włączyć tylko podczas tworzenia rejestru.
* Po włączeniu klucza zarządzanego przez klienta w rejestrze nie można go wyłączyć.
* [Zaufanie do zawartości](container-registry-content-trust.md) nie jest obecnie obsługiwane w rejestrze zaszyfrowanym za pomocą klucza zarządzanego przez klienta.
* W rejestrze zaszyfrowanym za pomocą klucza zarządzanego przez klienta dzienniki uruchamiania [zadań usługi ACR](container-registry-tasks-overview.md) są obecnie przechowywane tylko przez 24 godziny. Jeśli chcesz zachować dzienniki przez dłuższy czas, zobacz wskazówki dotyczące [eksportowania i przechowywania dzienników uruchamiania zadań](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć kroki interfejsu wiersza polecenia platformy Azure w tym artykule, trzeba interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Włączanie klucza zarządzanego przez klienta — cli

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom polecenie [tworzenia grupy az,][az-group-create] aby utworzyć grupę zasobów do tworzenia magazynu kluczy, rejestru kontenerów i innych wymaganych zasobów.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz [tożsamość zarządzaną](../active-directory/managed-identities-azure-resources/overview.md) przypisaną przez użytkownika dla zasobów platformy Azure za pomocą polecenia [tworzenia tożsamości az.][az-identity-create] Ta tożsamość będzie używana przez rejestr, aby uzyskać dostęp do usługi Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

W danych wyjściowych polecenia należy zwrócić `id` `principalId`uwagę na następujące wartości: i . Te wartości są potrzebne w późniejszych krokach, aby skonfigurować dostęp rejestru do magazynu kluczy.

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

Utwórz magazyn kluczy z [az keyvault utworzyć][az-keyvault-create] do przechowywania klucza zarządzanego przez klienta do szyfrowania rejestru. 

Aby zapobiec utracie danych spowodowanej przypadkowymi usunięciemi kluczy lub przechowalni kluczy, należy włączyć następujące ustawienia: **Usuwanie nietrwałe** i **ochrona przed przeczyszczaniem**. Poniższy przykład zawiera parametry dla tych ustawień: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Dodawanie zasad dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp. W następującym poleceniu [az keyvault set-policy][az-keyvault-set-policy] należy przekazać główny identyfikator utworzonej tożsamości zarządzanej, która została wcześniej przechowywana w zmiennej środowiskowej. Ustaw uprawnienia klawiszy, aby **uzyskać**, **unwrapKey**i **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Tworzenie klucza i uzyskanie identyfikatora klucza

Uruchom polecenie [tworzenia klucza az keyvault,][az-keyvault-key-create] aby utworzyć klucz w magazynie kluczy.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

W danych wyjściowych polecenia należy zanotować `kid`identyfikator klucza, . Ten identyfikator jest używany w następnym kroku:

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
Dla wygody, zapisz tę wartość w zmiennej środowiskowej:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Tworzenie rejestru z kluczem zarządzanym przez klienta

Uruchom [polecenie az acr create,][az-acr-create] aby utworzyć rejestr i włączyć klucz zarządzany przez klienta. Przekaż identyfikator dublowany tożsamości zarządzanej i identyfikator klucza, przechowywany wcześniej w zmiennych środowiskowych:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Pokaż stan szyfrowania

Aby pokazać, czy szyfrowanie rejestru z kluczem zarządzanym przez klienta jest włączone, uruchom polecenie [az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Włączanie klucza zarządzanego przez klienta — portal

### <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Utwórz [tożsamość zarządzaną](../active-directory/managed-identities-azure-resources/overview.md) przypisaną przez użytkownika dla zasobów platformy Azure w witrynie Azure Portal. Aby uzyskać instrukcje, zobacz [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Zanotuj **nazwę zasobu** tożsamości zarządzanej. Ta nazwa jest potrzebna w późniejszych krokach.

![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w witrynie Azure portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby uzyskać kroki tworzenia magazynu kluczy, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu portalu Azure.](../key-vault/secrets/quick-create-portal.md)

Podczas tworzenia magazynu kluczy dla klucza zarządzanego przez klienta na karcie **Podstawy** należy włączyć następujące ustawienia ochrony: **Usuwanie nietrwałe** i **Ochrona przed przeczyszczaniem**. Te ustawienia pomagają zapobiegać utracie danych spowodowanej przypadkowymi usunięciemi kluczy lub magazynu kluczy.

![Tworzenie magazynu kluczy w witrynie Azure portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Dodawanie zasad dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość mogła uzyskać do niego dostęp.

1. Przejdź do magazynu kluczy.
1. Wybierz **pozycję Zasady** > dostępu do ustawień **> +Dodaj zasady dostępu**.
1. Wybierz pozycję **Uprawnienia klucza**i wybierz pozycję **Pobierz**, **Rozpakuj klawisz**i **Zawiń klucz**.
1. Wybierz **pozycję Wybierz podmiot zabezpieczeń** i wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika.  
1. Wybierz **pozycję Dodaj**, a następnie wybierz pozycję **Zapisz**.

![Tworzenie zasad dostępu do magazynu kluczy](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Utwórz klucz

1. Przejdź do magazynu kluczy.
1. Wybierz **pozycję Klawisze ustawień** > **Keys**.
1. Wybierz **+Generuj/Zaimportuj** i wprowadź unikatową nazwę klucza.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Utwórz**.
1. Po utworzeniu wybierz klucz i zanotuj bieżącą wersję klucza.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Wybierz pozycję Utwórz rejestr > **kontenerów kontenerów** >  **kontenerów kontenerów****kontenerów**.
1. Na karcie **Podstawy** wybierz lub utwórz grupę zasobów i wprowadź nazwę rejestru. W **jednostce SKU** **wybierz**premium .
1. Na karcie **Szyfrowanie** w **obszarze Klucz zarządzany przez klienta**wybierz pozycję **Włączone**.
1. W **obszarze Tożsamość**wybierz utworzoną tożsamość zarządzaną.
1. W **obszarze Klucz szyfrowania**wybierz **pozycję Wybierz z przechowalni kluczy**.
1. W oknie **Wybierz klucz z usługi Azure Key Vault** wybierz magazyn kluczy, klucz i wersję utworzoną w poprzedniej sekcji.
1. Na karcie **Szyfrowanie** wybierz pozycję **Przejrzyj + utwórz**.
1. Wybierz **pozycję Utwórz,** aby wdrożyć wystąpienie rejestru.

![Tworzenie rejestru kontenerów w witrynie Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Aby wyświetlić stan szyfrowania rejestru w portalu, przejdź do rejestru. W obszarze **Ustawienia**wybierz **pozycję Szyfrowanie (podgląd)**.

## <a name="enable-customer-managed-key---template"></a>Włącz klucz zarządzany przez klienta — szablon

Za pomocą szablonu Menedżera zasobów można również utworzyć rejestr i włączyć szyfrowanie za pomocą klucza zarządzanego przez klienta. 

Poniższy szablon tworzy nowy rejestr kontenerów i tożsamość zarządzaną przypisaną przez użytkownika. Skopiuj następującą zawartość do nowego pliku i `CMKtemplate.json`zapisz ją przy użyciu nazwy pliku, takiej jak .

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
* Klucz przechowalni kluczy, identyfikowany przez identyfikator klucza

Uruchom następujące polecenie [tworzenia wdrożenia grupy AZ][az-group-deployment-create] w celu utworzenia rejestru przy użyciu poprzedniego pliku szablonu. Jeśli jest to wskazane, podaj nową nazwę rejestru i nazwę tożsamości zarządzanej, a także nazwę magazynu kluczy i identyfikator klucza, który został utworzony. 

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

Aby wyświetlić stan szyfrowania rejestru, uruchom polecenie [az acr encryption show-status][az-acr-encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Korzystanie z rejestru

Po włączeniu rejestru do szyfrowania danych przy użyciu klucza zarządzanego przez klienta, można wykonać te same operacje rejestru, które można wykonać w rejestrze, który nie jest szyfrowany za pomocą klucza zarządzanego przez klienta. Na przykład można uwierzytelnić za pomocą rejestru i wypychać obrazy platformy Docker. Zobacz przykładowe polecenia w [aplikacji Wypychanie i ciągnięcie obrazu](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Przycisk obracania

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Utwórz nowy klucz, a następnie zaktualizuj rejestr, aby zaszyfrować dane przy użyciu nowego klucza. Można wykonać te kroki przy użyciu interfejsu wiersza polecenia platformy Azure lub w portalu.

Na przykład uruchom polecenie [tworzenia klucza az keyvault,][az-keyvault-key-create] aby utworzyć nowy klucz:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Następnie uruchom polecenie [az acr rotate-key,][az-acr-encryption-rotate-key] przekazując nowy identyfikator klucza i główny identyfikator wcześniej skonfigurowanej tożsamości zarządzanej:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Odwołaj klucz

Odwołaj klucz szyfrowania zarządzanego przez klienta, zmieniając zasady dostępu w magazynie kluczy lub usuwając klucz. Na przykład użyj polecenia [az keyvault delete-policy,][az-keyvault-delete-policy] aby zmienić zasady dostępu tożsamości zarządzanej używanej przez rejestr. Przykład:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odwoływanie klucza skutecznie blokuje dostęp do wszystkich danych rejestru, ponieważ rejestr nie może uzyskać dostępu do klucza szyfrowania. Jeśli dostęp do klucza jest włączony lub zostanie przywrócony usunięty klucz, rejestr wybierze klucz, aby ponownie uzyskać dostęp do zaszyfrowanych danych rejestru.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [szyfrowaniu w spoczynku na platformie Azure](../security/fundamentals/encryption-atrest.md).
* Dowiedz się więcej o zasadach dostępu i [zabezpieczaniu dostępu do magazynu kluczy.](../key-vault/general/secure-your-key-vault.md)
* Aby przekazać opinię na temat kluczy zarządzanych przez klienta dla rejestru kontenerów platformy Azure, odwiedź [witrynę ACR GitHub](https://aka.ms/acr/issues).


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
