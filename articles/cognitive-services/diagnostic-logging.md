---
title: Rejestrowanie diagnostyczne
titleSuffix: Azure Cognitive Services
description: Ten przewodnik zawiera instrukcje krok po kroku dotyczące włączania rejestrowania diagnostycznego dla usługi poznawczej platformy Azure. Te dzienniki zapewniają rozbudowane, częste dane dotyczące operacji zasobu, które są używane do identyfikacji i debugowania problemu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: a2005ca7b32136ff0032d27e04035c46b2e4e904
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595367"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Włączanie rejestrowania diagnostycznego dla usługi Azure Cognitive Services

Ten przewodnik zawiera instrukcje krok po kroku dotyczące włączania rejestrowania diagnostycznego dla usługi poznawczej platformy Azure. Te dzienniki zapewniają rozbudowane, częste dane dotyczące operacji zasobu, które są używane do identyfikacji i debugowania problemu. Przed kontynuowaniem musisz mieć konto platformy Azure z subskrypcją co najmniej jedną usługą poznawczej, taką jak [Wyszukiwanie w sieci Web Bing](./bing-web-search/overview.md), [usługi mowy](./speech-service/overview.md)lub [Luis](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie diagnostyczne, musisz przechowywać dane dziennika. Ten samouczek używa usługi Azure Storage i Log Analytics.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) — przechowuje dzienniki diagnostyczne na potrzeby inspekcji zasad, statycznej analizy lub kopii zapasowej. Konto magazynu nie musi znajdować się w tej samej subskrypcji co zasób emitujący dzienniki, dopóki użytkownik, który konfiguruje ustawienie, ma odpowiedni dostęp do obu subskrypcji na platformie Azure.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) — elastyczne narzędzie do przeszukiwania dzienników i analizy umożliwiające analizę nieprzetworzonych dzienników generowanych przez zasób platformy Azure.

> [!NOTE]
> Dostępne są dodatkowe opcje konfiguracji. Aby dowiedzieć się więcej, zobacz [zbieranie i korzystanie z danych dzienników z zasobów platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-diagnostic-log-collection"></a>Włącz zbieranie dzienników diagnostycznych  

Zacznijmy od włączenia rejestrowania diagnostycznego przy użyciu Azure Portal.

> [!NOTE]
> Aby włączyć tę funkcję przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, Skorzystaj z instrukcji dostarczonych w temacie [zbieranie i korzystanie z danych dzienników z zasobów platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).

1. Przejdź do witryny Azure Portal. Następnie odszukaj i wybierz zasób Cognitive Services. Na przykład Twoja subskrypcja wyszukiwanie w sieci Web Bing.   
2. Następnie w menu nawigacji po lewej stronie Znajdź pozycję **monitorowanie** i wybierz pozycję **Ustawienia diagnostyczne**. Ten ekran zawiera wszystkie wcześniej utworzone ustawienia diagnostyczne dla tego zasobu.
3. Jeśli wcześniej utworzono zasób, którego chcesz użyć, możesz wybrać go teraz. W przeciwnym razie wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
4. Wprowadź nazwę tego ustawienia. Następnie wybierz pozycję **Archiwizuj na koncie magazynu** i **Wyślij do usługi log Analytics**.
5. Po wyświetleniu monitu o skonfigurowanie wybierz konto magazynu i obszar roboczy pakietu OMS, który ma być używany do przechowywania dzienników diagnostycznych. **Uwaga**: Jeśli nie masz konta magazynu lub obszaru roboczego OMS, postępuj zgodnie z monitami, aby go utworzyć.
6. Wybierz kolejno pozycje **Inspekcja**, **RequestResponse** i **AllMetrics**. Następnie ustaw okres przechowywania danych dzienników diagnostycznych. Jeśli zasady przechowywania mają wartość zero, zdarzenia dla tej kategorii dzienników są przechowywane przez czas nieokreślony.
7. Kliknij pozycję **Zapisz**.

Zarejestrowanie i przeanalizowanie danych może potrwać do dwóch godzin. Nie martw się, jeśli nie widzisz niczego od razu.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Wyświetlanie i eksportowanie danych diagnostycznych z usługi Azure Storage

Usługa Azure Storage to niezawodne rozwiązanie magazynu obiektów, które jest zoptymalizowane pod kątem przechowywania dużych ilości danych bez struktury. W tej sekcji dowiesz się, jak wysyłać zapytania do konta magazynu pod kątem łącznej liczby transakcji w okresie 30 dni i wyeksportować dane do programu Excel.

1. Na Azure Portal odszukaj zasób usługi Azure Storage, który został utworzony w ostatniej sekcji.
2. W menu nawigacji po lewej stronie Znajdź pozycję **monitorowanie** i wybierz pozycję **metryki**.
3. Użyj dostępnych list rozwijanych, aby skonfigurować zapytanie. Na potrzeby tego przykładu Ustaw zakres czasu na **ostatnie 30 dni** i metrykę na **transakcję**.
4. Po zakończeniu kwerendy zobaczysz wizualizację transakcji w ciągu ostatnich 30 dni. Aby wyeksportować te dane, użyj przycisku **Eksportuj do programu Excel** znajdującego się w górnej części strony.

Dowiedz się więcej na temat tego, co możesz zrobić z danymi diagnostycznymi w [usłudze Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Postępuj zgodnie z tymi instrukcjami, aby poznać dane usługi log Analytics dla zasobu.

1. W Azure Portal Znajdź i wybierz **log Analytics** z menu nawigacji po lewej stronie.
2. Znajdź i wybierz zasób, który został utworzony podczas włączania diagnostyki.
3. W obszarze **Ogólne** Znajdź i wybierz pozycję **dzienniki**. Na tej stronie można uruchamiać zapytania względem dzienników.

### <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kilka podstawowych zapytań Kusto, których można użyć do eksplorowania danych dziennika.

Uruchom to zapytanie dla wszystkich dzienników diagnostycznych z usługi Azure Cognitive Services w określonym przedziale czasu:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Uruchom to zapytanie, aby zobaczyć 10 najnowszych dzienników:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Uruchom to zapytanie w celu grupowania operacji według **zasobu**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Uruchom to zapytanie, aby znaleźć średni czas potrzebny do wykonania operacji:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Uruchom to zapytanie, aby wyświetlić ilość operacji w czasie dzielenia przez OperationName z liczbą Binned dla każdego dziesiątkachu.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak włączyć rejestrowanie, a także kategorie metryk i dzienników, które są obsługiwane przez różne usługi platformy Azure, zapoznaj się z [omówieniem metryk](../azure-monitor/data-platform.md) w Microsoft Azure i [Omówienie artykułów dotyczących dzienników diagnostycznych platformy Azure](../azure-monitor/essentials/platform-logs-overview.md) .
* Przeczytaj te artykuły, aby dowiedzieć się więcej o centrach zdarzeń:
  * [Co to jest usługa Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Przeczytaj artykuł [pobieranie metryk i dzienników diagnostycznych z usługi Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Przeczytaj [Opis wyszukiwania w dzienniku Azure monitor](../azure-monitor/logs/log-query-overview.md).