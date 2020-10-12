---
title: Administrowanie usługą w portalu
titleSuffix: Azure Cognitive Search
description: Zarządzanie usługą Azure Wyszukiwanie poznawcze, hostowaną usługą wyszukiwania w chmurze na Microsoft Azure przy użyciu Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 84ddc4b427f6dc168c044f34b41e81e3b0ff19e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935045"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administracja usługi dla platformy Azure Wyszukiwanie poznawcze w Azure Portal

> [!div class="op_single_selector"]
>
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs API REST](/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania oparta na chmurze służąca do tworzenia bogatego środowiska wyszukiwania w aplikacjach niestandardowych. W tym artykule omówiono zadania administracyjne usługi, które można wykonać w [Azure Portal](https://portal.azure.com) dla usługi wyszukiwania, która została już zainicjowana. Administrowanie usługami jest lekkie przez projektowanie, ograniczone do następujących zadań:

* Sprawdź magazyn przy użyciu linku **użycie** na średnim poziomie strony.
* Sprawdź woluminy zapytań i czas oczekiwania przy użyciu linku **monitorowania** średniej strony i czy żądania zostały ograniczone.
* Zarządzanie dostępem przy użyciu strony **klucze** po lewej stronie.
* Dostosuj pojemność przy użyciu strony **skalowanie** po lewej stronie.

Te same zadania wykonywane w portalu mogą być również obsługiwane programowo za pomocą [interfejsów API zarządzania](/rest/api/searchmanagement/) i polecenia [AZ. Search programu PowerShell](search-manage-powershell.md). Zadania administracyjne są w pełni reprezentowane przez portal i Interfejsy programistyczne. Nie ma żadnych określonych zadań administracyjnych, które są dostępne tylko w jednym elemencie modalnym.

Usługa Azure Wyszukiwanie poznawcze wykorzystuje inne usługi platformy Azure w celu lepszego monitorowania i zarządzania. Same dane przechowywane w usłudze wyszukiwania to zawartość (indeksy, indeksator i definicje źródeł danych oraz inne obiekty). Metryki zgłoszone do stron portalu są pobierane z dzienników wewnętrznych w ramach 30-dniowego cyklu. W przypadku przechowywania dzienników sterowanych przez użytkownika i zdarzeń dodatkowych konieczne będzie [Azure monitor](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Właściwości stałej usługi

Niektóre aspekty usługi wyszukiwania są określane podczas aprowizacji usługi i nie można ich zmienić później:

* Nazwa usługi (nie można zmienić nazwy usługi)
* Lokalizacja usługi (obecnie nie można przenieść nienaruszonej usługi do innego regionu)
* Maksymalna liczba replik i partycji (określona przez warstwę, podstawowa lub standardowa)

Jeśli zaczniesz od warstwy Podstawowa z maksymalną jedną partycją i potrzebujesz więcej partycji, musisz [utworzyć nową usługę](search-create-service-portal.md) w wyższej warstwie i ponownie utworzyć zawartość w nowej usłudze. 

## <a name="administrator-rights"></a>Prawa administratora

Inicjowanie obsługi administracyjnej lub likwidowanie samej usługi może odbywać się przez administratora subskrypcji platformy Azure lub współadministratora.

W przypadku dostępu do punktu końcowego każda osoba mająca dostęp do adresu URL usługi i klucza API-Key ma dostęp do zawartości. Aby uzyskać więcej informacji na temat kluczy, zobacz [Zarządzanie kluczami API-Keys](search-security-api-keys.md).

* Dostęp tylko do odczytu do usługi to prawa do zapytania, zwykle przyznawane aplikacji klienckiej przez nadanie jej adresu URL i klucza zapytania API-Key.
* Uprawnienia do odczytu i zapisu umożliwiają dodawanie, usuwanie i modyfikowanie obiektów serwera, w tym kluczy API, indeksów, indeksatorów, źródeł danych i harmonogramów. Dostęp do odczytu i zapisu jest przyznawany przez podanie adresu URL, klucza interfejsu API administratora.

Prawa do aparatu aprowizacji usług są udzielane za pomocą przypisań ról. [Kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md) to system autoryzacji oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) na potrzeby aprowizacji zasobów platformy Azure. 

W kontekście usługi Azure Wyszukiwanie poznawcze [przypisania ról platformy Azure](search-security-rbac.md) określają, kto może wykonywać zadania, bez względu na to, czy korzystają z [portalu](search-manage.md), [programu POWERSHELL](search-manage-powershell.md), czy [interfejsów API REST zarządzania](/rest/api/searchmanagement/search-howto-management-rest-api):

* Tworzenie lub usuwanie usługi
* Skalowanie usługi
* Usuwanie lub ponowne generowanie kluczy interfejsu API
* Włącz rejestrowanie diagnostyczne (Utwórz usługi)
* Włączanie analizy ruchu (tworzenie usług)

> [!TIP]
> Korzystając z mechanizmów opartych na platformie Azure, można zablokować subskrypcję lub zasób, aby zapobiec przypadkowemu lub nieautoryzowanemu usunięciu usługi wyszukiwania przez użytkowników z uprawnieniami administratora. Aby uzyskać więcej informacji, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanemu usunięciu](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Rejestrowanie i informacje o systemie

W warstwie Podstawowa i powyżej firma Microsoft monitoruje wszystkie usługi Wyszukiwanie poznawcze platformy Azure pod kątem dostępności na 99,9% według umów dotyczących poziomu usług (SLA). Jeśli usługa działa wolno lub żądanie przepływności spadnie poniżej progów SLA, zespoły pomocy technicznej Przejrzyj pliki dziennika dostępne dla nich i rozwiąż problem.

Usługa Azure Wyszukiwanie poznawcze wykorzystuje [Azure monitor](../azure-monitor/index.yml) do gromadzenia i przechowywania działań związanych z indeksowaniem i wykonywaniem zapytań. Usługa wyszukiwania sama przechowuje tylko swoją zawartość (indeksy, definicje indeksatorów, definicje źródeł danych, definicje zestawu umiejętności, mapy synonimów). Buforowanie i zarejestrowane informacje są przechowywane poza usługą, często na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat rejestrowania obciążeń indeksowania i zapytań, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

W oparciu o ogólne informacje o usłudze, korzystając tylko z udogodnień wbudowanych w platformę Azure Wyszukiwanie poznawcze, możesz uzyskać informacje w następujący sposób:

* Korzystając ze strony **Przegląd** usługi, za pośrednictwem powiadomień, właściwości i komunikatów o stanie.
* [Pobieranie właściwości usługi](/rest/api/searchmanagement/services)przy użyciu [programu PowerShell](search-manage-powershell.md) lub [interfejsu API REST zarządzania](/rest/api/searchmanagement/) . Nie ma nowych informacji ani operacji dostępnych w warstwie programistycznej. Interfejsy istnieją, aby można było pisać skrypty.

## <a name="monitor-resource-usage"></a>Monitorowanie użycia zasobów

Na pulpicie nawigacyjnym monitorowanie zasobów jest ograniczone do informacji wyświetlanych na pulpicie nawigacyjnym usługi i kilku metryk, które można uzyskać, wykonując zapytania dotyczące usługi. Na pulpicie nawigacyjnym usługi, w sekcji użycie, można szybko określić, czy poziomy zasobów partycji są odpowiednie dla aplikacji. Możesz zainicjować obsługę zasobów zewnętrznych, takich jak monitorowanie platformy Azure, jeśli chcesz przechwytywać i utrwalać zarejestrowane zdarzenia. Aby uzyskać więcej informacji, zobacz [monitorowanie platformy Azure wyszukiwanie poznawcze](search-monitor-usage.md).

Korzystając z interfejsu API REST usługi Search, można programowo uzyskać liczbę dokumentów i indeksów: 

* [Pobierz statystyki indeksu](/rest/api/searchservice/Get-Index-Statistics)
* [Liczenie dokumentów](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Odzyskiwanie po awarii i awaria usługi

Mimo że możemy odzyskać Twoje dane, usługa Azure Wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku wystąpienia awarii w klastrze lub na poziomie centrum danych. Jeśli klaster ulegnie awarii w centrum danych, zespół operacyjny wykryje i zacznie działać do przywracania usługi. Podczas przywracania usługi możesz zażądać kredytów na korzystanie z usługi, aby wyrównać niedostępność usługi na [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeśli ciągła usługa jest wymagana w przypadku wystąpienia awarii poza kontrolą firmy Microsoft, można [zainicjować obsługę administracyjną dodatkowej usługi](search-create-service-portal.md) w innym regionie i wdrożyć strategię replikacji geograficznej w celu zapewnienia, że indeksy są w pełni nadmiarowe dla wszystkich usług.

Klienci używający [indeksatorów](search-indexer-overview.md) do wypełniania i odświeżania indeksów mogą obsługiwać odzyskiwanie po awarii za pomocą indeksatorów specyficznych dla lokalizacji geograficznych wykorzystujących to samo źródło danych. Dwie usługi w różnych regionach, z których każdy uruchamia indeksator, może indeksować to samo źródło danych w celu zapewnienia nadmiarowości geograficznej. W przypadku indeksowania ze źródeł danych, które są również geograficznie nadmiarowe, należy pamiętać, że indeksatory usługi Azure Wyszukiwanie poznawcze mogą wykonywać tylko indeksowanie przyrostowe (Scalanie aktualizacji z nowych, zmodyfikowanych lub usuniętych dokumentów) z replik podstawowych. W przypadku zdarzenia trybu failover Pamiętaj, aby ponownie wskazać indeksator do nowej repliki podstawowej. 

Jeśli nie używasz indeksatorów, użyj kodu aplikacji do wypychania obiektów i danych do różnych usług wyszukiwania równolegle. Aby uzyskać więcej informacji, zobacz [wydajność i optymalizacja na platformie Azure wyszukiwanie poznawcze](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Ponieważ platforma Azure Wyszukiwanie poznawcze nie jest podstawowym rozwiązaniem do przechowywania danych, firma Microsoft nie udostępnia formalnego mechanizmu tworzenia kopii zapasowych i przywracania samoobsługowego. Można jednak użyć przykładowego kodu do **przywracania kopii zapasowej** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) , aby utworzyć kopię zapasową definicji indeksu i migawki do serii plików JSON, a następnie użyć tych plików do przywrócenia indeksu, w razie potrzeby. To narzędzie umożliwia również przenoszenie indeksów między warstwami usług.

W przeciwnym razie kod aplikacji używany do tworzenia i wypełniania indeksu jest opcją przywracania de facto w przypadku usunięcia indeksu przez pomyłkę. Aby ponownie skompilować indeks, należy go usunąć (przy założeniu, że istnieje), ponownie utworzyć indeks w usłudze i załadować ponownie przez pobranie danych z podstawowego magazynu danych.

## <a name="scale-up-or-down"></a>Skalowanie w górę lub w dół

Każda usługa wyszukiwania jest uruchamiana z co najmniej jedną repliką i jedną partycją. Jeśli zarejestrowano się w celu uzyskania [większej pojemności](search-limits-quotas-capacity.md), kliknij pozycję **Skaluj** w okienku nawigacji po lewej stronie, aby dostosować użycie zasobów.

Po dodaniu pojemności za pośrednictwem dowolnego zasobu usługa korzysta z nich automatycznie. W Twojej części nie są wymagane żadne dalsze działania, ale istnieje niewielkie opóźnienie przed zrealizowaniem nowego zasobu. Udostępnienie dodatkowych zasobów może potrwać 15 minut lub dłużej.

### <a name="add-replicas"></a>Dodaj repliki

Rosnące zapytania na sekundę (zapytań) lub osiąganie wysokiej dostępności są wykonywane przez dodanie replik. Każda replika ma jedną kopię indeksu, więc dodanie jednej repliki tłumaczy do jednego indeksu dostępnego do obsługi żądań zapytań usługi. Aby zapewnić wysoką dostępność, wymagane są co najmniej 3 repliki (zobacz [Dostosowywanie pojemności](search-capacity-planning.md) do szczegółów).

Usługa wyszukiwania mająca więcej replik może równoważyć obciążenie żądaniami zapytań w większej liczbie indeksów. Uwzględniając poziom woluminu zapytania, przepływność zapytań jest szybsza, gdy istnieje więcej kopii indeksu dostępnych do obsługi żądania. Jeśli występują opóźnienia zapytań, można spodziewać się pozytywnego wpływu na wydajność, gdy dodatkowe repliki są w trybie online.

Mimo że przepływność zapytań jest dodawana podczas dodawania replik, nie jest ona dokładnie podwójna ani potrójna podczas dodawania replik do usługi. Wszystkie aplikacje wyszukiwania podlegają zewnętrznym czynnikom, które mogą wykonywać pingowanie względem wydajności zapytań. Złożone zapytania i opóźnienia sieci to dwa czynniki, które przyczyniają się do wahań czasów odpowiedzi na zapytanie.

### <a name="add-partitions"></a>Dodawanie partycji

Jest to bardziej powszechne w przypadku dodawania replik, ale w przypadku ograniczenia magazynu można dodać partycje, aby uzyskać więcej pojemności. Warstwa, w której zainicjowano obsługę partycji, określa, czy można dodać partycje. Warstwa Podstawowa jest zablokowana na jednej partycji. Warstwy Standardowa i nowsze obsługują dodatkowe partycje.

Partycje są dodawane w dzielnikach 12 (w odróżnieniu od 1, 2, 3, 4, 6 lub 12). To jest artefakt fragmentowania. Indeks jest tworzony w 12 fragmentów, który może być przechowywany na 1 partycji lub równo podzielony na 2, 3, 4, 6 lub 12 partycji (jedną fragmentu na partycję).

### <a name="remove-replicas"></a>Usuwanie replik

Po upływie okresów dużych ilości zapytań można użyć suwaka, aby zmniejszyć liczbę replik po ukończeniu obciążeń zapytań wyszukiwania. W Twojej części nie są wymagane żadne dalsze czynności. Zmniejszenie liczby replik zwalnia maszyny wirtualne w centrum danych. Wykonywanie zapytań i operacji pozyskiwania danych zostanie teraz uruchomione na mniejszej liczbie maszyn wirtualnych niż wcześniej. Minimalnym wymaganiem jest jedna replika.

### <a name="remove-partitions"></a>Usuwanie partycji

W przeciwieństwie do usuwania replik, które nie wymagają żadnych dodatkowych nakładów pracy, może być konieczne wykonanie pewnych zadań w przypadku używania większej ilości miejsca niż można je zmniejszyć. Na przykład jeśli Twoje rozwiązanie używa trzech partycji, downsizing do jednej lub dwóch partycji wygeneruje błąd, jeśli nowe miejsce do magazynowania jest mniejsze niż wymagane do hostowania Twojego indeksu. Jak można się spodziewać, wybrane opcje polegają na usunięciu indeksów lub dokumentów w skojarzonym indeksie, aby zwolnić miejsce lub zachować bieżącą konfigurację.

Nie ma metody wykrywania informującej, który indeks fragmentów jest przechowywany w określonych partycjach. Każda partycja zapewnia około 25 GB miejsca w magazynie, dlatego należy zmniejszyć ilość miejsca w magazynie do rozmiaru, który można obsłużyć przez liczbę posiadanych partycji. Jeśli chcesz przywrócić jedną partycję, należy dopasować wszystkie 12 fragmentów.

Aby ułatwić planowanie w przyszłości, warto sprawdzić magazyn (przy użyciu [statystyk Get index](/rest/api/searchservice/Get-Index-Statistics)), aby zobaczyć, ile faktycznie używasz. 

## <a name="next-steps"></a>Następne kroki

* Automatyzowanie przy użyciu [programu PowerShell](search-manage-powershell.md)

* Przegląd technik [wydajności i optymalizacji](search-performance-optimization.md)

* Przejrzyj [funkcje zabezpieczeń](search-security-overview.md) , aby chronić zawartość i operacje

* Włączanie [rejestrowania diagnostycznego](search-monitor-logs.md) w celu monitorowania obciążeń zapytań i indeksowania