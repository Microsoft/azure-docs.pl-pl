---
title: Konfigurowanie własnego klucza do szyfrowania danych Azure Event Hubs danych w spoczynku
description: Ten artykuł zawiera informacje na temat sposobu konfigurowania własnego klucza na Azure Event Hubs danych.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 33587812121051d93aa8b939c3df70530ba65c5e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812448"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta na potrzeby szyfrowania Azure Event Hubs danych w spoczynku przy użyciu Azure Portal
Azure Event Hubs szyfrowanie danych magazynowych za pomocą usługi Azure szyfrowanie usługi Storage (Azure SSE). Usługa Event Hubs używa usługi Azure Storage do przechowywania danych. Wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli używasz własnego klucza (nazywanego również kluczem Bring Your Own Key (BYOK) lub kluczem zarządzanym przez klienta, dane są nadal szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft, ale dodatkowo klucz zarządzany przez firmę Microsoft zostanie zaszyfrowany przy użyciu klucza zarządzanego przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania kluczy zarządzanych przez firmę Microsoft. Włączenie funkcji BYOK jest procesem instalacji jeden raz w przestrzeni nazw.

> [!NOTE]
> - Funkcja BYOK jest obsługiwana przez [Event Hubs dedykowanych klastrów z jedną dzierżawą.](event-hubs-dedicated-overview.md) Nie można go włączyć dla standardowych Event Hubs nazw.
> - Szyfrowanie można włączyć tylko dla nowych lub pustych przestrzeni nazw. Jeśli przestrzeń nazw zawiera centra zdarzeń, operacja szyfrowania nie powiedzie się.

Możesz użyć Azure Key Vault do zarządzania kluczami i inspekcji użycia klucza. Możesz utworzyć własne klucze i przechowywać je w magazynie kluczy lub użyć interfejsów API Azure Key Vault do generowania kluczy. Aby uzyskać więcej informacji o Azure Key Vault, zobacz [Co to jest Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy przy użyciu kluczy zarządzanych przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz Szybki [start:](../key-vault/general/quick-create-portal.md)tworzenie magazynu Azure Key Vault użyciu Azure Portal .

> [!IMPORTANT]
> Korzystanie z kluczy zarządzanych przez klienta Azure Event Hubs wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to:  **Usuwanie nie softowe** **i nie przeczyszczanie**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do swojego Event Hubs — warstwa Dedykowana klastra.
1. Wybierz przestrzeń nazw, dla której chcesz włączyć usługę BYOK.
1. Na stronie **Ustawienia** przestrzeni nazw usługi Event Hubs wybierz **pozycję Szyfrowanie**. 
1. Wybierz szyfrowanie kluczy zarządzanych przez klienta w **spoczynku,** jak pokazano na poniższej ilustracji. 

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy przy użyciu kluczy
Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany przez klienta z Azure Event Hubs nazw. Event Hubs obsługuje tylko Azure Key Vault. Jeśli w **poprzedniej** sekcji włączysz opcję Szyfrowanie za pomocą klucza zarządzanego przez klienta, musisz zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć skonfigurowane opcje **Usuwania** nie soft i Nie **przeczyszczaj** dla klucza. Te ustawienia można skonfigurować przy użyciu programu [PowerShell lub](../key-vault/general/key-vault-recovery.md) interfejsu [wiersza polecenia](../key-vault/general/key-vault-recovery.md).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [Szybki start.](../key-vault/general/overview.md) Aby uzyskać więcej informacji na temat importowania istniejących kluczy, zobacz [About keys, secrets, and certificates (Informacje o kluczach, wpisach tajnych i certyfikatach).](../key-vault/general/about-keys-secrets-certificates.md)
1. Aby włączyć zarówno usuwanie nie soft, jak i ochronę przed przeczyszczaniem podczas tworzenia magazynu, użyj [polecenia az keyvault create.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczaniem do istniejącego magazynu (z włączoną obsługą usuwania nie soft), użyj [polecenia az keyvault update.](/cli/azure/keyvault#az_keyvault_update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, korzystając z następujących kroków:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Zaimportuj** z menu **Klucze** w obszarze **Ustawienia**.
        
        ![Wybierz przycisk Wygeneruj/Zaimportuj](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ustaw **ustawienie Opcje** na **generowanie** i nadaj kluczowi nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 
    1. Teraz możesz wybrać ten klucz do skojarzenia z Event Hubs nazw w celu szyfrowania z listy rozwijanej. 

        ![Wybieranie klucza z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Wypełnij szczegóły klucza, a następnie kliknij pozycję **Wybierz**. Umożliwi to szyfrowanie klucza zarządzanego przez firmę Microsoft przy użyciu klucza (klucza zarządzanego przez klienta). 


## <a name="rotate-your-encryption-keys"></a>Obracanie kluczy szyfrowania
Klucz można obrócić w magazynie kluczy przy użyciu mechanizmu rotacji usługi Azure Key Vault. W celu zautomatyzowania rotacji kluczy można również ustawić daty aktywacji i wygaśnięcia. Usługa Event Hubs wykryje nowe wersje kluczy i zacznie z nich korzystać automatycznie.

## <a name="revoke-access-to-keys"></a>Odwoływanie dostępu do kluczy
Odwołanie dostępu do kluczy szyfrowania nie spowoduje przeczyszczania danych z Event Hubs. Nie można jednak uzyskać dostępu do danych z Event Hubs nazw. Klucz szyfrowania można odwołać za pomocą zasad dostępu lub usuwając klucz. Dowiedz się więcej o zasadach dostępu i zabezpieczaniu magazynu kluczy z [bezpiecznego dostępu do magazynu kluczy.](../key-vault/general/security-features.md)

Po odwołaniu klucza szyfrowania usługa Event Hubs w zaszyfrowanej przestrzeni nazw stanie się niedziałała. Jeśli dostęp do klucza jest włączony lub klucz usuwania zostanie przywrócony, usługa Event Hubs wybierze klucz, aby można było uzyskać dostęp do danych z zaszyfrowanej Event Hubs nazw.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych 
Ustawienie dzienników diagnostycznych dla przestrzeni nazw z włączoną usługą BYOK zapewnia wymagane informacje o operacjach. Te dzienniki można włączyć, a następnie przesyłać strumieniowo do centrum zdarzeń, analizować za pomocą analizy dzienników lub przesyłać strumieniowo do magazynu w celu wykonania dostosowanych analiz. Aby dowiedzieć się więcej na temat dzienników diagnostycznych, zobacz [Overview of Azure Diagnostic logs (Omówienie dzienników diagnostycznych platformy Azure).](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Włączanie dzienników użytkowników
Wykonaj następujące kroki, aby włączyć dzienniki dla kluczy zarządzanych przez klienta.

1. W Azure Portal przejdź do przestrzeni nazw z włączoną usługą BYOK.
1. Wybierz **pozycję Ustawienia diagnostyczne w** obszarze **Monitorowanie.**

    ![Wybieranie ustawień diagnostycznych](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Wybierz **pozycję +Dodaj ustawienie diagnostyczne.** 

    ![Wybierz pozycję Dodaj ustawienie diagnostyczne](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Podaj nazwę **i** wybierz miejsce, do którego chcesz przesyłać strumieniowo dzienniki.
1. Wybierz **pozycję CustomerManagedKeyUserLogs i** zapisz **.** Ta akcja włącza dzienniki usługi BYOK w przestrzeni nazw .

    ![Wybieranie opcji dzienników użytkownika klucza zarządzanego przez klienta](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schemat dziennika 
Wszystkie dzienniki są przechowywane w JavaScript Object Notation (JSON). Każdy wpis ma pola ciągów, które używają formatu opisanego w poniższej tabeli. 

| Nazwa | Opis |
| ---- | ----------- | 
| Nazwa_zadania | Opis zadania, które zakończyło się niepowodzeniem. |
| ActivityId | Identyfikator wewnętrzny używany do śledzenia. |
| category | Definiuje klasyfikację zadania. Jeśli na przykład klucz z magazynu kluczy jest wyłączony, będzie to kategoria informacji lub jeśli nie można odpakować klucza, może to być błąd. |
| resourceId | Azure Resource Manager identyfikator zasobu |
| keyVault | Pełna nazwa magazynu kluczy. |
| key | Nazwa klucza używana do szyfrowania Event Hubs nazw. |
| Wersja | Wersja używanego klucza. |
| operation | Operacja wykonywana na kluczu w magazynie kluczy. Na przykład wyłącz/włącz klucz, opakuj lub odpakuj |
| kod | Kod skojarzony z operacją. Przykład: Kod błędu, 404 oznacza, że klucz nie został znaleziony. |
| message | Dowolny komunikat o błędzie skojarzony z operacją |

Oto przykład dziennika klucza zarządzanego przez klienta:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Włączanie szyfrowania przy użyciu Resource Manager szablonu
W tej sekcji pokazano, jak wykonać następujące zadania przy **użyciu Azure Resource Manager szablonów**. 

1. Tworzenie Event Hubs **nazw przy** użyciu tożsamości usługi zarządzanej.
2. Utwórz magazyn **kluczy i** przyznaj tożsamości usługi dostęp do magazynu kluczy. 
3. Zaktualizuj przestrzeń Event Hubs przy użyciu informacji o magazynie kluczy (klucz/wartość). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Tworzenie klastra Event Hubs przestrzeni nazw z tożsamością usługi zarządzanej
W tej sekcji przedstawiono sposób tworzenia przestrzeni nazw usługi Azure Event Hubs tożsamości usługi zarządzanej przy użyciu szablonu Azure Resource Manager programu PowerShell. 

1. Utwórz szablon Azure Resource Manager, aby utworzyć Event Hubs nazw z tożsamością usługi zarządzanej. Nazwij plik: **CreateEventHubClusterAndNamespace.jsna**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Utwórz plik parametrów szablonu o nazwie : **CreateEventHubClusterAndNamespaceParams.jsna .** 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>` — Nazwa klastra Event Hubs klastra    
    > - `<EventHubsNamespaceName>` — Nazwa twojej Event Hubs nazw
    > - `<Location>` - Lokalizacja twojej Event Hubs nazw

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia Event Hubs nazw. Następnie pobierz identyfikator przestrzeni nazw Event Hubs do późniejszego użycia. Przed uruchomieniem polecenia zastąp nazwą `{MyRG}` grupy zasobów.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Udzielanie Event Hubs przestrzeni nazw do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z włączoną ochroną przed **przeczyszczaniem** i **usuwaniem nie soft-delete.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OR)    
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy.** Przed uruchomieniem polecenia określ wartości dla grupy zasobów i nazw magazynu kluczy. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu magazynu kluczy tak, aby tożsamość zarządzana Event Hubs może uzyskać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora przestrzeni nazw Event Hubs z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Szyfrowanie danych w Event Hubs nazw przy użyciu klucza zarządzanego przez klienta z magazynu kluczy
Do tej pory zostały wykonane następujące czynności: 

1. Utworzono przestrzeń nazw Premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznaj tożsamości zarządzanej dostęp do magazynu kluczy. 

W tym kroku zaktualizujemy przestrzeń nazw usługi Event Hubs informacjami o magazynie kluczy. 

1. Utwórz plik JSON o **CreateEventHubClusterAndNamespace.jso** następującej zawartości: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Utwórz plik parametrów szablonu: **UpdateEventHubClusterAndNamespaceParams.jsna stronie**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>` — Nazwa klastra Event Hubs klastra.        
    > - `<EventHubsNamespaceName>` - Nazwa twojej Event Hubs nazw
    > - `<Location>` - Lokalizacja twojej Event Hubs nazw
    > - `<KeyVaultName>` — Nazwa magazynu kluczy
    > - `<KeyName>` — Nazwa klucza w magazynie kluczy

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Najlepszym rozwiązaniem jest włączenie dzienników, jak pokazano w poprzedniej sekcji. Ułatwia ona śledzenie działań po włączeniu szyfrowania BYOK. Ułatwia to również określanie zakresu problemów.

Poniżej przedstawiono typowe kody błędów, które należy szukać, gdy szyfrowanie BYOK jest włączone.

| Akcja | Kod błędu | Wynikowy stan danych |
| ------ | ---------- | ----------------------- | 
| Usuwanie uprawnienia do opakowywania/odpakowywania z magazynu kluczy | 403 |    Niedostępne |
| Usuń członkostwo roli usługi AAD z podmiotu zabezpieczeń usługi AAD, który przyznał uprawnienia do opakowywania/odpakowywania | 403 |  Niedostępne |
| Usuwanie klucza szyfrowania z magazynu kluczy | 404 | Niedostępne |
| Usuwanie magazynu kluczy | 404 | Niedostępność (przy założeniu, że włączono usuwanie nie soft-delete, co jest ustawieniem wymaganym). |
| Zmiana okresu ważności klucza szyfrowania w taki sposób, aby wygasł | 403 |   Niedostępne  |
| Zmiana NBF (nie wcześniej), aby klucz szyfrowania klucza nie był aktywny | 403 | Niedostępne  |
| Wybranie opcji **Zezwalaj na usługi MSFT dla** zapory magazynu kluczy lub w inny sposób blokującej dostęp sieciowy do magazynu kluczy z kluczem szyfrowania | 403 | Niedostępne |
| Przenoszenie magazynu kluczy do innej dzierżawy | 404 | Niedostępne |  
| Sporadyczne problemy z siecią lub błąd DNS/AAD/MSI |  | Dostępne przy użyciu klucza szyfrowania danych w pamięci podręcznej |

> [!IMPORTANT]
> Aby włączyć funkcję geograficznego drgowania w przestrzeni nazw korzystającej z szyfrowania BYOK, pomocnicza przestrzeń nazw do parowania musi znajdować się w dedykowanym klastrze i musi mieć w nim włączoną tożsamość zarządzaną przypisaną przez system. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd usługi Event Hubs](event-hubs-about.md)
- [Key Vault omówienie](../key-vault/general/overview.md)