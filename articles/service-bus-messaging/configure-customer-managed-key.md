---
title: Konfigurowanie własnego klucza do szyfrowania danych Azure Service Bus danych w spoczynku
description: Ten artykuł zawiera informacje na temat sposobu konfigurowania własnego klucza na Azure Service Bus danych.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6b982f01b02e7aa99f1b83e2f590e3660cb69c54
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751350"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania Azure Service Bus danych w spoczynku przy użyciu Azure Portal
Azure Service Bus Premium zapewnia szyfrowanie danych magazynowych za pomocą usługi Azure szyfrowanie usługi Storage (Azure SSE). Service Bus Premium używa usługi Azure Storage do przechowywania danych. Wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli używasz własnego klucza (nazywanego również kluczem Bring Your Own Key (BYOK) lub kluczem zarządzanym przez klienta, dane są nadal szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft, ale dodatkowo klucz zarządzany przez firmę Microsoft zostanie zaszyfrowany przy użyciu klucza zarządzanego przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania kluczy zarządzanych przez firmę Microsoft. Włączenie funkcji BYOK jest procesem instalacji jeden raz w przestrzeni nazw.

Istnieją pewne zastrzeżenia do klucza zarządzanego przez klienta dotyczące szyfrowania po stronie usługi. 
- Ta funkcja jest obsługiwana przez [Azure Service Bus Premium.](service-bus-premium-messaging.md) Nie można go włączyć dla warstwy Standardowa Service Bus przestrzeni nazw.
- Szyfrowanie można włączyć tylko dla nowych lub pustych przestrzeni nazw. Jeśli przestrzeń nazw zawiera jakiekolwiek kolejki lub tematy, operacja szyfrowania nie powiedzie się.

Możesz użyć Azure Key Vault do zarządzania kluczami i inspekcji użycia klucza. Możesz utworzyć własne klucze i przechowywać je w magazynie kluczy lub użyć interfejsów API Azure Key Vault do generowania kluczy. Aby uzyskać więcej informacji o Azure Key Vault, zobacz [Co to jest Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy przy użyciu kluczy zarządzanych przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz Szybki [start:](../key-vault/general/quick-create-portal.md)tworzenie magazynu Azure Key Vault użyciu Azure Portal .

> [!IMPORTANT]
> Używanie kluczy zarządzanych przez klienta Azure Service Bus wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to:  **Usuwanie nie softowe** **i nie przeczyszczaj**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do przestrzeni nazw Service Bus Premium.
2. Na stronie **Ustawienia** przestrzeni nazw usługi Service Bus wybierz pozycję **Szyfrowanie.**
3. Wybierz szyfrowanie kluczy zarządzanych przez klienta w **spoczynku,** jak pokazano na poniższej ilustracji.

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy przy użyciu kluczy

Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany przez klienta z Azure Service Bus nazw. Service Bus obsługuje tylko Azure Key Vault. Jeśli w **poprzedniej** sekcji włączysz opcję Szyfrowanie za pomocą klucza zarządzanego przez klienta, musisz zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć skonfigurowane opcje **Usuwania** nie soft i Nie **przeczyszczaj** dla klucza. Te ustawienia można skonfigurować przy użyciu programu [PowerShell lub](../key-vault/general/key-vault-recovery.md) interfejsu [wiersza polecenia](../key-vault/general/key-vault-recovery.md).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [Szybki start.](../key-vault/general/overview.md) Aby uzyskać więcej informacji na temat importowania istniejących kluczy, zobacz [About keys, secrets, and certificates (Informacje o kluczach, wpisach tajnych i certyfikatach).](../key-vault/general/about-keys-secrets-certificates.md)
1. Aby włączyć zarówno usuwanie nie soft, jak i ochronę przed przeczyszczaniem podczas tworzenia magazynu, użyj [polecenia az keyvault create.](/cli/azure/keyvault#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczaniem do istniejącego magazynu (z włączoną obsługą usuwania nie soft), użyj [polecenia az keyvault update.](/cli/azure/keyvault#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, korzystając z następujących kroków:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Zaimportuj** z menu **Klucze** w obszarze **Ustawienia**.
        
        ![Wybierz przycisk Wygeneruj/Zaimportuj](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ustaw **ustawienie Opcje** na **generowanie** i nadaj kluczowi nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 

    1. Teraz możesz wybrać ten klucz do skojarzenia z Service Bus w celu szyfrowania z listy rozwijanej. 

        ![Wybieranie klucza z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > W celu zapewnienia nadmiarowości można dodać maksymalnie 3 klucze. Jeśli jeden z kluczy wygasł lub nie jest dostępny, pozostałe klucze będą używane do szyfrowania.
        
    1. Wypełnij szczegóły klucza, a następnie kliknij pozycję **Wybierz**. Umożliwi to szyfrowanie klucza zarządzanego przez firmę Microsoft przy użyciu klucza (klucza zarządzanego przez klienta). 


    > [!IMPORTANT]
    > Jeśli chcesz używać klucza zarządzanego przez klienta wraz z odzyskiwaniem po awarii geograficznych, zapoznaj się z tą sekcją. 
    >
    > Aby włączyć szyfrowanie klucza zarządzanego przez firmę [](../key-vault/general/security-overview.md) Microsoft za pomocą klucza zarządzanego przez klienta, zasady dostępu są ustawiane dla tożsamości zarządzanej usługi Service Bus w określonym usłudze Azure KeyVault. Zapewnia to kontrolowany dostęp do usługi Azure KeyVault z Azure Service Bus nazw.
    >
    > W związku z tym:
    > 
    >   * Jeśli [geograficzne odzyskiwanie po](service-bus-geo-dr.md) awarii jest już włączone dla Service Bus nazw i chcesz włączyć klucz zarządzany przez klienta, wówczas 
    >     * Przerwij parowanie
    >     * [Skonfiguruj zasady dostępu dla tożsamości](../key-vault/general/assign-access-policy-portal.md) zarządzanej dla podstawowej i pomocniczej przestrzeni nazw magazynu kluczy.
    >     * Skonfiguruj szyfrowanie w podstawowej przestrzeni nazw.
    >     * Ponownie sparuj podstawowe i pomocnicze przestrzenie nazw.
    > 
    >   * Jeśli chcesz włączyć funkcję geograficznego drgd w Service Bus, w której klucz zarządzany przez klienta jest już ustawiony, —
    >     * [Skonfiguruj zasady dostępu dla tożsamości](../key-vault/general/assign-access-policy-portal.md) zarządzanej dla pomocniczej przestrzeni nazw do magazynu kluczy.
    >     * Sparuj podstawowe i pomocnicze przestrzenie nazw.


## <a name="rotate-your-encryption-keys"></a>Obracanie kluczy szyfrowania

Klucz w magazynie kluczy można obrócić przy użyciu mechanizmu rotacji usługi Azure Key Vault. W celu zautomatyzowania rotacji kluczy można również ustawić daty aktywacji i wygaśnięcia. Usługa Service Bus wykryje nowe wersje kluczy i rozpocznie ich automatyczne używanie.

## <a name="revoke-access-to-keys"></a>Odwoływanie dostępu do kluczy

Odwołanie dostępu do kluczy szyfrowania nie spowoduje przeczyszczania danych z Service Bus. Nie można jednak uzyskać dostępu do danych z Service Bus nazw. Klucz szyfrowania można odwołać za pomocą zasad dostępu lub przez usunięcie klucza. Dowiedz się więcej o zasadach dostępu i zabezpieczaniu magazynu kluczy z [bezpiecznego dostępu do magazynu kluczy.](../key-vault/general/security-overview.md)

Po odwołaniu klucza szyfrowania usługa Service Bus w zaszyfrowanej przestrzeni nazw stanie się niedziałała. Jeśli dostęp do klucza jest włączony lub usunięty klucz zostanie przywrócony, usługa Service Bus wybierze klucz, aby można było uzyskać dostęp do danych z zaszyfrowanej Service Bus nazw.

## <a name="caching-of-keys"></a>Buforowanie kluczy
Wystąpienie Service Bus sonduje wymienione na liście klucze szyfrowania co 5 minut. Buforuje je i używa do następnego sondowania, które trwa po 5 minutach. Tak długo, jak jest dostępny co najmniej jeden klucz, kolejki i tematy są dostępne. Jeśli wszystkie wymienione klucze będą niedostępne podczas sondowania, wszystkie kolejki i tematy staną się niedostępne. 

Poniżej przedstawiono więcej informacji: 

- Co 5 minut usługa Service Bus sonduje wszystkie klucze zarządzane przez klienta wymienione w rekordzie przestrzeni nazw:
    - Jeśli klucz został obrócony, rekord zostanie zaktualizowany przy użyciu nowego klucza.
    - Jeśli klucz został odwołany, klucz zostanie usunięty z rekordu.
    - Jeśli wszystkie klucze zostały odwołane, stan szyfrowania przestrzeni nazw ma wartość **Odwołano.** Nie można uzyskać dostępu do danych z Service Bus nazw. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Włączanie szyfrowania przy użyciu Resource Manager szablonu
W tej sekcji pokazano, jak wykonać następujące zadania przy **użyciu Azure Resource Manager szablonów**. 

1. Utwórz przestrzeń **nazw usługi Service Bus** Premium przy użyciu tożsamości usługi **zarządzanej.**
2. Utwórz magazyn **kluczy i** przyznaj tożsamości usługi dostęp do magazynu kluczy. 
3. Zaktualizuj przestrzeń Service Bus przy użyciu informacji o magazynie kluczy (klucz/wartość). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Tworzenie przestrzeni nazw usługi Service Bus Premium przy użyciu tożsamości usługi zarządzanej
W tej sekcji pokazano, jak utworzyć przestrzeń nazw Azure Service Bus tożsamości usługi zarządzanej przy użyciu szablonu Azure Resource Manager programu PowerShell. 

1. Utwórz szablon Azure Resource Manager, aby utworzyć Service Bus w warstwie Premium z tożsamością usługi zarządzanej. Nazwij plik: **CreateServiceBusPremiumNamespace.jsna**: 

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
2. Utwórz plik parametrów szablonu o nazwie : **CreateServiceBusPremiumNamespaceParams.jsna .** 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>` — Nazwa twojej Service Bus nazw
    > - `<Location>` - Lokalizacja twojej Service Bus nazw

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia przestrzeni nazw Service Bus Premium. Następnie pobierz identyfikator przestrzeni nazw Service Bus do późniejszego użycia. Przed uruchomieniem polecenia zastąp nazwą `{MyRG}` grupy zasobów.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Udzielanie Service Bus tożsamości przestrzeni nazw do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z włączoną ochroną przed **przeczyszczaniem** **i usuwaniem nie soft-delete.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OR)
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy.** Przed uruchomieniem polecenia określ wartości dla grupy zasobów i nazw magazynu kluczy. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu magazynu kluczy tak, aby tożsamość zarządzana Service Bus może uzyskać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora przestrzeni nazw Service Bus z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Szyfrowanie danych w Service Bus nazw przy użyciu klucza zarządzanego przez klienta z magazynu kluczy
Do tej pory zostały wykonane następujące czynności: 

1. Utworzono przestrzeń nazw Premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznaj tożsamości zarządzanej dostęp do magazynu kluczy. 

W tym kroku zaktualizujemy przestrzeń nazw usługi Service Bus informacjami o magazynie kluczy. 

1. Utwórz plik JSON o **UpdateServiceBusNamespaceWithEncryption.jso** następującej zawartości: 

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

2. Utwórz plik parametrów szablonu:UpdateServiceBusNamespaceWithEncryptionParams.js **na stronie**.

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<ServiceBusNamespaceName>` — Nazwa twojej Service Bus nazw
    > - `<Location>` - Lokalizacja twojej Service Bus nazw
    > - `<KeyVaultName>` — Nazwa magazynu kluczy
    > - `<KeyName>` — Nazwa klucza w magazynie kluczy  

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć Resource Manager szablonu. Przed uruchomieniem polecenia zastąp nazwą `{MyRG}` grupy zasobów. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Service Bus omówienie](service-bus-messaging-overview.md)
- [Key Vault omówienie](../key-vault/general/overview.md)