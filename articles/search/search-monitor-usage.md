---
title: Monitorowanie operacji i aktywności
titleSuffix: Azure Cognitive Search
description: Włącz rejestrowanie, Pobierz metryki działania zapytania, użycie zasobów i inne dane systemu z usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462330"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitoruj operacje i działania Wyszukiwanie poznawcze platformy Azure

W tym artykule wprowadzono monitorowanie na poziomie usługi (zasobów) na poziomie obciążenia (zapytania i indeksowanie) i sugeruje strukturę monitorowania dostępu użytkowników.

W całym spektrum będziesz używać kombinacji wbudowanych usług, takich jak Azure Monitor, a także interfejsów API usługi, które zwracają statystyki, liczniki i stan. Zrozumienie zakresu możliwości może pomóc utworzyć pętlę do przesyłania opinii, aby można było rozwiązać problemy w miarę ich występowania.

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Wiele usług, w tym Azure Wyszukiwanie poznawcze, wykorzystują [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) do alertów, metryk i danych diagnostycznych rejestrowania. W przypadku usługi Azure Wyszukiwanie poznawcze wbudowana infrastruktura monitorowania jest używana głównie do monitorowania na poziomie zasobów (kondycji usługi) i [monitorowania zapytań](search-monitor-queries.md).

Poniższy zrzut ekranu ułatwia zlokalizowanie funkcji Azure Monitor w portalu.

+ Karta **monitorowanie** znajdująca się na stronie głównej omówienia pokazuje kluczowe metryki w skrócie.
+ **Dziennik aktywności**, poniżej przeglądu, raporty dotyczące akcji na poziomie zasobów: kondycja usługi i powiadomienia o żądaniu klucza interfejsu API.
+ **Monitorowanie**, dalsze Wyświetlanie listy, umożliwia Konfigurowanie alertów, metryk i dzienników diagnostycznych. Utwórz je, gdy będą potrzebne. Po zebraniu i zapisaniu danych można wykonywać zapytania lub wizualizować informacje w celu uzyskania szczegółowych informacji.

![Azure Monitor integrację w usłudze wyszukiwania](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrację w usłudze wyszukiwania")

### <a name="precision-of-reported-numbers"></a>Precyzja zgłoszonych liczb

Strony portalu są odświeżane co kilka minut. W związku z tym liczby raportowane w portalu są przybliżone i mają na celu zapewnienie ogólnego znaczenia, jak dobrze system obsługuje żądania. Rzeczywiste metryki, takie jak zapytania na sekundę (zapytań), mogą być większe lub mniejsze niż liczba wyświetlana na stronie.

## <a name="activity-logs-and-service-health"></a>Dzienniki aktywności i kondycja usługi

[**Dziennik aktywności**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) zbiera informacje z Azure Resource Manager i raportów o zmianach w kondycji usługi. Dziennik aktywności można monitorować pod kątem krytycznych, błędów i ostrzeżeń związanych z kondycją usługi.

W przypadku zadań w ramach usługi — takich jak zapytania, indeksowanie lub tworzenie obiektów, zobaczysz ogólne powiadomienia informacyjne, takie jak *Pobieranie klucza administratora* i *Uzyskiwanie kluczy zapytań* dla każdego żądania, ale nie konkretnej akcji. Aby uzyskać informacje dotyczące tego ziarna, należy skonfigurować rejestrowanie diagnostyczne.

Możesz uzyskać dostęp do **dziennika aktywności** z okienka nawigacji po lewej stronie lub z powiadomień w górnym pasku poleceń okna lub na stronie **diagnozowanie i rozwiązywanie problemów** .

## <a name="monitor-storage"></a>Monitorowanie magazynu

Strony z kartami wbudowane na stronie Przegląd raportują na temat użycia zasobów. Te informacje stają się dostępne zaraz po rozpoczęciu korzystania z usługi, bez konieczności konfigurowania, a strona jest odświeżana co kilka minut. 

Jeśli podejmujesz decyzje dotyczące warstwy, [która ma być używana na potrzeby obciążeń produkcyjnych](search-sku-tier.md), lub czy należy [dostosować liczbę aktywnych replik i partycji](search-capacity-planning.md), te metryki mogą pomóc w podejmowaniu tych decyzji, pokazując, jak szybko zużywane są zasoby i jak również Bieżąca konfiguracja obsługuje istniejące obciążenie.

Alerty powiązane z magazynem nie są obecnie dostępne; Użycie magazynu nie jest agregowane lub zarejestrowane w tabeli **AzureMetrics** w Azure monitor. Należy [utworzyć rozwiązanie niestandardowe](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) , które emituje powiadomienia związane z zasobami, gdzie kod sprawdza rozmiar magazynu i obsługuje odpowiedź. Aby uzyskać więcej informacji o metrykach magazynu, zobacz [Pobieranie statystyk usług](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

W przypadku monitorowania wizualnego w portalu karta **użycie** pokazuje dostępność zasobów względem bieżących [limitów](search-limits-quotas-capacity.md) narzuconych przez warstwę usług. 

Poniższa ilustracja dotyczy bezpłatnej usługi, która jest ograniczona do 3 obiektów każdego typu i 50 MB miejsca w magazynie. Usługa podstawowa lub standardowa ma wyższe limity i w przypadku zwiększenia liczby partycji Maksymalna ilość miejsca w magazynie jest proporcjonalna.

![Stan użycia względem limitów warstwy](./media/search-monitor-usage/usage-tab.png
 "Stan użycia względem limitów warstwy")

## <a name="monitor-workloads"></a>Monitoruj obciążenia

Zarejestrowane zdarzenia obejmują te powiązane z indeksowaniem i zapytaniami. Tabela **AzureDiagnostics** w log Analytics zbiera dane operacyjne powiązane z zapytaniami i indeksowanie.

Większość zarejestrowanych danych jest przeznaczonych dla operacji tylko do odczytu. W przypadku innych operacji Create-Update-Delete, które nie są przechwytywane w dzienniku, można wysłać zapytanie do usługi wyszukiwania w celu uzyskania informacji o systemie.

| OperationName | Opis |
|---------------|-------------|
| Servicestatystyka | Ta operacja to rutynowe wywołanie [pobierania statystyk usługi](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), wywoływane bezpośrednio lub niejawnie, aby wypełnić stronę omówienia portalu po jej załadowaniu lub odświeżeniu. |
| Zapytanie. Search |  Zażądaj zapytań względem indeksu, aby uzyskać informacje na temat zarejestrowanych zapytań, zobacz temat [monitorowanie zapytań](search-monitor-queries.md) .|
| Indeksowanie. index  | Ta operacja jest wywołaniem do [dodawania, aktualizowania lub usuwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| zwiększa. Prototyp | Jest to indeks utworzony przez Kreatora importu danych. |
| Indeksatory. Utwórz | Utwórz indeksator jawnie lub niejawnie za pomocą Kreatora importu danych. |
| Indeksatory. Get | Zwraca nazwę indeksatora za każdym razem, gdy indeksator jest uruchomiony. |
| Indeksatory. stan | Zwraca stan indeksatora za każdym razem, gdy indeksator jest uruchomiony. |
| Źródła danych. Pobierz | Zwraca nazwę źródła danych przy każdym uruchomieniu indeksatora.|
| Indeksy. Get | Zwraca nazwę indeksu za każdym razem, gdy indeksator jest uruchamiany. |

### <a name="kusto-queries-about-workloads"></a>Kusto zapytania dotyczące obciążeń

Jeśli włączono rejestrowanie, możesz wysyłać zapytania do **AzureDiagnostics** , aby uzyskać listę operacji uruchomionych w usłudze i kiedy. Możesz również skorelować działanie, aby zbadać zmiany wydajności.

#### <a name="example-list-operations"></a>Przykład: operacje na liście 

Zwróć listę operacji i liczbę każdej z nich.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Przykład: skorelowanie operacji

Skorelować żądanie zapytania z operacjami indeksowania i Renderuj punkty danych na wykresie czasu, aby zobaczyć operacje, które się pokrywają.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Korzystanie z interfejsów API wyszukiwania

Zarówno interfejs API REST platformy Azure Wyszukiwanie poznawcze, jak i zestaw .NET SDK zapewniają programistyczny dostęp do metryk usług, informacji o indeksie i indeksatorze oraz liczby dokumentów.

+ [Pobierz statystyki usługi](/rest/api/searchservice/get-service-statistics)
+ [Pobierz statystyki indeksu](/rest/api/searchservice/get-index-statistics)
+ [Pobierz liczby dokumentów](/rest/api/searchservice/count-documents)
+ [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorowanie dostępu użytkowników

Ponieważ indeksy wyszukiwania są składnikiem większej aplikacji klienckiej, nie ma żadnej wbudowanej metodologii do kontrolowania i monitorowania dostępu dla poszczególnych użytkowników do indeksu. Założono, że żądania pochodzą z aplikacji klienckiej dla żądań administratora lub zapytania. Operacje odczytu i zapisu administratora obejmują tworzenie, aktualizowanie i usuwanie obiektów w całej usłudze. Operacje tylko do odczytu są zapytania względem kolekcji dokumentów, w zakresie do pojedynczego indeksu. 

W związku z tym informacje widoczne w dziennikach aktywności są odwołaniami do wywołań przy użyciu kluczy administracyjnych lub kluczy zapytań. Odpowiedni klucz jest uwzględniany w żądaniach pochodzących z kodu klienta. Usługa nie jest wyposażona w obsługę tokenów tożsamości ani personifikacji.

Jeśli istnieją wymagania biznesowe dotyczące autoryzacji dla poszczególnych użytkowników, zalecenie jest integrowane z Azure Active Directory. Możesz użyć $filter i tożsamości użytkowników, aby [przyciąć wyniki wyszukiwania](search-security-trimming-for-azure-search-with-aad.md) dokumentów, których użytkownik nie powinien zobaczyć. 

Nie ma możliwości rejestrowania tych informacji niezależnie od ciągu zapytania zawierającego parametr $filter. Aby uzyskać szczegółowe informacje o raportowaniu ciągów zapytań, zobacz temat [monitorowanie zapytań](search-monitor-queries.md) .

## <a name="next-steps"></a>Następne kroki

Fluency z Azure Monitor jest niezbędne do nadzoru dowolnej usługi platformy Azure, w tym takich zasobów jak Azure Wyszukiwanie poznawcze. Jeśli nie znasz Azure Monitor, poświęć trochę czasu na zapoznanie się z artykułami dotyczącymi zasobów. Oprócz samouczków, w tym miejscu warto zacząć od artykułu.

> [!div class="nextstepaction"]
> [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
