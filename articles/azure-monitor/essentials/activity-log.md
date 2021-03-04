---
title: Dziennik aktywności platformy Azure
description: Wyświetl dziennik aktywności platformy Azure i wyślij go do Azure Monitor dzienników, platformy Azure Event Hubs i usługi Azure Storage.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.openlocfilehash: 557fc6e358f371b47c1df314508e3565d843a28c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049189"
---
# <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure
Dziennik aktywności jest to [dziennik platformy](./platform-logs-overview.md) Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Zawiera on takie informacje jak czas zmodyfikowania zasobu lub czas uruchomienia maszyny wirtualnej. Dziennik aktywności można wyświetlić w Azure Portal lub pobrać wpisów przy użyciu programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać dodatkowe funkcje, należy utworzyć ustawienie diagnostyczne służące do wysyłania dziennika aktywności do [dzienników Azure monitor](../logs/data-platform-logs.md), do usługi Azure Event Hubs do przekazywania poza platformę Azure lub do usługi Azure Storage w celu archiwizacji. Ten artykuł zawiera szczegółowe informacje na temat wyświetlania dziennika aktywności i wysyłania go do różnych miejsc docelowych.

Zobacz [Tworzenie ustawień diagnostycznych, aby wysyłać dzienniki platformy i metryki do różnych miejsc docelowych](./diagnostic-settings.md) w celu uzyskania szczegółowych informacji dotyczących tworzenia ustawień diagnostycznych.

> [!NOTE]
> Wpisy w dzienniku aktywności są generowane przez system i nie można ich zmienić ani usunąć.

## <a name="view-the-activity-log"></a>Wyświetlanie dziennika aktywności
Dostęp do dziennika aktywności można uzyskać z poziomu większości menu w witrynie Azure Portal. Menu, z poziomu którego jest otwierany, określa jego filtr początkowy. Jeśli otworzysz go z menu **monitor** , jedynym filtrem będzie w subskrypcji. Jeśli otworzysz go z menu zasobów, filtr zostanie ustawiony na ten zasób. Filtr można zawsze zmienić, aby wyświetlić wszystkie pozostałe wpisy. Kliknij przycisk **Dodaj filtr** , aby dodać do filtru dodatkowe właściwości.

![Wyświetl dziennik aktywności](./media/activity-log/view-activity-log.png)

Aby uzyskać opis kategorii dzienników aktywności, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md#categories).

### <a name="view-change-history"></a>Wyświetl historię zmian

W przypadku niektórych zdarzeń można wyświetlić historię zmian, która pokazuje, jakie zmiany wystąpiły w czasie tego zdarzenia. Wybierz zdarzenie z dziennika aktywności, do którego chcesz się zapoznać. Wybierz kartę **historia zmian (wersja zapoznawcza)** , aby wyświetlić wszystkie skojarzone zmiany z tym zdarzeniem.

![Zmień listę historii dla zdarzenia](media/activity-log/change-history-event.png)

Jeśli ze zdarzeniem są skojarzone jakiekolwiek zmiany, zostanie wyświetlona lista zmian, które można wybrać. Spowoduje to otwarcie strony **historia zmian (wersja zapoznawcza)** . Na tej stronie są widoczne zmiany w zasobie. W poniższym przykładzie można zobaczyć nie tylko zmiany rozmiarów maszyny wirtualnej, ale informacje o poprzednim rozmiarze maszyny wirtualnej były przed zmianą i zmianami w nim. Aby dowiedzieć się więcej na temat historii zmian, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).

![Strona historii zmian przedstawiająca różnice](media/activity-log/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>Inne metody pobierania zdarzeń dziennika aktywności
Możesz również uzyskać dostęp do zdarzeń dziennika aktywności przy użyciu poniższych metod.

- Użyj polecenia cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) , aby pobrać dziennik aktywności z programu PowerShell. Zobacz [przykłady Azure monitor programu PowerShell](../powershell-samples.md#retrieve-activity-log).
- Użyj polecenia [AZ monitor Activity-Log](/cli/azure/monitor/activity-log) , aby pobrać dziennik aktywności z interfejsu CLI.  Zobacz [przykłady interfejsu wiersza polecenia Azure monitor](../cli-samples.md#view-activity-log).
- Użyj [interfejsu API REST Azure monitor](/rest/api/monitor/) , aby pobrać dziennik aktywności z klienta Rest. 


## <a name="send-to-log-analytics-workspace"></a>Wysyłanie do obszaru roboczego usługi Log Analytics
 Wyślij dziennik aktywności do obszaru roboczego Log Analytics, aby włączyć funkcje [dzienników Azure monitor](../logs/data-platform-logs.md) , które obejmują następujące elementy:

- Skorelowanie danych dziennika aktywności z innymi danymi monitorowania zbieranymi przez Azure Monitor.
- Konsolidowanie wpisów dzienników z wielu subskrypcji platformy Azure i dzierżawców w jedną lokalizację do analizy razem.
- Za pomocą zapytań dzienników można wykonywać złożone analizy i uzyskiwać szczegółowe informacje o wpisach dziennika aktywności.
- Alerty dzienników z wpisami aktywności umożliwiają bardziej złożoną logikę alertów.
- Przechowywanie wpisów dziennika aktywności przez dłużej niż 90 dni.
- Brak opłat za pozyskiwanie danych dziennika aktywności przechowywanych w obszarze roboczym Log Analytics.
- Nie są naliczane opłaty za przechowywanie danych do 90 dni dla danych dziennika aktywności przechowywanych w obszarze roboczym Log Analytics.

[Utwórz ustawienie diagnostyczne](./diagnostic-settings.md) , aby wysłać dziennik aktywności do obszaru roboczego log Analytics. Dziennik aktywności można wysłać z dowolnej pojedynczej subskrypcji do maksymalnie pięciu obszarów roboczych. Zbieranie dzienników z dzierżaw wymaga usługi [Azure Lighthouse](../../lighthouse/index.yml).

Dane dziennika aktywności w obszarze roboczym Log Analytics są przechowywane w tabeli o nazwie *Azure* , którą można pobrać z [zapytaniem dziennika](../logs/log-query-overview.md) w [log Analytics](../logs/log-analytics-tutorial.md). Struktura tej tabeli różni się w zależności od [kategorii wpisu dziennika](activity-log-schema.md). Aby uzyskać opis właściwości tabeli, zobacz [informacje dotyczące Azure monitor danych](/azure/azure-monitor/reference/tables/azureactivity).

Aby na przykład wyświetlić liczbę rekordów dziennika aktywności dla każdej kategorii, użyj poniższego zapytania.

```kusto
AzureActivity
| summarize count() by CategoryValue
```

Aby pobrać wszystkie rekordy z kategorii administracyjnej, należy użyć poniższego zapytania.

```kusto
AzureActivity
| where CategoryValue == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>Wyślij do Event Hubs platformy Azure
Wyślij dziennik aktywności do usługi Azure Event Hubs w celu wysłania wpisów poza platformą Azure, na przykład do innych rozwiązań usługi log Analytics SIEM lub innych. Zdarzenia dziennika aktywności z centrów zdarzeń są używane w formacie JSON z `records` elementem zawierającym rekordy w poszczególnych ładunku. Schemat jest zależny od kategorii i został opisany w [schemacie z konta magazynu i centrów zdarzeń](activity-log-schema.md).

Poniżej przedstawiono przykładowe dane wyjściowe z Event Hubs dla dziennika aktywności:

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>Wyślij do usługi Azure Storage
Wyślij dziennik aktywności do konta usługi Azure Storage, jeśli chcesz przechowywać dane dziennika dłużej niż 90 dni na potrzeby inspekcji, statycznej analizy lub kopii zapasowej. Jeśli musisz tylko zachować zdarzenia przez 90 dni lub mniej, nie musisz konfigurować archiwizowania do konta magazynu, ponieważ zdarzenia dziennika aktywności są przechowywane na platformie Azure przez 90 dni.

Po wysłaniu dziennika aktywności do platformy Azure kontener magazynu jest tworzony na koncie magazynu zaraz po wystąpieniu zdarzenia. Obiekty blob w kontenerze używają następującej konwencji nazewnictwa:

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Na przykład konkretny obiekt BLOB może mieć nazwę podobną do następującej:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

Każdy obiekt blob PT1H.json zawiera obiekt blob JSON ze zdarzeniami, które wystąpiły w ciągu godziny określonej w adresie URL obiektu blob (na przykład h = 12). Zdarzenia występujące w danej chwili są na bieżąco dołączane do pliku PT1H.json. Wartość minuta (m = 00) jest zawsze równa 00, ponieważ zdarzenia dziennika zasobów są podzielone na pojedyncze obiekty blob na godzinę.

Każde zdarzenie jest przechowywane w PT1H.jsw pliku o następującym formacie, który używa wspólnego schematu najwyższego poziomu, ale jest unikatowa dla każdej kategorii, zgodnie z opisem w  [schemacie dziennika aktywności](activity-log-schema.md).

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>Metody starszej kolekcji
W tej sekcji opisano starsze metody zbierania dziennika aktywności, które były używane przed ustawieniami diagnostycznymi. Jeśli używasz tych metod, rozważ przechodzenie do ustawień diagnostycznych, które zapewniają lepszą funkcjonalność i spójność z dziennikami zasobów.

### <a name="log-profiles"></a>Profile dziennika
Profile dzienników są starszą metodą wysyłania dziennika aktywności do usługi Azure Storage lub centrów zdarzeń. Poniższa procedura umożliwia kontynuowanie pracy z profilem dziennika lub wyłączenie go w celu migracji do ustawień diagnostycznych.

1. Z menu **Azure monitor** w Azure Portal wybierz pozycję **Dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/activity-log/diagnostic-settings.png)

4. Kliknij transparent purpurowy dla starszego środowiska.

    ![Starsze środowisko](media/activity-log/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>Konfigurowanie profilu dziennika przy użyciu programu PowerShell

Jeśli profil dziennika już istnieje, najpierw musisz usunąć istniejący profil dziennika, a następnie utworzyć nowy.

1. Użyj `Get-AzLogProfile` , aby określić, czy profil dziennika istnieje.  W przypadku istnienia profilu dziennika należy zwrócić uwagę na Właściwość *name* .

1. Użyj polecenia `Remove-AzLogProfile` , aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Użyj `Add-AzLogProfile` , aby utworzyć nowy profil dziennika:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Właściwość | Wymagane | Opis |
    | --- | --- | --- |
    | Nazwa |Tak |Nazwa profilu dziennika. |
    | StorageAccountId |Nie |Identyfikator zasobu konta magazynu, w którym ma zostać zapisany dziennik aktywności. |
    | serviceBusRuleId |Nie |Service Bus Identyfikator reguły dla przestrzeni nazw Service Bus, w której chcesz utworzyć Centra zdarzeń. Jest to ciąg o formacie: `{service bus resource ID}/authorizationrules/{key name}` . |
    | Lokalizacja |Tak |Rozdzielana przecinkami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. |
    | RetentionInDays |Tak |Liczba dni, przez jaką zdarzenia mają być przechowywane na koncie magazynu, z zakresu od 1 do 365. Wartość zero przechowuje dzienniki w nieskończoność. |
    | Kategoria |Nie |Rozdzielana przecinkami lista kategorii zdarzeń, które mają być zbierane. Możliwe wartości to _Write_, _delete_ i _Action_. |

### <a name="example-script"></a>Przykładowy skrypt
Poniżej znajduje się przykładowy skrypt programu PowerShell służący do tworzenia profilu dziennika, który zapisuje dziennik aktywności zarówno do konta magazynu, jak i do centrum zdarzeń.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -StorageAccountId  $storageAccountId -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurowanie profilu dziennika przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli profil dziennika już istnieje, należy najpierw usunąć istniejący profil dziennika, a następnie utworzyć nowy profil dziennika.

1. Użyj `az monitor log-profiles list` , aby określić, czy profil dziennika istnieje.
2. Użyj polecenia `az monitor log-profiles delete --name "<log profile name>` , aby usunąć profil dziennika przy użyciu wartości z właściwości *Nazwa* .
3. Użyj `az monitor log-profiles create` , aby utworzyć nowy profil dziennika:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Właściwość | Wymagane | Opis |
    | --- | --- | --- |
    | name |Tak |Nazwa profilu dziennika. |
    | Magazyn — identyfikator konta |Tak |Identyfikator zasobu konta magazynu, do którego mają zostać zapisane dzienniki aktywności. |
    | locations |Tak |Rozdzielana spacjami lista regionów, dla których chcesz zbierać zdarzenia dziennika aktywności. Możesz wyświetlić listę wszystkich regionów dla subskrypcji przy użyciu programu `az account list-locations --query [].name` . |
    | dni |Tak |Liczba dni przechowywania zdarzeń między 1 a 365. Wartość zerowa spowoduje przechowywanie dzienników w nieskończoność (w nieskończoność).  Jeśli wartość jest równa zero, wartość parametru enabled powinna być równa false. |
    |enabled | Tak |Prawda czy fałsz?  Służy do włączania lub wyłączania zasad przechowywania.  W przypadku wartości true wartość parametru Days musi być większa niż 0.
    | categories |Tak |Rozdzielana spacjami lista kategorii zdarzeń, które powinny być zbierane. Możliwe wartości to Write, DELETE i Action. |


### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Starsza metoda wysyłania dziennika aktywności do obszaru roboczego Log Analytics nawiązuje połączenie dziennika w konfiguracji obszaru roboczego. 

1. Z menu **log Analytics obszary robocze** w Azure Portal wybierz obszar roboczy, w którym ma zostać zebrany dziennik aktywności.
1. W sekcji **źródła danych obszaru roboczego** menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz połączyć.

    ![Zrzut ekranu przedstawia Log Analytics obszar roboczy z wybranym dziennikiem aktywności platformy Azure.](media/activity-log/workspaces.png)

2. Kliknij przycisk **Połącz** , aby połączyć dziennik aktywności w subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączona z innym obszarem roboczym, kliknij przycisk **Rozłącz** jako pierwszy, aby rozłączyć ją.

    ![Połącz obszary robocze](media/activity-log/connect-workspace.png)


Aby wyłączyć to ustawienie, wykonaj tę samą procedurę i kliknij przycisk **Rozłącz** , aby usunąć subskrypcję z obszaru roboczego.

### <a name="data-structure-changes"></a>Zmiany struktury danych
Ustawienia diagnostyczne wysyłają te same dane, co w przypadku starszej metody używanej do wysyłania dziennika aktywności ze zmianami struktury tabeli *usługi Azure* .

Kolumny w poniższej tabeli zostały zaniechane w zaktualizowanym schemacie. Nadal istnieją w *usłudze Azure* , ale nie będą miały żadnych danych. Zastąpienie tych kolumn nie jest nowe, ale zawierają te same dane, co kolumna przestarzała. Są one w innym formacie, więc może być konieczne zmodyfikowanie zapytań dziennika, które z nich korzystają. 

| Kolumna przestarzała | Kolumna zastępująca |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| Kategoria          | CategoryValue          |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> W niektórych przypadkach wartości w tych kolumnach mogą być napisane wielkimi literami. Jeśli masz zapytanie zawierające te kolumny, do wykonania porównania bez uwzględniania wielkości liter należy użyć [operatora =~](/azure/kusto/query/datatypes-string-operators).

Następująca kolumna została dodana do *usługi Azure* w zaktualizowanym schemacie:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Activity Log Analytics rozwiązanie do monitorowania
Rozwiązanie do monitorowania Log Analytics platformy Azure zostanie wycofane wkrótce i zastąpione przez skoroszyt przy użyciu zaktualizowanego schematu w obszarze roboczym Log Analytics. Nadal możesz użyć rozwiązania, jeśli jest już włączone, ale może być używane tylko wtedy, gdy zbierasz dziennik aktywności przy użyciu starszych ustawień. 



### <a name="use-the-solution"></a>Korzystanie z rozwiązania
Do monitorowania są dostępne rozwiązania z menu **Monitoruj** w Azure Portal. Wybierz pozycję **więcej** w sekcji **szczegółowe informacje** , aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **dzienniki aktywności platformy Azure** przedstawia liczbę rekordów operacji **platformy Azure** w obszarze roboczym.

![Kafelek dzienników aktywności platformy Azure](media/activity-log/azure-activity-logs-tile.png)


Kliknij kafelek **dzienniki aktywności platformy Azure** , aby otworzyć widok **dzienniki aktywności platformy Azure** . Widok zawiera części wizualizacji w poniższej tabeli. Każda część wykazuje do 10 elementów spełniających kryteria tych części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie zgodne rekordy, klikając pozycję **Zobacz wszystko** u dołu części.

![Pulpit nawigacyjny dzienników aktywności platformy Azure](media/activity-log/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Włącz rozwiązanie dla nowych subskrypcji
Nie będzie już można dodawać rozwiązania Logs dzienników aktywności do subskrypcji przy użyciu Azure Portal. Możesz dodać go przy użyciu poniższej procedury z szablonem Menedżer zasobów. 

1. Skopiuj poniższy kod JSON do pliku o nazwie *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Wdróż szablon przy użyciu następujących poleceń programu PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z omówieniem dzienników platformy](./platform-logs-overview.md)
* [Przejrzyj schemat zdarzeń dziennika aktywności](activity-log-schema.md)
* [Utwórz ustawienie diagnostyczne, aby wysyłać dzienniki aktywności do innych miejsc docelowych](./diagnostic-settings.md)