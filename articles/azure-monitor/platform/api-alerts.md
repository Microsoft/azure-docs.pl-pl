---
title: Korzystanie z interfejsu API REST alertów Log Analytics
description: Interfejs API REST alertów Log Analytics umożliwia tworzenie i zarządzanie alertami w Log Analytics, które są częścią Log Analytics.  Ten artykuł zawiera szczegółowe informacje o interfejsie API i kilku przykładach służących do wykonywania różnych operacji.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: dce340db90c1528c46c1be0bc172751a04feaf31
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294079"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Tworzenie reguł alertów i zarządzanie nimi w Log Analytics z interfejsem API REST 

> [!IMPORTANT]
> Zgodnie z [ogłoszonymi](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)obszarami roboczymi usługi log Analytics utworzonymi po *1 czerwca 2019,* zarządzanie regułami alertów przy użyciu bieżącego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules/). Zachęcamy klientów do [przełączania się do bieżącego interfejsu API](./alerts-log-api-switch.md) w starszych obszarach roboczych, aby korzystać z [zalet](./alerts-log-api-switch.md#benefits)Azure monitor scheduledQueryRules. W tym artykule opisano zarządzanie regułami alertów przy użyciu starszego interfejsu API.

Interfejs API REST alertów Log Analytics umożliwia tworzenie i zarządzanie alertami w programie Log Analytics.  Ten artykuł zawiera szczegółowe informacje o interfejsie API i kilku przykładach służących do wykonywania różnych operacji.

Interfejs API REST usługi Log Analytics Search to RESTful i można uzyskać do niego dostęp za pośrednictwem interfejsu API REST Azure Resource Manager. W tym dokumencie znajdują się przykłady, do których dostęp do interfejsu API uzyskuje się za pomocą wiersza polecenia programu PowerShell przy użyciu  [ARMClient](https://github.com/projectkudu/ARMClient), narzędzia wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Korzystanie z ARMClient i programu PowerShell to jedna z wielu opcji dostępu do interfejsu API wyszukiwania Log Analytics. Za pomocą tych narzędzi można użyć interfejsu API Azure Resource Manager RESTful, aby umożliwić wywoływanie Log Analytics obszarów roboczych i wykonywać w nich polecenia przeszukiwania. Interfejs API będzie wyprowadzał wyniki wyszukiwania w formacie JSON, co pozwala na używanie wyników wyszukiwania na wiele różnych sposobów programistycznie.

## <a name="prerequisites"></a>Wymagania wstępne
Obecnie alerty mogą być tworzone tylko przy użyciu zapisanego wyszukiwania w Log Analytics.  Aby uzyskać więcej informacji, możesz zapoznać się z [interfejsem API REST przeszukiwania dzienników](../log-query/log-query-overview.md) .

## <a name="schedules"></a>Harmonogramy
Zapisane wyszukiwanie może mieć co najmniej jeden harmonogram. Harmonogram definiuje częstotliwość uruchamiania wyszukiwania oraz przedział czasu, w którym zostały zidentyfikowane kryteria.
Harmonogramy mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| Interwał |Częstotliwość uruchamiania wyszukiwania. Mierzona w minutach. |
| QueryTimeSpan |Przedział czasu, w którym są oceniane kryteria. Musi być równa lub większa niż interwał. Mierzona w minutach. |
| Wersja |Używana wersja interfejsu API.  Obecnie ta wartość powinna zawsze wynosić 1. |

Rozważmy na przykład zapytanie dotyczące zdarzenia z interwałem wynoszącym 15 minut i przedziału czasu wynoszącym 30 minut. W takim przypadku zapytanie powinno być uruchamiane co 15 minut, a alert zostanie wyzwolony, jeśli kryteria nadal rozwiązują wartość true w okresie 30-minutowym.

### <a name="retrieving-schedules"></a>Pobieranie harmonogramów
Użyj metody get, aby pobrać wszystkie harmonogramy dla zapisanego wyszukiwania.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20
```

Użyj metody get z IDENTYFIKATORem harmonogramu, aby pobrać konkretny harmonogram dla zapisanego wyszukiwania.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

Poniżej znajduje się Przykładowa odpowiedź na harmonogram.

```json
{
   "value": [{
      "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
      "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
      "properties": {
         "Interval": 15,
         "QueryTimeSpan": 15,
         "Enabled": true,
      }
   }]
}
```

### <a name="creating-a-schedule"></a>Tworzenie harmonogramu
Użyj metody PUT z unikatowym IDENTYFIKATORem harmonogramu, aby utworzyć nowy harmonogram.  Dwa harmonogramy nie mogą mieć tego samego identyfikatora, nawet jeśli są skojarzone z różnymi zapisanymi wyszukiwaniami.  Podczas tworzenia harmonogramu w konsoli Log Analytics jest tworzony identyfikator GUID dla identyfikatora harmonogramu.

> [!NOTE]
> Nazwa wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API Log Analytics muszą być pisane małymi literami.

```powershell
$scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="editing-a-schedule"></a>Edytowanie harmonogramu
Użyj metody PUT z istniejącym IDENTYFIKATORem harmonogramu dla tego samego zapisanego wyszukiwania, aby zmodyfikować ten harmonogram. w przykładzie poniżej harmonogram jest wyłączony. Treść żądania musi zawierać element *ETag* harmonogramu.

```powershell
$scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson
```

### <a name="deleting-schedules"></a>Usuwanie harmonogramów
Użyj metody Delete z IDENTYFIKATORem harmonogramu, aby usunąć harmonogram.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20
```

## <a name="actions"></a>Akcje
Harmonogram może mieć wiele akcji. Akcja może definiować jeden lub więcej procesów do wykonania, takich jak wysyłanie wiadomości e-mail lub Uruchamianie elementu Runbook, lub Definiowanie progu, który określa, kiedy wyniki wyszukiwania pasują do niektórych kryteriów.  Niektóre akcje definiują oba elementy, aby procesy były wykonywane po spełnieniu progu.

Wszystkie akcje mają właściwości w poniższej tabeli.  Różne typy alertów mają różne dodatkowe właściwości, które opisano poniżej.

| Właściwość | Opis |
|:--- |:--- |
| `Type` |Typ akcji.  Obecnie możliwe wartości to alert i element webhook. |
| `Name` |Nazwa wyświetlana alertu. |
| `Version` |Używana wersja interfejsu API.  Obecnie ta wartość powinna zawsze wynosić 1. |

### <a name="retrieving-actions"></a>Pobieranie akcji

Użyj metody get, aby pobrać wszystkie akcje dla harmonogramu.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20
```

Użyj metody get z IDENTYFIKATORem akcji, aby pobrać konkretną akcję dla harmonogramu.

```powershell
armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20
```

### <a name="creating-or-editing-actions"></a>Tworzenie lub Edytowanie akcji
Użyj metody PUT z IDENTYFIKATORem akcji, który jest unikatowy dla harmonogramu, aby utworzyć nową akcję.  Po utworzeniu akcji w konsoli Log Analytics identyfikator GUID dotyczy identyfikatora akcji.

> [!NOTE]
> Nazwa wszystkich zapisanych wyszukiwań, harmonogramów i akcji utworzonych za pomocą interfejsu API Log Analytics muszą być pisane małymi literami.

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji dla tego samego zapisanego wyszukiwania, aby zmodyfikować ten harmonogram.  Treść żądania musi zawierać element ETag harmonogramu.

Format żądania tworzenia nowej akcji różni się w zależności od typu akcji, aby te przykłady zostały podane w poniższych sekcjach.

### <a name="deleting-actions"></a>Usuwanie akcji

Użyj metody Delete z IDENTYFIKATORem akcji, aby usunąć akcję.

```powershell
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20
```

### <a name="alert-actions"></a>Akcje alertów
Harmonogram powinien mieć jedną i tylko jedną akcję alertu.  Akcje alertów mają co najmniej jedną sekcję z poniższej tabeli.  Każdy z nich został szczegółowo opisany poniżej.

| Sekcja | Opis | Użycie |
|:--- |:--- |:--- |
| Próg |Kryteria dla momentu uruchomienia akcji.| Wymagane dla każdego alertu, przed lub po rozszerzeniu na platformę Azure. |
| Ważność |Etykieta używana do klasyfikowania alertu po wyzwoleniu.| Wymagane dla każdego alertu, przed lub po rozszerzeniu na platformę Azure. |
| Pomiń |Opcja zatrzymania powiadomień z alertu. | Opcjonalne dla każdego alertu, przed lub po rozszerzeniu na platformę Azure. |
| Grupy akcji |Identyfikatory obiektu akcji platformy Azure, w przypadku których wymagane są akcje, takich jak wiadomości E-mail, SMSs, połączenia głosowe, elementy webhook, Runbook automations, łączniki narzędzia ITSM itp.| Wymagane po rozszerzeniu alertów na platformę Azure|
| Dostosuj akcje|Modyfikuj wyjście standardowe dla akcji Select z elementu ActionManager| Opcjonalne dla każdego alertu można użyć po rozszerzeniu alertów na platformę Azure. |

### <a name="thresholds"></a>Progi
Akcja alertu powinna mieć jeden i tylko jeden próg.  Gdy wyniki zapisanego wyszukiwania są zgodne z progiem w akcji skojarzonej z tym wyszukiwaniem, zostaną uruchomione wszystkie inne procesy w tej akcji.  Akcja może również zawierać tylko wartość progową, aby można jej było używać z akcjami innych typów, które nie zawierają progów.

Progi mają właściwości podane w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| `Operator` |Operator dla porównania progu. <br> gt = większe niż <br> lt = mniejsze niż |
| `Value` |Wartość progowa. |

Rozważmy na przykład zapytanie dotyczące zdarzenia z interwałem wynoszącym 15 minut, przedziału czasu wynoszącym 30 minut i Prógem większym niż 10. W takim przypadku zapytanie powinno być uruchamiane co 15 minut, a alert zostanie wyzwolony, jeśli zwróci 10 zdarzeń, które zostały utworzone w okresie 30-minutowym.

Poniżej znajduje się Przykładowa odpowiedź dla akcji z tylko progiem.  

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Version": 1
}
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby utworzyć nową akcję progową dla harmonogramu.  

```powershell
$thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować akcję progową dla harmonogramu.  Treść żądania musi zawierać element ETag akcji.

```powershell
$thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson
```

#### <a name="severity"></a>Ważność
Log Analytics pozwala na klasyfikowanie alertów do kategorii w celu łatwiejszego zarządzania i klasyfikacja. Zdefiniowana ważność alertu to: informacyjny, ostrzegawczy i krytyczny. Są one mapowane na znormalizowaną skalę ważności alertów systemu Azure w następujący sposób:

|Poziom ważności Log Analytics  |Poziom ważności alertów platformy Azure  |
|---------|---------|
|`critical` |Ważność 0|
|`warning` |Ważność 1|
|`informational` | Ważność 2|

Poniżej znajduje się Przykładowa odpowiedź dla akcji z tylko progiem i ważnością. 

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Severity": "critical",
   "Version": 1
}
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby utworzyć nową akcję dla harmonogramu o ważności.  

```powershell
$thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować akcję ważności dla harmonogramu.  Treść żądania musi zawierać element ETag akcji.

```powershell
$thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson
```

#### <a name="suppress"></a>Pomiń
Alerty zapytań opartych na Log Analytics będą wyzwalane za każdym razem, gdy zostanie spełniony lub przekroczony próg. Na podstawie logiki implikowanej w zapytaniu może to spowodować, że alert zostanie wyzwolony dla wielu interwałów, a więc powiadomienia są również stale wysyłane. Aby zapobiec takiemu scenariuszowi, użytkownik może ustawić opcję pomijania, która instruuje Log Analytics, aby czekać przez określony czas, zanim powiadomienie zostanie wyzwolone po raz drugi dla reguły alertu. Tak więc jeśli Pomiń jest ustawiony przez 30 minut; następnie alert zostanie uruchomiony po raz pierwszy i będzie wysyłanych skonfigurowanych powiadomień. Następnie poczekaj 30 minut, zanim zostanie ponownie użyta powiadomienie o regule alertu. W okresie przejściowym reguła alertów będzie nadal uruchamiana, a powiadomienie jest pomijane przez Log Analytics przez określony czas, niezależnie od tego, ile razy reguła alertu została uruchomiona w tym okresie.

Właściwość pomijania reguły alertu Log Analytics jest określana przy użyciu wartości *ograniczenia* i okresu pomijania przy użyciu wartości *DurationInMinutes* .

Poniżej znajduje się Przykładowa odpowiedź dla akcji z tylko wartością progową, ważność i Właściwość pomijania

```json
"etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "My threshold action",
   "Threshold": {
      "Operator": "gt",
      "Value": 10
   },
   "Throttling": {
   "DurationInMinutes": 30
   },
   "Severity": "critical",
   "Version": 1
}
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby utworzyć nową akcję dla harmonogramu o ważności.  

```powershell
$AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować akcję ważności dla harmonogramu.  Treść żądania musi zawierać element ETag akcji.

```powershell
$AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson
```

#### <a name="action-groups"></a>Grupy akcji
Wszystkie alerty na platformie Azure używają grupy akcji jako domyślnego mechanizmu obsługi akcji. Za pomocą grupy akcji można określić akcje raz, a następnie skojarzyć grupę akcji z wieloma alertami — na platformie Azure. Bez konieczności, aby wielokrotnie zadeklarować te same akcje w trybie failover i ponownie. Grupy akcji obsługują wiele akcji — w tym wiadomości e-mail, wiadomości SMS, rozmowy głosowej, połączenia narzędzia ITSM, elementu Runbook usługi Automation, identyfikatora URI elementu webhook i nie tylko. 

W przypadku użytkowników, którzy rozszerzyły swoje alerty na platformę Azure — harmonogram powinien teraz zawierać szczegóły grupy akcji, które są przesyłane wraz z progiem, aby można było utworzyć alert. Szczegóły poczty E-mail, adresy URL elementu webhook, szczegóły automatyzacji elementu Runbook i inne akcje muszą być zdefiniowane w grupie akcji najpierw przed utworzeniem alertu. jeden może utworzyć [grupę akcji na podstawie Azure monitor](./action-groups.md) w portalu lub użyć [interfejsu API grupy akcji](/rest/api/monitor/actiongroups).

Aby dodać skojarzenie grupy akcji do alertu, określ unikatowy identyfikator Azure Resource Manager grupy akcji w definicji alertu. Poniżej przedstawiono przykładową ilustrację:

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ]
   },
   "Severity": "critical",
   "Version": 1
}
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby skojarzyć już istniejącą grupę akcji z harmonogramem.  Poniżej znajduje się przykładowa ilustracja użycia.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść żądania musi zawierać element ETag akcji.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

#### <a name="customize-actions"></a>Dostosuj akcje
Domyślnie akcje są zgodne z szablonem standardowym i formatem powiadomień. Jednak użytkownik może dostosować niektóre akcje, nawet jeśli są kontrolowane przez grupy akcji. Obecnie jest możliwe dostosowanie tematu wiadomości E-mail i ładunku elementu webhook.

##### <a name="customize-e-mail-subject-for-action-group"></a>Dostosuj temat wiadomości E-Mail dla grupy akcji
Domyślnie temat wiadomości e-mail dotyczący alertów to: powiadomienie o alercie `<AlertName>` dla `<WorkspaceName>` . Można go jednak dostosować, aby można było używać określonych słów lub tagów — aby można było łatwo zastosować reguły filtru w skrzynce odbiorczej. Szczegóły dotyczące dostosowywania nagłówka wiadomości e-mail muszą być wysyłane wraz ze szczegółami akcji, jak w poniższym przykładzie.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
      "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
}
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby skojarzyć już istniejącą grupę akcji z dostosowaniem do harmonogramu.  Poniżej znajduje się przykładowa ilustracja użycia.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść żądania musi zawierać element ETag akcji.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

##### <a name="customize-webhook-payload-for-action-group"></a>Dostosuj ładunek elementu webhook dla grupy akcji
Domyślnie element webhook, który został wysłany przez grupę akcji dla usługi log Analytics, ma ustaloną strukturę. Jednak jeden może dostosować ładunek JSON przy użyciu określonych zmiennych, aby spełnić wymagania punktu końcowego elementu webhook. Aby uzyskać więcej informacji, zobacz [Akcja elementu webhook dla reguł alertów dziennika](./alerts-log-webhook.md). 

Szczegóły dostosowywania elementu webhook muszą być wysyłane razem z informacjami o akcjach i są stosowane do wszystkich identyfikatorów URI elementu webhook określonych wewnątrz grupy akcji; jak pokazano poniżej.

```json
"etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
"properties": {
   "Type": "Alert",
   "Name": "test-alert",
   "Description": "I need to put a description here",
   "Threshold": {
      "Operator": "gt",
      "Value": 12
   },
   "AzNsNotification": {
      "GroupIds": [
         "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
      ],
   "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
   "CustomEmailSubject": "Azure Alert fired"
   },
   "Severity": "critical",
   "Version": 1
},
```

Użyj metody PUT z unikatowym IDENTYFIKATORem akcji, aby skojarzyć już istniejącą grupę akcji z dostosowaniem do harmonogramu.  Poniżej znajduje się przykładowa ilustracja użycia.

```powershell
$AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

Użyj metody PUT z istniejącym IDENTYFIKATORem akcji, aby zmodyfikować grupę akcji skojarzoną z harmonogramem.  Treść żądania musi zawierać element ETag akcji.

```powershell
$AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson
```

## <a name="next-steps"></a>Następne kroki

* Użyj [interfejsu API REST do przeszukiwania dzienników](../log-query/log-query-overview.md) w log Analytics.
* Informacje o [alertach dzienników w usłudze Azure monitor](./alerts-unified-log.md)
* Jak [tworzyć, edytować i zarządzać regułami alertów dziennika w usłudze Azure monitor](./alerts-log.md)

