---
title: Skonfiguruj własny klucz do szyfrowania danych usługi Azure Event Hubs magazynowanych
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania własnego klucza do szyfrowania danych Event Hubs platformy Azure.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c9d1ac1c3a3387600fed80939598baafe658054b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595990"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Skonfiguruj klucze zarządzane przez klienta do szyfrowania danych Event Hubs platformy Azure przechowywanych przy użyciu Azure Portal
Usługa Azure Event Hubs zapewnia szyfrowanie danych przechowywanych przy użyciu usługi Azure szyfrowanie usługi Storage (SSE platformy Azure). Usługa Event Hubs używa usługi Azure Storage do przechowywania danych. Wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Jeśli używasz własnego klucza (nazywanego również Bring Your Own Key (BYOK) lub kluczem zarządzanym przez klienta), dane są nadal szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft, ale dodatkowo klucz zarządzany przez firmę Microsoft zostanie zaszyfrowany przy użyciu klucza zarządzanego przez klienta. Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania kluczy zarządzanych przez firmę Microsoft. Włączenie funkcji BYOK to jednorazowy proces konfiguracji w przestrzeni nazw.

> [!NOTE]
> - Funkcja BYOK jest obsługiwana przez [Event Hubs dedykowanych klastrów z jedną dzierżawą](event-hubs-dedicated-overview.md) . Nie można jej włączyć dla przestrzeni nazw w warstwie Standardowa Event Hubs.
> - Szyfrowanie można włączyć tylko dla nowych lub pustych przestrzeni nazw. Jeśli przestrzeń nazw zawiera Centra zdarzeń, operacja szyfrowania zakończy się niepowodzeniem.

Za pomocą Azure Key Vault można zarządzać kluczami i przeprowadzać inspekcję użycia klucza. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy. Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy z kluczami zarządzanymi przez klienta przy użyciu Azure Portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy przy użyciu Azure Portal, zobacz [Szybki Start: tworzenie Azure Key Vault przy użyciu Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Użycie kluczy zarządzanych przez klienta w usłudze Azure Event Hubs wymaga skonfigurowania dwóch wymaganych właściwości magazynu kluczy. Są to:  **usuwanie nietrwałe** i **nie przeczyszczanie**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w Azure Portal. Jeśli jednak musisz włączyć te właściwości w istniejącym magazynie kluczy, musisz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włącz klucze zarządzane przez klienta
Aby włączyć klucze zarządzane przez klienta w Azure Portal, wykonaj następujące kroki:

1. Przejdź do klastra Event Hubs — warstwa Dedykowana.
1. Wybierz przestrzeń nazw, w której chcesz włączyć BYOK.
1. Na stronie **Ustawienia** obszaru nazw Event Hubs wybierz pozycję **szyfrowanie**. 
1. Wybierz **szyfrowanie klucza zarządzanego przez klienta** , jak pokazano na poniższej ilustracji. 

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami
Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany przez klienta z przestrzenią nazw platformy Azure Event Hubs. Event Hubs obsługuje tylko Azure Key Vault. Po włączeniu opcji **szyfrowania z kluczem zarządzanym przez klienta** w poprzedniej sekcji należy zaimportować klucz do Azure Key Vault. Ponadto klucze muszą mieć **nietrwałe usuwanie** i nie można ich **czyścić** skonfigurować dla klucza. Te ustawienia można skonfigurować przy użyciu [programu PowerShell](../key-vault/general/key-vault-recovery.md) lub [interfejsu wiersza polecenia](../key-vault/general/key-vault-recovery.md).

1. Aby utworzyć nowy magazyn kluczy, postępuj zgodnie z Azure Key Vault [przewodnika Szybki Start](../key-vault/general/overview.md). Aby uzyskać więcej informacji o importowaniu istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/general/about-keys-secrets-certificates.md).
1. Aby włączyć zarówno nietrwałe usuwanie, jak i przeczyszczanie, podczas tworzenia magazynu należy użyć polecenia [AZ Create webmagazynion](/cli/azure/keyvault#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczeniem do istniejącego magazynu (ma już włączony nietrwałe usuwanie), użyj polecenia [AZ webmagazyn Update](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klucze, wykonując następujące czynności:
    1. Aby utworzyć nowy klucz, wybierz pozycję **Generuj/Importuj** z menu **klucze** w obszarze **Ustawienia**.
        
        ![Przycisk Wybierz opcję Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ustaw **Opcje** w celu **wygenerowania** klucza i nadaj mu nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 
    1. Teraz możesz wybrać ten klucz do skojarzenia z przestrzenią nazw Event Hubs do szyfrowania z listy rozwijanej. 

        ![Wybierz klucz z magazynu kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Wprowadź szczegóły klucza i kliknij przycisk **Wybierz**. Spowoduje to włączenie szyfrowania klucza zarządzanego przez firmę Microsoft przy użyciu klucza (klucza zarządzanego przez klienta). 


## <a name="rotate-your-encryption-keys"></a>Obróć klucze szyfrowania
Możesz obrócić klucz w magazynie kluczy przy użyciu mechanizmu rotacji magazynów kluczy platformy Azure. Daty aktywacji i wygaśnięcia można także skonfigurować w celu zautomatyzowania rotacji kluczy. Usługa Event Hubs wykryje nowe wersje klucza i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Odwołaj dostęp do kluczy
Cofnięcie dostępu do kluczy szyfrowania nie spowoduje przeczyszczenia danych z Event Hubs. Nie można jednak uzyskać dostępu do danych z przestrzeni nazw Event Hubs. Możesz odwołać klucz szyfrowania za pomocą zasad dostępu lub usunąć klucz. Dowiedz się więcej na temat zasad dostępu i zabezpieczania magazynu kluczy, [Aby uzyskać bezpieczny dostęp do magazynu kluczy](../key-vault/general/secure-your-key-vault.md).

Po odwołaniu klucza szyfrowania usługa Event Hubs w zaszyfrowanej przestrzeni nazw stanie się niezależna. Jeśli dostęp do klucza jest włączony lub przywrócono klawisz Delete, usługa Event Hubs wybierze klucz, aby można było uzyskać dostęp do danych z przestrzeni nazw zaszyfrowanej Event Hubs.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych 
Ustawienie dzienników diagnostycznych dla przestrzeni nazw z włączonymi BYOKami umożliwia uzyskanie wymaganych informacji o operacjach. Te dzienniki mogą być włączone i później przesyłane strumieniowo do centrum zdarzeń lub analizowane przy użyciu usługi log Analytics lub przesyłane strumieniowo do magazynu w celu przeprowadzenia dostosowanej analizy. Aby dowiedzieć się więcej na temat dzienników diagnostycznych, zobacz [Omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Włącz dzienniki użytkowników
Wykonaj następujące kroki, aby włączyć dzienniki dla kluczy zarządzanych przez klienta.

1. W Azure Portal przejdź do przestrzeni nazw, dla której włączono BYOK.
1. Wybierz pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie**.

    ![Wybieranie ustawień diagnostycznych](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**. 

    ![Wybierz pozycję Dodaj ustawienie diagnostyczne](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Podaj **nazwę** i wybierz miejsce, do którego chcesz przesyłać strumieniowo dzienniki.
1. Wybierz pozycję **CustomerManagedKeyUserLogs** i **Zapisz**. Ta akcja włącza dzienniki dla BYOK w przestrzeni nazw.

    ![Wybierz opcję dzienniki użytkownika klucza zarządzanego przez klienta](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schemat dziennika 
Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższej tabeli. 

| Nazwa | Opis |
| ---- | ----------- | 
| TaskName | Opis zadania, które zakończyło się niepowodzeniem. |
| ActivityId | Wewnętrzny identyfikator używany do śledzenia. |
| category | Definiuje klasyfikację zadania. Na przykład jeśli klucz z magazynu kluczy jest wyłączany, będzie to kategoria informacji lub jeśli klucz nie może zostać rozpakowany, może to oznaczać, że wystąpił błąd. |
| resourceId | Identyfikator zasobu Azure Resource Manager |
| keyVault | Pełna nazwa magazynu kluczy. |
| key | Nazwa klucza służąca do szyfrowania przestrzeni nazw Event Hubs. |
| Wersja | Używana wersja klucza. |
| operation | Operacja wykonywana na kluczu w magazynie kluczy. Na przykład Wyłącz/Włącz klucz, Zawijaj lub Odpakuj |
| kod | Kod, który jest skojarzony z operacją. Przykład: kod błędu, 404 oznacza, że nie znaleziono klucza. |
| message | Dowolny komunikat o błędzie skojarzony z operacją |

Oto przykład dziennika dla klucza zarządzanego przez klienta:

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Użyj szablonu Menedżer zasobów, aby włączyć szyfrowanie
W tej sekcji przedstawiono sposób wykonywania następujących zadań przy użyciu **szablonów Azure Resource Manager**. 

1. Utwórz **przestrzeń nazw Event Hubs** przy użyciu tożsamości usługi zarządzanej.
2. Utwórz **Magazyn kluczy** i przyznaj tożsamości usługi dostęp do magazynu kluczy. 
3. Zaktualizuj przestrzeń nazw Event Hubs przy użyciu informacji o magazynie kluczy (klucz/wartość). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Tworzenie klastra Event Hubs i przestrzeni nazw z tożsamością usługi zarządzanej
W tej sekcji przedstawiono sposób tworzenia przestrzeni nazw platformy Azure Event Hubs z tożsamością usługi zarządzanej przy użyciu szablonu Azure Resource Manager i programu PowerShell. 

1. Utwórz szablon Azure Resource Manager, aby utworzyć przestrzeń nazw Event Hubs z tożsamością usługi zarządzanej. Nazwij plik: **CreateEventHubClusterAndNamespace.jsna**: 

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
2. Utwórz plik parametrów szablonu o nazwie: **CreateEventHubClusterAndNamespaceParams.jsna**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>` -Nazwa klastra Event Hubs    
    > - `<EventHubsNamespaceName>` -Nazwa przestrzeni nazw Event Hubs
    > - `<Location>` — Lokalizacja przestrzeni nazw Event Hubs

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia Event Hubs przestrzeni nazw. Następnie Pobierz identyfikator przestrzeni nazw Event Hubs, aby użyć jej później. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Udzielanie tożsamości przestrzeni nazw Event Hubs dostępu do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z **ochroną przed przeczyszczaniem** i włączonym **usuwaniem nietrwałego** . 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    ORAZ    
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy**. Określ wartości dla grup zasobów i nazw magazynów kluczy przed uruchomieniem polecenia. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu magazynu kluczy, tak aby zarządzana tożsamość przestrzeni nazw Event Hubs mogła uzyskać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora przestrzeni nazw Event Hubs z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Szyfruj dane w Event Hubs przestrzeni nazw z kluczem zarządzanym przez klienta z magazynu kluczy
Wykonano następujące kroki: 

1. Utworzono przestrzeń nazw Premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznaj zarządzanej tożsamości dostęp do magazynu kluczy. 

W tym kroku zostanie zaktualizowana przestrzeń nazw Event Hubs przy użyciu informacji o magazynie kluczy. 

1. Utwórz plik JSON o nazwie **CreateEventHubClusterAndNamespace.js** przy użyciu następującej zawartości: 

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

2. Utwórz plik parametrów szablonu: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>` — Nazwa klastra Event Hubs.        
    > - `<EventHubsNamespaceName>` -Nazwa przestrzeni nazw Event Hubs
    > - `<Location>` — Lokalizacja przestrzeni nazw Event Hubs
    > - `<KeyVaultName>` — Nazwa magazynu kluczy
    > - `<KeyName>` -Nazwa klucza w magazynie kluczy

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon Menedżer zasobów. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Najlepszym rozwiązaniem jest zawsze włączenie dzienników, jak pokazano w poprzedniej sekcji. Ułatwia śledzenie działań po włączeniu szyfrowania BYOK. Ułatwia ona również określanie zakresu problemów.

Poniżej przedstawiono kody typowych błędów, które należy wyszukać, gdy włączone jest szyfrowanie BYOK.

| Akcja | Kod błędu | Wynikający ze stanu danych |
| ------ | ---------- | ----------------------- | 
| Usuń uprawnienie zawijania/odwinięcia z magazynu kluczy | 403 |    Niedostępny |
| Usuń członkostwo roli usługi AAD z podmiotu usługi AAD, który udzielił uprawnienia do zawijania/depakowania | 403 |  Niedostępny |
| Usuwanie klucza szyfrowania z magazynu kluczy | 404 | Niedostępny |
| Usuń Magazyn kluczy | 404 | Niedostępne (przyjęto, że jest włączone polecenie Soft-Delete, które jest wymagane.) |
| Zmiana okresu ważności klucza szyfrowania w taki sposób, że już wygasł | 403 |   Niedostępny  |
| Zmiana protokołu NBF (nie wcześniej) w taki sposób, że klucz szyfrowania klucza nie jest aktywny | 403 | Niedostępny  |
| Wybieranie opcji **Zezwalaj na usługi MSFT** dla zapory magazynu kluczy lub w inny sposób blokowanie dostępu do sieci do magazynu kluczy, który ma klucz szyfrowania | 403 | Niedostępny |
| Przeniesienie magazynu kluczy do innej dzierżawy | 404 | Niedostępny |  
| Sporadyczny problem z siecią lub awaria usługi DNS/AAD/MSI |  | Dostępne przy użyciu klucza szyfrowania danych w pamięci podręcznej |

> [!IMPORTANT]
> Aby włączyć funkcję Geo-DR w przestrzeni nazw używającej szyfrowania BYOK, pomocnicza przestrzeń nazw dla parowania musi znajdować się w dedykowanym klastrze i musi mieć włączoną dla niej tożsamość zarządzaną przez system. Aby dowiedzieć się więcej, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd usługi Event Hubs](event-hubs-about.md)
- [Przegląd Key Vault](../key-vault/general/overview.md)