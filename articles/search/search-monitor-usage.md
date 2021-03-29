---
title: Monitorowanie operacji i aktywności
titleSuffix: Azure Cognitive Search
description: Włącz rejestrowanie, Pobierz metryki działania zapytania, użycie zasobów i inne dane systemu z usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: aa224a09317aafd49ae10c89ae0c50455ddd4602
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709926"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitoruj operacje i działania Wyszukiwanie poznawcze platformy Azure

Ten artykuł zawiera omówienie pojęć i narzędzi związanych z monitorowaniem Wyszukiwanie poznawcze platformy Azure. Do całościowego monitorowania można użyć kombinacji wbudowanych funkcji i usług dodatków, takich jak Azure Monitor.

Całkowicie można śledzić następujące elementy:

* Usługa: kondycja/dostępność oraz zmiany w konfiguracji usługi.
* Magazyn: zarówno używany, jak i dostępny, wraz z licznikami dla każdego typu zawartości względem limitu przydziału dozwolonego dla warstwy usług.
* Działanie zapytania: wolumin, opóźnienie i zapytania z ograniczeniami lub porzucone. Zarejestrowane żądania zapytań wymagają [Azure monitor](#add-azure-monitor).
* Działanie indeksowania: wymaga [rejestrowania diagnostycznego](#add-azure-monitor) w Azure monitor.

Usługa wyszukiwania nie obsługuje uwierzytelniania na użytkownika, więc w dziennikach nie zostaną znalezione żadne informacje o tożsamości.

## <a name="built-in-monitoring"></a>Wbudowane monitorowanie

Wbudowane monitorowanie odnosi się do działań, które są rejestrowane przez usługę wyszukiwania. Z wyjątkiem diagnostyki, nie jest wymagana żadna konfiguracja dla tego poziomu monitorowania.

Usługa Azure Wyszukiwanie poznawcze przechowuje dane wewnętrzne w ramach 30-dniowego harmonogramu raportowania dotyczącego kondycji usług i metryk zapytań, które można znaleźć w portalu lub za pomocą tych [interfejsów API REST](#monitoring-apis).

Poniższy zrzut ekranu ułatwia lokalizowanie informacji o monitorowaniu w portalu. Dane staną się dostępne zaraz po rozpoczęciu korzystania z usługi. Strony portalu są odświeżane co kilka minut.

* Karta **monitorowanie** na głównej stronie przeglądu pokazuje ilość zapytań, opóźnienia i informacje o tym, czy usługa podlega ciśnieniu.
* **Dziennik aktywności** w okienku nawigacji po lewej stronie jest połączony z Azure Resource Manager. Dziennik aktywności raportuje działania podejmowane przez Menedżer zasobów: dostępność i stan usługi, zmiany pojemności (repliki i partycje) oraz działania związane z kluczami interfejsu API.
* Ustawienia **monitorowania** , w dalszej postaci, umożliwiają Konfigurowanie alertów, wizualizacji metryk i dzienników diagnostycznych. Utwórz je, gdy będą potrzebne. Po zebraniu i zapisaniu danych można wykonywać zapytania lub wizualizować informacje w celu uzyskania szczegółowych informacji.

  ![Azure Monitor integrację w usłudze wyszukiwania](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrację w usłudze wyszukiwania")

> [!NOTE]
> Ze względu na to, że strony portalu są odświeżane co kilka minut, raportowane liczby są przybliżone i mają na celu ogólne zrozumienie, jak dobrze system obsługuje żądania. Rzeczywiste metryki, takie jak zapytania na sekundę (zapytań), mogą być większe lub mniejsze niż liczba wyświetlana na stronie. Jeśli dokładność jest wymagana, należy rozważyć użycie interfejsów API.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>Interfejsy API przydatne do monitorowania

Korzystając z następujących interfejsów API, można pobrać te same informacje, które znajdują się na kartach monitorowanie i użycie w portalu.

* [Pobierz statystyki usługi](/rest/api/searchservice/get-service-statistics)
* [Pobierz statystyki indeksu](/rest/api/searchservice/get-index-statistics)
* [Pobierz liczby dokumentów](/rest/api/searchservice/count-documents)
* [Pobierz stan indeksatora](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Dzienniki aktywności i kondycja usługi

Na stronie [**Dziennik aktywności**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) w portalu zbierane są informacje z Azure Resource Manager i raportów o zmianach w kondycji usługi. Dziennik aktywności można monitorować pod kątem krytycznych, błędów i ostrzeżeń związanych z kondycją usługi.

Typowe wpisy obejmują odwołania do kluczy interfejsu API — ogólne powiadomienia informacyjne, takie jak *Pobieranie klucza administratora* i *Uzyskiwanie kluczy zapytań*. Te działania wskazują żądania, które zostały wykonane przy użyciu klucza administratora (Tworzenie lub usuwanie obiektów) lub klucza zapytania, ale nie wyświetlają samego żądania. Aby uzyskać informacje dotyczące tego ziarna, należy skonfigurować rejestrowanie diagnostyczne.

Możesz uzyskać dostęp do **dziennika aktywności** z okienka nawigacji po lewej stronie lub z powiadomień w górnym pasku poleceń okna lub na stronie **diagnozowanie i rozwiązywanie problemów** .

### <a name="monitor-storage-in-the-usage-tab"></a>Monitorowanie magazynu na karcie użycie

W przypadku monitorowania wizualnego w portalu karta **użycie** pokazuje dostępność zasobów względem bieżących [limitów](search-limits-quotas-capacity.md) narzuconych przez warstwę usług. Jeśli podejmujesz decyzje dotyczące warstwy, [która ma być używana na potrzeby obciążeń produkcyjnych](search-sku-tier.md), lub czy należy [dostosować liczbę aktywnych replik i partycji](search-capacity-planning.md), te metryki mogą pomóc w podejmowaniu tych decyzji, pokazując, jak szybko zużywane są zasoby i jak również Bieżąca konfiguracja obsługuje istniejące obciążenie.

Poniższa ilustracja dotyczy bezpłatnej usługi, która jest ograniczona do 3 obiektów każdego typu i 50 MB miejsca w magazynie. Usługa podstawowa lub standardowa ma wyższe limity i w przypadku zwiększenia liczby partycji Maksymalna ilość miejsca w magazynie jest proporcjonalna.

![Stan użycia względem limitów warstwy](./media/search-monitor-usage/usage-tab.png
 "Stan użycia względem limitów warstwy")

> [!NOTE]
> Alerty powiązane z magazynem nie są obecnie dostępne; Użycie magazynu nie jest agregowane lub zarejestrowane w tabeli **AzureMetrics** w Azure monitor. Aby uzyskać alerty magazynu, należy [utworzyć niestandardowe rozwiązanie](../azure-monitor/insights/solutions.md) , które emituje powiadomienia związane z zasobami, gdzie kod sprawdza rozmiar magazynu i obsługuje odpowiedź.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Monitorowanie dodatków za pomocą Azure Monitor

Wiele usług, w tym Azure Wyszukiwanie poznawcze, integrują się z [Azure monitor](../azure-monitor/index.yml) w celu uzyskania dodatkowych alertów, metryk i danych diagnostycznych rejestrowania. 

[Włącz rejestrowanie diagnostyczne](search-monitor-logs.md) dla usługi Search, jeśli chcesz kontrolować zbieranie i przechowywanie danych. Zarejestrowane zdarzenia przechwycone przez Azure Monitor są przechowywane w tabeli **AzureDiagnostics** i składają się z danych operacyjnych związanych z zapytaniami i indeksami.

Azure Monitor oferuje kilka opcji magazynu, a wybór określa, jak można wykorzystać dane:

* Wybierz pozycję Azure Blob Storage, aby [wizualizować dane dziennika](search-monitor-logs-powerbi.md) w raportach Power BI.
* Wybierz Log Analytics, jeśli chcesz eksplorować dane za poorednictwem zapytań Kusto.

Azure Monitor ma własną strukturę rozliczeń i dzienniki diagnostyczne, do których odwołuje się ta sekcja, mają skojarzony koszt. Aby uzyskać więcej informacji, zobacz [użycie i szacowane koszty w Azure monitor](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Monitorowanie dostępu użytkowników

Ponieważ indeksy wyszukiwania są składnikiem większej aplikacji klienckiej, nie ma żadnej wbudowanej metodologii do kontrolowania i monitorowania dostępu dla poszczególnych użytkowników do indeksu. Założono, że żądania pochodzą z aplikacji klienckiej, która wyświetla żądanie administratora lub zapytania. Operacje odczytu i zapisu administratora obejmują tworzenie, aktualizowanie i usuwanie obiektów w całej usłudze. Operacje tylko do odczytu są zapytania względem kolekcji dokumentów, w zakresie do pojedynczego indeksu. 

W związku z tym informacje widoczne w dziennikach aktywności są odwołaniami do wywołań przy użyciu kluczy administracyjnych lub kluczy zapytań. Odpowiedni klucz jest uwzględniany w żądaniach pochodzących z kodu klienta. Usługa nie jest wyposażona w obsługę tokenów tożsamości ani personifikacji.

Jeśli istnieją wymagania biznesowe dotyczące autoryzacji dla poszczególnych użytkowników, zalecenie jest integrowane z Azure Active Directory. Możesz użyć $filter i tożsamości użytkowników, aby [przyciąć wyniki wyszukiwania](search-security-trimming-for-azure-search-with-aad.md) dokumentów, których użytkownik nie powinien zobaczyć. 

Nie ma możliwości rejestrowania tych informacji niezależnie od ciągu zapytania zawierającego parametr $filter. Aby uzyskać szczegółowe informacje o raportowaniu ciągów zapytań, zobacz temat [monitorowanie zapytań](search-monitor-queries.md) .

## <a name="next-steps"></a>Następne kroki

Fluency z Azure Monitor jest niezbędne do nadzoru dowolnej usługi platformy Azure, w tym takich zasobów jak Azure Wyszukiwanie poznawcze. Jeśli nie znasz Azure Monitor, poświęć trochę czasu na zapoznanie się z artykułami dotyczącymi zasobów. Oprócz samouczków, w tym miejscu warto zacząć od artykułu.

> [!div class="nextstepaction"]
> [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)