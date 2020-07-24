---
title: Rozwiązywanie problemów z alertami dzienników w Azure Monitor | Microsoft Docs
description: Typowe problemy, błędy i rozwiązania dla reguł alertów dziennika na platformie Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: b8edbbc397a56f4fcf5b3ae070f04ca61659d98d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87045348"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Rozwiązywanie problemów z alertami dzienników w Azure Monitor  

W tym artykule opisano sposób rozwiązywania typowych problemów z alertami dzienników w Azure Monitor. Zawiera również rozwiązania typowych problemów z funkcjonalnością i konfiguracją alertów dzienników.

Termin *alerty dziennika* opisuje reguły, które są uruchamiane na podstawie zapytania dziennika w [obszarze roboczym usługi Azure log Analytics](../log-query/get-started-portal.md) lub na [platformie Azure Application Insights](../log-query/log-query-overview.md). Dowiedz się więcej o funkcjach, terminologii i typach w [alertach dzienników w Azure monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> W tym artykule nie są brane pod uwagę przypadki, w których Azure Portal pokazuje wyzwalaną regułę alertu, a powiadomienie nie jest wykonywane przez skojarzoną grupę akcji. W takich przypadkach zapoznaj się z tematem szczegóły w temacie [Tworzenie i zarządzanie grupami akcji w Azure Portal](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alert dziennika nie został wywołany

Poniżej przedstawiono niektóre typowe przyczyny, dla których stan skonfigurowanej [reguły alertu dziennika w Azure monitor](../platform/alerts-log.md) nie jest wyświetlany [jako *wyzwolone* w oczekiwany sposób](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Czas pozyskiwania danych dzienników

Alert dziennika okresowo uruchamia zapytanie na podstawie [log Analytics](../log-query/get-started-portal.md) lub [Application Insights](../log-query/log-query-overview.md). Ponieważ Azure Monitor przetwarza wiele terabajtów danych z tysięcy klientów z różnych źródeł na całym świecie, usługa jest podatna na różne opóźnienia czasu. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych w dziennikach Azure monitor](../platform/data-ingestion-time.md).

Aby wyeliminować opóźnienia, system czeka i ponawia próbę zapytania o alert wiele razy, jeśli znalezienie wymaganych danych nie zostało jeszcze odebrane. System ma ustawiony wykładniczy czas oczekiwania. Alert dziennika jest wyzwalany dopiero po udostępnieniu danych, więc opóźnienie może być spowodowane spowolnieniem pozyskiwania danych dziennika.

### <a name="incorrect-time-period-configured"></a>Skonfigurowano nieprawidłowy okres

Zgodnie z opisem w artykule dotyczącym [terminologii dla alertów dziennika](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), czas określony w konfiguracji określa zakres czasu zapytania. Zapytanie zwraca tylko rekordy, które zostały utworzone w tym zakresie.

Przedział czasu ogranicza dane pobierane dla kwerendy dziennika, aby zapobiec nadużyciu i obejść każde polecenie czasu (na przykład **temu**) używane w zapytaniu dziennika. Na przykład jeśli okres jest ustawiony na 60 minut, a zapytanie jest uruchamiane o godzinie 1:15 PM, do zapytania dziennika są używane tylko rekordy utworzone między 12:15 PM i 1:15 PM. Jeśli zapytanie dziennika używa polecenia czasu, takiego jak **temu (1D)**, zapytanie nadal używa tylko danych z przedziału od 12:15 do 1:15 PM, ponieważ okres jest ustawiony na ten interwał.

Sprawdź, czy okres w konfiguracji jest zgodny z zapytaniem. Na przykład pokazane wcześniej, jeśli zapytanie dziennika używa **temu (1D)** z zielonym znacznikiem, okres powinien być ustawiony na 24 godziny lub 1 440 minut (wskazany czerwony). To ustawienie zapewnia, że zapytanie działa zgodnie z oczekiwaniami.

![Okres](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Opcja pomijania alertów jest ustawiona

Zgodnie z opisem w kroku 8 artykułu dotyczącego [tworzenia reguły alertu dziennika w Azure Portal](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal), alerty dzienników zapewniają opcję **pomijania alertów** , aby pominąć wyzwalanie i akcje powiadamiania przez skonfigurowany czas. W związku z tym może się zdarzyć, że alert nie został wywołany. W rzeczywistości został on wywołany, ale został pominięty.  

![Pomiń alerty](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Reguła alertu pomiaru metryki jest niepoprawna

*Alerty dzienników pomiarów metryk* są podtypem alertów dziennika, które mają specjalne możliwości i składnię kwerendy o ograniczonym alercie. Reguła alertu dotyczącego dziennika pomiarów metryk wymaga, aby dane wyjściowe zapytania były serią czasową metryk. Oznacza to, że dane wyjściowe to tabela z odrębnymi okresami o równym rozmiarze, wraz z odpowiadającymi im wartościami zagregowanymi.

Możesz zdecydować się na dodatkowe zmienne w tabeli wraz z **AggregatedValue**. Te zmienne mogą służyć do sortowania tabeli.

Załóżmy na przykład, że reguła dotycząca alertu dziennika pomiarów metryk została skonfigurowana jako:

- Zapytanie o`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Przedział czasu wynoszący 6 godzin
- Próg 50
- Logika alertu z trzema kolejnymi naruszeniami
- **Agreguj** według wybranych jako **$Table**

Ponieważ polecenie zawiera **Podsumowanie... przez** i zawiera dwie zmienne (**sygnatura czasowa** i **$Table**), system wybiera **$Table** do **agregowania**. System sortuje tabelę wyników według pola **$Table** , jak pokazano na poniższym zrzucie ekranu. Następnie analizuje wiele wystąpień **AggregatedValue** dla każdego typu tabeli (na przykład **availabilityResults**), aby sprawdzić, czy wystąpiły trzy lub więcej kolejnych naruszeń.

![Wykonywanie zapytania pomiaru metryki z wieloma wartościami](media/alert-log-troubleshoot/LogMMQuery.png)

Ponieważ wartość **zagregowana** jest definiowana w **$Table**, dane są sortowane w kolumnie **$Table** (oznaczonej kolorem czerwonym). Następnie grupujemy i szukamy typów **agregacji po** polu.

Na przykład dla **$Table**wartości dla **availabilityResults** będą traktowane jako jeden wykres lub jednostka (oznaczone kolorem pomarańczowym). W tej wartości wykres, usługa alertu sprawdza trzy kolejne naruszenia (oznaczone kolorem zielonym). Naruszenia wyzwalają alert dla wartości tabeli **availabilityResults**.

Podobnie w przypadku wystąpienia trzech kolejnych naruszeń dla dowolnej innej wartości **$Table**dla tego samego działania zostanie wyzwolone inne powiadomienie o alercie. Usługa alertów automatycznie sortuje wartości z jednego wykresu/jednostki (wskazanego w kolorze pomarańczowym) według czasu.

Teraz Załóżmy, że reguła dla alertu dziennika pomiarów metryk została zmodyfikowana, a zapytanie było `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` . Pozostała część konfiguracji pozostaje taka sama jak wcześniej, włącznie z logiką alertu dla trzech kolejnych naruszeń. Opcja **agregacja** w tym przypadku domyślnie ma **sygnaturę czasową** . W zapytaniu podano tylko jedną wartość **podsumowania... przez** (to oznacza **znacznik czasu**). Podobnie jak w poprzednim przykładzie, dane wyjściowe na końcu wykonywania byłyby opisane w następujący sposób.

   ![Wykonanie zapytania pomiaru metryki z wartością pojedynczą](media/alert-log-troubleshoot/LogMMtimestamp.png)

Ponieważ wartość **zagregowana** jest definiowana w **sygnaturze czasowej**, dane są sortowane w kolumnie **sygnatur czasowych** (oznaczone kolorem czerwonym). Następnie grupujemy według **sygnatur czasowych**. Na przykład wartości dla `2018-10-17T06:00:00Z` będą traktowane jako jeden wykres/jednostka (oznaczone kolorem pomarańczowym). W tym wykreśleniu wartości/jednostce usługa alertów nie będzie wyszukiwać następujących po sobie naruszeń (ponieważ każda wartość **sygnatury czasowej** ma tylko jeden wpis). Alert nigdy nie zostanie wyzwolony. W takim przypadku użytkownik musi być:

- Dodaj zmienną fikcyjną lub istniejącą zmienną (na przykład **$Table**), aby poprawnie sortować przy użyciu pola **Aggregate** on.
- Skonfiguruj ponownie regułę alertu tak, aby korzystała z logiki alertu na podstawie **całkowitego naruszenia** .

## <a name="log-alert-fired-unnecessarily"></a>Alert dziennika jest niepotrzebny

Skonfigurowana [reguła alertu dziennika w Azure monitor](../platform/alerts-log.md) może zostać nieoczekiwanie wyzwolona podczas wyświetlania [alertów na platformie Azure](../platform/alerts-managing-alert-states.md). W poniższych sekcjach opisano niektóre typowe przyczyny.

### <a name="alert-triggered-by-partial-data"></a>Alert wywołany przez częściowe dane

Log Analytics i Application Insights podlegają opóźnieniu i przetwarzaniu. Po uruchomieniu zapytania dotyczącego alertu dziennika może się okazać, że żadne dane nie są dostępne lub dostępne są tylko niektóre dane. Aby uzyskać więcej informacji, zobacz czas pozyskiwania [danych dziennika w Azure monitor](../platform/data-ingestion-time.md).

W zależności od sposobu skonfigurowania reguły alertu może wystąpić niepowodzenie uruchomienia w przypadku braku danych lub częściowych danych w dziennikach w czasie wykonywania alertu. W takich przypadkach zalecamy zmianę kwerendy lub konfiguracji alertu.

Na przykład, jeśli skonfigurujesz regułę alertu dziennika, która ma być wyzwalana, gdy liczba wyników zapytania analitycznego jest mniejsza niż 5, alert zostanie wyzwolony, gdy nie ma danych (rekord zerowy) lub częściowe wyniki (jeden rekord). Jednak po opóźnieniu pozyskiwania danych te same zapytania z pełnymi danymi mogą dać wynik 10 rekordów.

### <a name="alert-query-output-is-misunderstood"></a>Dane wyjściowe zapytania alertu są interpretowane

W zapytaniu analitycznym jest udostępniana logika dla alertów dzienników. Zapytanie analityczne może używać różnych danych Big Data i funkcji matematycznych. Usługa alertów uruchamia zapytanie w określonych odstępach czasu z danymi w określonym przedziale czasu. Usługa alertów wprowadza drobne zmiany zapytania na podstawie typu alertu. Tę zmianę można wyświetlić w sekcji **zapytania, która ma zostać wykonana** na ekranie **Konfigurowanie logiki sygnałów** :

![Zapytanie do wykonania](media/alert-log-troubleshoot/LogAlertPreview.png)

**Zapytanie, które ma zostać wykonane** , jest uruchamiane przez usługę alertów dziennika. Jeśli chcesz zrozumieć, jakie dane wyjściowe zapytania o Alert mogą występować przed utworzeniem alertu, możesz uruchomić określone zapytanie i przedział czasu za pośrednictwem [portalu analizy](../log-query/log-query-overview.md) lub [interfejsu API analizy](/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alert dziennika został wyłączony

W poniższych sekcjach wymieniono przyczyny, dla których Azure Monitor mogą wyłączyć [regułę alertu dziennika](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Zasób, w którym utworzono alert, już nie istnieje

Reguły alertów dziennika utworzone w Azure Monitor kierować do określonego zasobu, takiego jak obszar roboczy usługi Azure Log Analytics, aplikacja Application Insights platformy Azure i zasób platformy Azure. Usługa alertu dziennika uruchomi następnie zapytanie analizy podane w regule dla określonego celu. Jednak po utworzeniu reguły użytkownicy często przejdą do usuwania z platformy Azure lub przechodzą na platformę Azure — obiekt docelowy reguły alertu dziennika. Ponieważ obiekt docelowy reguły alertu nie jest już prawidłowy, wykonywanie reguły kończy się niepowodzeniem.

W takich przypadkach Azure Monitor wyłącza alert dziennika i gwarantuje, że opłaty nie są niepotrzebne, gdy nie będzie można stale uruchamiać reguły przez okres zmienny (na przykład tydzień). Możesz sprawdzić dokładną godzinę, Azure Monitor wyłączyć alert dziennika za pośrednictwem [dziennika aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md). W dzienniku aktywności platformy Azure zdarzenie jest dodawane, gdy Azure Monitor wyłącza regułę alertu dziennika.

Następujące przykładowe zdarzenie w dzienniku aktywności platformy Azure dotyczy reguły alertu, która została wyłączona z powodu ciągłego błędu.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Zapytanie użyte w alercie dziennika jest nieprawidłowe

Każda reguła alertu dziennika utworzona w Azure Monitor w ramach swojej konfiguracji musi określać zapytanie analityczne, które będzie okresowo uruchamiać usługa alertów. Zapytanie analityczne może mieć poprawną składnię w momencie tworzenia lub aktualizowania reguły. Czasami jednak w określonym czasie zapytanie podane w regule alertu dziennika może opracowywać problemy składniowe i spowodować niepowodzenie wykonania reguły. Niektóre typowe powody, dla których zapytanie analityczne podane w regule alertu dziennika może opracowywać błędy:

- Zapytanie jest zapisywana do [uruchomienia dla wielu zasobów](../log-query/cross-workspace-query.md). I co najmniej jeden z określonych zasobów już nie istnieje.
- Skonfigurowany alert dotyczący [dziennika typu pomiaru metryki](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) nie jest zgodny z normami składni
- Nie przekazano przepływu danych do platformy analitycznej. [Wykonanie zapytania powoduje błąd,](https://dev.loganalytics.io/documentation/Using-the-API/Errors) ponieważ nie ma danych dla podanego zapytania.
- Zmiany w [języku zapytań](/azure/kusto/query/) zawierają poprawiony format poleceń i funkcji. Dlatego zapytanie podane wcześniej w regule alertu nie jest już prawidłowe.

[Azure Advisor](../../advisor/advisor-overview.md) ostrzega o tym zachowaniu. Zostanie dodane zalecenie dotyczące konkretnej reguły alertu dziennika na Azure Advisor, w kategorii wysoka dostępność z średnim wpływem oraz opis "Napraw regułę alertu dziennika w celu zapewnienia monitorowania".

> [!NOTE]
> Jeśli zapytanie alertu w regule alertu dziennika nie zostanie usunięte po podaniu przez Azure Advisor zalecenia przez siedem dni, Azure Monitor spowoduje wyłączenie alertu dziennika i upewnienie się, że nie są one rozliczane niepotrzebnie, gdy zasada nie będzie działać ciągle przez okres o zmiennym rozmiarze (7 dni). Dokładny czas, w którym Azure Monitor wyłączyć regułę alertu dziennika, można znaleźć, szukając zdarzenia w [dzienniku aktywności platformy Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="alert-rule-quota-was-reached"></a>Osiągnięto limit przydziału reguły alertów

Liczba reguł alertów przeszukiwania dzienników na subskrypcję i zasób podlega limitom przydziału opisanym [tutaj](https://docs.microsoft.com/azure/azure-monitor/service-limits).

### <a name="recommended-steps"></a>Zalecane kroki
    
Jeśli osiągnięto limit przydziału, następujące kroki mogą pomóc w rozwiązaniu problemu.

1. Spróbuj usunąć lub wyłączyć reguły alertów przeszukiwania dzienników, które nie są już używane.
2. Aby zwiększyć limit przydziału, utwórz wniosek o pomoc techniczną i podaj następujące informacje:

    - Identyfikatory subskrypcji, dla których trzeba zwiększyć limity przydziału
    - Przyczyna zwiększenia limitu przydziału
    - Typ zasobu dla zwiększenia przydziału: **log Analytics**, **Application Insights** danych.
    - Żądany limit przydziału


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Aby sprawdzić bieżące użycie nowych reguł alertów dziennika
    
#### <a name="from-the-azure-portal"></a>Z witryny Azure Portal

1. Otwórz ekran *Alerty* i kliknij pozycję *Zarządzaj regułami alertów*
2. Filtruj do odpowiedniej subskrypcji przy użyciu kontrolki listy rozwijanej *Subskrypcja*
3. Pamiętaj, aby nie filtrować do określonej grupy zasobów, typu zasobu ani zasobu
4. W kontrolce listy rozwijanej *Typ sygnału* wybierz pozycję "przeszukiwanie dzienników".
5. Sprawdź, czy kontrolka listy rozwijanej *Stan* ma wartość „Włączono”
6. Łączna liczba reguł alertów przeszukiwania dzienników zostanie wyświetlonych powyżej listy reguł

#### <a name="from-api"></a>Za pomocą interfejsu API

- PowerShell- [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule?view=azps-3.7.0)
- Interfejs API REST — [lista według subskrypcji](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat alertów dziennika na platformie Azure](../platform/alerts-unified-log.md).
- Dowiedz się więcej o [Application Insights](../log-query/log-query-overview.md).
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).
