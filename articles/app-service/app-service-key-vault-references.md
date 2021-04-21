---
title: Używanie odwołań do usługi Key Vault
description: Dowiedz się, jak skonfigurować Azure App Service i Azure Functions do używania Azure Key Vault odwołań. Udostępnij Key Vault tajne w kodzie aplikacji.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e6f46b205790d81a3e76d2aafbcf7e13dbb5afd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815220"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Używanie odwołań Key Vault dla App Service i Azure Functions

W tym temacie opisano sposób pracy z wpisami tajnymi z Azure Key Vault aplikacji App Service lub Azure Functions bez konieczności zmiany kodu. [Azure Key Vault](../key-vault/general/overview.md) to usługa, która zapewnia scentralizowane zarządzanie wpisami tajnymi, z pełną kontrolą nad zasadami dostępu i historią inspekcji.

## <a name="granting-your-app-access-to-key-vault"></a>Udzielanie aplikacji dostępu do Key Vault

Aby odczytywać wpisy tajne z Key Vault, musisz utworzyć magazyn i nadać aplikacji uprawnienia dostępu do niego.

1. Utwórz magazyn kluczy, korzystając z Key Vault [Szybki start.](../key-vault/secrets/quick-create-cli.md)

1. Utwórz tożsamość [zarządzaną przypisaną przez](overview-managed-identity.md) system dla aplikacji.

   > [!NOTE] 
   > Key Vault aktualnie obsługują tylko tożsamości zarządzane przypisane przez system. Nie można używać tożsamości przypisanych przez użytkownika.

1. Utwórz zasady [dostępu w Key Vault](../key-vault/general/security-features.md#privileged-access) dla utworzonej wcześniej tożsamości aplikacji. Włącz uprawnienie "Pobierz" klucz tajny dla tych zasad. Nie należy konfigurować "autoryzowanej aplikacji" ani ustawień, ponieważ nie jest to `applicationId` zgodne z tożsamością zarządzaną.

### <a name="access-network-restricted-vaults"></a>Uzyskiwanie dostępu do magazynów z ograniczeniami sieci

> [!NOTE]
> Aplikacje oparte na systemie Linux nie są obecnie w stanie rozpoznać wpisów tajnych z sieciowego magazynu kluczy, chyba że aplikacja jest hostowana w [App Service Environment.](./environment/intro.md)

Jeśli magazyn jest skonfigurowany z ograniczeniami [sieci,](../key-vault/general/overview-vnet-service-endpoints.md)należy również upewnić się, że aplikacja ma dostęp do sieci.

1. Upewnij się, że aplikacja ma skonfigurowane możliwości sieciowego ruchu wychodzącego, zgodnie z opisem w te App Service [i](./networking-features.md) Azure Functions [sieci.](../azure-functions/functions-networking-options.md)

2. Upewnij się, że konfiguracja magazynu odpowiada sieci lub podsieci, za pośrednictwem której aplikacja będzie mieć do niego dostęp.

> [!IMPORTANT]
> Uzyskiwanie dostępu do magazynu za pośrednictwem integracji z siecią wirtualną jest obecnie niezgodne z automatycznymi aktualizacjami wpisów tajnych [bez określonej wersji](#rotation).

## <a name="reference-syntax"></a>Składnia referencyjna

Odwołanie Key Vault ma postać , gdzie jest `@Microsoft.KeyVault({referenceString})` `{referenceString}` zastępowany przez jedną z następujących opcji:

> [!div class="mx-tdBreakAll"]
> | Ciąg odwołania                                                            | Opis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | **SecretUri** powinien być pełnym adresem URI płaszczyzny danych dla tajnego Key Vault, opcjonalnie z uwzględnieniem wersji, np. `https://myvault.vault.azure.net/secrets/mysecret/` lub`https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName =_vaultName_; SecretName =_secretName_; SecretVersion =_secretVersion_ | Nazwa **magazynu jest** wymagana i powinna mieć nazwę zasobu Key Vault magazynu. **SecretName** jest wymagany i powinien być nazwą docelowego tajnego. SecretVersion **jest** opcjonalny, ale jeśli istnieje wskazuje wersję tajnego do użycia. |

Na przykład kompletne odwołanie wyglądałoby następująco:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Inna możliwość:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Wymiana

> [!IMPORTANT]
> [Uzyskiwanie dostępu do magazynu za pośrednictwem integracji z siecią](#access-network-restricted-vaults) wirtualną jest obecnie niezgodne z automatycznymi aktualizacjami wpisów tajnych bez określonej wersji.

Jeśli wersja nie jest określona w odwołani, aplikacja będzie używać najnowszej wersji, która istnieje w Key Vault. Gdy staną się dostępne nowsze wersje, takie jak zdarzenie rotacji, aplikacja zostanie automatycznie zaktualizowana i rozpocznie korzystanie z najnowszej wersji w ciągu jednego dnia. Wszelkie zmiany konfiguracji wprowadzone w aplikacji powodują natychmiastową aktualizację do najnowszych wersji wszystkich przywołynych wpisów tajnych.

## <a name="source-application-settings-from-key-vault"></a>Ustawienia aplikacji źródłowej z Key Vault

Key Vault odwołań mogą być używane jako wartości ustawień [aplikacji,](configure-common.md#configure-app-settings)co umożliwia zachowanie wpisów tajnych Key Vault, a nie konfiguracji lokacji. Ustawienia aplikacji są bezpiecznie szyfrowane w spoczynku, ale jeśli potrzebujesz możliwości zarządzania kluczami tajnymi, powinny one przejść do Key Vault.

Aby użyć Key Vault dla ustawienia aplikacji, ustaw odwołanie jako wartość ustawienia. Aplikacja może odwoływać się do klucza tajnego za pośrednictwem jego klucza w zwykły sposób. Nie są wymagane żadne zmiany kodu.

> [!TIP]
> Większość ustawień aplikacji korzystających Key Vault odwołań powinna być oznaczona jako ustawienia miejsca, ponieważ dla każdego środowiska powinny być oddzielne magazyny.

### <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

W przypadku automatyzowania wdrożeń zasobów za Azure Resource Manager szablonów może być konieczne sekwencji zależności w określonej kolejności, aby ta funkcja działała. Należy pamiętać, że ustawienia aplikacji należy zdefiniować jako własny zasób, a nie za pomocą właściwości `siteConfig` w definicji witryny. Jest to spowodowane tym, że lokację należy najpierw zdefiniować, aby tożsamość przypisana przez system została utworzona za jej pomocą i można było jej użyć w zasadach dostępu.

Przykładowy przykładowy szablon aplikacji funkcji może wyglądać następująco:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> W tym przykładzie wdrożenie kontroli źródła zależy od ustawień aplikacji. Jest to zwykle niebezpieczne zachowanie, ponieważ aktualizacja ustawienia aplikacji zachowuje się asynchronicznie. Jednak ponieważ uwzględniono ustawienie `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplikacji, aktualizacja jest synchroniczna. Oznacza to, że wdrożenie kontroli źródła rozpocznie się dopiero po pełnej aktualizacji ustawień aplikacji.

## <a name="troubleshooting-key-vault-references"></a>Rozwiązywanie problemów z odwołaniami Key Vault danych

Jeśli odwołanie nie zostanie prawidłowo rozpoznane, zamiast tego zostanie użyta wartość odwołania. Oznacza to, że dla ustawień aplikacji zostanie utworzona zmienna środowiskowa, której wartość ma `@Microsoft.KeyVault(...)` składnię. Może to spowodować zgłaszanie błędów przez aplikację, ponieważ oczekuje ona tajnego działania określonej struktury.

Najczęściej jest to spowodowane błędną konfiguracją zasad Key Vault [dostępu.](#granting-your-app-access-to-key-vault) Jednak może to być również spowodowane tym, że klucz tajny już nie istnieje lub błąd składniowy w samym odwołaniu.

Jeśli składnia jest poprawna, możesz wyświetlić inne przyczyny błędu, sprawdzając bieżący stan rozwiązania w portalu. Przejdź do ustawień aplikacji i wybierz pozycję "Edytuj" dla odpowiedniego odwołania. Poniżej konfiguracji ustawień powinny zostać wyświetlony informacje o stanie, w tym o wszelkich błędach. Brak tych implikuje, że składnia odwołania jest nieprawidłowa.

Możesz również użyć jednego z wbudowanych detektorów, aby uzyskać dodatkowe informacje.

### <a name="using-the-detector-for-app-service"></a>Korzystanie z detektora dla App Service

1. W portalu przejdź do swojej aplikacji.
2. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
3. Wybierz **pozycję Dostępność i wydajność,** a następnie wybierz pozycję Aplikacja internetowa nie **działa.**
4. Znajdź **Key Vault diagnostyki ustawień aplikacji i** kliknij pozycję Więcej **informacji.**


### <a name="using-the-detector-for-azure-functions"></a>Korzystanie z narzędzia do wykrywania Azure Functions

1. W portalu przejdź do swojej aplikacji.
2. Przejdź do **funkcji platformy.**
3. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
4. Wybierz **pozycję Dostępność i wydajność,** a następnie wybierz pozycję Aplikacja funkcji nie działa lub zgłasza **błędy.**
5. Kliknij pozycję **Key Vault diagnostyki ustawień aplikacji.**
