---
title: Rejestrowanie diagnostyczne
titleSuffix: Azure Cognitive Services
description: Ten przewodnik zawiera instrukcje krok po kroku dotyczące włączania rejestrowania diagnostycznego dla usługi Azure Cognitive Service. Te dzienniki zapewniają rozbudowane, częste dane dotyczące działania zasobu, które są używane do identyfikacji i debugowania problemów.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816341"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Włączanie rejestrowania diagnostycznego dla Azure Cognitive Services

Ten przewodnik zawiera instrukcje krok po kroku dotyczące włączania rejestrowania diagnostycznego dla usługi Azure Cognitive Service. Te dzienniki zapewniają rozbudowane, częste dane dotyczące działania zasobu, które są używane do identyfikacji i debugowania problemów. Przed kontynuowaniem musisz mieć konto platformy Azure z subskrypcją co najmniej jednej usługi Cognitive Service, takiej jak [wyszukiwanie w sieci Web Bing,](./bing-web-search/overview.md) [Speech Services](./speech-service/overview.md)lub [LUIS.](./luis/what-is-luis.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie diagnostyczne, musisz gdzieś przechowywać dane dziennika. W tym samouczku są używane usługi Azure Storage i Log Analytics.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) — przechowuje dzienniki diagnostyczne inspekcji zasad, analizy statycznej lub tworzenia kopii zapasowych. Konto magazynu nie musi być w tej samej subskrypcji co zasób emitujący dzienniki, o ile użytkownik, który konfiguruje to ustawienie, ma odpowiedni dostęp RBAC platformy Azure do obu subskrypcji.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) — elastyczne narzędzie do wyszukiwania i analizy dzienników, które umożliwia analizowanie nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

> [!NOTE]
> Dostępne są dodatkowe opcje konfiguracji. Aby dowiedzieć się więcej, zobacz Collect and consume log data from your Azure resources (Zbieranie i korzystanie z danych [dzienników z zasobów platformy Azure).](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Włączanie zbierania dzienników diagnostycznych  

Zacznijmy od włączenia rejestrowania diagnostycznego przy użyciu Azure Portal.

> [!NOTE]
> Aby włączyć tę funkcję przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, skorzystaj z instrukcji podanych w tesłudze Collect and consume log data from your Azure resources (Zbieranie i używanie danych dzienników [z zasobów platformy Azure).](../azure-monitor/essentials/platform-logs-overview.md)

1. Przejdź do witryny Azure Portal. Następnie znajdź i wybierz Cognitive Services zasobów. Na przykład Twoja subskrypcja usługi wyszukiwanie w sieci Web Bing.   
2. Następnie w menu nawigacji po lewej stronie znajdź pozycję **Monitorowanie** i wybierz **pozycję Ustawienia diagnostyczne.** Ten ekran zawiera wszystkie wcześniej utworzone ustawienia diagnostyczne dla tego zasobu.
3. Jeśli istnieje wcześniej utworzony zasób, który chcesz użyć, możesz wybrać go teraz. W przeciwnym razie **wybierz pozycję + Dodaj ustawienie diagnostyczne.**
4. Wprowadź nazwę ustawienia. Następnie wybierz pozycję **Zarchiwizuj na koncie magazynu i** wyślij do usługi Log **Analytics.**
5. Po wyświetleniu monitu o skonfigurowanie wybierz konto magazynu i obszar roboczy OMS, których chcesz użyć do przechowywania dzienników diagnostycznych. **Uwaga:** Jeśli nie masz konta magazynu ani obszaru roboczego OMS, postępuj zgodnie z monitami, aby je utworzyć.
6. Wybierz **pozycję Audit**(Inspekcja), **RequestResponse (Odpowiada na** żądanie) i **AllMetrics (Wszystkiemetryki).** Następnie ustaw okres przechowywania danych dziennika diagnostycznego. Jeśli zasady przechowywania są ustawione na zero, zdarzenia dla tej kategorii dziennika są przechowywane przez czas nieokreślony.
7. Kliknij pozycję **Zapisz**.

Zanim dane rejestrowania będą dostępne do wykonywania zapytań i analizowania, może upłynieć do dwóch godzin. Dlatego nie martw się, jeśli nie widzisz niczego od razu.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Wyświetlanie i eksportowanie danych diagnostycznych z usługi Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie do magazynowania obiektów zoptymalizowane pod kątem przechowywania dużych ilości danych bez struktury. W tej sekcji dowiesz się, jak odpytować konto magazynu o łączną liczbę transakcji w okresie 30 dni i wyeksportować dane do programu Excel.

1. W Azure Portal znajdź zasób usługi Azure Storage utworzony w ostatniej sekcji.
2. W menu nawigacji po lewej stronie znajdź pozycję **Monitorowanie** i wybierz **pozycję Metryki.**
3. Użyj dostępnych listy rozwijanych, aby skonfigurować zapytanie. W tym przykładzie ustawmy zakres czasu Ostatnie **30** dni, a metrykę na **wartość Transakcja**.
4. Po zakończeniu zapytania zobaczysz wizualizację transakcji z ostatnich 30 dni. Aby wyeksportować te dane, użyj **przycisku Eksportuj** do programu Excel znajdującego się w górnej części strony.

Dowiedz się więcej o tym, co można zrobić z danymi diagnostycznym w [usłudze Azure Storage.](../storage/blobs/storage-blobs-introduction.md)

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Postępuj zgodnie z tymi instrukcjami, aby eksplorować dane analizy dzienników dla zasobu.

1. W okienku Azure Portal pozycję **Log Analytics** z menu nawigacji po lewej stronie i wybierz ją.
2. Znajdź i wybierz zasób utworzony podczas włączania diagnostyki.
3. W **obszarze Ogólne** znajdź i wybierz pozycję **Dzienniki.** Na tej stronie można uruchamiać zapytania dotyczące dzienników.

### <a name="sample-queries"></a>Przykładowe zapytania

Oto kilka podstawowych zapytań usługi Kusto, których można użyć do eksplorowanie danych dzienników.

Uruchom to zapytanie dla wszystkich dzienników diagnostycznych Azure Cognitive Services określonym przedziale czasu:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Uruchom to zapytanie, aby wyświetlić 10 najnowszych dzienników:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Uruchom to zapytanie, aby pogrupować operacje według **zasobu**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Uruchom to zapytanie, aby znaleźć średni czas wykonywania operacji:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Uruchom to zapytanie, aby wyświetlić liczbę operacji podzielonych w czasie według operationName z liczbami podzielonymi na pojemniki co 10 s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak włączyć rejestrowanie, a także kategorie metryk i dzienników, [](../azure-monitor/data-platform.md) które są obsługiwane przez różne usługi platformy Azure, przeczytaj artykuły Omówienie metryk w usłudze Microsoft Azure i Omówienie dzienników diagnostycznych platformy [Azure.](../azure-monitor/essentials/platform-logs-overview.md)
* Przeczytaj następujące artykuły, aby dowiedzieć się więcej o centrach zdarzeń:
  * [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Przeczytaj [temat Pobieranie metryk i dzienników diagnostycznych z usługi Azure Storage.](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)
* Zapoznaj się z tematem Understand log searches in Azure Monitor logs (Informacje o [wyszukiwaniu w dziennikach w Azure Monitor dziennikach).](../azure-monitor/logs/log-query-overview.md)