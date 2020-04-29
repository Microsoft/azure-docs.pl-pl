---
title: Pule wystąpień (wersja zapoznawcza)
description: W tym artykule opisano pule wystąpień Azure SQL Database (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 0938fbe94cb0d1e6dae3dcb84950a11f90dd9db8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878157"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Co to są pule wystąpień SQL Database (wersja zapoznawcza)?

Pule wystąpień są nowym zasobem w Azure SQL Database, który zapewnia wygodny i ekonomiczny sposób migrowania mniejszych wystąpień SQL do chmury na dużą skalę.

Pule wystąpień umożliwiają wstępną aprowizację zasobów obliczeniowych zgodnie z całkowitymi wymaganiami migracji. Następnie możesz wdrożyć kilka osobnych wystąpień zarządzanych — do osiągnięcia wstępnie aprowizowanego poziomu obliczeń. Na przykład w przypadku wstępnego udostępnienia usługi 8 rdzeni wirtualnych można wdrożyć dwa 2 rdzeń wirtualny i jedno 4 wystąpienia rdzeń wirtualny, a następnie przeprowadzić migrację baz danych do tych wystąpień. Przed udostępnieniem pul wystąpień mniejsze i mniejsze obciążenia intensywnie korzystające z mocy obliczeniowej będą często musiały zostać skonsolidowane w większym zarządzanym wystąpieniu podczas migracji do chmury. Konieczność migrowania grup baz danych do dużego wystąpienia zwykle wymaga starannego planowania pojemności i zarządzania zasobami, dodatkowych zagadnień związanych z zabezpieczeniami oraz pewnej dodatkowej konsolidacji danych na poziomie wystąpienia.

Ponadto pule wystąpień obsługują natywną integrację sieci wirtualnej, dzięki czemu można wdrożyć wiele pul wystąpień i wiele pojedynczych wystąpień w tej samej podsieci.


## <a name="key-capabilities-of-instance-pools"></a>Kluczowe możliwości pul wystąpień

Pule wystąpień zapewniają następujące korzyści:

1. Możliwość hostowania 2 rdzeń wirtualny wystąpień. *Tylko dla wystąpień w pulach wystąpień. \**
2. Czas wdrożenia przewidywalny i szybki (do 5 minut).
3. Minimalna alokacja adresów IP.

Na poniższym diagramie przedstawiono pulę wystąpień z wieloma wystąpieniami wdrożonymi w ramach podsieci sieci wirtualnej.

![Pula wystąpień z wieloma wystąpieniami](./media/sql-database-instance-pools/instance-pools1.png)

Pule wystąpień umożliwiają wdrożenie wielu wystąpień na tej samej maszynie wirtualnej, w której rozmiar obliczeń maszyny wirtualnej jest oparty na łącznej liczbie rdzeni wirtualnych przydzieloną dla puli. Ta architektura umożliwia *partycjonowanie* maszyny wirtualnej w wielu wystąpieniach, które mogą być dowolnym obsługiwanym rozmiarem, w tym 2 rdzeni wirtualnych (2 rdzeń wirtualny wystąpienia są dostępne tylko dla wystąpień w pulach).

Operacje zarządzania w wystąpieniach w puli są znacznie szybsze po początkowym wdrożeniu puli. Te operacje są szybsze, ponieważ wdrożenie lub rozszerzenie [klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (dedykowany zestaw maszyn wirtualnych) nie jest częścią aprowizacji wystąpienia zarządzanego.

Ponieważ wszystkie wystąpienia w puli współużytkują tę samą maszynę wirtualną, łączna alokacja adresów IP nie zależy od liczby wdrożonych wystąpień, co jest wygodne do wdrożenia w podsieciach z wąskim zakresem adresów IP.

Każda pula ma stały przydział adresów IP zawierający tylko dziewięć adresów IP (nie obejmuje pięciu adresów IP w podsieci, które są zarezerwowane do własnych potrzeb). Aby uzyskać szczegółowe informacje, zobacz [wymagania dotyczące rozmiaru podsieci dla pojedynczych wystąpień](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scenariusze aplikacji dla pul wystąpień

Poniższa lista zawiera główne przypadki użycia, w których należy uwzględnić pule wystąpień:

- Migracja *grupy wystąpień SQL* w tym samym czasie, w których większość jest mniejsza niż rozmiar (na przykład 2 lub 4 rdzeni wirtualnych).
- Scenariusze, w których są ważne *i przewidywalne Tworzenie lub skalowanie wystąpienia* . Na przykład wdrożenie nowej dzierżawy w środowisku aplikacji SaaS z wieloma dzierżawcami, które wymaga możliwości na poziomie wystąpienia.
- Scenariusze, w których obowiązuje *stały koszt* lub *limit wydatków* . Można na przykład uruchomić udostępnione środowiska deweloperskie lub testowe o stałym (lub nierzadko zmienionym rozmiarze), gdzie okresowo Wdrażaj wystąpienia zarządzane.
- Scenariusze, w których *minimalna alokacja adresów IP* w podsieci sieci wirtualnej jest ważna. Wszystkie wystąpienia w puli współużytkują maszynę wirtualną, więc liczba przyznanych adresów IP jest mniejsza niż w przypadku pojedynczych wystąpień.


## <a name="architecture-of-instance-pools"></a>Architektura pul wystąpień

Pule wystąpień mają podobną architekturę do zwykłych wystąpień zarządzanych (*pojedyncze wystąpienia*). Aby obsługiwać [wdrożenia w ramach usługi Azure Virtual Networks (sieci wirtualnych)](../virtual-network/virtual-network-for-azure-services.md) i zapewnić izolację i bezpieczeństwo klientów, pule wystąpień również polegają na [klastrach wirtualnych](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Klastry wirtualne reprezentują dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta.

Główna różnica między dwoma modelami wdrażania polega na tym, że pule wystąpień zezwalają na wiele wdrożeń procesów SQL Server w tym samym węźle maszyny wirtualnej, które są zasobami zarządzanymi przy użyciu [obiektów zadań systemu Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), natomiast pojedyncze wystąpienia są zawsze same w węźle maszyny wirtualnej.

Na poniższym diagramie przedstawiono pulę wystąpień i dwa poszczególne wystąpienia wdrożone w tej samej podsieci i przedstawiono główne szczegóły architektury dla obu modeli wdrażania:

![Pula wystąpień i dwa poszczególne wystąpienia](./media/sql-database-instance-pools/instance-pools2.png)

Każda pula wystąpień tworzy oddzielny klaster wirtualny poniżej. Wystąpienia w puli i pojedyncze wystąpienia wdrożone w tej samej podsieci nie współdzielą zasobów obliczeniowych przyznanych do SQL Server procesów i składników bramy, co zapewnia przewidywalność wydajności.

## <a name="instance-pools-resource-limitations"></a>Ograniczenia zasobów pul wystąpień

Istnieje kilka ograniczeń zasobów dotyczących pul wystąpień i wystąpień wewnątrz pul:

- Pule wystąpień są dostępne tylko na sprzęcie 5 rdzeń.
- Wystąpienia w puli mają dedykowany procesor CPU i pamięć RAM, więc zagregowana liczba rdzeni wirtualnych we wszystkich wystąpieniach musi być mniejsza lub równa liczbie rdzeni wirtualnych przydzielonych do puli.
- Wszystkie [limity poziomu wystąpienia](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) mają zastosowanie do wystąpień utworzonych w ramach puli.
- Oprócz limitów na poziomie wystąpienia istnieją również dwa limity nakładane *na poziomie puli wystąpień*:
  - Łączny rozmiar magazynu na pulę (8 TB).
  - Łączna liczba baz danych na pulę (100).

Całkowita alokacja magazynu i liczba baz danych we wszystkich wystąpieniach muszą być mniejsze lub równe limitom udostępnionym przez pule wystąpień.

- Pule wystąpień obsługują 8, 16, 24, 32, 40, 64 i 80 rdzeni wirtualnych.
- Wystąpienia zarządzane wewnątrz pul obsługują 2, 4, 8, 16, 24, 32, 40, 64 i 80 rdzeni wirtualnych.
- Wystąpienia zarządzane wewnątrz pul obsługują rozmiary magazynu z zakresu od 32 GB do 8 TB, z wyjątkiem:
  - 2 rdzeń wirtualny wystąpienia obsługują rozmiary z zakresu od 32 GB do 640 GB
  - 4 rdzeń wirtualny wystąpienia obsługują rozmiary z zakresu od 32 GB do 2 TB

[Właściwość warstwy usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) jest skojarzona z zasobem puli wystąpień, dlatego wszystkie wystąpienia w puli muszą być takie same jak warstwa usługi puli. W tej chwili dostępna jest tylko Ogólnego przeznaczenia warstwa usługi (Zobacz w poniższej sekcji dotyczącej ograniczeń w bieżącej wersji zapoznawczej).

### <a name="public-preview-limitations"></a>Ograniczenia publicznej wersji zapoznawczej

Publiczna wersja zapoznawcza ma następujące ograniczenia:

- Obecnie dostępna jest tylko Ogólnego przeznaczenia warstwa usługi.
- Pule wystąpień nie mogą być skalowane w publicznej wersji zapoznawczej, dlatego należy zachować ostrożność planowania pojemności przed wdrożeniem.
- Azure Portal obsługa tworzenia i konfigurowania puli wystąpień nie jest jeszcze dostępna. Wszystkie operacje na pulach wystąpień są obsługiwane tylko za poorednictwem programu PowerShell. Początkowe wdrożenie wystąpienia w wstępnie utworzonej puli jest również obsługiwane tylko za pomocą programu PowerShell. Po wdrożeniu w puli wystąpienia zarządzane można aktualizować za pomocą Azure Portal.
- Wystąpienia zarządzane utworzone poza pulą nie mogą zostać przeniesione do istniejącej puli, a wystąpienia utworzone w puli nie mogą być przenoszone poza wystąpieniem pojedynczego wystąpienia lub do innej puli.
- Cennik wystąpienia zarezerwowanego (dołączona licencja lub z Korzyść użycia hybrydowego platformy Azure) jest niedostępny.

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Wystąpienia utworzone w pulach obsługują te same [poziomy zgodności i funkcje obsługiwane w jednym zarządzanym wystąpieniu](sql-database-managed-instance.md#sql-features-supported).

Każde wystąpienie zarządzane wdrożone w puli ma oddzielne wystąpienie programu SQL Agent.

Opcjonalne funkcje lub funkcje, które wymagają wybrania określonych wartości (takich jak sortowanie na poziomie wystąpienia, strefa czasowa, publiczny punkt końcowy dla ruchu danych, grupy trybu failover), są konfigurowane na poziomie wystąpienia i mogą być różne dla każdego wystąpienia w puli.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Chociaż wystąpienia zarządzane w ramach pul mają dedykowany rdzeń wirtualny i pamięć RAM, współużytkują dysk lokalny (na potrzeby użycia bazy danych tempdb) i zasoby sieciowe. Prawdopodobnie nie jest to możliwe, ale można napotkać efekt *sąsiedniego szumu* , jeśli wiele wystąpień w puli ma duże zużycie zasobów w tym samym czasie. W przypadku zaobserwowania tego zachowania należy rozważyć wdrożenie tych wystąpień do większej puli lub jako pojedyncze wystąpienia.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Ponieważ wystąpienia wdrożone w puli współużytkują tę samą maszynę wirtualną, warto rozważyć wyłączenie funkcji, które wprowadzają wyższe zagrożenia bezpieczeństwa lub w celu uzyskania prawidłowej kontroli uprawnień dostępu do tych funkcji. Na przykład integracja środowiska CLR, natywne wykonywanie kopii zapasowych i przywracanie, poczta e-mail bazy danych itp.

## <a name="instance-pool-support-requests"></a>Żądania obsługi puli wystąpień

Utwórz żądania obsługi dla pul wystąpień i zarządzaj nimi w [Azure Portal](https://portal.azure.com).

Jeśli występują problemy związane z wdrożeniem puli wystąpień (Tworzenie lub usuwanie), upewnij się, że w polu **podtyp problemu** określono **Pule wystąpień** .

![żądania obsługi pul wystąpień](./media/sql-database-instance-pools/support-request.png)

Jeśli występują problemy związane z pojedynczymi wystąpieniami lub bazami danych w ramach puli, należy utworzyć zwykły bilet pomocy technicznej dla Azure SQL Database wystąpieniami zarządzanymi.

Aby utworzyć większe wdrożenia wystąpienia zarządzanego (z pulami wystąpień lub bez nich), może być konieczne uzyskanie większego limitu przydziału regionalnego. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](quota-increase-request.md). Należy pamiętać, że jeśli używasz pul wystąpień, logika wdrażania porównuje całkowite użycie rdzeń wirtualny *na poziomie puli* z limitem przydziału, aby określić, czy można tworzyć nowe zasoby bez zwiększania limitu przydziału.

## <a name="instance-pool-billing"></a>Rozliczenia puli wystąpień

Pule wystąpień umożliwiają niezależne skalowanie zasobów obliczeniowych i magazynu. Klienci są obciążani opłatami za obliczenia skojarzone z zasobem puli mierzonym w rdzeni wirtualnych i magazynem skojarzonym z każdym wystąpieniem mierzonym w gigabajtach (pierwszy 32 GB jest bezpłatny dla każdego wystąpienia).

Cena rdzeń wirtualny puli jest naliczana niezależnie od liczby wystąpień wdrożonych w tej puli.

W przypadku cen obliczeniowych (mierzonych w rdzeni wirtualnych) dostępne są dwie opcje cenowe:

  1. *Uwzględniona licencja*: cena licencji SQL jest uwzględniona. Dotyczy to klientów, którzy nie zdecydują się na stosowanie istniejących licencji SQL Server z programem Software Assurance.
  2. *Korzyść użycia hybrydowego platformy Azure*: obniżona cena obejmująca Korzyść użycia hybrydowego platformy Azure do SQL Server. Klienci mogą zrezygnować z tej ceny przy użyciu istniejących licencji SQL Server z programem Software Assurance. Aby uzyskać uprawnienia i inne szczegóły, zobacz [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Dla poszczególnych wystąpień w puli nie można ustawić różnych opcji cenowych. Wszystkie wystąpienia w puli nadrzędnej muszą mieć licencję uwzględnioną w cenie lub Korzyść użycia hybrydowego platformy Azure cenę. Model licencji dla puli można zmienić po utworzeniu puli.

> [!IMPORTANT]
> Jeśli określisz model licencji dla wystąpienia, które jest inne niż w puli, zostanie użyta cena puli, a wartość poziomu wystąpienia jest ignorowana.

Jeśli tworzysz pule wystąpień w ramach [subskrypcji kwalifikujących się do skorzystania z zalet tworzenia i testowania](https://azure.microsoft.com/pricing/dev-test/), będziesz automatycznie otrzymywać obniżone stawki o wartości do 55% w wystąpieniu zarządzanym usługi Azure SQL.

Aby uzyskać szczegółowe informacje o cenach puli wystąpień, zapoznaj się z sekcją *Pule wystąpień* na [stronie cennika wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z pulami wystąpień, zobacz [SQL Database pule wystąpień — Przewodnik](sql-database-instance-pools-how-to.md).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem korzystającym z Azure Database Migration Service (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przy użyciu wbudowanej analizy rozwiązywania problemów, zobacz [monitorowanie Azure SQL Database przy użyciu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [SQL Database cenach wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).
