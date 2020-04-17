---
title: Konfigurowanie własnego klucza do szyfrowania danych usługi Azure Service Bus w stanie spoczynku
description: Ten artykuł zawiera informacje dotyczące konfigurowania własnego klucza do szyfrowania obsługi danych usługi Azure Service Bus.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: 82a5fbef8c307d60d82b147f04a2a687b8b0433e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459070"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych usługi Azure Service Bus w stanie spoczynku przy użyciu witryny Azure portal
Usługa Azure Service Bus Premium zapewnia szyfrowanie danych w spoczynku za pomocą szyfrowania usługi Azure Storage Service (Azure SSE). Usługa Service Bus Premium korzysta z usługi Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="overview"></a>Omówienie
Usługa Azure Service Bus obsługuje teraz opcję szyfrowania danych w spoczynku za pomocą kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta (Bring Your Own Key — BYOK). ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania usługi Azure Service Bus w spoczynku.

Włączenie funkcji BYOK jest procesem jednorazowej konfiguracji w obszarze nazw.

> [!NOTE]
> Istnieją pewne zastrzeżenia do klucza zarządzanego przez klienta dla szyfrowania po stronie usługi. 
>   * Ta funkcja jest obsługiwana przez warstwę [Usługi Azure Service Bus Premium.](service-bus-premium-messaging.md) Nie można włączyć dla obszarów nazw usługi Service Bus warstwy standardowej.
>   * Szyfrowanie można włączyć tylko dla nowych lub pustych obszarów nazw. Jeśli obszar nazw zawiera dane, operacja szyfrowania zakończy się niepowodzeniem.

Za pomocą usługi Azure Key Vault można zarządzać kluczami i kontrolować użycie klucza. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy za pomocą kluczy zarządzanych przez klienta przy użyciu witryny Azure portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą witryny Azure portal, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> Przy użyciu kluczy zarządzanych przez klienta z usługi Azure Service Bus wymaga, że magazyn kluczy mają dwie wymagane właściwości skonfigurowane. Są to: **Soft Delete** i **Nie czyścić**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w witrynie Azure portal. Jednak jeśli chcesz włączyć te właściwości w istniejącym magazynie kluczy, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta
Aby włączyć klucze zarządzane przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do obszaru nazw Usługi Service Bus Premium.
2. Na stronie **Ustawienia** obszaru nazw usługi Service Bus wybierz pozycję **Szyfrowanie**.
3. Wybierz **szyfrowanie klucza zarządzanego przez klienta w spoczynku,** jak pokazano na poniższej ilustracji.

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami

Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany klienta z obszarem nazw usługi Azure Service Bus. Usługa Service Bus obsługuje tylko usługę Azure Key Vault. Jeśli **włączysz opcję Szyfrowanie z kluczem zarządzanym przez klienta** w poprzedniej sekcji, musisz zaimportować klucz do usługi Azure Key Vault. Ponadto klucze muszą być skonfigurowane do **usuwania nietrwałego** i **nie czyścić** dla tego klucza. Te ustawienia można skonfigurować za pomocą programu [PowerShell](../key-vault/general/soft-delete-powershell.md) lub [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Aby utworzyć nową przechowalnię kluczy, postępuj zgodnie z [programem Szybki start](../key-vault/general/overview.md)usługi Azure Key Vault . Aby uzyskać więcej informacji na temat importowania istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/about-keys-secrets-and-certificates.md).
1. Aby włączyć zarówno ochronę usuwania nietrwałego, jak i oczyszczania podczas tworzenia przechowalni, użyj polecenia [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczaniem do istniejącego magazynu (który ma już włączone usuwanie nietrwałe), użyj polecenia [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klawisze, wykonując następujące kroki:
    1. Aby utworzyć nowy klucz, wybierz **polecenie Generuj/Zaimportuj** z menu **Klawisze** w obszarze **Ustawienia**.
        
        ![Wybierz przycisk Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ustaw **opcje,** aby **wygenerować** i nadać kluczowi nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 

    1. Teraz można wybrać ten klucz do skojarzenia z obszarem nazw usługi Service Bus do szyfrowania z listy rozwijanej. 

        ![Wybieranie klucza z przechowalni kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Aby uzyskać nadmiarowość, można dodać maksymalnie 3 klucze. W przypadku, gdy jeden z kluczy wygasł lub nie jest dostępny, inne klucze będą używane do szyfrowania.
        
    1. Wypełnij szczegóły klucza i kliknij przycisk **Wybierz**. Umożliwi to szyfrowanie danych w pozostałej części obszaru nazw za pomocą klucza zarządzanego przez klienta. 


    > [!IMPORTANT]
    > Jeśli chcesz użyć klucza zarządzanego przez Klienta wraz z odzyskiwaniem po awarii geograficznej, zapoznaj się z poniższymi 
    >
    > Aby włączyć szyfrowanie w spoczynku za pomocą klucza zarządzanego przez klienta, zasady [dostępu](../key-vault/general/secure-your-key-vault.md) są konfiguracyjne dla tożsamości zarządzanej usługi Service Bus na określonym usłudze Azure KeyVault. Zapewnia to kontrolowany dostęp do usługi Azure KeyVault z obszaru nazw usługi Azure Service Bus.
    >
    > W związku z tym:
    > 
    >   * Jeśli [odzyskiwanie po awarii geograficznej](service-bus-geo-dr.md) jest już włączone dla obszaru nazw usługi Service Bus i chcesz włączyć klucz zarządzany przez klienta, 
    >     * Przerwij parowanie
    >     * [Skonfiguruj zasady dostępu](../key-vault/general/managed-identity.md) dla tożsamości zarządzanej zarówno dla podstawowych, jak i pomocniczych obszarów nazw do magazynu kluczy.
    >     * Konfigurowanie szyfrowania w głównym obszarze nazw.
    >     * Ponownie sparuj podstawowe i pomocnicze przestrzenie nazw.
    > 
    >   * Jeśli chcesz włączyć funkcję Geo-DR w obszarze nazw usługi Service Bus, w którym klucz zarządzany przez klienta jest już skonfigurowany,
    >     * [Skonfiguruj zasady dostępu](../key-vault/general/managed-identity.md) dla tożsamości zarządzanej pomocniczego obszaru nazw do magazynu kluczy.
    >     * Sparuj podstawowe i pomocnicze przestrzenie nazw.


## <a name="rotate-your-encryption-keys"></a>Obracanie kluczy szyfrowania

Klucz można obracać w magazynie kluczy przy użyciu mechanizmu rotacji usługi Azure Key Vaults. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rotacji kluczy i inspekcji](../key-vault/secrets/key-rotation-log-monitoring.md). Daty aktywacji i wygaśnięcia można również ustawić tak, aby automatyzować rotację kluczy. Usługa Service Bus wykryje nowe wersje kluczy i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Cofanie dostępu do kluczy

Cofnięcie dostępu do kluczy szyfrowania nie przeczyści danych z usługi Service Bus. Jednak nie można uzyskać dostępu do danych z obszaru nazw usługi Service Bus. Klucz szyfrowania można odwołać za pomocą zasad dostępu lub usunięcia klucza. Dowiedz się więcej o zasadach dostępu i zabezpieczaniu magazynu kluczy z [bezpiecznego dostępu do magazynu kluczy.](../key-vault/general/secure-your-key-vault.md)

Po odwołaniu klucza szyfrowania usługa Service Bus w zaszyfrowanym obszarze nazw stanie się niesprawna. Jeśli dostęp do klucza jest włączony lub klucz usunięty zostanie przywrócony, usługa Service Bus wybierze klucz, dzięki czemu można uzyskać dostęp do danych z zaszyfrowanego obszaru nazw usługi Service Bus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Włączanie szyfrowania za pomocą szablonu Menedżera zasobów
W tej sekcji pokazano, jak wykonać następujące zadania przy użyciu **szablonów usługi Azure Resource Manager**. 

1. Utwórz obszar nazw usługi Service Bus **premium** z **tożsamością usługi zarządzanej**.
2. Utwórz **magazyn kluczy** i udziel dostępu tożsamości usługi do magazynu kluczy. 
3. Zaktualizuj obszar nazw usługi Service Bus za pomocą informacji o przechowalni kluczy (klucz/wartość). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Tworzenie obszaru nazw usługi Premium Service Bus z tożsamością usługi zarządzanej
W tej sekcji pokazano, jak utworzyć obszar nazw usługi Azure Service Bus z tożsamością usługi zarządzanej przy użyciu szablonu usługi Azure Resource Manager i programu PowerShell. 

1. Utwórz szablon usługi Azure Resource Manager, aby utworzyć obszar nazw warstwy premium usługi Service Bus z tożsamością usługi zarządzanej. Nazwij plik: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Utwórz plik parametru szablonu o nazwie: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>`- Nazwa obszaru nazw usługi Service Bus
    > - `<Location>`- Lokalizacja obszaru nazw usługi Service Bus

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia obszaru nazw usługi Service Bus w warstwie Premium. Następnie pobierz identyfikator obszaru nazw usługi Service Bus, aby użyć go później. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Dostęp do tożsamości obszaru nazw usługi Grant Service Bus do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z **włączoną ochroną przed przeczyszczaniem** i **usuwaniem nietrwałym.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (LUB)
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy**. Przed uruchomieniem polecenia należy określić wartości nazw grup zasobów i magazynów kluczy. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu do magazynu kluczy, tak aby zarządzana tożsamość obszaru nazw usługi Service Bus mogła uzyskiwać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora obszaru nazw usługi Service Bus z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Szyfrowanie danych w obszarze nazw usługi Service Bus za pomocą klucza zarządzanego przez klienta z magazynu kluczy
Do tej pory wykonaliście następujące czynności: 

1. Utworzono obszar nazw premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznano dostęp do tożsamości zarządzanej do magazynu kluczy. 

W tym kroku zaktualizujesz obszar nazw usługi Service Bus z informacjami o magazynie kluczy. 

1. Utwórz plik JSON o nazwie **UpdateServiceBusNamespaceWithEncryption.json** z następującą zawartością: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Tworzenie pliku parametru szablonu: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>`- Nazwa obszaru nazw usługi Service Bus
    > - `<Location>`- Lokalizacja obszaru nazw usługi Service Bus
    > - `<KeyVaultName>`- Nazwa magazynu kluczy
    > - `<KeyName>`- Nazwa klucza w magazynie kluczy  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon Menedżera zasobów. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Omówienie usługi Service Bus](service-bus-messaging-overview.md)
- [Omówienie przechowalni kluczy](../key-vault/general/overview.md)


