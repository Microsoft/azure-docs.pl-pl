---
title: Rejestrowanie diagnostyczne dla Azure Analysis Services | Microsoft Docs
description: Opisuje sposób konfigurowania rejestrowania w celu monitorowania Azure Analysis Services serwera.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769215"
---
# <a name="setup-diagnostic-logging"></a>Konfigurowanie rejestrowania diagnostycznego

Ważnym elementem każdego rozwiązania Analysis Services jest monitorowanie działania serwerów. Usługi Azure Analysis Services są zintegrowane z Azure Monitor. Za [Azure Monitor zasobów](../azure-monitor/essentials/platform-logs-overview.md)można monitorować i wysyłać dzienniki do usługi Azure [Storage,](https://azure.microsoft.com/services/storage/)przesyłać je strumieniowo do usługi [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)i eksportować do Azure Monitor [dzienników.](../azure-monitor/overview.md)

![Rejestrowanie zasobów w magazynie, Event Hubs lub Azure Monitor dzienników](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co zostało zarejestrowane?

Możesz wybrać **kategorie Aparat,** **Usługa** **i** Metryki.

### <a name="engine"></a>Aparat

Wybranie **opcji Aparat** rejestruje wszystkie ustawienia [xEvent.](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) Nie można wybrać poszczególnych zdarzeń. 

|Kategorie XEvent |Nazwa zdarzenia  |
|---------|---------|
|Inspekcja zabezpieczeń    |   Logowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Wylogowanie inspekcji      |
|Inspekcja zabezpieczeń    |   Serwer inspekcji uruchamia się i zatrzymuje      |
|Raporty postępu     |   Początek raportu postępu      |
|Raporty postępu     |   Postęp zakończenia raportu      |
|Raporty postępu     |   Bieżący raport postępu      |
|Zapytania     |  Początek zapytania       |
|Zapytania     |   Koniec zapytania      |
|Polecenia     |  Początek polecenia       |
|Polecenia     |  Koniec polecenia       |
|Błędy & ostrzeżenia     |   Błąd      |
|Odnajdywanie     |   Odnajdywanie końca      |
|Powiadomienie     |    Powiadomienie     |
|Sesja     |  Inicjowanie sesji       |
|Blokady    |  Zakleszczenie       |
|Przetwarzanie zapytań     |   Początek zapytania se VertiPaq      |
|Przetwarzanie zapytań     |   VertiPaq SE Query End      |
|Przetwarzanie zapytań     |   Dopasowanie pamięci podręcznej zapytań se VertiPaq      |
|Przetwarzanie zapytań     |   Początek zapytania bezpośredniego      |
|Przetwarzanie zapytań     |  Koniec zapytania bezpośredniego       |

### <a name="service"></a>Usługa

|Nazwa operacji  |Występuje, gdy  |
|---------|---------|
|ResumeServer     |    Wznawianie serwera     |
|SuspendServer    |   Wstrzymywanie serwera      |
|DeleteServer     |    Usuwanie serwera     |
|RestartServer    |     Użytkownik ponownie uruchamia serwer za pomocą programu SSMS lub PowerShell    |
|GetServerLogFiles    |    Użytkownik eksportuje dziennik serwera za pomocą programu PowerShell     |
|ExportModel     |   Użytkownik eksportuje model w portalu przy użyciu funkcji Otwórz w Visual Studio     |

### <a name="all-metrics"></a>Wszystkie metryki

Kategoria Metryki rejestruje te same [metryki serwera](analysis-services-monitor.md#server-metrics) w tabeli AzureMetrics. Jeśli używasz skalowania [](analysis-services-scale-out.md) zapytań w celu oddzielenia metryk dla każdej repliki do odczytu, zamiast tego użyj tabeli AzureDiagnostics, gdzie **operationName** jest równe **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Konfigurowanie rejestrowania diagnostycznego

### <a name="azure-portal"></a>Azure Portal

1. Na [Azure Portal](https://portal.azure.com) > kliknij pozycję **Ustawienia diagnostyczne** na lewym pasku nawigacyjnym, a następnie kliknij pozycję Włącz **diagnostykę.**

    ![Włącz rejestrowanie zasobów dla Azure Cosmos DB w Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. W obszarze **Ustawienia diagnostyczne** określ następujące opcje: 

    * **Nazwa**. Wprowadź nazwę dzienników do utworzenia.

    * **Zarchiwizuj na koncie magazynu.** Aby użyć tej opcji, musisz mieć istniejące konto magazynu, z które chcesz nawiązać połączenie. Zobacz [Tworzenie konta magazynu.](../storage/common/storage-account-create.md) Postępuj zgodnie z instrukcjami, Resource Manager konto ogólnego przeznaczenia, a następnie wybierz konto magazynu, wracając do tej strony w portalu. Może upłynąć kilka minut, zanim konta nowo utworzonego magazynu pojawią się w menu rozwijanym.
    * **Przesyłanie strumieniowe do centrum zdarzeń**. Aby użyć tej opcji, do nawiązania połączenia potrzebna jest istniejąca przestrzeń nazw centrum zdarzeń i centrum zdarzeń. Aby dowiedzieć się więcej, zobacz temat [Tworzenie przestrzeni nazw usługi Event Hubs i centrum zdarzeń za pomocą witryny Azure Portal](../event-hubs/event-hubs-create.md). Następnie wróć do tej strony w portalu, aby wybrać przestrzeń nazw i nazwę zasad centrum zdarzeń.
    * **Wyślij do Azure Monitor (obszar roboczy usługi Log Analytics).** Aby użyć tej opcji, użyj istniejącego obszaru roboczego lub [utwórz nowy zasób](../azure-monitor/logs/quick-create-workspace.md) obszaru roboczego w portalu. Aby uzyskać więcej informacji na temat wyświetlania dzienników, zobacz [Wyświetlanie dzienników w obszarze roboczym](#view-logs-in-log-analytics-workspace) usługi Log Analytics w tym artykule.

    * **Aparat**. Wybierz tę opcję, aby rejestrować xEvents. W przypadku archiwizowania na koncie magazynu możesz wybrać okres przechowywania dla dzienników zasobów. Dzienniki są automatycznie wydeletowane po upływie okresu przechowywania.
    * **Usługa**. Wybierz tę opcję, aby rejestrować zdarzenia na poziomie usługi. W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dla dzienników zasobów. Dzienniki są automatycznie wydeletowane po upływie okresu przechowywania.
    * **Metryki**. Wybierz tę opcję, aby przechowywać pełne dane w [metryce](analysis-services-monitor.md#server-metrics). W przypadku archiwizowania na koncie magazynu można wybrać okres przechowywania dla dzienników zasobów. Dzienniki są automatycznie wydeletowane po upływie okresu przechowywania.

3. Kliknij pozycję **Zapisz**.

    Jeśli zostanie wyświetlony komunikat o błędzie "Nie można zaktualizować diagnostyki dla \<workspace name> programu . Subskrypcja nie \<subscription id> jest zarejestrowana do korzystania z microsoft.insights". Postępuj zgodnie [z instrukcjami Diagnostyka Azure](../azure-monitor/essentials/resource-logs.md) rozwiązywania problemów, aby zarejestrować konto, a następnie ponów próbę wykonania tej procedury.

    Jeśli chcesz zmienić sposób, w jaki dzienniki zasobów będą zapisywane w dowolnym momencie w przyszłości, możesz wrócić do tej strony, aby zmodyfikować ustawienia.

### <a name="powershell"></a>PowerShell

Poniżej znajdują się podstawowe polecenia, które przysuwają informacje. Jeśli potrzebujesz pomocy krok po kroku dotyczącej konfigurowania rejestrowania na koncie magazynu przy użyciu programu PowerShell, zobacz samouczek w dalszej części tego artykułu.

Aby włączyć metryki i rejestrowanie zasobów przy użyciu programu PowerShell, użyj następujących poleceń:

- Aby włączyć przechowywanie dzienników zasobów na koncie magazynu, użyj tego polecenia:

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

- Aby włączyć wysyłanie dzienników zasobów do obszaru roboczego usługi Log Analytics, użyj tego polecenia:

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
* Pamiętaj, aby ustawić okres przechowywania dla tak, aby stare dzienniki zostały usunięte z konta magazynu.

## <a name="view-logs-in-log-analytics-workspace"></a>Wyświetlanie dzienników w obszarze roboczym usługi Log Analytics

Metryki i zdarzenia serwera są zintegrowane z platformą xEvents w zasobie obszaru roboczego usługi Log Analytics w celu analizy side-by-side. Obszar roboczy usługi Log Analytics można również skonfigurować do odbierania zdarzeń z innych usług platformy Azure, zapewniając całościowy widok danych rejestrowania diagnostycznego w całej architekturze.

Aby wyświetlić dane diagnostyczne, w obszarze roboczym usługi Log Analytics otwórz **pozycję Dzienniki**  z menu po lewej stronie.

![Opcje przeszukiwania dzienników w Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

W konstruktorze zapytań rozwiń w **programie LogManagement**  >  **AzureDiagnostics**. Usługa AzureDiagnostics obejmuje zdarzenia aparatu i usługi. Zwróć uwagę, że zapytanie jest tworzone na bieżąco. Pole EventClass zawiera nazwy xEvent, które mogą wyglądać znajomo, jeśli używasz zdarzeń xEvent do rejestrowania \_ lokalnego. Kliknij **pozycję EventClass \_ s** lub jedną z nazw zdarzeń, a obszar roboczy usługi Log Analytics kontynuuje tworzenie zapytania. Pamiętaj, aby zapisywać zapytania, dzięki czemu będzie można skorzystać z nich później.

### <a name="example-queries"></a>Przykładowe zapytania

#### <a name="example-1"></a>Przykład 1

Poniższe zapytanie zwraca czasy trwania dla każdego zdarzenia zakończenia/odświeżenia zapytania dla modelowej bazy danych i serwera. W przypadku skalowania w zewnątrz wyniki są podzielone według repliki, ponieważ liczba replik jest uwzględniona w ServerName_s. Grupowanie według RootActivityId_g zmniejsza liczbę wierszy pobranych z interfejsu API REST usługi Diagnostyka Azure i pomaga utrzymać limity zgodnie z opisem w te [tematze Log Analytics Rate limits](https://dev.loganalytics.io/documentation/Using-the-API/Limits)(Limity szybkości usługi Log Analytics).

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

Poniższe zapytanie zwraca zużycie pamięci i QPU dla serwera. W przypadku skalowania w zewnątrz wyniki są podzielone według repliki, ponieważ liczba replik jest uwzględniona w ServerName_s.

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

Poniższe zapytanie zwraca liczbę wierszy odczytu/s Analysis Services liczników wydajności aparatu dla serwera.

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

Istnieją setki zapytań, z których można skorzystać. Aby dowiedzieć się więcej na temat zapytań, zobacz [Get started with Azure Monitor log queries (Wprowadzenie do zapytań dziennika Azure Monitor dziennika).](../azure-monitor/logs/get-started-queries.md)


## <a name="turn-on-logging-by-using-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

W tym szybkim samouczku utworzysz konto magazynu w tej samej subskrypcji i grupie zasobów co serwer usługi Analysis Service. Następnie użyj Set-AzDiagnosticSetting, aby włączyć rejestrowanie diagnostyczne, wysyłając dane wyjściowe do nowego konta magazynu.

### <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten samouczek, musisz mieć następujące zasoby:

* Istniejący serwer Azure Analysis Services serwera. Aby uzyskać instrukcje dotyczące tworzenia zasobu serwera, zobacz Tworzenie serwera w [programie Azure Portal](analysis-services-create-server.md)lub Create an Azure Analysis Services server by using PowerShell (Tworzenie serwera Azure Analysis Services [przy użyciu programu PowerShell).](analysis-services-create-powershell.md)

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

Następnie, aby określić subskrypcję skojarzoną z kontem Azure Analysis Services rejestrowania, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jeśli masz wiele subskrypcji skojarzonych z kontem, ważne jest, aby określić subskrypcję.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Tworzenie nowego konta magazynu dla dzienników

Dla dzienników możesz użyć istniejącego konta magazynu, pod warunkiem, że należy ono do tej samej subskrypcji co serwer. W tym samouczku utworzysz nowe konto magazynu przeznaczone do przechowywania Analysis Services danych. Aby to ułatwić, przechowujesz szczegóły konta magazynu w zmiennej o nazwie **sa**.

Należy również użyć tej samej grupy zasobów, która zawiera Analysis Services serwera. Zastąp wartości `awsales_resgroup` , `awsaleslogs` i `West Central US` własnymi wartościami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identyfikowanie konta serwera dla dzienników

Ustaw nazwę konta na zmienną o nazwie **account**, gdzie ResourceName jest nazwą konta.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie, użyj Set-AzDiagnosticSetting cmdlet wraz ze zmiennymi dla nowego konta magazynu, konta serwera i kategorii. Uruchom następujące polecenie, ustawiając **flagę -Enabled** na **$true**:

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

Te dane wyjściowe potwierdzają, że rejestrowanie jest teraz włączone dla serwera, zapisując informacje na koncie magazynu.

Można również ustawić zasady przechowywania dla dzienników, aby starsze dzienniki zostały automatycznie usunięte. Na przykład ustaw zasady przechowywania przy użyciu **flagi -RetentionEnabled** na **wartość $true**, a parametr **-RetentionInDays** na **wartość 90**. Dzienniki starsze niż 90 dni są automatycznie usuwane.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [Azure Monitor rejestrowania zasobów.](../azure-monitor/essentials/platform-logs-overview.md)

Zobacz [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) w pomocy programu PowerShell.
