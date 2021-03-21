---
title: Co to jest usługa Azure SQL?
description: 'Dowiedz się więcej na temat różnych opcji w ramach rodziny usługi Azure SQL Services: Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i SQL Server na maszynie wirtualnej platformy Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server Cloud, SQL Server w chmurze, PaaS Database, Cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: ea097119111d5dbd5eba3c11aba549d201186e3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592187"
---
# <a name="what-is-azure-sql"></a>Co to jest usługa Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Usługa Azure SQL to rodzina zarządzanych, bezpiecznych i inteligentnych produktów, które korzystają z aparatu bazy danych SQL Server w chmurze platformy Azure.

- **Azure SQL Database**: obsługa nowoczesnych aplikacji w chmurze w inteligentnej, zarządzanej bazie danych, która obejmuje obliczenia bezserwerowe. 
- **Wystąpienie zarządzane Azure SQL**: modernizowanie istniejących aplikacji SQL Server na dużą skalę dzięki inteligentnemu w pełni zarządzanemu wystąpieniu jako usługi z niemal 100% zgodnością funkcji z aparatem bazy danych SQL Server. Najlepsze dla większości migracji do chmury.
- **SQL Server na maszynach wirtualnych platformy Azure**: podnieś i Przenieś swoje obciążenia SQL Server z łatwością i Zachowaj 100% SQL Server zgodność i dostęp na poziomie systemu operacyjnego. 
 
Usługa Azure SQL jest oparta na znanym aparacie SQL Server, dzięki czemu można z łatwością migrować aplikacje i nadal korzystać z narzędzi, języków i zasobów, z którymi znasz. Twoje umiejętności i możliwości przesyłania danych do chmury, dzięki czemu masz jeszcze więcej możliwości. 

Dowiedz się, w jaki sposób każdy produkt mieści się w usłudze Azure SQL Data Platform firmy Microsoft w celu dopasowania do odpowiedniej opcji wymagań firmy. Bez względu na to, czy priorytetem jest oszczędność kosztów, czy minimalne administrowanie, ten artykuł może pomóc w ustaleniu, które podejście spełnia wymagania biznesowe.

Jeśli dopiero zaczynasz skorzystać z usługi Azure SQL, zapoznaj się z informacjami o tym, *co to jest usługa Azure SQL* Video, z naszej szczegółowej [serii wideo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Jak możemy ulepszyć usługę Azure SQL? [Wypełnij ankietę](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Omówienie

W dzisiejszym świecie opartym na danych, dzięki czemu Transformacja cyfrowa jest coraz częściej zależała od możliwości zarządzania ogromnymi ilościami danych i korzystania z jej potencjału. Jednak dzisiejsze dane Estates są coraz bardziej skomplikowane, z hostowanymi lokalnie danymi, w chmurze lub na granicy sieci. Deweloperzy tworzący aplikacje inteligentne i immersyjny mogą znaleźć ograniczenia, które mogą ostatecznie wpłynąć na ich środowisko pracy. Ograniczenia wynikające z niezgodnych platform, niewystarczających zabezpieczeń danych, niewystarczającej ilości zasobów i barier wydajnościowych zwiększają złożoność, która może wstrzymywać modernizację i opracowywanie aplikacji. 

Podczas każdej dyskusji dotyczącej baz danych Azure w zestawieniu z lokalnymi bazami danych programu SQL Server należy przede wszystkim pamiętać o tym, że można używać ich wszystkich. Platforma danych firmy Microsoft korzysta z technologii SQL Serverej i udostępnia ją na fizycznych maszynach lokalnych, środowiskach chmur prywatnych, środowiskach chmur prywatnych obsługiwanych przez inne firmy i w chmurze publicznej. 


### <a name="fully-managed-and-always-up-to-date"></a>W pełni zarządzane i zawsze aktualne 

Poświęcaj więcej czasu na innowacje i mniej czasu na stosowanie poprawek, aktualizowanie i wykonywanie kopii zapasowych baz danych. Platforma Azure to jedyna chmura z Evergreen SQL, która automatycznie stosuje najnowsze aktualizacje i poprawki, dzięki czemu bazy danych są zawsze aktualne — eliminuje to kłopoty z pomocą techniczną. Nawet złożone zadania, takie jak dostrajanie wydajności, wysoka dostępność, odzyskiwanie po awarii i kopie zapasowe, są zautomatyzowane, dzięki czemu możesz skupić się na aplikacjach.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Ochrona danych za pomocą wbudowanych funkcji inteligentnych zabezpieczeń 

Platforma Azure stale monitoruje dane pod kątem zagrożeń. Za pomocą usługi Azure SQL można:

- Koryguj potencjalne zagrożenia w czasie rzeczywistym za pomocą inteligentnych alertów dotyczących [zaawansowanego wykrywania zagrożeń](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) i oceny luk w zabezpieczeniach. 
- Uzyskaj wiodącą w branży ochronę wielowarstwową za pomocą [wbudowanych mechanizmów kontroli zabezpieczeń](https://azure.microsoft.com/overview/security/) , takich jak T-SQL, uwierzytelnianie, Sieć i zarządzanie kluczami. 
- Skorzystaj z najbardziej kompleksowego poziomu [zgodności](https://azure.microsoft.com/overview/trusted-cloud/compliance/) dla dowolnej usługi bazy danych w chmurze. 


### <a name="business-motivations"></a>Motywacje biznesowe

Istnieje kilka czynników, które mogą wpływać na decyzję o wyborze między różnymi ofertami dotyczącymi danych:

- [Koszt](#cost): opcja PaaS i IaaS obejmuje podstawową cenę obejmującą podstawową infrastrukturę i Licencjonowanie. Jednak w przypadku opcji IaaS należy zainwestować dodatkowy czas i zasoby w celu zarządzania bazą danych, a w programie PaaS te funkcje administracyjne zawarte w cenie. Usługa IaaS umożliwia zamykanie zasobów, gdy nie są używane w celu obniżenia kosztów, podczas gdy PaaS jest zawsze uruchomiona, chyba że zostaną usunięte i ponownie utworzone zasoby, gdy są potrzebne.
- [Administracja](#administration): opcje PaaS zmniejszają ilość czasu potrzebną do zainwestowania w celu administrowania bazą danych. Jednak ogranicza także zakres niestandardowych zadań administracyjnych i skryptów, które można wykonać lub uruchomić. Na przykład środowisko CLR nie jest obsługiwane w przypadku SQL Database, ale jest obsługiwane dla wystąpienia wystąpienia zarządzanego SQL. Ponadto żadne opcje wdrażania w programie PaaS nie obsługują używania flag śledzenia.
- [Umowa dotycząca poziomu usług](#service-level-agreement-sla): zarówno IaaS, jak i PaaS zapewniają wysoką, branżową umowę SLA. Opcja PaaS gwarantuje dostępność na 99,99% umowy SLA, podczas gdy IaaS gwarantuje gwarancję zgodności z 99,95% dla infrastruktury, co oznacza, że konieczne jest zaimplementowanie dodatkowych mechanizmów w celu zapewnienia dostępności baz danych. Możesz uzyskać dostęp do 99,99% umowy SLA, tworząc dodatkową maszynę wirtualną SQL i implementując rozwiązanie o wysokiej dostępności w usłudze SQL Server. 
- [Czas przejścia na platformę Azure](#market): SQL Server na maszynie wirtualnej platformy Azure to dokładne dopasowanie środowiska, dlatego migracja z lokalizacji lokalnej do maszyny wirtualnej platformy Azure nie jest inna niż przenoszenie baz danych z jednego serwera lokalnego do drugiego. Wystąpienie zarządzane SQL umożliwia również łatwą migrację; mogą jednak wystąpić pewne zmiany, które należy zastosować przed rozpoczęciem migracji. 


## <a name="service-comparison"></a>Porównanie usług

   ![Opcje SQL Server w chmurze: SQL Server na IaaS lub SaaS SQL Database w chmurze.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak widać na diagramie, każda oferta usługi może być scharakteryzowana według poziomu administrowania przez infrastrukturę i stopnia wydajności.

Na platformie Azure możesz mieć SQL Server obciążenia działające jako usługa hostowana ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) lub infrastruktura hostowana ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). W ramach programu PaaS masz wiele opcji produktu i warstw usług w poszczególnych opcjach. Kluczowym pytaniem, które należy zadać w przypadku podejmowania decyzji o wyborze między PaaS lub IaaS, jest zarządzanie bazą danych, stosowanie poprawek i wykonywanie kopii zapasowych. Czy chcesz delegować te operacje na platformę Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) to relacyjna baza danych jako usługa (DBaaS) hostowana na platformie Azure, która znajduje się w kategorii branżowej *platformy jako usługi (PaaS)*. 
- Najlepsze dla nowoczesnych aplikacji w chmurze, które chcą korzystać z najnowszych stabilnych funkcji SQL Server oraz ograniczeń czasowych w programowaniu i marketingu. 
- W pełni zarządzany aparat bazy danych SQL Server w oparciu o najnowszą stabilną wersję Enterprise SQL Server. W SQL Database dostępne są dwie opcje wdrażania oparte na standardowym sprzęcie i oprogramowaniu, które są własnością, hostowane i utrzymywane przez firmę Microsoft. 

Za pomocą SQL Server można używać wbudowanych funkcji i funkcji, które wymagają obszernej konfiguracji (lokalnie lub na maszynie wirtualnej platformy Azure). Płatność za korzystanie z usługi SQL Database odbywa się zgodnie z rzeczywistym użyciem, a opcje pozwalają na skalowanie w górę lub w poziomie, dzięki czemu klienci mają do dyspozycji większe możliwości bez najmniejszych zakłóceń. SQL Database zawiera dodatkowe funkcje, które nie są dostępne w SQL Server, takie jak wbudowana wysoka dostępność, analiza i zarządzanie.


Azure SQL Database oferuje następujące opcje wdrażania:
  - Jako [*pojedyncza baza danych*](database/single-database-overview.md) z własnym zestawem zasobów zarządzanych za pośrednictwem [serwera logicznego SQL](database/logical-servers.md). Pojedyncza baza danych jest podobna do [zawartej bazy danych](/sql/relational-databases/databases/contained-databases) w SQL Server. Ta opcja jest zoptymalizowana pod kątem nowoczesnego opracowywania nowych aplikacji w chmurze. Dostępne są opcje [skalowania](database/service-tier-hyperscale.md) i [bezserwerowe](database/serverless-tier-overview.md) .
  - [*Pula elastyczna*](database/elastic-pool-overview.md), która jest kolekcją baz danych ze współdzielonym zestawem zasobów zarządzanych za pośrednictwem [serwera logicznego SQL](database/logical-servers.md). Pojedyncze bazy danych można przenieść do i z puli elastycznej. Ta opcja jest zoptymalizowana pod kątem nowoczesnego opracowywania nowych aplikacji w chmurze przy użyciu wielodostępnego wzorca aplikacji SaaS. Pule elastyczne zapewniają ekonomiczne rozwiązanie do zarządzania wydajnością wielu baz danych, które mają wzorce użycia zmiennych.

### <a name="azure-sql-managed-instance"></a>Wystąpienie zarządzane Azure SQL

[Wystąpienie zarządzane Azure SQL](managed-instance/sql-managed-instance-paas-overview.md) znajduje się w kategorii branżowej *platformy jako usługi (PaaS)* i jest najlepsze w przypadku większości migracji do chmury. Wystąpienie zarządzane SQL to zbiór baz danych systemu i użytkowników z współdzielonym zestawem zasobów, które są gotowe do przydzielenia i przesunięcia.  
- Najlepsze dla nowych aplikacji lub istniejących aplikacji lokalnych, które chcą korzystać z najnowszych stabilnych funkcji SQL Server i które są migrowane do chmury przy minimalnych zmianach. Wystąpienie wystąpienia zarządzanego SQL jest podobne do wystąpienia [aparatu bazy danych Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview) oferują udostępnione zasoby dla baz danych i dodatkowych funkcji o zakresie wystąpienia. 
- Wystąpienie zarządzane SQL obsługuje migrację bazy danych z lokalizacji lokalnej z minimalnym brakiem zmian w bazie danych. Ta opcja zapewnia wszystkie zalety PaaS Azure SQL Database ale dodaje funkcje, które były wcześniej dostępne tylko w SQL Server maszynach wirtualnych. Obejmuje to natywną sieć wirtualną i niemal 100% zgodności z lokalnymi SQL Server. Wystąpienia wystąpienia zarządzanego SQL zapewniają pełny dostęp SQL Server i zgodność funkcji migracji serwerów SQL na platformę Azure.

### <a name="sql-server-on-azure-vm"></a>Program SQL Server na maszynie wirtualnej platformy Azure

[SQL Server na maszynie wirtualnej platformy Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) znajduje się w kategorii branżowej *infrastruktura jako usługa (IaaS)* i umożliwia uruchamianie SQL Server wewnątrz w pełni zarządzanej maszyny wirtualnej na platformie Azure. 
- Najlepsze dla migracji i aplikacji wymagających dostępu na poziomie systemu operacyjnego. Maszyny wirtualne SQL na platformie Azure są gotowe do użycia w przypadku istniejących aplikacji, które wymagają szybkiej migracji do chmury z minimalnymi zmianami lub bez zmian. Usługa SQL Virtual Machines oferuje pełną kontrolę administracyjną nad wystąpieniem SQL Server i bazowego systemu operacyjnego na potrzeby migracji na platformę Azure. 
- Scenariusze szybkiego tworzenia i testowania, gdy nie chcesz kupować lokalnego nieprodukcyjnego sprzętu dla programu SQL Server. Maszyny wirtualne SQL działają również na znormalizowanym sprzęcie, który jest własnością, hostowana i obsługiwana przez firmę Microsoft. W przypadku korzystania z maszyn wirtualnych SQL możesz wybrać opcję płatność zgodnie z rzeczywistym użyciem, aby uzyskać licencję SQL Server już dołączoną do SQL Server obrazu lub łatwo korzystać z istniejącej licencji. Możesz również zatrzymać lub wznowić maszynę wirtualną zgodnie z wymaganiami. 
- SQL Server zainstalowane i hostowane w chmurze są uruchamiane na maszynach wirtualnych z systemem Windows Server lub Linux działających na platformie Azure, znanych również jako infrastruktura jako usługa (IaaS). Usługi SQL Virtual Machines są dobrym rozwiązaniem w przypadku migrowania lokalnych baz danych SQL Server i aplikacji bez żadnej zmiany w bazie danych. Wszystkie najnowsze wersje SQL Server są dostępne do zainstalowania na maszynie wirtualnej IaaS. 

    Najbardziej znaczącą różnicą między SQL Database i wystąpieniem zarządzanym SQL jest to, że SQL Server na platformie Azure Virtual Machines zapewnia pełną kontrolę nad aparatem bazy danych. Możesz wybrać, kiedy należy rozpocząć konserwację/stosowanie poprawek, zmienić model odzyskiwania na prosty lub zbiorczo-zarejestrowany, wstrzymać lub uruchomić usługę w razie potrzeby i można w pełni dostosować aparat bazy danych SQL Server. Dodatkowa kontrola obejmuje dodanie odpowiedzialności za zarządzanie maszyną wirtualną.
- Optymalizacja pod kątem migrowania istniejących aplikacji na platformę Azure lub rozszerzania istniejących aplikacji lokalnych do chmury w ramach wdrożeń hybrydowych. Ponadto przy użyciu programu SQL Server na maszynie wirtualnej można tworzyć i testować tradycyjne aplikacje programu SQL Server. W przypadku maszyn wirtualnych SQL masz pełne prawa administracyjne w ramach dedykowanego wystąpienia SQL Server i maszyny wirtualnej opartej na chmurze. To doskonałe rozwiązanie, gdy firma ma już dostępne zasoby informatyczne do obsługi maszyn wirtualnych. Te funkcje umożliwiają stworzenie doskonale dopasowanego systemu, który spełni określone wymagania aplikacji związane z wydajnością i dostępnością.

W poniższej tabeli wymieniono dodatkowe różnice, ale *zarówno SQL Database, jak i wystąpienie zarządzane SQL są zoptymalizowane pod kątem obniżenia ogólnych kosztów zarządzania do minimum dla aprowizacji i zarządzania wieloma bazami danych.* Bieżące koszty administracyjne są skracane, ponieważ nie trzeba zarządzać wszystkimi maszynami wirtualnymi, systemem operacyjnym ani oprogramowaniem bazy danych. Nie wymaga zarządzania uaktualnieniami, wysoką dostępnością ani [kopiami zapasowymi](database/automated-backups-overview.md). 

Ogólnie rzecz biorąc, SQL Database i wystąpienie zarządzane SQL mogą znacząco zwiększyć liczbę baz danych zarządzanych przez pojedynczy zasób IT lub deweloperski. [Pule elastyczne](database/elastic-pool-overview.md) obsługują również architekturę aplikacji z wieloma dzierżawcami z funkcjami obejmującymi izolację dzierżawy i możliwość skalowania w celu obniżenia kosztów dzięki udostępnieniu zasobów między bazami danych. [Wystąpienie zarządzane SQL](managed-instance/sql-managed-instance-paas-overview.md) zapewnia obsługę funkcji z zakresem wystąpienia, co umożliwia łatwą migrację istniejących aplikacji, a także udostępnianie zasobów między bazami danych.

### <a name="comparison-table"></a>Tabela porównawcza

| Azure SQL Database | Wystąpienie zarządzane Azure SQL | Program SQL Server na maszynie wirtualnej platformy Azure |
| :--- | :--- | :--- |
|Obsługa większości lokalnych możliwości na poziomie bazy danych. Dostępne są najczęściej używane funkcje SQL Server.<br/>dostępność na 99,995%.<br/>Wbudowane kopie zapasowe, poprawki, odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Możliwość przypisywania niezbędnych zasobów (procesor CPU/magazyn) do poszczególnych baz danych.<br/>Wbudowana Zaawansowana analiza i zabezpieczenia.<br/>Online zmiana zasobów (procesor CPU/magazyn).| Obsługuje niemal wszystkie funkcje na poziomie wystąpienia lokalnego i na poziomie bazy danych. Wysoka zgodność z SQL Server.<br/>dostępność na 99,99%.<br/>Wbudowane kopie zapasowe, poprawki, odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Łatwa migracja z SQL Server.<br/>Prywatny adres IP w usłudze Azure Virtual Network.<br/>Wbudowana Zaawansowana analiza i zabezpieczenia.<br/>Online zmiana zasobów (procesor CPU/magazyn).| Masz pełną kontrolę nad aparatem SQL Server. Obsługuje wszystkie możliwości lokalne.<br/>Do 99,99% czasu dostępności.<br/>Pełna zgodność ze zgodną wersją SQL Server lokalnego.<br/>Stała, dobrze znana wersja aparatu bazy danych.<br/>Łatwa migracja z SQL Server.<br/>Prywatny adres IP w usłudze Azure Virtual Network.<br/>Można wdrożyć aplikacje lub usługi na hoście, na którym znajduje się SQL Server.|
|Migracja z SQL Server może być trudne.<br/>Niektóre funkcje SQL Server są niedostępne.<br/>Bez gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczyste).<br/>Zgodność z wersją SQL Server można uzyskać tylko przy użyciu poziomów zgodności bazy danych.<br/>Obsługa prywatnego adresu IP za pomocą [prywatnego linku platformy Azure](database/private-endpoint-overview.md).|Nadal istnieje pewna minimalna liczba funkcji SQL Server, które nie są dostępne.<br/>Bez gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczyste).<br/>Zgodność z wersją SQL Server można uzyskać tylko przy użyciu poziomów zgodności bazy danych.|Musisz zarządzać kopiami zapasowymi i poprawkami.<br>Musisz zaimplementować własne rozwiązanie High-Availability.<br/>Podczas zmiany zasobów (procesor CPU/magazyn) występuje przestój|
| Bazy danych o pojemności do 100 TB. | Do 8 TB. | SQL Server wystąpienia o pojemności do 256 TB magazynu. Wystąpienie może obsługiwać dowolną liczbę baz danych zależnie od potrzeb. |
| Aplikacja lokalna może uzyskać dostęp do danych w Azure SQL Database. | [Implementacja natywnej sieci wirtualnej](managed-instance/vnet-existing-add-subnet.md) i łączność ze środowiskiem lokalnym za pomocą usługi Azure Express Route lub VPN Gateway. | W przypadku maszyn wirtualnych SQL można korzystać z aplikacji, które działają częściowo w chmurze i częściowo lokalnie. Można na przykład rozszerzyć sieć lokalną i domenę usługi Active Directory do chmury za pośrednictwem usługi [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Aby uzyskać więcej informacji na temat rozwiązań w chmurze hybrydowej, zobacz [rozszerzanie lokalnych rozwiązań danych do chmury](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Koszt

Niezależnie od tego, czy jesteś pewien, że jest to strapped dla środków pieniężnych, czy zespół w firmie, który działa pod kątem ścisłych ograniczeń budżetowych, ograniczone finansowanie jest często podstawowym sterownikiem podczas decydowania o sposobie hostowania baz danych. W tej sekcji omówiono podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure skojarzone z rodziną usług Azure SQL.  Poznasz również sposób obliczania całkowitego kosztu aplikacji.

### <a name="billing-and-licensing-basics"></a>Podstawowe informacje dotyczące rozliczeń i licencjonowania

Obecnie zarówno **SQL Database** , jak i **wystąpienie zarządzane SQL** są sprzedawane jako usługa i są dostępne z kilkoma opcjami i w kilku warstwach usług z różnymi cenami dla zasobów, które są rozliczane godzinowo według ustalonej stawki na podstawie wybranej warstwy usług i rozmiaru. Aby uzyskać najnowsze informacje o bieżących obsługiwanych warstwach usług, rozmiarach obliczeniowych i kwotach magazynu, zobacz [model zakupu oparty na](database/service-tiers-dtu.md) jednostkach DTU dla [modelu zakupu SQL Database i oparty na rdzeń wirtualny w przypadku wystąpienia zarządzanego SQL Database i SQL](database/service-tiers-vcore.md).

- Za pomocą SQL Database można wybrać warstwę usług, która odpowiada potrzebom z szerokiego zakresu cen, rozpoczynając od 5 USD/miesiąc dla warstwy Podstawowa i można utworzyć [Pule elastyczne](database/elastic-pool-overview.md) umożliwiające udostępnianie zasobów między bazami danych w celu ograniczenia kosztów i przekroczenia użycia.
- W przypadku wystąpienia zarządzanego SQL możesz również wprowadzić własną licencję. Aby uzyskać więcej informacji na temat przygotowywania licencji, zobacz [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/) lub Użyj [kalkulatora korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) , aby dowiedzieć się, jak **zaoszczędzić do 40%**.

Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/). Możesz dynamicznie dostosować warstwy usług i rozmiary obliczeń, aby odpowiadały potrzebom różnych przepływności aplikacji.

W przypadku **SQL Database** i **wystąpienia zarządzanego SQL** oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez platformę Azure, co zmniejsza koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](database/automated-backups-overview.md) pozwala osiągnąć znaczne oszczędności, zwłaszcza w przypadku dużej liczby baz danych.

Korzystając z **języka SQL na maszynach wirtualnych platformy Azure**, możesz użyć dowolnych z SQL Server obrazów dostarczonych przez platformę (w tym licencji) lub skorzystać z licencji SQL Server. Dostępne są wszystkie obsługiwane wersje SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) i wersje (Developer, Express, Web, standard, Enterprise). Ponadto są dostępne wersje bring-your-own-license (BYOL) obrazów. W przypadku użycia obrazów dostarczanych przez platformę Azure koszty operacyjne zależą od rozmiaru maszyny wirtualnej, a także od wybranej wersji programu SQL Server. Niezależnie od rozmiaru maszyny wirtualnej lub wersji SQL Server, użytkownik uiszcza koszt licencjonowania za minutę SQL Server i serwera z systemem Windows lub Linux wraz z kosztem usługi Azure Storage dla dysków maszyny wirtualnej. Opcja rozliczania co minutę pozwala używać programu SQL Server tak długo, jak jest to konieczne bez wykupywania dodatkowych licencji programu SQL Server. Jeśli wprowadzisz własną licencję SQL Server na platformę Azure, opłaty są naliczone wyłącznie za koszty związane z serwerem i magazynem. Więcej informacji na temat przenoszenia własnej licencji można znaleźć w temacie [Przenośność licencji za pośrednictwem programu Software Assurance w systemie Azure](https://azure.microsoft.com/pricing/license-mobility/). Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Obliczanie całkowitego kosztu aplikacji

Po rozpoczęciu korzystania z platformy w chmurze koszt korzystania z aplikacji obejmuje koszt nowych i bieżących kosztów administracyjnych oraz koszty usług w chmurze publicznej.

Więcej informacji na temat cen zawierają następujące zasoby:

- [SQL Database & cennika wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Cennik usługi Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/) Machines dla [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) i dla [systemu Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administracja

Dla wielu firm decyzja o przeniesieniu usług do usługi w chmurze wiąże się zarówno z ograniczeniem złożoności w zakresie administracji, jak i obniżeniem kosztów. Dzięki usługom IaaS i PaaS platforma Azure zarządza podstawową infrastrukturą i automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i w przypadku awarii serwera w centrum danych.

- Za pomocą **SQL Database** i **wystąpienia zarządzanego SQL** można nadal zarządzać bazą danych, ale nie musisz już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia. Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji i administrowania.
- Dzięki **programowi SQL na maszynie wirtualnej platformy Azure** masz pełną kontrolę nad konfiguracją systemu operacyjnego i wystąpienia SQL Server. W przypadku maszyny wirtualnej można określić, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych oraz kiedy zainstalować dodatkowe oprogramowanie, np. program antywirusowy. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Platforma Azure umożliwia zmianę rozmiaru maszyny wirtualnej zgodnie z zapotrzebowaniem. Więcej informacji można znaleźć w temacie [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/sizes.md) (Rozmiary maszyn wirtualnych i usług w chmurze na platformie Azure).

## <a name="service-level-agreement-sla"></a>Umowa dotycząca poziomu usług (SLA)

W przypadku wielu działów IT spełniających warunki umowy dotyczącej poziomu usług (SLA) to najwyższy priorytet. W tej sekcji opisano, jakie warunki umowy SLA stosuje się do poszczególnych opcji obsługi bazy danych.

W przypadku **Azure SQL Database** i **wystąpienia zarządzanego usługi Azure SQL** firma Microsoft oferuje umowę SLA dostępności 99,99%. Najnowsze informacje znajdują się w sekcji [Umowa dotycząca poziomu usług](https://azure.microsoft.com/support/legal/sla/sql-database/).

W przypadku **bazy danych SQL na maszynie wirtualnej platformy Azure** firma Microsoft oferuje umowę SLA dostępności 99,95%, która obejmuje tylko maszynę wirtualną. Umowa SLA nie obejmuje procesów (np. programu SQL Server) uruchomionych na maszynie wirtualnej i wymaga obsługi przynajmniej dwóch wystąpień maszyny wirtualnej w zbiorze dostępności. Najnowsze informacje znajdują się w artykule [Maszyny wirtualne — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Aby zapewnić wysoką dostępność bazy danych (HA) na maszynach wirtualnych, należy skonfigurować jedną z obsługiwanych opcji wysokiej dostępności w SQL Server, na przykład [zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Użycie obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia osiągnięcie dostępności bazy danych na poziomie > 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Czas przejścia na platformę Azure

**Azure SQL Database** to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas na rynek dla nowych rozwiązań jest krytyczny. Dzięki funkcjonalności przypominającej model DBA jest doskonała dla architektów i deweloperów chmury, ponieważ zmniejsza potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

**Wystąpienie zarządzane usługi Azure SQL** znacznie upraszcza migrację istniejących aplikacji na platformę Azure, umożliwiając szybkie przenoszenie aplikacji bazy danych na rynek na platformie Azure.

**SQL na maszynie wirtualnej platformy Azure** jest idealnym rozwiązaniem, jeśli istniejące lub nowe aplikacje wymagają dużych baz danych lub dostępu do wszystkich funkcji w SQL Server lub Windows/Linux, i chcesz uniknąć czasu i kosztów nabycia nowego sprzętu lokalnego. Jest to również dobre dopasowanie, gdy chcesz migrować istniejące lokalne aplikacje i bazy danych na platformę Azure tak, jak w przypadku, gdy SQL Database lub wystąpienie zarządzane SQL nie jest dobrym dopasowaniem. Ponieważ nie musisz zmieniać warstw prezentacji, aplikacji i danych, możesz zaoszczędzić czas i budżet na potrzeby ponownej architektury istniejącego rozwiązania. Zamiast tego możesz skoncentrować się na migracji wszystkich rozwiązań do platformy Azure i przeprowadzeniu optymalizacji wydajności, których może wymagać platforma Azure. Więcej informacje zawiera artykuł [Performance Best Practices for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md) (Najlepsze praktyki dotyczące wydajności dla programu SQL Server w usłudze Azure Virtual Machines).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą SQL Database, zobacz [Your first Azure SQL Database](database/single-database-create-quickstart.md) (Twoja pierwsza baza danych Azure SQL Database).
- Aby rozpocząć pracę z wystąpieniem zarządzanym SQL, zobacz [pierwsze wystąpienie zarządzane usługi Azure SQL](managed-instance/instance-create-quickstart.md) . 
- Zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Aby rozpocząć pracę z programem SQL Server na maszynach wirtualnych platformy Azure, zobacz temat [Aprowizowanie maszyny wirtualnej programu SQL Server w Portalu Azure](virtual-machines/windows/create-sql-vm-portal.md)
- [Zidentyfikuj odpowiednie SQL Database lub jednostkę SKU wystąpienia zarządzanego przez program SQL dla lokalnej bazy danych](/sql/dma/dma-sku-recommend-sql-db/).