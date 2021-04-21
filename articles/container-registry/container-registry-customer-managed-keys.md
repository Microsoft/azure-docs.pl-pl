---
title: Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta
description: Dowiedz się więcej na temat szyfrowania w spoczynku rejestru kontenerów platformy Azure i sposobu szyfrowania rejestru w witrynie Premium przy użyciu klucza zarządzanego przez klienta przechowywanego w usłudze Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 9ec32e32d187a3db07f023c78efbd301ef578cbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817039"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Szyfrowanie rejestru przy użyciu klucza zarządzanego przez klienta

Gdy przechowujesz obrazy i inne artefakty w rejestrze kontenerów platformy Azure, platforma Azure automatycznie szyfruje zawartość rejestru w spoczynku przy użyciu kluczy [zarządzanych przez usługę.](../security/fundamentals/encryption-models.md) Domyślne szyfrowanie można uzupełnić dodatkową warstwą szyfrowania przy użyciu klucza, który tworzysz i zarządzasz w Azure Key Vault (klucz zarządzany przez klienta). Ten artykuł zawiera instrukcje korzystania z interfejsu wiersza polecenia platformy Azure, interfejsu wiersza Azure Portal lub Resource Manager szablonu.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta jest obsługiwane przez integrację z [Azure Key Vault](../key-vault/general/overview.md): 

* Możesz utworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub użyć interfejsów API Azure Key Vault do generowania kluczy. 
* Za Azure Key Vault można również sprawdzić użycie klucza.
* Azure Container Registry obsługuje automatyczną rotację kluczy szyfrowania rejestru, gdy nowa wersja klucza jest dostępna w Azure Key Vault. Można również ręcznie obracać klucze szyfrowania rejestru.

Ta funkcja jest dostępna w warstwie **usługi rejestru** kontenerów w warstwie Premium. Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).


## <a name="things-to-know"></a>Co należy wiedzieć

* Obecnie klucz zarządzany przez klienta można włączyć tylko podczas tworzenia rejestru. Podczas włączania klucza należy skonfigurować tożsamość *zarządzaną przypisaną* przez użytkownika w celu uzyskania dostępu do magazynu kluczy.
* Po włączeniu szyfrowania za pomocą klucza zarządzanego przez klienta w rejestrze nie można wyłączyć szyfrowania.  
* Azure Container Registry obsługuje tylko klucze RSA lub RSA-HSM. Klucze krzywej wielokropka nie są obecnie obsługiwane.
* [Zaufanie do](container-registry-content-trust.md) zawartości nie jest obecnie obsługiwane w rejestrze zaszyfrowanym przy użyciu klucza zarządzanego przez klienta.
* W rejestrze zaszyfrowanym za pomocą klucza zarządzanego przez klienta uruchom dzienniki dla zadania usługi ACR [są](container-registry-tasks-overview.md) obecnie przechowywane tylko przez 24 godziny. Jeśli musisz przechowywać dzienniki przez dłuższy czas, zobacz wskazówki dotyczące eksportowania i przechowywania [dzienników uruchamiania zadań.](container-registry-tasks-logs.md#alternative-log-storage)


> [!IMPORTANT]
> Jeśli planujesz przechowywanie klucza szyfrowania rejestru w istniejącym magazynie kluczy platformy Azure, który nie zezwala na dostęp publiczny i zezwala tylko na prywatny punkt końcowy lub wybrane sieci wirtualne, potrzebne są dodatkowe kroki konfiguracji. Zobacz [Artykuł Advanced scenario: Key Vault firewall (Zaawansowany](#advanced-scenario-key-vault-firewall) scenariusz: zapora sieciowa) w tym artykule.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatyczna lub ręczna aktualizacja wersji kluczy

Ważnym czynnikiem w zakresie zabezpieczeń rejestru zaszyfrowanego za pomocą klucza zarządzanego przez klienta jest to, jak często aktualizujesz (obracasz) klucz szyfrowania. Organizacja może mieć zasady zgodności, [](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) które wymagają regularnego aktualizowania wersji kluczy przechowywanych w Azure Key Vault, gdy są używane jako klucze zarządzane przez klienta. 

W przypadku konfigurowania szyfrowania rejestru za pomocą klucza zarządzanego przez klienta dostępne są dwie opcje aktualizowania wersji klucza używanej do szyfrowania:

*  Automatycznie aktualizuj wersję klucza — aby automatycznie aktualizować klucz zarządzany przez klienta, gdy nowa wersja jest dostępna w programie Azure Key Vault, należy pominąć wersję klucza po włączeniu szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Gdy rejestr jest szyfrowany przy użyciu klucza bez wersji, program Azure Container Registry regularnie sprawdza magazyn kluczy pod nową wersją klucza i aktualizuje klucz zarządzany przez klienta w ciągu 1 godziny. Azure Container Registry automatycznie używa najnowszej wersji klucza.

* **Ręcznie zaktualizuj wersję** klucza — aby użyć określonej wersji klucza do szyfrowania rejestru, określ tę wersję klucza po włączeniu szyfrowania rejestru przy użyciu klucza zarządzanego przez klienta. Gdy rejestr jest szyfrowany przy użyciu określonej wersji klucza, Azure Container Registry używa tej wersji do szyfrowania do momentu ręcznego obracania klucza zarządzanego przez klienta.

Aby uzyskać szczegółowe informacje, zobacz Choose key ID with or without key version (Wybieranie identyfikatora klucza z lub bez [niego)](#choose-key-id-with-or-without-key-version) i [Update key version](#update-key-version)(Aktualizowanie wersji klucza) w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki interfejsu wiersza polecenia platformy Azure opisane w tym artykule, potrzebujesz interfejsu wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej albo Azure Cloud Shell. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Włączanie klucza zarządzanego przez klienta — interfejs wiersza polecenia

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

W razie potrzeby uruchom [polecenie az group create,][az-group-create] aby utworzyć grupę zasobów na potrzeby tworzenia magazynu kluczy, rejestru kontenerów i innych wymaganych zasobów.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Utwórz tożsamość zarządzaną przypisaną przez użytkownika [dla zasobów platformy Azure za](../active-directory/managed-identities-azure-resources/overview.md) pomocą polecenia az identity [create.][az-identity-create] Ta tożsamość będzie używana przez rejestr w celu uzyskania dostępu do Key Vault usługi.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

W danych wyjściowych polecenia zanotuj następujące wartości: `id` i `principalId` . Te wartości będą potrzebne w kolejnych krokach, aby skonfigurować dostęp rejestru do magazynu kluczy.

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

Dla wygody przechowuj następujące wartości w zmiennych środowiskowych:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Utwórz magazyn kluczy za pomocą [az keyvault create,][az-keyvault-create] aby przechowywać klucz zarządzany przez klienta na potrzeby szyfrowania rejestru. 

Domyślnie ustawienie usuwania **nie soft jest** automatycznie włączone w nowym magazynie kluczy. Aby zapobiec utracie danych spowodowanej przypadkowym usunięciem klucza lub magazynu kluczy, włącz również **ustawienie ochrony przed przeczyszczaniem.**

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Aby użyć go w kolejnych krokach, pobierz identyfikator zasobu magazynu kluczy:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Włączanie dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość może uzyskać do niego dostęp. W poniższym [poleceniu az keyvault set-policy][az-keyvault-set-policy] przekaż identyfikator podmiotu zabezpieczeń utworzonej tożsamości zarządzanej przechowywany wcześniej w zmiennej środowiskowej. Ustaw uprawnienia klucza, **aby pobrać**, **unwrapKey** i **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Alternatywnie możesz użyć kontroli [dostępu na Key Vault](../key-vault/general/rbac-guide.md) Azure w celu przypisania uprawnień do tożsamości w celu uzyskania dostępu do magazynu kluczy. Na przykład przypisz do tożsamości Key Vault crypto service encryption za pomocą [polecenia az role assignment create:](/cli/azure/role/assignment#az_role_assignment_create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Tworzenie klucza i uzyskiwanie identyfikatora klucza

Uruchom polecenie [az keyvault key create,][az-keyvault-key-create] aby utworzyć klucz w magazynie kluczy.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

W danych wyjściowych polecenia zanotuj identyfikator klucza, `kid` . Użyjemy tego identyfikatora w następnym kroku:

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

### <a name="choose-key-id-with-or-without-key-version"></a>Wybieranie identyfikatora klucza z wersją klucza lub bez niego

Dla wygody przechowaj format wybierany dla identyfikatora klucza w zmiennej $keyID środowiskowej. Możesz użyć identyfikatora klucza z wersją lub klucza bez wersji.

#### <a name="manual-key-rotation---key-id-with-version"></a>Ręczna rotacja kluczy — identyfikator klucza z wersją

W przypadku szyfrowania rejestru za pomocą klucza zarządzanego przez klienta ten klucz umożliwia tylko ręczną rotację kluczy w Azure Container Registry.

W tym przykładzie jest przechowywane właściwość `kid` klucza:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatyczna rotacja kluczy — identyfikator klucza pomija wersję 

W przypadku szyfrowania rejestru za pomocą klucza zarządzanego przez klienta ten klucz umożliwia automatyczną rotację kluczy po wykryciu nowej wersji klucza w Azure Key Vault.

W tym przykładzie wersja jest usuwana z właściwości `kid` klucza:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Tworzenie rejestru przy użyciu klucza zarządzanego przez klienta

Uruchom polecenie [az acr create,][az-acr-create] aby utworzyć rejestr w warstwie usługi Premium i włączyć klucz zarządzany przez klienta. Przekaż identyfikator tożsamości zarządzanej i identyfikator klucza, przechowywane wcześniej w zmiennych środowiskowych:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Pokazywanie stanu szyfrowania

Aby sprawdzić, czy jest włączone szyfrowanie rejestru za pomocą klucza zarządzanego przez klienta, uruchom [polecenie az acr encryption show:][az-acr-encryption-show]

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

## <a name="enable-customer-managed-key---portal"></a>Włączanie klucza zarządzanego przez klienta — portal

### <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Utwórz tożsamość zarządzaną przypisaną [przez użytkownika dla zasobów platformy Azure w](../active-directory/managed-identities-azure-resources/overview.md) Azure Portal. Aby uzyskać instrukcje, [zobacz Tworzenie tożsamości przypisanej przez użytkownika.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

Użyj nazwy tożsamości w kolejnych krokach.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Tworzenie tożsamości przypisanej przez użytkownika w Azure Portal":::

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby uzyskać instrukcje dotyczące tworzenia magazynu kluczy, zobacz [Szybki start: tworzenie magazynu kluczy przy](../key-vault/general/quick-create-portal.md)użyciu Azure Portal .

Podczas tworzenia magazynu kluczy dla klucza zarządzanego  przez klienta na karcie Podstawowe włącz ustawienie **Przeczyść** ochronę. To ustawienie pomaga zapobiegać utracie danych spowodowanej przypadkowym usunięciem klucza lub magazynu kluczy.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Tworzenie magazynu kluczy w Azure Portal":::

### <a name="enable-key-vault-access"></a>Włączanie dostępu do magazynu kluczy

Skonfiguruj zasady dla magazynu kluczy, aby tożsamość może uzyskać do niego dostęp.

1. Przejdź do magazynu kluczy.
1. Wybierz **pozycję Ustawienia** Zasady dostępu > + Dodaj zasady  >  **dostępu.**
1. Wybierz **pozycję Uprawnienia klucza** i wybierz pozycję **Pobierz,** **Odpakuj klucz** i **Opakuj klucz.**
1. W **grupę** Wybierz podmiot zabezpieczeń wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika.  
1. Wybierz **pozycję Dodaj,** a następnie wybierz **pozycję Zapisz.**

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Tworzenie zasad dostępu magazynu kluczy":::

Alternatywnie możesz użyć kontroli [dostępu na Key Vault](../key-vault/general/rbac-guide.md) Azure w celu przypisania uprawnień do tożsamości w celu uzyskania dostępu do magazynu kluczy. Na przykład przypisz do tożsamości Key Vault szyfrowania usługi kryptograficznych.

1. Przejdź do magazynu kluczy.
1. Wybierz **pozycję Kontrola dostępu (IAM)**+  >  **Dodaj**  >  **przypisanie roli.**
1. W **oknie Dodawanie przypisania** roli:
    1. Wybierz **Key Vault użytkownika szyfrowania usługi kryptograficznych.** 
    1. Przypisz dostęp do **tożsamości zarządzanej przypisanej przez użytkownika.**
    1. Wybierz nazwę zasobu tożsamości zarządzanej przypisanej przez użytkownika, a następnie wybierz pozycję **Zapisz.**

### <a name="create-key-optional"></a>Utwórz klucz (opcjonalnie)

Opcjonalnie utwórz klucz w magazynie kluczy, który będzie używany do szyfrowania rejestru. Wykonaj następujące kroki, jeśli chcesz wybrać określoną wersję klucza jako klucz zarządzany przez klienta. 

1. Przejdź do magazynu kluczy.
1. Wybierz **pozycję Klucze**  >  **ustawień.**
1. Wybierz **pozycję +Generuj/Zaimportuj** i wprowadź unikatową nazwę klucza.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Utwórz.**
1. Po utworzeniu wybierz klucz, a następnie wybierz bieżącą wersję. Skopiuj **identyfikator klucza** wersji klucza.

### <a name="create-azure-container-registry"></a>Tworzenie rejestru kontenerów platformy Azure

1. Wybierz **pozycję Utwórz zasób**  >  **Kontenery**  >  **Container Registry**.
1. Na karcie **Podstawowe** wybierz lub utwórz grupę zasobów, a następnie wprowadź nazwę rejestru. W **sku** wybierz pozycję **Premium.**
1. Na karcie **Szyfrowanie** w obszarze **Klucz zarządzany przez klienta** wybierz pozycję **Włączone.**
1. W **tożsamości** wybierz utworzoną tożsamość zarządzaną.
1. W **szyfrowaniu** wybierz jedną z następujących opcji:
    * Wybierz **pozycję Wybierz Key Vault**, wybierz istniejący magazyn kluczy i klucz lub Utwórz **nowy.** Wybierany klucz nie jest wersjonarowany i umożliwia automatyczną rotację kluczy.
    * Wybierz **pozycję Wprowadź identyfikator URI** klucza i podaj identyfikator klucza bezpośrednio. Możesz podać numer URI klucza wersji (dla klucza, który musi być obracany ręcznie) lub bez wersji URI klucza (co umożliwia automatyczną rotację kluczy). 
1. Na karcie **Szyfrowanie** wybierz pozycję **Przejrzyj i utwórz.**
1. Wybierz **pozycję Utwórz,** aby wdrożyć wystąpienie rejestru.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Tworzenie zaszyfrowanego rejestru w Azure Portal":::

Aby wyświetlić stan szyfrowania rejestru w portalu, przejdź do rejestru. W **obszarze Ustawienia** wybierz pozycję **Szyfrowanie.**

## <a name="enable-customer-managed-key---template"></a>Włączanie klucza zarządzanego przez klienta — szablon

Można również użyć szablonu Resource Manager, aby utworzyć rejestr i włączyć szyfrowanie przy użyciu klucza zarządzanego przez klienta.

Poniższy szablon tworzy nowy rejestr kontenerów i tożsamość zarządzaną przypisaną przez użytkownika. Skopiuj następującą zawartość do nowego pliku i zapisz ją przy użyciu nazwy pliku, takiej jak `CMKtemplate.json` .

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

* Magazyn kluczy identyfikowany przez nazwę
* Klucz magazynu kluczy identyfikowany przez identyfikator klucza

Uruchom następujące polecenie [az deployment group create,][az-deployment-group-create] aby utworzyć rejestr przy użyciu poprzedniego pliku szablonu. Tam, gdzie jest to wskazane, podaj nową nazwę rejestru i nazwę tożsamości zarządzanej, a także nazwę magazynu kluczy i utworzony identyfikator klucza.

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

### <a name="show-encryption-status"></a>Wyświetlanie stanu szyfrowania

Aby wyświetlić stan szyfrowania rejestru, uruchom [polecenie az acr encryption show:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Korzystanie z rejestru

Po włączeniu klucza zarządzanego przez klienta w rejestrze można wykonać te same operacje rejestru, które są wykonywane w rejestrze, który nie jest zaszyfrowany za pomocą klucza zarządzanego przez klienta. Na przykład możesz uwierzytelnić się za pomocą rejestru i wypchnąć obrazy platformy Docker. Zobacz przykładowe polecenia w [tece Wypychanie i ściąganie obrazu.](container-registry-get-started-docker-cli.md)

## <a name="rotate-key"></a>Obracanie klucza

Zaktualizuj wersję klucza w Azure Key Vault lub utwórz nowy klucz, a następnie zaktualizuj rejestr w celu zaszyfrowania danych przy użyciu klucza. Te kroki można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu.

Podczas rotacji klucza zazwyczaj określa się tę samą tożsamość, która jest używana podczas tworzenia rejestru. Opcjonalnie skonfiguruj nową tożsamość przypisaną przez użytkownika w celu uzyskania dostępu do klucza lub włącz i określ tożsamość przypisaną przez system rejestru.

> [!NOTE]
> Upewnij się, że dla [tożsamości skonfigurowanej na](#enable-key-vault-access) potrzeby dostępu do klucza ustawiono wymagany dostęp do magazynu kluczy.

### <a name="update-key-version"></a>Aktualizowanie wersji klucza

Częstym scenariuszem jest zaktualizowanie wersji klucza używanej jako klucz zarządzany przez klienta. W zależności od konfiguracji szyfrowania rejestru klucz zarządzany przez klienta w programie Azure Container Registry jest automatycznie aktualizowany lub musi być ręcznie aktualizowany.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj [polecenia az keyvault key,][az-keyvault-key] aby utworzyć klucze magazynu kluczy lub zarządzać nimi. Aby utworzyć nową wersję klucza, uruchom [polecenie az keyvault key create:][az-keyvault-key-create]

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Następny krok zależy od konfiguracji szyfrowania rejestru:

* Jeśli rejestr jest skonfigurowany do wykrywania aktualizacji wersji klucza, klucz zarządzany przez klienta zostanie zaktualizowany automatycznie w ciągu 1 godziny.

* Jeśli rejestr jest skonfigurowany tak, aby wymagać ręcznego aktualizowania nowej wersji klucza, uruchom polecenie [az acr encryption rotate-key,][az-acr-encryption-rotate-key] przekazując nowy identyfikator klucza i tożsamość, którą chcesz skonfigurować:

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
> Po uruchomieniu programu można przekazać identyfikator klucza wersji lub identyfikator klucza bez `az acr encryption rotate-key` wersji. Jeśli używasz identyfikatora klucza bez wersji, rejestr jest następnie konfigurowany do automatycznego wykrywania późniejszych aktualizacji wersji klucza.

### <a name="portal"></a>Portal

Użyj ustawień szyfrowania  rejestru, aby zaktualizować ustawienia magazynu kluczy, klucza lub tożsamości używane dla klucza zarządzanego przez klienta.

Aby na przykład skonfigurować nowy klucz:

1. W portalu przejdź do rejestru.
1. W **obszarze Ustawienia** wybierz pozycję **Klucz** zmiany  >  **szyfrowania.**

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Obracanie klucza w Azure Portal":::
1. W **szyfrowaniu** wybierz jedną z następujących opcji:
    * Wybierz **pozycję Wybierz Key Vault**, wybierz istniejący magazyn kluczy i klucz lub Utwórz **nowy.** Wybierany klucz nie ma wersji i umożliwia automatyczną rotację kluczy.
    * Wybierz **pozycję Wprowadź identyfikator URI** klucza i podaj identyfikator klucza bezpośrednio. Możesz podać numer URI klucza wersji (dla klucza, który musi być obracany ręcznie) lub nieudzyzytowany klucz URI (który umożliwia automatyczną rotację kluczy).
1. Uzupełnij wybór klucza i wybierz pozycję **Zapisz.**

## <a name="revoke-key"></a>Odwołaj klucz

Odwołaj klucz szyfrowania zarządzany przez klienta, zmieniając zasady dostępu lub uprawnienia do magazynu kluczy albo usuwając klucz. Na przykład użyj polecenia [az keyvault delete-policy,][az-keyvault-delete-policy] aby zmienić zasady dostępu tożsamości zarządzanej używanej przez rejestr:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Odwołanie klucza skutecznie blokuje dostęp do wszystkich danych rejestru, ponieważ rejestr nie może uzyskać dostępu do klucza szyfrowania. Jeśli dostęp do klucza jest włączony lub usunięty klucz zostanie przywrócony, rejestr wybierze klucz, aby ponownie uzyskać dostęp do zaszyfrowanych danych rejestru.

## <a name="advanced-scenario-key-vault-firewall"></a>Zaawansowany scenariusz: Key Vault zapory

Klucz szyfrowania może być zapisywany przy użyciu istniejącego magazynu kluczy platformy Azure skonfigurowanego z zaporą [usługi Key Vault,](../key-vault/general/network-security.md)która nie zezwala na dostęp publiczny i zezwala tylko na prywatny punkt końcowy lub wybrane sieci wirtualne. 

W tym scenariuszu najpierw utwórz nową tożsamość przypisaną przez użytkownika, magazyn kluczy i rejestr kontenerów [](#enable-customer-managed-key---portal)zaszyfrowaną przy użyciu klucza zarządzanego przez klienta przy użyciu interfejsu wiersza polecenia platformy [Azure,](#enable-customer-managed-key---cli)portalu lub [szablonu](#enable-customer-managed-key---template). Szczegółowe instrukcje znajdują się w poprzednich sekcjach tego artykułu.
   > [!NOTE]
   > Nowy magazyn kluczy jest wdrażany poza zaporą. Jest on używany tylko tymczasowo do przechowywania klucza zarządzanego przez klienta.

Po utworzeniu rejestru przejdź do następujących kroków. Szczegółowe informacje znajdują się w poniższych sekcjach.

1. Włącz tożsamość przypisaną przez system rejestru.
1. Przyznaj tożsamości przypisanej przez system uprawnienia dostępu do kluczy w magazynie kluczy, który jest ograniczony Key Vault zapory.
1. Upewnij się, że zapora Key Vault umożliwia obejście przez zaufane usługi. Obecnie rejestr kontenerów platformy Azure może ominąć zaporę tylko w przypadku korzystania z jego tożsamości zarządzanej przez system. 
1. Wywróć klucz zarządzany przez klienta, wybierając klucz w magazynie kluczy, który jest ograniczony Key Vault zaporą.
1. Gdy magazyn kluczy utworzony poza zaporą nie będzie już potrzebny, można go usunąć.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Krok 1. Włączanie tożsamości przypisanej przez system do rejestru

1. W portalu przejdź do rejestru.
1. Wybierz **pozycję Settings** Identity  >   **(Tożsamość ustawień).**
1. W **obszarze Przypisano system** ustaw wartość **Stan** **na Wł.**. Wybierz pozycję **Zapisz**.
1. Skopiuj **identyfikator obiektu** tożsamości.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Krok 2. Udzielanie dostępu tożsamości przypisanej przez system do magazynu kluczy

1. W portalu przejdź do magazynu kluczy.
1. Wybierz **pozycję Ustawienia** Zasady dostępu > + Dodaj zasady  >  **dostępu.**
1. Wybierz **pozycję Uprawnienia klucza,** a następnie **wybierz pozycję Pobierz,** **Odpakuj klucz** i **Opakuj klucz.**
1. Wybierz **pozycję Wybierz** podmiot zabezpieczeń i wyszukaj identyfikator obiektu przypisanej przez system tożsamości zarządzanej lub nazwę rejestru.  
1. Wybierz **pozycję Dodaj,** a następnie wybierz **pozycję Zapisz.**

### <a name="step-3---enable-key-vault-bypass"></a>Krok 3. Włączanie obejścia magazynu kluczy

Aby uzyskać dostęp do magazynu kluczy skonfigurowanego Key Vault zaporą, rejestr musi pominąć zaporę. Upewnij się, że magazyn kluczy jest skonfigurowany tak, aby zezwalać na dostęp dowolnej [zaufanej usłudze.](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) Azure Container Registry jest jedną z zaufanych usług.

1. W portalu przejdź do magazynu kluczy.
1. Wybierz **pozycję Ustawienia**  >  **Sieć.**
1. Potwierdź, zaktualizuj lub dodaj ustawienia sieci wirtualnej. Aby uzyskać szczegółowe instrukcje, [zobacz Configure Azure Key Vault firewalls and virtual networks (Konfigurowanie zapór i sieci wirtualnych).](../key-vault/general/network-security.md)
1. W witrynie Allow Microsoft Trusted Services to bypass this firewall (Zezwalaj usługom zaufanym firmy Microsoft na **obejście tej zapory)** wybierz **pozycję Yes (Tak).** 

### <a name="step-4---rotate-the-customer-managed-key"></a>Krok 4. Obracanie klucza zarządzanego przez klienta

Po wykonaniu powyższych kroków obróć do klucza przechowywanego w magazynie kluczy za zaporą.

1. W portalu przejdź do rejestru.
1. W **obszarze Ustawienia** wybierz pozycję **Klucz** zmiany  >  **szyfrowania.**
1. W **tożsamość**, wybierz **opcję przypisane przez system.**
1. Wybierz **pozycję Key Vault**, a następnie wybierz nazwę magazynu kluczy, który jest za zaporą.
1. Wybierz istniejący klucz lub **utwórz nowy.** Wybierany klucz nie ma wersji i umożliwia automatyczną rotację kluczy.
1. Uzupełnij wybór klucza i wybierz pozycję **Zapisz.**

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="removing-managed-identity"></a>Usuwanie tożsamości zarządzanej


Jeśli spróbujesz usunąć tożsamość zarządzaną przypisaną przez użytkownika lub przypisaną przez system z rejestru używanego do konfigurowania szyfrowania, może zostać wyświetlony komunikat o błędzie podobny do:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Nie będzie można również zmienić (obrócić) klucza szyfrowania. Kroki rozwiązywania zależą od typu tożsamości używanej do szyfrowania.

**Tożsamość przypisana przez użytkownika**

Jeśli ten problem występuje z tożsamością przypisaną przez użytkownika, najpierw ponownie przypisz tożsamość przy użyciu identyfikatora GUID wyświetlanego w komunikacie o błędzie. Na przykład:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Następnie po zmianie klucza i przypisaniu innej tożsamości możesz usunąć oryginalną tożsamość przypisaną przez użytkownika.

**Tożsamość przypisana przez system**

Jeśli ten problem występuje w przypadku tożsamości przypisanej przez system, utwórz bilet pomocy technicznej platformy [Azure,](https://azure.microsoft.com/support/create-ticket/) aby uzyskać pomoc w przywróceniu tożsamości.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [szyfrowaniu danych magazynowych na platformie Azure.](../security/fundamentals/encryption-atrest.md)
* Dowiedz się więcej na temat zasad dostępu i sposobu [zabezpieczania dostępu do magazynu kluczy.](../key-vault/general/security-features.md)


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
