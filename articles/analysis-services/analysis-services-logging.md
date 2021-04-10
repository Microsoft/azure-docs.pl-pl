---
title: Rejestrowanie diagnostyczne dla Azure Analysis Services | Microsoft Docs
description: Opisuje sposób konfigurowania rejestrowania do monitorowania serwera Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582698"
---
# <a name="setup-diagnostic-logging"></a>Konfigurowanie rejestrowania diagnostycznego

Ważnym elementem dowolnego Analysis Services rozwiązania jest monitorowanie sposobu działania serwerów. Usługi Azure Analysis Services są zintegrowane z usługą Azure Monitor. Za pomocą [dzienników zasobów Azure monitor](../azure-monitor/essentials/platform-logs-overview.md)można monitorować i wysyłać dzienniki do [usługi Azure Storage](https://azure.microsoft.com/services/storage/), przesyłać strumieniowo je do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportować je do [dzienników Azure monitor](../azure-monitor/overview.md).

![Rejestrowanie zasobów do magazynu, Event Hubs lub dzienników Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co zostało zarejestrowane?

Można wybrać kategorię **aparat**, **Usługa** i **metryki** .

### <a name="engine"></a>Aparat

Wybieranie dzienników **aparatów** wszystkie [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nie można wybrać pojedynczych zdarzeń. 

|Kategorie systemu XEvent |Nazwa zdarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Logowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Wylogowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Serwer inspekcji zostanie uruchomiony i zatrzymany      |
|Raporty postępu     |   Początek raportu postępu      |
|Raporty postępu     |   Koniec raportu postępu      |
|Raporty postępu     |   Bieżący raport postępu      |
|Zapytania     |  Początek zapytania       |
|Zapytania     |   Koniec zapytania      |
|Polecenia     |  Początek polecenia       |
|Polecenia     |  Koniec polecenia       |
|Błędy & ostrzeżeń     |   Błąd      |
|Odnajdywanie     |   Odnajdź zakończenie      |
|Powiadomienie     |    Powiadomienie     |
|Sesja     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenie       |
|Przetwarzanie zapytania     |   Rozpoczęcie zapytania VertiPaq SE      |
|Przetwarzanie zapytania     |   Koniec zapytania VertiPaq SE      |
|Przetwarzanie zapytania     |   Dopasowanie pamięci podręcznej zapytań VertiPaq SE      |
|Przetwarzanie zapytania     |   Rozpoczęcie zapytania bezpośredniego      |
|Przetwarzanie zapytania     |  Zakończenie zapytania bezpośredniego       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|ResumeServer     |    Wznów serwer     |
|SuspendServer    |   Wstrzymywanie serwera      |
|DeleteServer     |    Usuwanie serwera     |
|RestartServer    |     Użytkownik uruchamia ponownie serwer za pomocą narzędzia SSMS lub PowerShell    |
|GetServerLogFiles    |    Użytkownik eksportuje dziennik serwera za pośrednictwem programu PowerShell     |
|ExportModel     |   Użytkownik eksportuje model w portalu przy użyciu polecenia Otwórz w programie Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria metryki rejestruje te same [metryki serwera](analysis-services-monitor.md#server-metrics) w tabeli AzureMetrics. Jeśli używasz [skalowania](analysis-services-scale-out.md) w poziomie kwerendy i musisz oddzielić metryki dla każdej repliki odczytu, zamiast tego użyj tabeli AzureDiagnostics, gdzie **OperationName** jest równa **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurowanie rejestrowania diagnostycznego

### <a name="azure-portal"></a>Azure Portal

1. W [Azure Portal](https://portal.azure.com) > Server kliknij pozycję **Ustawienia diagnostyczne** na lewym pasku nawigacyjnym, a następnie kliknij pozycję **Włącz diagnostykę**.

    ![Włącz rejestrowanie zasobów dla Azure Cosmos DB w Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W obszarze **Ustawienia diagnostyczne** określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dzienników do utworzenia.

    * **Archiwizuj na koncie magazynu**. Aby można było użyć tej opcji, należy połączyć się z istniejącym kontem magazynu. Zobacz [Tworzenie konta magazynu](../storage/common/storage-account-create.md). Postępuj zgodnie z instrukcjami, aby utworzyć konto Menedżer zasobów, ogólnego przeznaczenia, a następnie wybierz konto magazynu, Wróć do tej strony w portalu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Przesyłaj strumieniowo do centrum zdarzeń**. Aby można było użyć tej opcji, potrzebna jest istniejąca przestrzeń nazw centrum zdarzeń oraz centrum zdarzeń, z którymi można nawiązać połączenie. Aby dowiedzieć się więcej, zobacz temat [Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń za pomocą witryny Azure Portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać przestrzeń nazw centrum zdarzeń i nazwę zasad.
    * **Wyślij do Azure monitor (log Analytics obszar roboczy)**. Aby użyć tej opcji, użyj istniejącego obszaru roboczego lub [Utwórz nowy zasób obszaru roboczego](../azure-monitor/logs/quick-create-workspace.md) w portalu. Aby uzyskać więcej informacji na temat wyświetlania dzienników, zobacz [Wyświetlanie dzienników w obszarze roboczym log Analytics](#view-logs-in-log-analytics-workspace) w tym artykule.

    * **Aparat**. Wybierz tę opcję, aby zarejestrować xEvents. W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników zasobów. Dzienniki są usuwane autokasowanie po upływie okresu przechowywania.
    * **Usługa**. Wybierz tę opcję, aby rejestrować zdarzenia poziomu usługi. W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników zasobów. Dzienniki są usuwane autokasowanie po upływie okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metrykach](analysis-services-monitor.md#server-metrics). W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dzienników zasobów. Dzienniki są usuwane autokasowanie po upływie okresu przechowywania.

3. Kliknij pozycję **Zapisz**.

    Jeśli zostanie wyświetlony komunikat o błędzie "nie można zaktualizować diagnostyki dla programu \<workspace name> . Subskrypcja \<subscription id> nie jest zarejestrowana w celu korzystania z usługi Microsoft. Insights. Postępuj zgodnie z instrukcjami [Diagnostyka Azure](../azure-monitor/essentials/resource-logs.md) , aby zarejestrować konto, a następnie spróbuj ponownie wykonać tę procedurę.

    Jeśli chcesz zmienić sposób zapisywania dzienników zasobów w dowolnym momencie w przyszłości, możesz powrócić do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Oto podstawowe polecenia, które pomogą Ci się uzyskać. Jeśli chcesz uzyskać pomoc krok po kroku dotyczącą konfigurowania rejestrowania na koncie magazynu przy użyciu programu PowerShell, zobacz samouczek w dalszej części tego artykułu.

Aby włączyć metryki i rejestrowanie zasobów przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć magazyn dzienników zasobów na koncie magazynu, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   Identyfikator konta magazynu jest identyfikatorem zasobu konta magazynu, do którego chcesz wysłać dzienniki.

- Aby włączyć przesyłanie strumieniowe dzienników zasobów do centrum zdarzeń, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Identyfikator reguły usługi Azure Service Bus jest ciągiem o następującym formacie:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Aby włączyć wysyłanie dzienników zasobów do obszaru roboczego Log Analytics, użyj tego polecenia:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Identyfikator zasobu obszaru roboczego usługi Log Analytics można uzyskać za pomocą następującego polecenia:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Można połączyć te parametry, aby uaktywnić wiele opcji danych wyjściowych.

### <a name="rest-api"></a>Interfejs API REST

Dowiedz się, jak [zmienić ustawienia diagnostyczne przy użyciu interfejsu API REST usługi Azure Monitor](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Szablon usługi Menedżer zasobów

Dowiedz się, jak [włączyć ustawienia diagnostyczne podczas tworzenia zasobu przy użyciu szablonu usługi Menedżer zasobów](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Zarządzanie dziennikami

Dzienniki są zwykle dostępne w ciągu kilku godzin od skonfigurowania rejestrowania. Zarządzanie dziennikami na Twoim koncie magazynu zależy od Ciebie:

* Aby zabezpieczyć dzienniki poprzez ograniczenie dostępu do nich, użyj standardowych metod kontroli dostępu platformy Azure.
* Usuń dzienniki, których już nie chcesz przechowywać na koncie magazynu.
* Pamiętaj, aby ustawić okres przechowywania dla starych dzienników z konta magazynu.

## <a name="view-logs-in-log-analytics-workspace"></a>Wyświetlanie dzienników w obszarze roboczym Log Analytics

Metryki i zdarzenia serwera są zintegrowane z xEvents w ramach zasobu obszaru roboczego Log Analytics w celu przeprowadzenia analizy równoległej. Obszar roboczy Log Analytics może być również skonfigurowany do odbierania zdarzeń z innych usług platformy Azure, co zapewnia całościowy wgląd w dane rejestrowania diagnostycznego w ramach architektury.

Aby wyświetlić dane diagnostyczne, w obszarze roboczym Log Analytics Otwórz pozycję **dzienniki**  w menu po lewej stronie.

![Opcje przeszukiwania dzienników w Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

W konstruktorze zapytań rozwiń węzeł **LogManagement**  >  **AzureDiagnostics**. AzureDiagnostics obejmuje zdarzenia aparatu i usługi. Zwróć uwagę na to, że zapytanie jest tworzone na bieżąco. Pole EventClass \_ s zawiera nazwy systemu xEvent, które mogą wyglądać dobrze, jeśli użyto xEvents do rejestrowania lokalnego. Kliknij pozycję **EventClass \_ s** lub jeden z nazw zdarzeń, a obszar roboczy log Analytics kontynuuje konstruowanie zapytania. Pamiętaj, aby zapisywać zapytania, dzięki czemu będzie można skorzystać z nich później.

### <a name="example-queries"></a>Przykładowe zapytania

#### <a name="example-1"></a>Przykład 1

Następujące zapytanie zwraca czasy trwania dla każdego zdarzenia zakończenia/odświeżenia zapytania dla bazy danych i serwera modelu. W przypadku skalowania wyniki są rozbite przez replikę, ponieważ numer repliki jest uwzględniony w ServerName_s. Grupowanie według RootActivityId_g zmniejsza liczbę wierszy pobieranych z interfejsu API REST Diagnostyka Azure i pomaga w granicach, zgodnie z opisem w [log Analytics limitów szybkości](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Przykład 2

Następujące zapytanie zwraca pamięć i QPU użycie dla serwera. W przypadku skalowania wyniki są rozbite przez replikę, ponieważ numer repliki jest uwzględniony w ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Przykład 3

Następujące zapytanie zwraca liczniki wydajności aparatu Analysis Services odczyt/s dla serwera.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Istnieją setki zapytań, z których można skorzystać. Aby dowiedzieć się więcej o zapytaniach, zobacz Rozpoczynanie [pracy z dziennikami Azure monitor](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

W tym szybkim samouczku utworzysz konto magazynu w tej samej subskrypcji i grupie zasobów co serwer usługi Analysis Service. Następnie użyj Set-AzDiagnosticSetting, aby włączyć rejestrowanie diagnostyczne, wysyłając dane wyjściowe do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten samouczek, musisz dysponować następującymi zasobami:

* Istniejący serwer Azure Analysis Services. Aby uzyskać instrukcje dotyczące tworzenia zasobu serwera, zobacz [Tworzenie serwera w Azure Portal](analysis-services-create-server.md)lub [Tworzenie serwera Azure Analysis Services przy użyciu programu PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Nawiązywanie połączenia z subskrypcjami

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:  

```powershell
Connect-AzAccount
```

W podręcznym oknie przeglądarki wprowadź nazwę użytkownika i hasło dla konta platformy Azure. Program Azure PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem, i domyślnie użyje pierwszej.

Jeśli masz wiele subskrypcji, określ konkretne konto, które zostało użyte do utworzenia usługi Azure Key Vault. Wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

```powershell
Get-AzSubscription
```

Następnie, aby określić subskrypcję skojarzoną z zalogowanym kontem Azure Analysis Services, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, ważne jest, aby określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Możesz użyć istniejącego konta magazynu dla dzienników, pod warunkiem, że znajduje się on w tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu przeznaczone do Analysis Services dzienników. Aby to ułatwić, zapisujesz szczegóły konta magazynu w zmiennej o nazwie **sa**.

Należy także użyć tej samej grupy zasobów, która zawiera serwer Analysis Services. Podstaw wartości dla `awsales_resgroup` , `awsaleslogs` i `West Central US` z własnymi wartościami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identyfikowanie konta serwera dla dzienników

Ustaw nazwę konta na zmienną o nazwie **konto**, gdzie resourceName jest nazwą konta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie, należy użyć polecenia cmdlet Set-AzDiagnosticSetting wraz ze zmiennymi dla nowego konta magazynu, konta serwera i kategorii. Uruchom następujące polecenie, ustawiając flagę **-Enabled** na **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Dane wyjściowe powinny wyglądać następująco:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Dane wyjściowe potwierdzają, że rejestrowanie jest teraz włączone dla serwera, co pozwala na zapisywanie informacji na koncie magazynu.

Można również ustawić zasady przechowywania dla dzienników, aby starsze dzienniki były automatycznie usuwane. Na przykład ustaw zasady przechowywania przy użyciu flagi **-retentionenable** do **$true** i ustaw parametr **-RetentionInDays** na **90**. Dzienniki starsze niż 90 dni są usuwane automatycznie.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [rejestrowaniu zasobów Azure monitor](../azure-monitor/essentials/platform-logs-overview.md).

Zobacz [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) w pomocy programu PowerShell.