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
ms.date: 11/04/2019
ms.openlocfilehash: 3abbf2c8e0734d17aabadd2ae5f61cc03889964b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282928"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administracja usługi dla platformy Azure Wyszukiwanie poznawcze w Azure Portal
> [!div class="op_single_selector"]
> * [Narzędzia](search-manage-powershell.md)
> * [INTERFEJS API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Zestaw SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania oparta na chmurze służąca do tworzenia bogatego środowiska wyszukiwania w aplikacjach niestandardowych. W tym artykule omówiono zadania administracyjne usługi, które można wykonać w [Azure Portal](https://portal.azure.com) dla usługi wyszukiwania, która została już zainicjowana. Administrowanie usługami jest lekkie przez projektowanie, ograniczone do następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do *kluczy API-Keys* używanych na potrzeby dostępu do odczytu lub zapisu do usługi.
> * Dostosuj pojemność usługi, zmieniając alokację partycji i replik.
> * Monitoruj użycie zasobów względem maksymalnych limitów warstwy usług.

Należy zauważyć, że *uaktualnienie* nie jest wymienione jako zadanie administracyjne. Ponieważ zasoby są alokowane po aprowizacji usługi, przeniesienie do innej warstwy wymaga nowej usługi. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie usługi Azure wyszukiwanie poznawcze](search-create-service-portal.md).

Możesz monitorować wolumin zapytań i inne metryki oraz korzystać z tych szczegółowych informacji, aby dostosować usługę do krótszych czasów odpowiedzi. Aby uzyskać więcej informacji, zobacz [monitorowanie metryk użycia i kwerendy](search-monitor-usage.md) oraz [wydajność i optymalizację](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Prawa administratora
Inicjowanie obsługi administracyjnej lub likwidowanie samej usługi może odbywać się przez administratora subskrypcji platformy Azure lub współadministratora.

W ramach usługi każda osoba mająca dostęp do adresu URL usługi i klucza API administratora ma dostęp do odczytu i zapisu do usługi. Dostęp do odczytu i zapisu umożliwia dodawanie, usuwanie i modyfikowanie obiektów serwera, w tym klucze API, indeksy, indeksatory, źródła danych, harmonogramy i przypisania ról, które są implementowane za pomocą [ról zdefiniowanych](search-security-rbac.md)przez funkcję RBAC.

Wszystkie interakcje użytkownika z usługą Azure Wyszukiwanie poznawcze mieszczą się w jednym z następujących trybów: dostęp do odczytu i zapisu do usługi (prawa administratora) lub dostęp tylko do odczytu do usługi (uprawnienia zapytania). Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami interfejsu API](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Rejestrowanie i informacje o systemie
Usługa Azure Wyszukiwanie poznawcze nie uwidacznia plików dziennika dla pojedynczej usługi za pomocą portalu ani interfejsów programistycznych. W warstwie Podstawowa i powyżej firma Microsoft monitoruje wszystkie usługi Wyszukiwanie poznawcze platformy Azure pod kątem dostępności na 99,9% według umów dotyczących poziomu usług (SLA). Jeśli usługa działa wolno lub żądanie przepływności spadnie poniżej progów SLA, zespoły pomocy technicznej Przejrzyj pliki dziennika dostępne dla nich i rozwiąż problem.

W zakresie ogólnych informacji o usłudze możesz uzyskać informacje w następujący sposób:

* W portalu na pulpicie nawigacyjnym usługi, za pomocą powiadomień, właściwości i komunikatów o stanie.
* Korzystanie z [programu PowerShell](search-manage-powershell.md) lub [interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) w celu [pobrania właściwości usługi](https://docs.microsoft.com/rest/api/searchmanagement/services)lub stanu użycia zasobów indeksu.


<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorowanie użycia zasobów
Na pulpicie nawigacyjnym monitorowanie zasobów jest ograniczone do informacji wyświetlanych na pulpicie nawigacyjnym usługi i kilku metryk, które można uzyskać, wykonując zapytania dotyczące usługi. Na pulpicie nawigacyjnym usługi, w sekcji użycie, można szybko określić, czy poziomy zasobów partycji są odpowiednie dla aplikacji. Możesz zainicjować obsługę zasobów zewnętrznych, takich jak monitorowanie platformy Azure, jeśli chcesz przechwytywać i utrwalać zarejestrowane zdarzenia. Aby uzyskać więcej informacji, zobacz [monitorowanie platformy Azure wyszukiwanie poznawcze](search-monitor-usage.md).

Korzystając z interfejsu API REST usługi Search, można programowo uzyskać liczbę dokumentów i indeksów: 

* [Pobierz statystyki indeksu](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Liczenie dokumentów](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Odzyskiwanie po awarii i awaria usługi

Mimo że możemy odzyskać Twoje dane, usługa Azure Wyszukiwanie poznawcze nie zapewnia natychmiastowej pracy w trybie failover w przypadku wystąpienia awarii w klastrze lub na poziomie centrum danych. Jeśli klaster ulegnie awarii w centrum danych, zespół operacyjny wykryje i zacznie działać do przywracania usługi. Podczas przywracania usługi możesz zażądać kredytów na korzystanie z usługi, aby wyrównać niedostępność usługi na [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jeśli ciągła usługa jest wymagana w przypadku wystąpienia awarii poza kontrolą firmy Microsoft, można [zainicjować obsługę administracyjną dodatkowej usługi](search-create-service-portal.md) w innym regionie i wdrożyć strategię replikacji geograficznej w celu zapewnienia, że indeksy są w pełni nadmiarowe dla wszystkich usług.

Klienci używający [indeksatorów](search-indexer-overview.md) do wypełniania i odświeżania indeksów mogą obsługiwać odzyskiwanie po awarii za pomocą indeksatorów specyficznych dla lokalizacji geograficznych wykorzystujących to samo źródło danych. Dwie usługi w różnych regionach, z których każdy uruchamia indeksator, może indeksować to samo źródło danych w celu zapewnienia nadmiarowości geograficznej. W przypadku indeksowania ze źródeł danych, które są również geograficznie nadmiarowe, należy pamiętać, że indeksatory usługi Azure Wyszukiwanie poznawcze mogą wykonywać tylko indeksowanie przyrostowe (Scalanie aktualizacji z nowych, zmodyfikowanych lub usuniętych dokumentów) z replik podstawowych. W przypadku zdarzenia trybu failover Pamiętaj, aby ponownie wskazać indeksator do nowej repliki podstawowej. 

Jeśli nie używasz indeksatorów, użyj kodu aplikacji do wypychania obiektów i danych do różnych usług wyszukiwania równolegle. Aby uzyskać więcej informacji, zobacz [wydajność i optymalizacja na platformie Azure wyszukiwanie poznawcze](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Ponieważ platforma Azure Wyszukiwanie poznawcze nie jest podstawowym rozwiązaniem do przechowywania danych, firma Microsoft nie udostępnia formalnego mechanizmu tworzenia kopii zapasowych i przywracania samoobsługowego. Można jednak użyć przykładowego kodu do **przywracania kopii zapasowej** w ramach tego [przykładowego repozytorium platformy Azure wyszukiwanie poznawcze .NET](https://github.com/Azure-Samples/azure-search-dotnet-samples) , aby utworzyć kopię zapasową definicji indeksu i migawki do serii plików JSON, a następnie użyć tych plików do przywrócenia indeksu, w razie potrzeby. To narzędzie umożliwia również przenoszenie indeksów między warstwami usług.

W przeciwnym razie kod aplikacji używany do tworzenia i wypełniania indeksu jest opcją przywracania de facto w przypadku usunięcia indeksu przez pomyłkę. Aby ponownie skompilować indeks, należy go usunąć (przy założeniu, że istnieje), ponownie utworzyć indeks w usłudze i załadować ponownie przez pobranie danych z podstawowego magazynu danych.

<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skalowanie w górę lub w dół
Każda usługa wyszukiwania jest uruchamiana z co najmniej jedną repliką i jedną partycją. Jeśli zarejestrowano się w celu uzyskania [warstwy zapewniającej zasoby dedykowane](search-limits-quotas-capacity.md), kliknij kafelek **skalowanie** na pulpicie nawigacyjnym usługi, aby dostosować użycie zasobów.

Po dodaniu pojemności za pośrednictwem dowolnego zasobu usługa korzysta z nich automatycznie. W Twojej części nie są wymagane żadne dalsze działania, ale istnieje niewielkie opóźnienie przed zrealizowaniem nowego zasobu. Udostępnienie dodatkowych zasobów może potrwać 15 minut lub dłużej.

 ![][10]

### <a name="add-replicas"></a>Dodaj repliki
Rosnące zapytania na sekundę (zapytań) lub osiąganie wysokiej dostępności są wykonywane przez dodanie replik. Każda replika ma jedną kopię indeksu, więc dodanie jednej repliki tłumaczy do jednego indeksu dostępnego do obsługi żądań zapytań usługi. Do wysokiej dostępności są wymagane co najmniej 3 repliki (zobacz [Planowanie pojemności](search-capacity-planning.md) , aby uzyskać szczegółowe informacje).

Usługa wyszukiwania mająca więcej replik może równoważyć obciążenie żądaniami zapytań w większej liczbie indeksów. Uwzględniając poziom woluminu zapytania, przepływność zapytań jest szybsza, gdy istnieje więcej kopii indeksu dostępnych do obsługi żądania. Jeśli występują opóźnienia zapytań, można spodziewać się pozytywnego wpływu na wydajność, gdy dodatkowe repliki są w trybie online.

Mimo że przepływność zapytań jest dodawana podczas dodawania replik, nie jest ona dokładnie podwójna ani potrójna podczas dodawania replik do usługi. Wszystkie aplikacje wyszukiwania podlegają zewnętrznym czynnikom, które mogą wykonywać pingowanie względem wydajności zapytań. Złożone zapytania i opóźnienia sieci to dwa czynniki, które przyczyniają się do wahań czasów odpowiedzi na zapytanie.

### <a name="add-partitions"></a>Dodawanie partycji
Większość aplikacji usługi ma wbudowaną potrzebę tworzenia większej liczby replik, a nie partycji. W przypadku, gdy jest wymagana większa liczba dokumentów, można dodać partycje, jeśli utworzono konto w usłudze Standard. Warstwa Podstawowa nie zapewnia dodatkowych partycji.

W warstwie Standardowa partycje są dodawane w wielokrotnościach 12 (w odróżnieniu od 1, 2, 3, 4, 6 lub 12). To jest artefakt fragmentowania. Indeks jest tworzony w 12 fragmentów, który może być przechowywany na 1 partycji lub równo podzielony na 2, 3, 4, 6 lub 12 partycji (jedną fragmentu na partycję).

### <a name="remove-replicas"></a>Usuwanie replik
Po upływie okresów dużych ilości zapytań można użyć suwaka, aby zmniejszyć liczbę replik po ukończeniu obciążeń zapytań wyszukiwania. W Twojej części nie są wymagane żadne dalsze czynności. Zmniejszenie liczby replik zwalnia maszyny wirtualne w centrum danych. Wykonywanie zapytań i operacji pozyskiwania danych zostanie teraz uruchomione na mniejszej liczbie maszyn wirtualnych niż wcześniej. Minimalnym wymaganiem jest jedna replika.

### <a name="remove-partitions"></a>Usuwanie partycji
W przeciwieństwie do usuwania replik, które nie wymagają żadnych dodatkowych nakładów pracy, może być konieczne wykonanie pewnych zadań w przypadku używania większej ilości miejsca niż można je zmniejszyć. Na przykład jeśli Twoje rozwiązanie używa trzech partycji, downsizing do jednej lub dwóch partycji wygeneruje błąd, jeśli nowe miejsce do magazynowania jest mniejsze niż wymagane do hostowania Twojego indeksu. Jak można się spodziewać, wybrane opcje polegają na usunięciu indeksów lub dokumentów w skojarzonym indeksie, aby zwolnić miejsce lub zachować bieżącą konfigurację.

Nie ma metody wykrywania informującej, który indeks fragmentów jest przechowywany w określonych partycjach. Każda partycja zapewnia około 25 GB miejsca w magazynie, dlatego należy zmniejszyć ilość miejsca w magazynie do rozmiaru, który można obsłużyć przez liczbę posiadanych partycji. Jeśli chcesz przywrócić jedną partycję, należy dopasować wszystkie 12 fragmentów.

Aby ułatwić planowanie w przyszłości, warto sprawdzić magazyn (przy użyciu [statystyk Get index](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)), aby zobaczyć, ile faktycznie używasz. 

<a id="next-steps"></a>

## <a name="next-steps"></a>Następne kroki
Po zrozumieniu koncepcji związanych z administracją usługi należy rozważyć użycie [programu PowerShell](search-manage-powershell.md) w celu zautomatyzowania zadań.

Zalecamy również przejrzenie artykułu dotyczącego [wydajności i optymalizacji](search-performance-optimization.md).

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



