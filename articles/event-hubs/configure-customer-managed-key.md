---
title: Konfigurowanie własnego klucza do szyfrowania danych usługi Azure Event Hubs w stanie spoczynku
description: Ten artykuł zawiera informacje dotyczące konfigurowania własnego klucza do szyfrowania obsługi danych usługi Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459138"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych usługi Azure Event Hubs w stanie spoczynku przy użyciu witryny Azure portal
Usługa Azure Event Hubs zapewnia szyfrowanie danych w spoczynku za pomocą szyfrowania usługi Azure Storage Service (Azure SSE). Usługa Event Hubs polega na usłudze Azure Storage do przechowywania danych i domyślnie wszystkie dane przechowywane w usłudze Azure Storage są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="overview"></a>Omówienie
Usługa Azure Event Hubs obsługuje teraz opcję szyfrowania danych w spoczynku za pomocą kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta (Bring Your Own Key — BYOK). Ta funkcja umożliwia tworzenie, obracanie, wyłączanie i odwoływanie dostępu do kluczy zarządzanych przez klienta, które są używane do szyfrowania danych usługi Azure Event Hubs w spoczynku.

Włączenie funkcji BYOK jest procesem jednorazowej konfiguracji w obszarze nazw.

> [!NOTE]
> Funkcja BYOK jest obsługiwana przez centra zdarzeń dedykowane klastry [z jedną dzierżawą.](event-hubs-dedicated-overview.md) Nie można włączyć dla standardowych obszarów nazw centrów zdarzeń.

Za pomocą usługi Azure Key Vault można zarządzać kluczami i kontrolować użycie klucza. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy. Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)

W tym artykule pokazano, jak skonfigurować magazyn kluczy za pomocą kluczy zarządzanych przez klienta przy użyciu witryny Azure portal. Aby dowiedzieć się, jak utworzyć magazyn kluczy za pomocą witryny Azure portal, zobacz [Szybki start: Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](../key-vault/secrets/quick-create-portal.md).

> [!IMPORTANT]
> Przy użyciu kluczy zarządzanych przez klienta z usługi Azure Event Hubs wymaga, że magazyn kluczy mają dwie wymagane właściwości skonfigurowane. Są to: **Soft Delete** i **Nie czyścić**. Te właściwości są domyślnie włączone podczas tworzenia nowego magazynu kluczy w witrynie Azure portal. Jednak jeśli chcesz włączyć te właściwości w istniejącym magazynie kluczy, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="enable-customer-managed-keys"></a>Włączanie kluczy zarządzanych przez klienta
Aby włączyć klucze zarządzane przez klienta w witrynie Azure portal, wykonaj następujące kroki:

1. Przejdź do klastra dedykowanego centrów zdarzeń.
1. Wybierz obszar nazw, na którym chcesz włączyć funkcję BYOK.
1. Na stronie **Ustawienia** obszaru nazw Centrum zdarzeń wybierz pozycję **Szyfrowanie**. 
1. Wybierz **szyfrowanie klucza zarządzanego przez klienta w spoczynku,** jak pokazano na poniższej ilustracji. 

    ![Włączanie klucza zarządzanego przez klienta](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurowanie magazynu kluczy z kluczami
Po włączeniu kluczy zarządzanych przez klienta należy skojarzyć klucz zarządzany klienta z obszarem nazw usługi Azure Event Hubs. Usługa Event Hubs obsługuje tylko usługę Azure Key Vault. Jeśli **włączysz opcję Szyfrowanie z kluczem zarządzanym przez klienta** w poprzedniej sekcji, musisz zaimportować klucz do usługi Azure Key Vault. Ponadto klucze muszą być skonfigurowane do **usuwania nietrwałego** i **nie czyścić** dla tego klucza. Te ustawienia można skonfigurować za pomocą programu [PowerShell](../key-vault/general/soft-delete-powershell.md) lub [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Aby utworzyć nową przechowalnię kluczy, postępuj zgodnie z [programem Szybki start](../key-vault/general/overview.md)usługi Azure Key Vault . Aby uzyskać więcej informacji na temat importowania istniejących kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](../key-vault/about-keys-secrets-and-certificates.md).
1. Aby włączyć zarówno ochronę usuwania nietrwałego, jak i oczyszczania podczas tworzenia przechowalni, użyj polecenia [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Aby dodać ochronę przed przeczyszczaniem do istniejącego magazynu (który ma już włączone usuwanie nietrwałe), użyj polecenia [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Utwórz klawisze, wykonując następujące kroki:
    1. Aby utworzyć nowy klucz, wybierz **polecenie Generuj/Zaimportuj** z menu **Klawisze** w obszarze **Ustawienia**.
        
        ![Wybierz przycisk Generuj/Importuj](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ustaw **opcje,** aby **wygenerować** i nadać kluczowi nazwę.

        ![Utwórz klucz](./media/configure-customer-managed-key/create-key.png) 
    1. Teraz można wybrać ten klucz do skojarzenia z obszarem nazw Centrum zdarzeń do szyfrowania z listy rozwijanej. 

        ![Wybieranie klucza z przechowalni kluczy](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Wypełnij szczegóły klucza i kliknij przycisk **Wybierz**. Umożliwi to szyfrowanie danych w pozostałej części obszaru nazw za pomocą klucza zarządzanego przez klienta. 


## <a name="rotate-your-encryption-keys"></a>Obracanie kluczy szyfrowania
Klucz można obracać w magazynie kluczy przy użyciu mechanizmu rotacji usługi Azure Key Vaults. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rotacji kluczy i inspekcji](../key-vault/secrets/key-rotation-log-monitoring.md). Daty aktywacji i wygaśnięcia można również ustawić tak, aby automatyzować rotację kluczy. Usługa Event Hubs wykryje nowe wersje kluczy i rozpocznie korzystanie z nich automatycznie.

## <a name="revoke-access-to-keys"></a>Cofanie dostępu do kluczy
Cofnięcie dostępu do kluczy szyfrowania nie przeczyści danych z centrów zdarzeń. Jednak dane nie mogą być dostępne z obszaru nazw Centrum zdarzeń. Klucz szyfrowania można odwołać za pomocą zasad dostępu lub usunięcia klucza. Dowiedz się więcej o zasadach dostępu i zabezpieczaniu magazynu kluczy z [bezpiecznego dostępu do magazynu kluczy.](../key-vault/general/secure-your-key-vault.md)

Po odwołaniu klucza szyfrowania usługa Centrum zdarzeń w zaszyfrowanym obszarze nazw stanie się niesprawna. Jeśli dostęp do klucza jest włączony lub klucz usuwania zostanie przywrócony, usługa Centrum zdarzeń wybierze klucz, aby uzyskać dostęp do danych z zaszyfrowanego obszaru nazw Centrum zdarzeń.

## <a name="set-up-diagnostic-logs"></a>Konfigurowanie dzienników diagnostycznych 
Ustawianie dzienników diagnostycznych dla obszarów nazw z włączoną funkcją BYOK zapewnia wymagane informacje o operacjach, gdy obszar nazw jest szyfrowany za pomocą kluczy zarządzanych przez klienta. Te dzienniki można włączyć i później strumień do centrum zdarzeń lub analizowane za pośrednictwem analizy dziennika lub przesyłane strumieniowo do magazynu w celu wykonywania niestandardowych analiz. Aby dowiedzieć się więcej o dziennikach diagnostycznych, zobacz [Omówienie dzienników diagnostyki platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Włączanie dzienników użytkowników
Wykonaj następujące kroki, aby włączyć dzienniki dla kluczy zarządzanych przez klienta.

1. W witrynie Azure portal przejdź do obszaru nazw, który ma włączone BYOK.
1. Wybierz **ustawienia diagnostyczne** w obszarze **Monitorowanie**.

    ![Wybieranie ustawień diagnostycznych](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Wybierz **+Dodaj ustawienie diagnostyczne**. 

    ![Wybierz ustawienie dodaj diagnostykę](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Podaj **nazwę** i wybierz miejsce, do którego chcesz przesyłać strumieniowo dzienniki.
1. Wybierz **customermanagedKeyUserLogs** i **Zapisz**. Ta akcja umożliwia dzienniki dla BYOK w obszarze nazw.

    ![Wybierz opcję dzienników kluczowych użytkowników zarządzanych przez klienta](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schemat dziennika 
Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis ma pola ciągu, które używają formatu opisanego w poniższej tabeli. 

| Nazwa | Opis |
| ---- | ----------- | 
| Nazwa_zadania | Opis zadania, które nie powiodło się. |
| Activityid | Identyfikator wewnętrzny używany do śledzenia. |
| category | Definiuje klasyfikację zadania. Na przykład jeśli klucz z magazynu kluczy jest wyłączony, to będzie kategoria informacji lub jeśli klucz nie może być rozpakowany, może być objęty błędem. |
| resourceId | Identyfikator zasobu usługi Azure Resource Manager |
| keyVault (klawiszE Vault) | Pełna nazwa magazynu kluczy. |
| key | Nazwa klucza używana do szyfrowania obszaru nazw centrów zdarzeń. |
| version | Wersja używanego klucza. |
| Operacji | Operacja, która jest wykonywana na kluczu w magazynie kluczy. Na przykład wyłącz/włącz klawisz, zawijaj lub rozpakuj |
| kod | Kod, który jest skojarzony z operacją. Przykład: Kod błędu 404 oznacza, że klucz nie został znaleziony. |
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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Włączanie szyfrowania za pomocą szablonu Menedżera zasobów
W tej sekcji pokazano, jak wykonać następujące zadania przy użyciu **szablonów usługi Azure Resource Manager**. 

1. Utwórz **obszar nazw Centrum zdarzeń** z tożsamością usługi zarządzanej.
2. Utwórz **magazyn kluczy** i udziel dostępu tożsamości usługi do magazynu kluczy. 
3. Zaktualizuj obszar nazw Centrum zdarzeń o informacje o magazynie kluczy (klucz/wartość). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Tworzenie klastra i obszaru nazw centrów zdarzeń z tożsamością usługi zarządzanej
W tej sekcji pokazano, jak utworzyć obszar nazw usługi Azure Event Hubs z tożsamością usługi zarządzanej przy użyciu szablonu usługi Azure Resource Manager i programu PowerShell. 

1. Utwórz szablon usługi Azure Resource Manager, aby utworzyć obszar nazw centrum zdarzeń z tożsamością usługi zarządzanej. Nazwij plik: **CreateEventHubClusterAndNamespace.json**: 

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
2. Utwórz plik parametru szablonu o nazwie: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>`- Nazwa klastra Centrów Zdarzeń    
    > - `<EventHubsNamespaceName>`- Nazwa obszaru nazw Centrów zdarzeń
    > - `<Location>`- Lokalizacja obszaru nazw Centrów zdarzeń

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon w celu utworzenia obszaru nazw Centrum zdarzeń. Następnie pobierz identyfikator obszaru nazw Centrum zdarzeń, aby użyć go później. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Udziel dostępu do tożsamości obszaru nazw centrów zdarzeń do magazynu kluczy

1. Uruchom następujące polecenie, aby utworzyć magazyn kluczy z **włączoną ochroną przed przeczyszczaniem** i **usuwaniem nietrwałym.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (LUB)    
    
    Uruchom następujące polecenie, aby zaktualizować **istniejący magazyn kluczy**. Przed uruchomieniem polecenia należy określić wartości nazw grup zasobów i magazynów kluczy. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ustaw zasady dostępu do magazynu kluczy, tak aby zarządzana tożsamość obszaru nazw Centrum zdarzeń mogła uzyskać dostęp do wartości klucza w magazynie kluczy. Użyj identyfikatora obszaru nazw Centrum zdarzeń z poprzedniej sekcji. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Szyfrowanie danych w obszarze nazw Centrum zdarzeń za pomocą klucza zarządzanego przez klienta z magazynu kluczy
Do tej pory wykonaliście następujące czynności: 

1. Utworzono obszar nazw premium z tożsamością zarządzaną.
2. Utwórz magazyn kluczy i przyznano dostęp do tożsamości zarządzanej do magazynu kluczy. 

W tym kroku zaktualizujesz obszar nazw Centrum zdarzeń z informacjami o magazynie kluczy. 

1. Utwórz plik JSON o nazwie **CreateEventHubClusterAndNamespace.json** z następującą zawartością: 

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

2. Tworzenie pliku parametru szablonu: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Zastąp następujące wartości: 
    > - `<EventHubsClusterName>`- Nazwa klastra Centrów Zdarzeń.        
    > - `<EventHubsNamespaceName>`- Nazwa obszaru nazw Centrów zdarzeń
    > - `<Location>`- Lokalizacja obszaru nazw Centrów zdarzeń
    > - `<KeyVaultName>`- Nazwa magazynu kluczy
    > - `<KeyName>`- Nazwa klucza w magazynie kluczy

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
3. Uruchom następujące polecenie programu PowerShell, aby wdrożyć szablon Menedżera zasobów. Zamień `{MyRG}` na nazwę grupy zasobów przed uruchomieniem polecenia. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Najlepszym rozwiązaniem jest zawsze włączyć dzienniki, jak pokazano w poprzedniej sekcji. Pomaga w śledzeniu działań, gdy szyfrowanie BYOK jest włączone. Pomaga również w rozwiązywaniu problemów.

Poniżej przedstawiono typowe kody błędów, których należy szukać po włączeniu szyfrowania BYOK.

| Akcja | Kod błędu | Wynikowy stan danych |
| ------ | ---------- | ----------------------- | 
| Usuwanie uprawnień zawijania/odwijania z magazynu kluczy | 403 |    Niedostępne |
| Usuń członkostwo w roli usługi AAD z podmiotu AAD, który udzielił uprawnień zawijania/rozpakowuj | 403 |  Niedostępne |
| Usuwanie klucza szyfrowania z magazynu kluczy | 404 | Niedostępne |
| Usuwanie magazynu kluczy | 404 | Niedostępne (zakłada, że jest włączone usuwanie nietrwałe, co jest wymaganym ustawieniem). |
| Zmiana okresu ważności klucza szyfrowania w taki sposób, że wygasł już | 403 |   Niedostępne  |
| Zmiana NBF (nie wcześniej) w taki sposób, że klucz szyfrowania klucza nie jest aktywny | 403 | Niedostępne  |
| Wybieranie opcji **Zezwalaj na usługi MSFT** dla zapory magazynu kluczy lub inne blokowanie dostępu sieciowego do magazynu kluczy z kluczem szyfrowania | 403 | Niedostępne |
| Przenoszenie magazynu kluczy do innej dzierżawy | 404 | Niedostępne |  
| Sporadyczny problem z siecią lub awaria dns/AAD/MSI |  | Dostępne przy użyciu klucza szyfrowania danych w pamięci podręcznej |

> [!IMPORTANT]
> Aby włączyć funkcję Geo-DR w obszarze nazw korzystającym z szyfrowania BYOK, pomocniczy obszar nazw do parowania musi znajdować się w dedykowanym klastrze i musi mieć włączoną tożsamość zarządzaną przypisaną do systemu. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:
- [Przegląd usługi Event Hubs](event-hubs-about.md)
- [Omówienie przechowalni kluczy](../key-vault/general/overview.md)




