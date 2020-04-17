---
title: Korzystanie z odwołań do magazynu kluczy
description: Dowiedz się, jak skonfigurować usługę Azure App Service i usługi Azure Functions do korzystania z odwołań do usługi Azure Key Vault. Udostępnij kodowi aplikacji wpisy tajne usługi Key Vault.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: dd0a03ea76d517486bb9bda6d9628fb529166dd8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453731"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Korzystanie z odwołań do usługi App Service i usługi Azure

W tym temacie pokazano, jak pracować z wpisami tajnymi z usługi Azure Key Vault w usłudze app service lub aplikacji Usługi Azure bez konieczności żadnych zmian kodu. [Usługa Azure Key Vault](../key-vault/general/overview.md) to usługa zapewniająca scentralizowane zarządzanie wpisami tajnymi, z pełną kontrolą nad zasadami dostępu i historią inspekcji.

## <a name="granting-your-app-access-to-key-vault"></a>Przyznawanie aplikacji dostępu do usługi Key Vault

Aby odczytać wpisy tajne z usługi Key Vault, musisz utworzyć magazyn i udzielić aplikacji uprawnień dostępu do niego.

1. Utwórz magazyn kluczy, śledząc [szybki start usługi Key Vault](../key-vault/secrets/quick-create-cli.md).

1. Utwórz [tożsamości zarządzanej przypisanej przez system](overview-managed-identity.md) dla aplikacji.

   > [!NOTE] 
   > Odwołania do magazynu kluczy obsługują obecnie tylko tożsamości zarządzane przypisane przez system. Nie można używać tożsamości przypisanych przez użytkownika.

1. Utwórz [zasady dostępu w magazynie kluczy](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) dla tożsamości aplikacji utworzonej wcześniej. Włącz tajne uprawnienie "Pobierz" dla tej zasady. Nie należy konfigurować "autoryzowanej `applicationId` aplikacji" ani ustawień, ponieważ nie jest to zgodne z tożsamością zarządzaną.

    > [!NOTE]
    > Odwołania do magazynu kluczy nie są obecnie w stanie rozpoznać wpisów tajnych przechowywanych w magazynie kluczy z [ograniczeniami sieciowymi.](../key-vault/general/overview-vnet-service-endpoints.md)

## <a name="reference-syntax"></a>Składnia odwołania

Odwołanie do magazynu kluczy `@Microsoft.KeyVault({referenceString})`jest `{referenceString}` formularzem , gdzie jest zastępowany przez jedną z następujących opcji:

> [!div class="mx-tdBreakAll"]
> | Ciąg odwołania                                                            | Opis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | **SecretUri** powinien być pełnym identyfikatorem URI płaszczyzny danych tajnego w magazynie kluczy, w tym wersji, np.https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName =_vaultName_; SecretName =_secretName_; SecretVersion =_secretVersion_ | **VaultName** powinien nazwę zasobu magazynu kluczy. **SecretName** powinna być nazwa klucza tajnego docelowego. **SecretVersion** powinna być wersją klucza tajnego do użycia. |

Na przykład pełne odwołanie z Version będzie wyglądać następująco:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Inna możliwość:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Ustawienia aplikacji źródłowych z przechowalni kluczy

Odwołania do magazynu kluczy mogą być używane jako wartości [dla ustawień aplikacji,](configure-common.md#configure-app-settings)co pozwala zachować wpisy tajne w magazynie kluczy zamiast konfiguracji witryny. Ustawienia aplikacji są bezpiecznie szyfrowane w spoczynku, ale jeśli potrzebujesz tajnych funkcji zarządzania, powinny one przejść do usługi Key Vault.

Aby użyć odwołania do magazynu kluczy dla ustawienia aplikacji, ustaw odwołanie jako wartość ustawienia. Aplikacja może odwoływać się do klucza tajnego za pośrednictwem jego klucza jako normalne. Nie są wymagane żadne zmiany kodu.

> [!TIP]
> Większość ustawień aplikacji przy użyciu odwołań do usługi Key Vault powinna być oznaczona jako ustawienia gniazda, ponieważ powinny mieć oddzielne przechowalnia dla każdego środowiska.

### <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

Podczas automatyzacji wdrożeń zasobów za pośrednictwem szablonów usługi Azure Resource Manager, może być konieczne sekwencjonowanie zależności w określonej kolejności, aby ta funkcja działała. Należy zauważyć, że należy zdefiniować ustawienia aplikacji jako własny `siteConfig` zasób, a nie przy użyciu właściwości w definicji witryny. Dzieje się tak, ponieważ lokacja musi być najpierw zdefiniowana, aby tożsamość przypisana do systemu została utworzona z nią i może być używana w zasadach dostępu.

Przykładowy szablon psuedo dla aplikacji funkcyjnej może wyglądać następująco:

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
> W tym przykładzie wdrożenie kontroli źródła zależy od ustawień aplikacji. Jest to zwykle niebezpieczne zachowanie, ponieważ aktualizacja ustawienia aplikacji zachowuje się asynchronicznie. Jednak ponieważ uwzględniliśmy ustawienie `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplikacji, aktualizacja jest synchroniczowa. Oznacza to, że wdrożenie kontroli źródła rozpocznie się dopiero po pełnej aktualizacji ustawień aplikacji.

## <a name="troubleshooting-key-vault-references"></a>Rozwiązywanie problemów z odwołaniami do magazynu kluczy

Jeśli odwołanie nie zostanie poprawnie rozpoznane, zamiast tego zostanie użyta wartość referencyjna. Oznacza to, że w przypadku ustawień aplikacji zostanie `@Microsoft.KeyVault(...)` utworzona zmienna środowiskowa, której wartość ma składnię. Może to spowodować, że aplikacja do rzucania błędów, ponieważ spodziewał się tajemnicy określonej struktury.

Najczęściej jest to spowodowane błędną konfiguracją [zasad dostępu usługi Key Vault.](#granting-your-app-access-to-key-vault) Jednak może to być również ze względu na klucz tajny już nie istnieje lub błąd składni w samym odwołaniu.

Jeśli składnia jest poprawna, można wyświetlić inne przyczyny błędu, sprawdzając bieżący stan rozpoznawania w portalu. Przejdź do ustawień aplikacji i wybierz "Edytuj", aby uzyskać informacje na temat. Poniżej konfiguracji ustawień powinny być widoczne informacje o stanie, w tym wszelkie błędy. Brak tych oznacza, że składnia odwołania jest nieprawidłowa.

Możesz również użyć jednego z wbudowanych detektorów, aby uzyskać dodatkowe informacje.

### <a name="using-the-detector-for-app-service"></a>Korzystanie z detektora dla usługi app service

1. W portalu przejdź do aplikacji.
2. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
3. Wybierz **pozycję Dostępność i wydajność** i wybierz pozycję W dół aplikacji sieci **Web.**
4. Znajdź **diagnostykę ustawień aplikacji magazynu kluczy** i kliknij przycisk **Więcej informacji**.


### <a name="using-the-detector-for-azure-functions"></a>Korzystanie z detektora dla funkcji platformy Azure

1. W portalu przejdź do aplikacji.
2. Przejdź do **funkcji platformy.**
3. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
4. Wybierz **pozycję Dostępność i wydajność** i wybierz pozycję Aplikacja funkcja w dół lub błędy **raportowania.**
5. Kliknij **diagnostykę ustawień aplikacji magazynu kluczy.**
