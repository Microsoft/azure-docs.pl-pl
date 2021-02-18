---
title: Używanie odwołań do usługi Key Vault
description: Dowiedz się, jak skonfigurować Azure App Service i Azure Functions do korzystania z Azure Key Vault odwołań. Udostępnienie Key Vault wpisów tajnych dla kodu aplikacji.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 69fc0d6f3c4e18b34555a099f4e28e278ca3bdad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635391"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Użyj Key Vault odwołań dla App Service i Azure Functions

W tym temacie przedstawiono sposób pracy z wpisami tajnymi Azure Key Vault w App Service lub Azure Functions aplikacji bez konieczności wprowadzania jakichkolwiek zmian w kodzie. [Azure Key Vault](../key-vault/general/overview.md) to usługa zapewniająca scentralizowane zarządzanie kluczami tajnymi z pełną kontrolą nad zasadami dostępu i historią inspekcji.

## <a name="granting-your-app-access-to-key-vault"></a>Udzielanie aplikacji dostępu do Key Vault

Aby można było odczytać wpisy tajne z Key Vault, należy utworzyć magazyn i nadać aplikacji uprawnienia dostępu do niej.

1. Utwórz magazyn kluczy, postępując zgodnie z [przewodnikiem Szybki start Key Vault](../key-vault/secrets/quick-create-cli.md).

1. Utwórz [skojarzoną z systemem tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji.

   > [!NOTE] 
   > Odwołania Key Vault obecnie obsługują tylko tożsamości zarządzane przypisane do systemu. Nie można używać tożsamości przypisanych do użytkownika.

1. Utwórz [zasady dostępu w Key Vault](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) dla utworzonej wcześniej tożsamości aplikacji. Włącz uprawnienie "Pobieranie" klucza tajnego dla tych zasad. Nie należy konfigurować "autoryzowanej aplikacji" ani `applicationId` ustawień, ponieważ nie są one zgodne z zarządzaną tożsamością.

   > [!IMPORTANT]
   > Odwołania Key Vault nie są obecnie dostępne do rozpoznawania wpisów tajnych przechowywanych w magazynie kluczy z [ograniczeniami sieci](../key-vault/general/overview-vnet-service-endpoints.md) , chyba że aplikacja jest hostowana w [App Service Environment](./environment/intro.md).

## <a name="reference-syntax"></a>Składnia odwołania

Odwołanie Key Vault ma postać `@Microsoft.KeyVault({referenceString})` , gdzie `{referenceString}` jest zastępowana jedną z następujących opcji:

> [!div class="mx-tdBreakAll"]
> | Ciąg odwołania                                                            | Opis                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_SecretUri_                                                       | **SecretUri** powinien być pełnym identyfikatorem URI płaszczyzny danych dla wpisu tajnego w Key Vault, opcjonalnie, łącznie z wersją, np. `https://myvault.vault.azure.net/secrets/mysecret/` lub`https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | Magazynname =_magazynname_; Secretname =_secretname_; Wersjawpisutajnego =_wersjawpisutajnego_ | Nazwa **magazynu** jest wymagana i powinna być nazwą zasobu Key Vault. **Wpis tajny** jest wymagany i powinien być nazwą docelowego klucza tajnego. **Wersjawpisutajnego** jest opcjonalne, ale jeśli jest obecny, wskazuje wersję klucza tajnego do użycia. |

Na przykład kompletne odwołanie będzie wyglądać następująco:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Inna możliwość:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Wymiana

Jeśli w odwołaniu nie określono wersji, aplikacja będzie używać najnowszej wersji, która istnieje w Key Vault. Gdy staną się dostępne nowsze wersje, na przykład w przypadku zdarzenia rotacji, aplikacja zostanie automatycznie zaktualizowana i rozpocznie korzystanie z najnowszej wersji w ciągu jednego dnia. Wszystkie zmiany konfiguracji wprowadzone w aplikacji spowodują natychmiastowe zaktualizowanie najnowszych wersji wszystkich wpisów tajnych, do których się odwołuje.

## <a name="source-application-settings-from-key-vault"></a>Ustawienia aplikacji źródłowej z Key Vault

Odwołania Key Vault mogą być używane jako wartości [ustawień aplikacji](configure-common.md#configure-app-settings), co pozwala zachować wpisy tajne w Key Vault zamiast konfiguracji lokacji. Ustawienia aplikacji są bezpiecznie szyfrowane w stanie spoczynku, ale jeśli potrzebujesz funkcji tajnego zarządzania, należy przejść do Key Vault.

Aby użyć odwołania Key Vault dla ustawienia aplikacji, należy ustawić odwołanie jako wartość ustawienia. Twoja aplikacja może odwoływać się do klucza tajnego za pomocą jego klucza jako normalnego. Nie są wymagane żadne zmiany w kodzie.

> [!TIP]
> Większość ustawień aplikacji używających odwołań Key Vault powinna być oznaczona jako ustawienia miejsca, ponieważ dla każdego środowiska należy mieć oddzielne magazyny.

### <a name="azure-resource-manager-deployment"></a>Wdrożenie usługi Azure Resource Manager

W przypadku automatyzowania wdrożeń zasobów za pomocą szablonów Azure Resource Manager może być konieczne sekwencjonowanie zależności w określonej kolejności, aby ta funkcja działała. Pamiętaj, że musisz zdefiniować ustawienia aplikacji jako własny zasób zamiast używać `siteConfig` właściwości w definicji lokacji. Wynika to z faktu, że lokacja musi być zdefiniowana jako pierwsza, aby można było utworzyć tożsamość przypisaną do systemu i użyć jej w zasadach dostępu.

Przykładowy szablon aplikacji funkcji może wyglądać następująco:

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
> W tym przykładzie wdrożenie kontroli źródła zależy od ustawień aplikacji. Jest to zwykle niebezpieczne zachowanie, ponieważ aktualizacja ustawienia aplikacji zachowuje się asynchronicznie. Jednak ze względu na to, że zostało dołączone `WEBSITE_ENABLE_SYNC_UPDATE_SITE` ustawienie aplikacji, aktualizacja jest synchroniczna. Oznacza to, że wdrożenie kontroli źródła rozpocznie się dopiero po całkowitym zaktualizowaniu ustawień aplikacji.

## <a name="troubleshooting-key-vault-references"></a>Rozwiązywanie problemów dotyczących Key Vault

Jeśli odwołanie nie zostanie prawidłowo rozwiązane, zamiast tego zostanie użyta wartość referencyjna. Oznacza to, że dla ustawień aplikacji zostanie utworzona zmienna środowiskowa, której wartość ma `@Microsoft.KeyVault(...)` składnię. Może to spowodować, że aplikacja zgłosi błędy, ponieważ oczekuje ona wpisu tajnego określonej struktury.

Najczęściej jest to spowodowane niepoprawną konfiguracją [zasad dostępu Key Vault](#granting-your-app-access-to-key-vault). Jednak może być również przyczyną, że wpis tajny nie jest już istniejący lub błąd składniowy w samej odwołaniu.

Jeśli składnia jest poprawna, można wyświetlić inne przyczyny błędu, sprawdzając bieżący stan rozwiązania w portalu. Przejdź do ustawień aplikacji i wybierz pozycję "Edytuj" dla odnośnego odwołania. Na stronie Konfiguracja ustawienia powinny być widoczne informacje o stanie, w tym wszelkie błędy. Brak tych informacji oznacza, że Składnia odwołania jest nieprawidłowa.

Możesz również użyć jednego z wbudowanych detektorów, aby uzyskać dodatkowe informacje.

### <a name="using-the-detector-for-app-service"></a>Używanie narzędzia wykrywania dla App Service

1. W portalu przejdź do swojej aplikacji.
2. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
3. Wybierz pozycję **dostępność i wydajność** , a następnie wybierz pozycję **aplikacja internetowa.**
4. Znajdź **Key Vault Diagnostyka ustawień aplikacji** i kliknij pozycję **więcej informacji**.


### <a name="using-the-detector-for-azure-functions"></a>Używanie narzędzia wykrywania dla Azure Functions

1. W portalu przejdź do swojej aplikacji.
2. Przejdź do **opcji platformy.**
3. Kliknij pozycję **Diagnozowanie i rozwiązywanie problemów**.
4. Wybierz opcję **dostępność i wydajność** , a następnie wybierz pozycję **aplikacja funkcjonalna lub raportowanie błędów.**
5. Kliknij pozycję **Key Vault Diagnostyka ustawień aplikacji.**
