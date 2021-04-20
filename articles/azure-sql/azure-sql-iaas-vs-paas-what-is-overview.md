---
title: Co to jest usługa Azure SQL?
description: 'Dowiedz się więcej o różnych opcjach w ramach Azure SQL usług: Azure SQL Database, Azure SQL Managed Instance i SQL Server na maszynie wirtualnej platformy Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server chmury, SQL Server w chmurze, baza danych PaaS, SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 394b3390386c60e2a64f52dd944dfcdb0d33951e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727028"
---
# <a name="what-is-azure-sql"></a>Co to jest usługa Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL to rodzina zarządzanych, bezpiecznych i inteligentnych produktów, które korzystają z SQL Server bazy danych w chmurze platformy Azure.

- **Azure SQL Database:** obsługa nowoczesnych aplikacji w chmurze w inteligentnej, zarządzanej usłudze bazy danych, która obejmuje obliczenia bez serwera. 
- **Azure SQL Managed Instance:** Modernizuj istniejące aplikacje usługi SQL Server na dużą skalę za pomocą inteligentnego, w pełni zarządzanego wystąpienia jako usługi, z niemal 100% parzystością funkcji z aparatem SQL Server bazy danych. Najlepsze rozwiązanie w przypadku większości migracji do chmury.
- SQL Server maszyn wirtualnych platformy **Azure:** łatwe mieść i przesuwanie obciążeń platformy SQL Server oraz utrzymywanie 100% SQL Server zgodności i dostępu na poziomie systemu operacyjnego. 
 
Azure SQL korzysta ze znanego aparatu SQL Server, dzięki czemu można łatwo migrować aplikacje i nadal korzystać ze znanych narzędzi, języków i zasobów. Twoje umiejętności i doświadczenie przenosz do chmury, aby jeszcze bardziej korzystać z tego, co już masz. 

Dowiedz się, jak każdy produkt pasuje do platformy danych Azure SQL firmy Microsoft, aby dopasować go do odpowiednich opcji dla Twoich wymagań biznesowych. Niezależnie od tego, czy priorytet ma oszczędność kosztów, czy minimalną administrację, ten artykuł może pomóc w podjęciu decyzji o tym, które podejście spełnia najważniejsze wymagania biznesowe.

Jeśli jesteś nowym użytkownikom Azure SQL, zobacz  wideo Co to jest Azure SQL z naszej szczegółowej serii filmów wideo [Azure SQL wideo:](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Jak możemy poprawić Azure SQL? [Weź udział w ankiecie](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="overview"></a>Omówienie

W dzisiejszym świecie opartym na danych transformacja cyfrowa coraz bardziej zależy od możliwości zarządzania olbrzymimi ilościami danych i wykorzystania ich potencjału. Jednak współczesne majątek danych jest coraz bardziej złożony, a dane są hostowane lokalnie, w chmurze lub na brzegu sieci. Deweloperzy, którzy są twórcami inteligentnych i immersywnych aplikacji, mogą być ograniczeni ograniczeniami, które mogą ostatecznie wpłynąć na ich środowisko. Ograniczenia wynikające z niezgodnych platform, nieodpowiednie zabezpieczenia danych, niewystarczające zasoby i bariery cenowo-wydajnościowe tworzą złożoność, która może utrudnić modernizację i opracowywanie aplikacji. 

Podczas każdej dyskusji dotyczącej baz danych Azure w zestawieniu z lokalnymi bazami danych programu SQL Server należy przede wszystkim pamiętać o tym, że można używać ich wszystkich. Platforma danych firmy Microsoft korzysta SQL Server technologii i udostępnia ją na maszynach fizycznych, w środowiskach chmur prywatnych, w środowiskach chmur prywatnych hostowanych przez inne firmy i w chmurze publicznej. 


### <a name="fully-managed-and-always-up-to-date"></a>W pełni zarządzane i zawsze aktualne 

Poświęcaj więcej czasu na wprowadzanie innowacji, a mniej na stosowanie poprawek, aktualizowanie i kopię zapasową baz danych. Platforma Azure jest jedyną chmurą z zawszezielonym bazą danych SQL, która automatycznie stosuje najnowsze aktualizacje i poprawki, dzięki czemu bazy danych są zawsze aktualne, eliminując problemy związane z zakończeniem obsługi. Nawet złożone zadania, takie jak dostrajanie wydajności, wysoka dostępność, odzyskiwanie po awarii i kopie zapasowe, są zautomatyzowane, co pozwala skupić się na aplikacjach.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Ochrona danych za pomocą wbudowanych inteligentnych zabezpieczeń 

Platforma Azure stale monitoruje twoje dane pod adresem zagrożeń. Za Azure SQL można:

- Korygowanie potencjalnych zagrożeń w czasie rzeczywistym dzięki inteligentnemu zaawansowanemu [wykrywaniu zagrożeń](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) i proaktywnym alertom oceny luk w zabezpieczeniach. 
- Uzyskaj wiodącą w branży, wielowarstwową ochronę z wbudowanymi mechanizmami kontroli zabezpieczeń, w tym T-SQL, uwierzytelnianiem, siecią i zarządzaniem kluczami. [](https://azure.microsoft.com/overview/security/) 
- Skorzystaj z najbardziej kompleksowego zakresu [zgodności dowolnej](https://azure.microsoft.com/overview/trusted-cloud/compliance/) usługi bazy danych w chmurze. 


### <a name="business-motivations"></a>Motywacje biznesowe

Istnieje kilka czynników, które mogą mieć wpływ na decyzję o wybraniu między różnymi ofertami danych:

- [Koszt:](#cost)zarówno opcja PaaS, jak i IaaS obejmują cenę bazową, która obejmuje podstawową infrastrukturę i licencjonowanie. Jednak w przypadku usługi IaaS należy zainwestować dodatkowy czas i zasoby do zarządzania bazą danych, podczas gdy w przypadku usługi PaaS te funkcje administracyjne są uwzględnione w cenie. IaaS umożliwia zamykanie zasobów, gdy nie są one potrzebne do obniżenia kosztów, podczas gdy usługi PaaS są zawsze uruchomione, chyba że zasoby są upuszczane i ponownie tworzyć w razie potrzeby.
- [Administracja:](#administration)Opcje PaaS skracają czas potrzebny do zainwestowania w administrowanie bazą danych. Jednak ogranicza również zakres niestandardowych zadań administracyjnych i skryptów, które można wykonywać lub uruchamiać. Na przykład clr nie jest obsługiwany w SQL Database, ale jest obsługiwany dla wystąpienia SQL Managed Instance. Ponadto żadne opcje wdrażania w usługach PaaS nie obsługują używania flag śledzenia.
- [Umowa poziomu usług:](#service-level-agreement-sla)Usługi IaaS i PaaS zapewniają umowę SLA o wysokim standardzie branżowym. Opcja PaaS gwarantuje 99,99% umowy SLA, podczas gdy rozwiązanie IaaS gwarantuje 99,95% umowy SLA dla infrastruktury, co oznacza, że należy zaimplementować dodatkowe mechanizmy w celu zapewnienia dostępności baz danych. Umowa SLA na poziomie 99,99% można uzyskać, tworząc dodatkową maszynę wirtualną SQL i wdrażając rozwiązanie wysokiej dostępności SQL Server zawsze wł. grupy dostępności. 
- Czas na przejście na platformę [Azure:](#market)usługa SQL Server na maszynie wirtualnej platformy Azure to dokładne dopasowanie środowiska, dlatego migracja ze środowiska lokalnego na maszynę wirtualną platformy Azure nie różni się od przenoszenia baz danych z jednego serwera lokalnego na inny. SQL Managed Instance umożliwia także łatwą migrację; Mogą jednak wystąpić pewne zmiany, które należy zastosować przed migracją. 


## <a name="service-comparison"></a>Porównanie usług

   ![Opcje SQL Server chmury: SQL Server na platformie IaaS lub SaaS SQL Database w chmurze.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak widać na diagramie, poszczególne oferty usług można scharakteryzować według poziomu administracji nad infrastrukturą oraz stopnia efektywności kosztowej.

Na platformie Azure obciążenia SQL Server mogą działać jako usługa hostowana[(PaaS)](https://azure.microsoft.com/overview/what-is-paas/)lub hostowana infrastruktura[(IaaS).](https://azure.microsoft.com/overview/what-is-iaas/) W ramach usługi PaaS masz wiele opcji produktów i warstw usług w ramach każdej opcji. Kluczowe pytanie, które należy zadać podczas podejmowania decyzji między usługami PaaS i IaaS, to: czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) jest relacyjną bazą danych jako usługą (DBaaS) hostowaną na platformie Azure, która należy do kategorii branżowej *PaaS (Platforma* jako usługa). 
- Najlepsze rozwiązanie w przypadku nowoczesnych aplikacji w chmurze, które chcą korzystać z najnowszych stabilnych SQL Server i mają ograniczenia czasowe w zakresie opracowywania i marketingu. 
- W pełni zarządzany SQL Server bazy danych oparty na najnowszych stabilnych Enterprise Edition SQL Server. SQL Database ma dwie opcje wdrażania zbudowane na standardowym sprzęcie i oprogramowaniu, które jest własnością firmy Microsoft, jest przez nie hostowane i obsługiwane. 

Dzięki SQL Server można używać wbudowanych funkcji i funkcji, które wymagają rozbudowanych konfiguracji (lokalnie lub na maszynie wirtualnej platformy Azure). Płatność za korzystanie z usługi SQL Database odbywa się zgodnie z rzeczywistym użyciem, a opcje pozwalają na skalowanie w górę lub w poziomie, dzięki czemu klienci mają do dyspozycji większe możliwości bez najmniejszych zakłóceń. SQL Database dodatkowe funkcje, które nie są dostępne w SQL Server, takie jak wbudowana wysoka dostępność, inteligencja i zarządzanie.


Azure SQL Database oferuje następujące opcje wdrażania:
  - Jako [*pojedyncza baza danych*](database/single-database-overview.md) z własnym zestawem zasobów zarządzanych za pośrednictwem [serwera logicznego SQL.](database/logical-servers.md) Pojedyncza baza danych jest podobna do [zawartej bazy danych w](/sql/relational-databases/databases/contained-databases) SQL Server. Ta opcja jest zoptymalizowana pod kątem nowoczesnego tworzenia aplikacji dla nowych aplikacji urodzinych w chmurze. [Dostępne są](database/service-tier-hyperscale.md) opcje [hiperskali i bez](database/serverless-tier-overview.md) serwera.
  - Elastyczna [*pula*](database/elastic-pool-overview.md), która jest kolekcją baz danych z udostępnionym zestawem zasobów zarządzanych za pośrednictwem [logicznego serwera SQL.](database/logical-servers.md) Pojedyncze bazy danych można przenosić do elastycznej puli i z nich. Ta opcja jest zoptymalizowana pod kątem nowoczesnego tworzenia aplikacji dla nowych aplikacji w chmurze przy użyciu wielodostępnych wzorców aplikacji SaaS. Elastyczne pule zapewniają ekonomiczne rozwiązanie do zarządzania wydajnością wielu baz danych, które mają wzorce użycia zmiennych.

### <a name="azure-sql-managed-instance"></a>Wystąpienie zarządzane Azure SQL

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) należy do kategorii branżowej typu Platforma jako usługa *(PaaS)* i jest najlepszy w przypadku większości migracji do chmury. SQL Managed Instance to kolekcja baz danych systemu i użytkowników ze współużytkowym zestawem zasobów, które są gotowe do użycia w przypadku migowania "lift-and-shift".  
- Najlepsze rozwiązanie dla nowych lub istniejących aplikacji lokalnych, które chcą korzystać z najnowszych stabilnych SQL Server i które są migrowane do chmury przy minimalnych zmianach. Wystąpienie klasy SQL Managed Instance jest podobne do wystąpienia aparatu bazy danych usługi [Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview) oferującego udostępnione zasoby dla baz danych i dodatkowe funkcje w zakresie wystąpienia. 
- SQL Managed Instance obsługuje migrację baz danych ze środowisk lokalnych przy minimalnej lub bez zmian w bazie danych. Ta opcja zapewnia wszystkie zalety usługi PaaS Azure SQL Database ale dodaje możliwości, które były wcześniej dostępne tylko w SQL Server wirtualnych. Obejmuje to natywną sieć wirtualną i niemal 100% zgodność z lokalnymi SQL Server. Wystąpienia usługi SQL Managed Instance zapewniają pełną SQL Server i zgodność funkcji na temat migrowania serwerów SQL na platformę Azure.

### <a name="sql-server-on-azure-vm"></a>Program SQL Server na maszynie wirtualnej platformy Azure

[SQL Server maszyny](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) wirtualnej platformy Azure należy do kategorii branżowej Infrastruktura jako usługa *(IaaS)* i umożliwia uruchamianie usługi SQL Server wewnątrz w pełni zarządzanej maszyny wirtualnej na platformie Azure. 
- SQL Server zainstalowana i hostowana w chmurze działa na maszynach wirtualnych z systemem Windows Server lub Linux uruchomionych na platformie Azure, nazywanych również infrastrukturą jako usługą (IaaS). Maszyny wirtualne SQL są dobrą opcją do migrowania lokalnych baz danych i SQL Server baz danych i aplikacji bez żadnych zmian w bazie danych. Wszystkie najnowsze wersje i wydania usługi SQL Server są dostępne do zainstalowania na maszynie wirtualnej IaaS. 
- Najlepsze rozwiązanie w przypadku migracji i aplikacji wymagających dostępu na poziomie systemu operacyjnego. Maszyny wirtualne SQL na platformie Azure są gotowe do migracji "lift-and-shift" dla istniejących aplikacji, które wymagają szybkiej migracji do chmury przy minimalnych zmianach lub bez żadnych zmian. Maszyny wirtualne SQL zapewniają pełną kontrolę administracyjną nad SQL Server i podstawowymi systemami operacyjnymi w celu migracji na platformę Azure. 
- Najważniejszą różnicą między SQL Database a SQL Managed Instance jest to, że SQL Server azure Virtual Machines umożliwia pełną kontrolę nad aparatem bazy danych. Możesz wybrać, kiedy rozpocząć konserwację/stosowanie poprawek, zmienić model odzyskiwania na prosty lub rejestrowany zbiorczo, wstrzymywać lub uruchamiać usługę w razie potrzeby, a także w pełni dostosować SQL Server bazy danych. Dzięki tej dodatkowej kontroli dodatkowa odpowiedzialność za zarządzanie maszyną wirtualną jest dodatkowa.
- Scenariusze szybkiego tworzenia i testowania, gdy nie chcesz kupować lokalnego nieprodukcyjnego sprzętu dla programu SQL Server. Maszyny wirtualne SQL działają również na standardowym sprzęcie, który jest własnością firmy Microsoft, jest przez nie hostowany i utrzymywany. W przypadku korzystania z maszyn wirtualnych SQL można płacić zgodnie z użyciem licencji usługi SQL Server już zawartej w obrazie SQL Server lub łatwo użyć istniejącej licencji. Możesz również w razie potrzeby zatrzymać lub wznowić działanie maszyny wirtualnej. 
- Zoptymalizowany pod kątem migrowania istniejących aplikacji na platformę Azure lub rozszerzania istniejących aplikacji lokalnych na chmurę we wdrożeniach hybrydowych. Ponadto przy użyciu programu SQL Server na maszynie wirtualnej można tworzyć i testować tradycyjne aplikacje programu SQL Server. W przypadku maszyn wirtualnych SQL masz pełne prawa administracyjne do dedykowanego SQL Server i maszyny wirtualnej opartej na chmurze. To doskonałe rozwiązanie, gdy firma ma już dostępne zasoby informatyczne do obsługi maszyn wirtualnych. Te funkcje umożliwiają stworzenie doskonale dopasowanego systemu, który spełni określone wymagania aplikacji związane z wydajnością i dostępnością.


### <a name="comparison-table"></a>Tabela porównawcza

W poniższej tabeli wymieniono dodatkowe różnice, ale zarówno SQL Database, jak i SQL Managed Instance są optymalizowane w celu zmniejszenia ogólnych kosztów zarządzania do minimum w celu aprowowania wielu baz danych i zarządzania *nimi.* Bieżące koszty administracyjne są zmniejszane, ponieważ nie trzeba zarządzać żadnymi maszynami wirtualnymi, systemem operacyjnym ani oprogramowaniem bazy danych. Nie wymaga zarządzania uaktualnieniami, wysoką dostępnością ani [kopiami zapasowymi](database/automated-backups-overview.md). 

Ogólnie rzecz biorąc, SQL Database i SQL Managed Instance znacznie zwiększyć liczbę baz danych zarządzanych przez pojedynczy zasób IT lub dewelopera. [Pule elastyczne](database/elastic-pool-overview.md) obsługują również architektury aplikacji wielodostępnych SaaS z funkcjami, w tym izolacją dzierżawców i możliwością skalowania w celu obniżenia kosztów przez udostępnianie zasobów między bazami danych. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md) zapewnia obsługę funkcji w zakresie wystąpienia, co umożliwia łatwą migrację istniejących aplikacji, a także udostępnianie zasobów między bazami danych. Podczas gdy [SQL Server maszyn](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) wirtualnych platformy Azure zapewniają administratorom baz danych środowisko najbardziej podobne do środowiska lokalnego, które znają. 


| Azure SQL Database | Wystąpienie zarządzane Azure SQL | Program SQL Server na maszynie wirtualnej platformy Azure |
| :--- | :--- | :--- |
|Obsługuje większość lokalnych możliwości na poziomie bazy danych. Najczęściej używane funkcje SQL Server są dostępne.<br/>Gwarantowana dostępność na poziomie 99,995%.<br/>Wbudowane kopie zapasowe, stosowanie poprawek i odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Możliwość przypisywania niezbędnych zasobów (procesor/magazyn) do poszczególnych baz danych.<br/>Wbudowana zaawansowana inteligencja i zabezpieczenia.<br/>Zmiana zasobów w trybie online (procesor/magazyn).| Obsługuje niemal wszystkie lokalne możliwości na poziomie wystąpienia i bazy danych. Wysoka zgodność z SQL Server.<br/>Gwarantowana dostępność na poziomie 99,99%.<br/>Wbudowane kopie zapasowe, stosowanie poprawek i odzyskiwanie.<br/>Najnowsza stabilna wersja aparatu bazy danych.<br/>Łatwa migracja z SQL Server.<br/>Prywatny adres IP w usłudze Azure Virtual Network.<br/>Wbudowana zaawansowana inteligencja i zabezpieczenia.<br/>Zmiana zasobów w trybie online (procesor/magazyn).| Masz pełną kontrolę nad aparatem SQL Server danych. Obsługuje wszystkie funkcje lokalne.<br/>Dostępność do 99,99%.<br/>Pełna parzystość z pasującą wersją lokalnej SQL Server.<br/>Poprawiono dobrze znaną wersję aparatu bazy danych.<br/>Łatwa migracja z SQL Server.<br/>Prywatny adres IP w usłudze Azure Virtual Network.<br/>Istnieje możliwość wdrażania aplikacji lub usług na hoście, na którym SQL Server jest umieszczana.|
|Migracja z SQL Server może być trudna.<br/>Niektóre SQL Server są niedostępne.<br/>Brak gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczysty).<br/>Zgodność z wersją SQL Server można osiągnąć tylko przy użyciu poziomów zgodności bazy danych.<br/>Obsługa prywatnych adresów IP za [pomocą Azure Private Link](database/private-endpoint-overview.md).|Nadal istnieje minimalna liczba funkcji SQL Server, które nie są dostępne.<br/>Brak gwarantowanego dokładnego czasu konserwacji (ale niemal przezroczysty).<br/>Zgodność z wersją SQL Server można osiągnąć tylko przy użyciu poziomów zgodności bazy danych.|Musisz zarządzać kopiami zapasowym i poprawkami.<br>Musisz zaimplementować własne High-Availability rozwiązania.<br/>Istnieje przestój podczas zmiany zasobów (procesor/magazyn)|
| Bazy danych o pojemności do 100 TB. | Do 8 TB. | SQL Server wystąpienia z magazynem o pojemności do 256 TB. Wystąpienie może obsługiwać dowolną liczbę baz danych zależnie od potrzeb. |
| Aplikacja lokalna może uzyskać dostęp do danych w Azure SQL Database. | [Natywna implementacja sieci wirtualnej](managed-instance/vnet-existing-add-subnet.md) i łączność ze środowiskiem lokalnym przy użyciu usługi Azure Express Route lub VPN Gateway. | Maszyny wirtualne SQL mogą mieć aplikacje, które działają częściowo w chmurze i częściowo lokalnie. Można na przykład rozszerzyć sieć lokalną i domenę usługi Active Directory do chmury za pośrednictwem usługi [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Aby uzyskać więcej informacji na temat rozwiązań chmury hybrydowej, zobacz [Extending on-premises data solutions to the cloud (Rozszerzanie lokalnych rozwiązań danych do chmury).](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud) |


## <a name="cost"></a>Koszt

Niezależnie od tego, czy jesteś startupem, który jest zagorzały na gotówkę, czy też zespołem z siedzibą firmy, która działa w ramach ścisłego budżetu, ograniczone fundusze są często głównym czynnikiem decydującym o decyzjech dotyczących hostowania baz danych. W tej sekcji poznasz podstawy rozliczeń i licencjonowania na platformie Azure skojarzone z Azure SQL rodziny usług.  Poznasz również sposób obliczania całkowitego kosztu aplikacji.

### <a name="billing-and-licensing-basics"></a>Podstawowe informacje dotyczące rozliczeń i licencjonowania

Obecnie usługi **SQL Database** i **SQL Managed Instance** są sprzedawane jako usługa i są dostępne z kilkoma opcjami i w kilku warstwach usług z różnymi cenami zasobów, z których wszystkie są rozliczane godzinowo według stałej stawki na podstawie warstwy usługi i rozmiaru zasobów obliczeniowych. Aby uzyskać najnowsze informacje na temat bieżących obsługiwanych warstw usług, rozmiarów zasobów obliczeniowych i wielkości magazynu, zobacz Model zakupów oparty na jednostkach [DTU](database/service-tiers-dtu.md) dla modelu zakupów opartych na modelu zakupów opartym na modelu SQL Database i opartym na rdzeniu SQL Database i [SQL Managed Instance](database/service-tiers-vcore.md).

- Dzięki usłudze SQL Database możesz wybrać warstwę usług, która odpowiada Twoim potrzebom, spośród szerokiej gamy cen, [](database/elastic-pool-overview.md) począwszy od 5 USD/miesiąc dla warstwy Podstawowa, i utworzyć elastyczne pule w celu udostępniania zasobów między bazami danych, aby zmniejszyć koszty i obsłużyć skoki użycia.
- Dzięki SQL Managed Instance możesz również korzystać z własnej licencji. Aby uzyskać więcej informacji na temat licencjonowania własnego, zobacz [](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) przenośność licencji w ramach programu Software Assurance na platformie [Azure](https://azure.microsoft.com/pricing/license-mobility/) lub skorzystaj z kalkulatora Korzyść użycia hybrydowego platformy Azure, aby zobaczyć, jak zaoszczędzić do **40%.**

Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/). Możesz dynamicznie dostosowywać warstwy usług i rozmiary obliczeniowe, aby dopasować je do różnych potrzeb aplikacji w zakresie przepływności.

Dzięki **SQL Database** i **SQL Managed Instance** oprogramowanie bazy danych jest automatycznie konfigurowane, poprawiane i uaktualniane przez platformę Azure, co zmniejsza koszty administracyjne. Ponadto [wbudowana funkcja tworzenia kopii zapasowych](database/automated-backups-overview.md) pozwala osiągnąć znaczne oszczędności, zwłaszcza w przypadku dużej liczby baz danych.

W **przypadku usługi SQL na** maszyn wirtualnych platformy Azure można używać dowolnych obrazów SQL Server dostarczanych przez platformę (co obejmuje licencję) lub SQL Server licencji. Dostępne są wszystkie obsługiwane wersje SQL Server (2008R2, 2012, 2014, 2016, 2017, 2019) i wersje (Developer, Express, Web, Standard, Enterprise). Ponadto są dostępne wersje bring-your-own-license (BYOL) obrazów. W przypadku użycia obrazów dostarczanych przez platformę Azure koszty operacyjne zależą od rozmiaru maszyny wirtualnej, a także od wybranej wersji programu SQL Server. Niezależnie od rozmiaru maszyny wirtualnej lub wersji SQL Server opłaty za minutę licencjonowania systemu SQL Server i systemu Windows lub Linux Server, wraz z kosztami usługi Azure Storage dla dysków maszyn wirtualnych. Opcja rozliczania co minutę pozwala używać programu SQL Server tak długo, jak jest to konieczne bez wykupywania dodatkowych licencji programu SQL Server. Jeśli masz własną licencję usługi SQL Server na platformę Azure, opłaty są naliczane tylko za serwer i magazyn. Więcej informacji na temat przenoszenia własnej licencji można znaleźć w temacie [Przenośność licencji za pośrednictwem programu Software Assurance w systemie Azure](https://azure.microsoft.com/pricing/license-mobility/). Rozliczany jest również internetowy ruch wychodzący po zwykłych [stawkach transferu danych](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Obliczanie całkowitego kosztu aplikacji

Po rozpoczęciu korzystania z platformy w chmurze koszt działania aplikacji obejmuje koszty związane z nowym opracowywaniem i bieżącymi kosztami administracyjnymi, a także koszty usług platformy chmury publicznej.

Więcej informacji na temat cen zawierają następujące zasoby:

- [SQL Database & SQL Managed Instance cen](https://azure.microsoft.com/pricing/details/sql-database/)
- [Cennik maszyny wirtualnej dla](https://azure.microsoft.com/pricing/details/virtual-machines/) [języka SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) i systemu [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administracja

Dla wielu firm decyzja o przeniesieniu usług do usługi w chmurze wiąże się zarówno z ograniczeniem złożoności w zakresie administracji, jak i obniżeniem kosztów. Dzięki usługom IaaS i PaaS platforma Azure administruje podstawową infrastrukturą i automatycznie replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii, konfiguruje i uaktualnia oprogramowanie bazy danych, zarządza równoważeniem obciążenia i w przypadku awarii serwera w centrum danych działa w sposób przezroczysty.

- Dzięki **SQL Database** i **SQL Managed Instance** można nadal administrować bazą danych, ale nie trzeba już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykładowe elementy, którymi można dalej zarządzać to bazy danych, identyfikatory logowania, dostrajanie indeksów i zapytań oraz inspekcja i zabezpieczenia. Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji i administracji.
- Usługa **SQL na maszynie wirtualnej platformy Azure** ma pełną kontrolę nad systemem operacyjnym i SQL Server konfiguracji wystąpienia. W przypadku maszyny wirtualnej to Ty decydujesz, kiedy należy zaktualizować/uaktualnić system operacyjny i oprogramowanie bazy danych oraz kiedy zainstalować dodatkowe oprogramowanie, takie jak oprogramowanie antywirusowe. Dostępnych jest kilka funkcji automatycznych, które znacznie upraszczają instalowanie poprawek, tworzenie kopii zapasowej i zapewnianie wysokiej dostępności. Ponadto możesz kontrolować rozmiar maszyny wirtualnej, liczbę dysków oraz ich konfiguracje magazynu. Platforma Azure umożliwia zmianę rozmiaru maszyny wirtualnej zgodnie z zapotrzebowaniem. Więcej informacji można znaleźć w temacie [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/sizes.md) (Rozmiary maszyn wirtualnych i usług w chmurze na platformie Azure).

## <a name="service-level-agreement-sla"></a>Umowa sla (Service Level Agreement)

W przypadku wielu działów IT spełnianie zobowiązań wynikających z umowy dotyczącej poziomu usług (SLA) jest najwyższym priorytetem. W tej sekcji opisano, jakie warunki umowy SLA stosuje się do poszczególnych opcji obsługi bazy danych.

W przypadku **Azure SQL Database** **i Azure SQL Managed Instance** firma Microsoft zapewnia dostępność na 99,99%. Aby uzyskać najnowsze informacje, zobacz [Umowa o poziomie usług](https://azure.microsoft.com/support/legal/sla/sql-database/).

W **przypadku usługi SQL na maszynie wirtualnej** platformy Azure firma Microsoft zapewnia 99,95% umowy SLA dotyczącej dostępności, która obejmuje tylko maszynę wirtualną. Umowa SLA nie obejmuje procesów (np. programu SQL Server) uruchomionych na maszynie wirtualnej i wymaga obsługi przynajmniej dwóch wystąpień maszyny wirtualnej w zbiorze dostępności. Najnowsze informacje znajdują się w artykule [Maszyny wirtualne — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Aby uzyskać wysoką dostępność bazy danych w ramach maszyn wirtualnych, należy skonfigurować jedną z obsługiwanych opcji wysokiej dostępności w programie SQL Server, taką jak zawsze [wł. grupy dostępności.](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) Użycie obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA, ale umożliwia osiągnięcie dostępności bazy danych na poziomie > 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Czas na przejście na platformę Azure

**Azure SQL Database** to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas wejścia na rynek nowych rozwiązań mają kluczowe znaczenie. Dzięki funkcjonalności przypominającej model DBA jest doskonała dla architektów i deweloperów chmury, ponieważ zmniejsza potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

**Azure SQL Managed Instance** znacznie upraszcza migrację istniejących aplikacji na platformę Azure, umożliwiając szybkie przeniesienie zmigrowanych aplikacji baz danych na rynek na platformie Azure.

**Usługa SQL** na maszynie wirtualnej platformy Azure jest idealnym rozwiązaniem, jeśli istniejące lub nowe aplikacje wymagają dużych baz danych lub dostępu do wszystkich funkcji w systemie SQL Server lub Windows/Linux, i chcesz uniknąć czasu i wydatków związanych z pozyskiwaniem nowego sprzętu lokalnego. Jest ona również dobrym rozwiązaniem, gdy chcesz zmigrować istniejące lokalne aplikacje i bazy danych na platformę Azure w stanie takim, w którym usługa SQL Database lub SQL Managed Instance nie jest dobrym rozwiązaniem. Ponieważ nie musisz zmieniać prezentacji, aplikacji i warstw danych, oszczędzasz czas i budżet na zmianie architektury istniejącego rozwiązania. Zamiast tego możesz skoncentrować się na migracji wszystkich rozwiązań do platformy Azure i przeprowadzeniu optymalizacji wydajności, których może wymagać platforma Azure. Więcej informacje zawiera artykuł [Performance Best Practices for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md) (Najlepsze praktyki dotyczące wydajności dla programu SQL Server w usłudze Azure Virtual Machines).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z usługą SQL Database, zobacz [Your first Azure SQL Database](database/single-database-create-quickstart.md) (Twoja pierwsza baza danych Azure SQL Database).
- Zobacz [temat Your first Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md) to get started with SQL Managed Instance (Pierwsza Azure SQL Managed Instance, aby rozpocząć pracę z SQL Managed Instance. 
- Zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Aby rozpocząć pracę z programem SQL Server na maszynach wirtualnych platformy Azure, zobacz temat [Aprowizowanie maszyny wirtualnej programu SQL Server w Portalu Azure](virtual-machines/windows/create-sql-vm-portal.md)
- [Zidentyfikuj SQL Database lub SQL Managed Instance SKU dla lokalnej bazy danych.](/sql/dma/dma-sku-recommend-sql-db/)
