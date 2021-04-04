---
title: Co to jest pula wystąpień zarządzanych Azure SQL?
titleSuffix: Azure SQL Managed Instance
description: Poznaj usługi Azure SQL Managed Instances (wersja zapoznawcza) — funkcję, która zapewnia wygodny i ekonomiczny sposób migrowania mniejszych baz danych SQL Server do chmury na dużą skalę i zarządzania wieloma wystąpieniami zarządzanymi.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347068"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Co to jest pula wystąpień zarządzanych Azure SQL (wersja zapoznawcza)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pule wystąpień w wystąpieniu zarządzanym usługi Azure SQL zapewniają wygodny i ekonomiczny sposób migrowania mniejszych wystąpień SQL Server do chmury na dużą skalę.

Pule wystąpień umożliwiają wstępną aprowizację zasobów obliczeniowych zgodnie z całkowitymi wymaganiami migracji. Następnie możesz wdrożyć kilka osobnych wystąpień zarządzanych — do osiągnięcia wstępnie aprowizowanego poziomu obliczeń. Na przykład w przypadku wstępnego udostępnienia usługi 8 rdzeni wirtualnych można wdrożyć wystąpienie 2 2-rdzeń wirtualny i 1 4-rdzeń wirtualny, a następnie przeprowadzić migrację baz danych do tych wystąpień. Przed udostępnieniem pul wystąpień mniejsze i mniejsze obciążenia intensywnie korzystające z mocy obliczeniowej będą często musiały zostać skonsolidowane w większym zarządzanym wystąpieniu podczas migracji do chmury. Konieczność migrowania grup baz danych do dużego wystąpienia zwykle wymaga starannego planowania pojemności i zarządzania zasobami, dodatkowych zagadnień związanych z zabezpieczeniami oraz pewnej dodatkowej konsolidacji danych na poziomie wystąpienia.

Ponadto pule wystąpień obsługują natywną integrację sieci wirtualnej, dzięki czemu można wdrożyć wiele pul wystąpień i wiele pojedynczych wystąpień w tej samej podsieci.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Pule wystąpień zapewniają następujące korzyści:

1. Możliwość hostowania wystąpień 2-rdzeń wirtualny. *\* Tylko dla wystąpień w pulach wystąpień*.
2. Czas wdrożenia przewidywalny i szybki (do 5 minut).
3. Minimalna alokacja adresów IP.

Na poniższym diagramie przedstawiono pulę wystąpień z wieloma wystąpieniami zarządzanymi wdrożonymi w ramach podsieci sieci wirtualnej.

![Pula wystąpień z wieloma wystąpieniami](./media/instance-pools-overview/instance-pools1.png)

Pule wystąpień umożliwiają wdrożenie wielu wystąpień na tej samej maszynie wirtualnej, gdzie rozmiar obliczeń maszyny wirtualnej zależy od łącznej liczby rdzeni wirtualnych przydzieloną dla puli. Ta architektura umożliwia *partycjonowanie* maszyny wirtualnej w wielu wystąpieniach, które mogą być dowolnym obsługiwanym rozmiarem, w tym 2 rdzeni wirtualnych (wystąpienia 2-rdzeń wirtualny są dostępne tylko dla wystąpień w pulach).

Po wdrożeniu początkowym operacje zarządzania w wystąpieniach w puli są znacznie szybsze. Wynika to z faktu, że wdrożenie lub rozszerzenie [klastra wirtualnego](connectivity-architecture-overview.md#high-level-connectivity-architecture) (dedykowany zestaw maszyn wirtualnych) nie jest częścią aprowizacji wystąpienia zarządzanego.

Ponieważ wszystkie wystąpienia w puli współużytkują tę samą maszynę wirtualną, łączna alokacja adresów IP nie zależy od liczby wdrożonych wystąpień, co jest wygodne do wdrożenia w podsieciach z wąskim zakresem adresów IP.

Każda pula ma stały przydział adresów IP zawierający tylko dziewięć adresów IP (nie obejmuje pięciu adresów IP w podsieci, które są zarezerwowane do własnych potrzeb). Aby uzyskać szczegółowe informacje, zobacz [wymagania dotyczące rozmiaru podsieci dla pojedynczych wystąpień](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Scenariusze aplikacji

Poniższa lista zawiera główne przypadki użycia, w których należy uwzględnić pule wystąpień:

- Migracja *grupy wystąpień SQL Server* w tym samym czasie, w których większość jest mniejszym rozmiarem (na przykład 2 lub 4 rdzeni wirtualnych).
- Scenariusze, w których są ważne *i przewidywalne Tworzenie lub skalowanie wystąpienia* . Na przykład wdrożenie nowej dzierżawy w środowisku aplikacji SaaS z wieloma dzierżawcami, które wymaga możliwości na poziomie wystąpienia.
- Scenariusze, w których obowiązuje *stały koszt* lub *limit wydatków* . Można na przykład uruchomić udostępnione środowiska deweloperskie lub testowe o stałym (lub nierzadko zmienionym rozmiarze), gdzie okresowo Wdrażaj wystąpienia zarządzane.
- Scenariusze, w których *minimalna alokacja adresów IP* w podsieci sieci wirtualnej jest ważna. Wszystkie wystąpienia w puli współużytkują maszynę wirtualną, więc liczba przyznanych adresów IP jest mniejsza niż w przypadku pojedynczych wystąpień.

## <a name="architecture"></a>Architektura

Pule wystąpień mają podobną architekturę do regularnego (*pojedynczego*) wystąpienia zarządzanego. Aby obsługiwać [wdrożenia w ramach sieci wirtualnych platformy Azure](../../virtual-network/virtual-network-for-azure-services.md) i zapewnić izolację i bezpieczeństwo klientów, pule wystąpień również polegają na [klastrach wirtualnych](connectivity-architecture-overview.md#high-level-connectivity-architecture). Klastry wirtualne reprezentują dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta.

Główna różnica między dwoma modelami wdrażania polega na tym, że pule wystąpień zezwalają na wiele wdrożeń procesów SQL Server w tym samym węźle maszyny wirtualnej, które są zasobami zarządzanymi przy użyciu [obiektów zadań systemu Windows](/windows/desktop/ProcThread/job-objects), natomiast pojedyncze wystąpienia są zawsze same w węźle maszyny wirtualnej.

Na poniższym diagramie przedstawiono pulę wystąpień i dwa poszczególne wystąpienia wdrożone w tej samej podsieci i przedstawiono główne szczegóły architektury dla obu modeli wdrażania:

![Pula wystąpień i dwa poszczególne wystąpienia](./media/instance-pools-overview/instance-pools2.png)

Każda pula wystąpień tworzy oddzielny klaster wirtualny poniżej. Wystąpienia w puli i pojedyncze wystąpienia wdrożone w tej samej podsieci nie współdzielą zasobów obliczeniowych przyznanych do SQL Server procesów i składników bramy, co zapewnia przewidywalność wydajności.

## <a name="resource-limitations"></a>Ograniczenia zasobów

Istnieje kilka ograniczeń zasobów dotyczących pul wystąpień i wystąpień wewnątrz pul:

- Pule wystąpień są dostępne tylko na sprzęcie 5 rdzeń.
- Wystąpienia zarządzane w puli mają dedykowany procesor CPU i pamięć RAM, więc zagregowana liczba rdzeni wirtualnych we wszystkich wystąpieniach musi być mniejsza lub równa liczbie rdzeni wirtualnych przydzielonych do puli.
- Wszystkie [limity na poziomie wystąpienia](resource-limits.md#service-tier-characteristics) mają zastosowanie do wystąpień utworzonych w ramach puli.
- Oprócz limitów na poziomie wystąpienia istnieją również dwa limity nakładane *na poziomie puli wystąpień*:
  - Łączny rozmiar magazynu na pulę (8 TB).
  - Łączna liczba baz danych użytkownika na pulę. Ten limit zależy od wartości rdzeni wirtualnych puli:
    - 8 rdzeni wirtualnych Pula obsługuje do 200 baz danych,
    - 16 rdzeni wirtualnych puli obsługuje do 400 baz danych,
    - 24 i większa Pula rdzeni wirtualnych obsługuje do 500 baz danych.
- Nie można ustawić administratora usługi AAD dla wystąpień wdrożonych wewnątrz puli wystąpień, dlatego nie można użyć uwierzytelniania usługi AAD.

Całkowita alokacja magazynu i liczba baz danych we wszystkich wystąpieniach muszą być mniejsze lub równe limitom udostępnionym przez pule wystąpień.

- Pule wystąpień obsługują 8, 16, 24, 32, 40, 64 i 80 rdzeni wirtualnych.
- Wystąpienia zarządzane wewnątrz pul obsługują 2, 4, 8, 16, 24, 32, 40, 64 i 80 rdzeni wirtualnych.
- Wystąpienia zarządzane wewnątrz pul obsługują rozmiary magazynu z zakresu od 32 GB do 8 TB, z wyjątkiem:
  - 2 rdzeń wirtualny wystąpienia obsługują rozmiary z zakresu od 32 GB do 640 GB,
  - 4 rdzeń wirtualny wystąpienia obsługują rozmiary z zakresu od 32 GB do 2 TB.
- Wystąpienia zarządzane wewnątrz pul mają limit do 100 baz danych użytkownika na wystąpienie, z wyjątkiem 2 wystąpień rdzeń wirtualny, które obsługują do 50 baz danych użytkownika na wystąpienie.

[Właściwość warstwy usług](resource-limits.md#service-tier-characteristics) jest skojarzona z zasobem puli wystąpień, więc wszystkie wystąpienia w puli muszą być tą samą warstwą usług, co warstwa usługi puli. W tej chwili dostępna jest tylko Ogólnego przeznaczenia warstwa usługi (Zobacz w poniższej sekcji dotyczącej ograniczeń w bieżącej wersji zapoznawczej).

### <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Publiczna wersja zapoznawcza ma następujące ograniczenia:

- Obecnie dostępna jest tylko Ogólnego przeznaczenia warstwa usługi.
- Pule wystąpień nie mogą być skalowane w ramach publicznej wersji zapoznawczej, dlatego należy zachować ostrożność planowania pojemności przed wdrożeniem.
- Azure Portal obsługa tworzenia i konfigurowania puli wystąpień nie jest jeszcze dostępna. Wszystkie operacje na pulach wystąpień są obsługiwane tylko za poorednictwem programu PowerShell. Początkowe wdrożenie wystąpienia w wstępnie utworzonej puli jest również obsługiwane tylko za pomocą programu PowerShell. Po wdrożeniu w puli wystąpienia zarządzane można aktualizować przy użyciu Azure Portal.
- Wystąpienia zarządzane utworzone poza pulą nie mogą zostać przeniesione do istniejącej puli, a wystąpienia utworzone w puli nie mogą być przenoszone poza wystąpieniem pojedynczego wystąpienia lub do innej puli.
- Cennik wystąpienia [rezerwowanej pojemności](../database/reserved-capacity-overview.md) nie jest dostępny.

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Wystąpienia zarządzane utworzone w pulach obsługują te same [poziomy zgodności i funkcje, które są obsługiwane w jednym zarządzanym wystąpieniu](sql-managed-instance-paas-overview.md#sql-features-supported).

Każde wystąpienie zarządzane wdrożone w puli ma oddzielne wystąpienie programu SQL Agent.

Opcjonalne funkcje lub funkcje, które wymagają wybrania określonych wartości (takich jak sortowanie na poziomie wystąpienia, strefa czasowa, publiczny punkt końcowy dla ruchu danych, grupy trybu failover), są konfigurowane na poziomie wystąpienia i mogą być różne dla każdego wystąpienia w puli.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Chociaż wystąpienia zarządzane w ramach pul mają dedykowany rdzeń wirtualny i pamięć RAM, współużytkują dysk lokalny (na potrzeby użycia bazy danych tempdb) i zasoby sieciowe. Prawdopodobnie nie jest to możliwe, ale można napotkać efekt *sąsiedniego szumu* , jeśli wiele wystąpień w puli ma duże zużycie zasobów w tym samym czasie. W przypadku zaobserwowania tego zachowania należy rozważyć wdrożenie tych wystąpień do większej puli lub jako pojedyncze wystąpienia.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Ponieważ wystąpienia wdrożone w puli współużytkują tę samą maszynę wirtualną, warto rozważyć wyłączenie funkcji, które wprowadzają wyższe zagrożenia bezpieczeństwa lub w celu uzyskania prawidłowej kontroli uprawnień dostępu do tych funkcji. Na przykład integracja środowiska CLR, natywne wykonywanie kopii zapasowych i przywracanie, poczta e-mail bazy danych itp.

## <a name="instance-pool-support-requests"></a>Żądania obsługi puli wystąpień

Utwórz żądania obsługi dla pul wystąpień i zarządzaj nimi w [Azure Portal](https://portal.azure.com).

Jeśli występują problemy związane z wdrożeniem puli wystąpień (Tworzenie lub usuwanie), upewnij się, że w polu **podtyp problemu** określono **Pule wystąpień** .

![Żądania obsługi pul wystąpień](./media/instance-pools-overview/support-request.png)

Jeśli występują problemy związane z pojedynczym zarządzanym wystąpieniem lub bazą danych w ramach puli, należy utworzyć zwykły bilet pomocy technicznej dla wystąpienia zarządzanego Azure SQL.

Aby utworzyć większe wdrożenia wystąpienia zarządzanego SQL (z pulami wystąpień lub bez nich), może być konieczne uzyskanie większego limitu przydziału regionalnego. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](../database/quota-increase-request.md). Logika wdrażania dla pul wystąpień porównuje całkowite użycie rdzeń wirtualny *na poziomie puli* z limitem przydziału, aby określić, czy można tworzyć nowe zasoby bez zwiększania limitu przydziału.

## <a name="instance-pool-billing"></a>Rozliczenia puli wystąpień

Pule wystąpień umożliwiają niezależne skalowanie zasobów obliczeniowych i magazynu. Klienci są obciążani opłatami za obliczenia skojarzone z zasobem puli mierzonym w rdzeni wirtualnych i magazynem skojarzonym z każdym wystąpieniem mierzonym w gigabajtach (pierwszy 32 GB jest bezpłatny dla każdego wystąpienia).

Cena rdzeń wirtualny puli jest naliczana niezależnie od liczby wystąpień wdrożonych w tej puli.

W przypadku cen obliczeniowych (mierzonych w rdzeni wirtualnych) dostępne są dwie opcje cenowe:

  1. *Uwzględniona licencja*: cena SQL Server licencji jest uwzględniona. Dotyczy to klientów, którzy nie zdecydują się na stosowanie istniejących licencji SQL Server z programem Software Assurance.
  2. *Korzyść użycia hybrydowego platformy Azure*: obniżona cena obejmująca Korzyść użycia hybrydowego platformy Azure do SQL Server. Klienci mogą zrezygnować z tej ceny przy użyciu istniejących licencji SQL Server z programem Software Assurance. Aby uzyskać uprawnienia i inne szczegóły, zobacz [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Dla poszczególnych wystąpień w puli nie można ustawić różnych opcji cenowych. Wszystkie wystąpienia w puli nadrzędnej muszą mieć licencję uwzględnioną w cenie lub Korzyść użycia hybrydowego platformy Azure cenę. Model licencji dla puli można zmienić po utworzeniu puli.

> [!IMPORTANT]
> Jeśli określisz model licencji dla wystąpienia, które jest inne niż w puli, zostanie użyta cena puli, a wartość poziomu wystąpienia jest ignorowana.

Jeśli tworzysz pule wystąpień w ramach [subskrypcji kwalifikujących się do skorzystania z zalet tworzenia i testowania](https://azure.microsoft.com/pricing/dev-test/), będziesz automatycznie otrzymywać obniżone stawki o wartości do 55% w wystąpieniu zarządzanym usługi Azure SQL.

Aby uzyskać szczegółowe informacje o cenach puli wystąpień, zapoznaj się z sekcją *Pule wystąpień* na [stronie cennika wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z pulami wystąpień, zobacz [Przewodnik dotyczący pul wystąpień zarządzanych przez program SQL](instance-pools-configure.md).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [Common Features usługi Azure SQL](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przez usługę SQL z wbudowaną analizą rozwiązywania problemów, zobacz temat [monitorowanie wystąpienia zarządzanego Azure SQL przy użyciu Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
