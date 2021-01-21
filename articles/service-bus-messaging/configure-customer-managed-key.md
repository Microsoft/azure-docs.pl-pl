---
title: Skonfiguruj własny klucz szyfrowania danych Azure Service Bus przechowywanych w spoczynku
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania własnego klucza do szyfrowania danych Azure Service Bus Rest.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3e8f3a599ee5fe40c85a93dd58d36e6cd611c9ea
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631770"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania Azure Service Bus danych przechowywanych przy użyciu Azure Portal
Azure Service Bus Premium zapewnia szyfrowanie danych przechowywanych przy użyciu usługi Azure szyfrowanie usługi Storage (SSE platformy Azure). Service Bus Premium bazuje na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="overview"></a>Omówienie
Azure Service Bus teraz obsługuje opcję szyfrowania danych przechowywanych przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta (Bring Your Own Key-BYOK). Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania Azure Service Bus w stanie spoczynku.

Włączenie funkcji BYOK to jednorazowy proces konfiguracji w przestrzeni nazw.

> [!NOTE]
> Klucz zarządzany przez klienta ma pewne zastrzeżenia dotyczące szyfrowania po stronie usługi. 
>   * Ta funkcja jest obsługiwana przez [Azure Service Bus warstwy Premium](service-bus-premium-messaging.md) . Nie można jej włączyć dla Service Bus przestrzeni nazw w warstwie Standardowa.
>   * Szyfrowanie można włączyć tylko dla nowych lub pustych przestrzeni nazw. Jeśli przestrzeń nazw zawiera jakiekolwiek kolejki lub tematy, operacja szyfrowania zakończy się niepowodzeniem.

Za pomocą Azure Key Vault można zarządzać kluczami i przeprowadzać inspekcję użycia klucza. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: tworzenie Azure Key Vault przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Użycie kluczy zarządzanych przez klienta w programie Azure Service Bus wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to:  **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do przestrzeni nazw w warstwie Premium Service Bus.
2. Na stronie **Ustawienia** obszaru nazw Service Bus wybierz pozycję **szyfrowanie**.
3. Wybierz **szyfrowanie klucza zarządzanego przez klienta** , jak pokazano na poniższej ilustracji.

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami

Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany klienta z przestrzenią nazw Azure Service Bus. Service Bus obsługuje tylko Azure Key Vault. Po włączeniu opcji **szyfrowania z kluczem zarządzanym przez klienta** w poprzedniej sekcji należy zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć **nietrwałe usuwanie** i nie można ich **czyścić** skonfigurować dla klucza. Te ustawienia można skonfigurować przy użyciu [programu PowerShell](../key-vault/general/key-vault-recovery.md) lub [interfejsu wiersza polecenia](../key-vault/general/key-vault-recovery.md).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [przewodnika Szybki Start](../key-vault/general/overview.md). Aby uzyskać więcej informacji o importowaniu istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/general/about-keys-secrets-certificates.md).
1. Aby włączyć zarówno nietrwałe usuwanie, jak i przeczyszczanie, podczas tworzenia magazynu należy użyć polecenia [AZ Create webmagazynion](/cli/azure/keyvault#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczeniem do istniejącego magazynu (ma już włączony nietrwałe usuwanie), użyj polecenia [AZ webmagazyn Update](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, wykonując następujące czynności:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.
        
        ![Przycisk Wybierz opcję Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 

    1. Teraz możesz wybrać ten klucz do skojarzenia z przestrzenią nazw Service Bus do szyfrowania z listy rozwijanej. 

        ![Wybierz klucz z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Aby zapewnić nadmiarowość, można dodać maksymalnie 3 klucze. W przypadku, gdy jeden z kluczy wygasł lub jest niedostępny, inne klucze będą używane do szyfrowania.
        
    1. Wprowadź szczegóły klucza i kliknij przycisk **Wybierz**. Umożliwi to szyfrowanie danych przechowywanych w przestrzeni nazw za pomocą klucza zarządzanego przez klienta. 


    > [!IMPORTANT]
    > Jeśli zamierzasz używać klucza zarządzanego przez klienta wraz z odzyskiwaniem po awarii geograficznej, zapoznaj się z poniższymi tematami. 
    >
    > Aby włączyć szyfrowanie w spoczynku z kluczem zarządzanym przez klienta, skonfigurowano [zasady dostępu](../key-vault/general/secure-your-key-vault.md) dla tożsamości zarządzanej Service Bus w określonym magazynie kluczy platformy Azure. Zapewnia to kontrolowany dostęp do magazynu kluczy platformy Azure z przestrzeni nazw Azure Service Bus.
    >
    > Z tego powodu:
    > 
    >   * Jeśli funkcja [odzyskiwania po awarii geograficznej](service-bus-geo-dr.md) jest już włączona dla przestrzeni nazw Service Bus i chcesz włączyć klucz zarządzany przez klienta, 
    >     * Przerwij parowanie
    >     * [Skonfiguruj zasady dostępu](../key-vault/general/assign-access-policy-portal.md) dla tożsamości zarządzanej zarówno dla głównych, jak i pomocniczych przestrzeni nazw dla magazynu kluczy.
    >     * Skonfiguruj szyfrowanie dla podstawowej przestrzeni nazw.
    >     * Ponowne parowanie podstawowych i pomocniczych przestrzeni nazw.
    > 
    >   * Jeśli chcesz włączyć lokalizację geograficzną w Service Bus przestrzeni nazw, w której klucz zarządzany przez klienta został już skonfigurowany, a następnie —
    >     * [Skonfiguruj zasady dostępu](../key-vault/general/assign-access-policy-portal.md) dla zarządzanej tożsamości dla pomocniczej przestrzeni nazw dla magazynu kluczy.
    >     * Parowanie podstawowych i pomocniczych przestrzeni nazw.


## <a name="rotate-your-encryption-keys"></a>Obróć klucze szyfrowania

Możesz obrócić klucz w magazynie kluczy przy użyciu mechanizmu rotacji magazynów kluczy platformy Azure. Daty aktywacji i wygaśnięcia można także skonfigurować w celu zautomatyzowania rotacji kluczy. Usługa Service Bus wykryje nowe wersje klucza i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Odwołaj dostęp do kluczy

Cofnięcie dostępu do kluczy szyfrowania nie spowoduje przeczyszczenia danych z Service Bus. Nie można jednak uzyskać dostępu do danych z przestrzeni nazw Service Bus. Możesz odwołać klucz szyfrowania za pomocą zasad dostępu lub usunąć klucz. Dowiedz się więcej na temat zasad dostępu i zabezpieczania magazynu kluczy, [Aby uzyskać bezpieczny dostęp do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

Po odwołaniu klucza szyfrowania Usługa Service Bus w zaszyfrowanej przestrzeni nazw stanie się niezależna. Jeśli dostęp do klucza jest włączony lub przywrócono usunięty klucz, Usługa Service Bus wybierze klucz, aby można było uzyskać dostęp do danych z przestrzeni nazw zaszyfrowanej Service Bus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Użyj szablonu Menedżer zasobów, aby włączyć szyfrowanie
W tej sekcji przedstawiono sposób wykonywania następujących zadań przy użyciu **szablonów Azure Resource Manager**. 

1. Utwórz przestrzeń nazw Service Bus **Premium** przy użyciu **tożsamości usługi zarządzanej**.
2. Utwórz **Magazyn kluczy** i przyznaj tożsamości usługi dostęp do magazynu kluczy. 
3. Zaktualizuj przestrzeń nazw Service Bus przy użyciu informacji o magazynie kluczy (klucz/wartość). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Tworzenie przestrzeni nazw Service Bus Premium przy użyciu tożsamości usługi zarządzanej
W tej sekcji przedstawiono sposób tworzenia Azure Service Bus przestrzeni nazw z tożsamością usługi zarządzanej przy użyciu szablonu Azure Resource Manager i programu PowerShell. 

1. Utwórz szablon Azure Resource Manager, aby utworzyć przestrzeń nazw warstwy Premium Service Bus z tożsamością usługi zarządzanej. Nazwij plik: **CreateServiceBusPremiumNamespace.jsna**: 

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
2. Utwórz plik parametrów szablonu o nazwie: **CreateServiceBusPremiumNamespaceParams.jsna**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>` -Nazwa przestrzeni nazw Service Bus
    > - `<Location>` — Lokalizacja przestrzeni nazw Service Bus

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia przestrzeni nazw w warstwie Premium Service Bus. Następnie Pobierz identyfikator przestrzeni nazw Service Bus, aby użyć jej później. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Udzielanie tożsamości przestrzeni nazw Service Bus dostępu do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z **ochroną przed przeczyszczaniem** i włączonym **usuwaniem nietrwałego** . 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    ORAZ
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy**. Określ wartości dla grup zasobów i nazw magazynów kluczy przed uruchomieniem polecenia. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu magazynu kluczy, tak aby zarządzana tożsamość przestrzeni nazw Service Bus mogła uzyskać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora przestrzeni nazw Service Bus z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Szyfruj dane w Service Bus przestrzeni nazw z kluczem zarządzanym przez klienta z magazynu kluczy
Wykonano następujące kroki: 

1. Utworzono przestrzeń nazw Premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznaj zarządzanej tożsamości dostęp do magazynu kluczy. 

W tym kroku zostanie zaktualizowana przestrzeń nazw Service Bus przy użyciu informacji o magazynie kluczy. 

1. Utwórz plik JSON o nazwie **UpdateServiceBusNamespaceWithEncryption.js** przy użyciu następującej zawartości: 

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

2. Utwórz plik parametrów szablonu: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>` -Nazwa przestrzeni nazw Service Bus
    > - `<Location>` — Lokalizacja przestrzeni nazw Service Bus
    > - `<KeyVaultName>` — Nazwa magazynu kluczy
    > - `<KeyName>` -Nazwa klucza w magazynie kluczy  

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon Menedżer zasobów. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd Service Bus](service-bus-messaging-overview.md)
- [Przegląd Key Vault](../key-vault/general/overview.md)