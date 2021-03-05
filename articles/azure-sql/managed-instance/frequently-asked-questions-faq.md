---
title: Często zadawane pytania
titleSuffix: Azure SQL Managed Instance
description: Usługa Azure SQL Managed Instance (często zadawane pytania)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 9e8ef5bf9d5d8000bfdd581cd7e9ae1a1a775fc9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178137"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Usługa Azure SQL Managed Instance (często zadawane pytania)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera najbardziej typowe pytania dotyczące [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Obsługiwane funkcje

**Gdzie mogę znaleźć listę funkcji obsługiwanych w wystąpieniu zarządzanym SQL?**

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym SQL, zobacz [funkcje wystąpienia zarządzanego usługi Azure SQL](../database/features-comparison.md).

Różnice dotyczące składni i zachowań między wystąpieniem zarządzanym i SQL Server usługi Azure SQL można znaleźć w temacie [różnice w języku T-SQL z SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Specyfikacja techniczna, limity zasobów i inne ograniczenia
 
**Gdzie można znaleźć cechy techniczne i limity zasobów dla wystąpienia zarządzanego SQL?**

Aby uzyskać dostęp do charakterystyki generowania sprzętu, zobacz [różnice techniczne w generacjach sprzętowych](resource-limits.md#hardware-generation-characteristics).
Aby uzyskać dostęp do dostępnych warstw usług i ich cech, zobacz [różnice techniczne między warstwami usług](resource-limits.md#service-tier-characteristics).

**Do jakiej warstwy usług mam prawo?**

Każdy klient kwalifikuje się do korzystania z dowolnej warstwy usług. Jeśli jednak chcesz wymienić istniejące licencje na oferty z rabatem na wystąpienie zarządzane usługi Azure SQL, korzystając z [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/), weź pod uwagę, że klienci SQL Server Enterprise Edition z programem Software Assurance są uprawnieni do obsługi [ogólnego przeznaczenia](../database/service-tier-general-purpose.md) lub [krytyczne dla działania firmy](../database/service-tier-business-critical.md) warstw wydajności i SQL Server Standard wydania z programem Software Assurance, kwalifikują się tylko do warstwy wydajności ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [określone prawa AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Jakie typy subskrypcji są obsługiwane w przypadku wystąpienia zarządzanego SQL?**

Aby zapoznać się z listą obsługiwanych typów subskrypcji, zobacz [obsługiwane typy subskrypcji](resource-limits.md#supported-subscription-types). 

**Które regiony platformy Azure są obsługiwane?**

Wystąpienia zarządzane można tworzyć w większości regionów świadczenia usługi Azure. Zobacz [Obsługiwane regiony dla wystąpienia zarządzanego SQL](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Jeśli potrzebujesz wystąpienia zarządzanego w regionie, który nie jest obecnie obsługiwany, [Wyślij żądanie pomocy technicznej za pośrednictwem Azure Portal](../database/quota-increase-request.md).

**Czy istnieją jakieś ograniczenia przydziału dla wdrożeń wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane ma dwa domyślne limity: limit liczby podsieci, których można użyć, oraz limit liczby rdzeni wirtualnych, które można udostępnić. Limity różnią się w zależności od typów i regionów subskrypcji. Aby uzyskać listę ograniczeń zasobów regionalnych według typu subskrypcji, zobacz tabela z [ograniczenia zasobów regionalnych](resource-limits.md#regional-resource-limitations). Są to limity nietrwałe, które można zwiększyć na żądanie. Jeśli potrzebujesz udostępnić więcej wystąpień zarządzanych w Twoich regionach, Wyślij żądanie obsługi, aby zwiększyć przydział przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](../database/quota-increase-request.md).

**Czy mogę zwiększyć liczbę baz danych (100) na moim wystąpieniu zarządzanym na żądanie?**

Nie, a obecnie nie ma żadnych zatwierdzonych planów, aby zwiększyć liczbę baz danych w wystąpieniu zarządzanym SQL. 

**Gdzie mogę przeprowadzić migrację, jeśli mam więcej niż 8TB danych?**
Można rozważyć Migrowanie do innych wersji platformy Azure, które pasują do obciążenia: [Azure SQL Database skalowanie](../database/service-tier-hyperscale.md) lub [SQL Server na platformie Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Gdzie można przeprowadzić migrację, jeśli mam określone wymagania sprzętowe, takie jak większa ilość pamięci RAM, aby rdzeń wirtualny współczynnik lub większą liczbę procesorów CPU?**
Możesz rozważyć przeprowadzenie migracji do [SQL Server na platformie Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) lub [Azure SQL Database](../database/sql-database-paas-overview.md) zoptymalizowanej pod kątem pamięci/procesora.

## <a name="known-issues-and-defects"></a>Znane problemy i wady

**Gdzie mogę znaleźć znane problemy i wady?**

Aby poznać wady i znane problemy, zobacz [znane problemy](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nowe funkcje

**Gdzie mogę znaleźć najnowsze funkcje i funkcje dostępne w publicznej wersji zapoznawczej?**

Aby zapoznać się z nowymi funkcjami i wersjami zapoznawczymi, zobacz [Informacje o wersji](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Tworzenie, aktualizowanie, usuwanie lub przenoszenie wystąpienia zarządzanego SQL

**Jak mogę zainicjować wystąpienie zarządzane SQL?**

Można zainicjować obsługę wystąpienia z [Azure Portal](instance-create-quickstart.md), [programu PowerShell](scripts/create-configure-managed-instance-powershell.md), [interfejsu wiersza polecenia platformy Azure](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) i [szablonów ARM](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates).

**Czy mogę zainicjować zarządzane wystąpienia w istniejącej subskrypcji?**

Tak, możesz udostępnić wystąpienie zarządzane w istniejącej subskrypcji, jeśli ta subskrypcja należy do [obsługiwanych typów subskrypcji](resource-limits.md#supported-subscription-types).

**Dlaczego nie można zainicjować obsługi administracyjnej wystąpienia zarządzanego w podsieci, która rozpoczyna się od cyfry?**

Jest to bieżące ograniczenie składnika podstawowego, który weryfikuje nazwę podsieci względem wyrażenia regularnego ^ [a-za-z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. Wszystkie nazwy, które przekazują wyrażenie regularne i są prawidłowymi nazwami podsieci, są obecnie obsługiwane.

**Jak mogę skalować moje wystąpienie zarządzane?**

Wystąpienie zarządzane można skalować z poziomu [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [programu PowerShell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi#az-sql-mi-update) lub [szablonów usługi ARM](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates).

**Czy mogę przenieść wystąpienie zarządzane z jednego regionu do innego?**

Tak, możesz. Aby uzyskać instrukcje, zobacz [przenoszenie zasobów między regionami](../database/move-resources-across-regions.md).

**Jak mogę usunąć moje wystąpienie zarządzane?**

Wystąpienia zarządzane można usunąć za pomocą Azure Portal, [programu PowerShell](/powershell/module/az.sql/remove-azsqlinstance?preserve-view=true&view=azps-4.3.0), [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi#az-sql-mi-delete) lub [interfejsów API REST Menedżer zasobów](/rest/api/sql/managedinstances/delete).

**Ile czasu zajmuje utworzenie lub zaktualizowanie wystąpienia lub przywrócenie bazy danych?**

Oczekiwany czas utworzenia nowego wystąpienia zarządzanego lub zmiany warstw usług (rdzeni wirtualnych, Storage) zależy od kilku czynników. Zobacz [operacje zarządzania](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Konwencje nazewnictwa

**Czy wystąpienie zarządzane może mieć taką samą nazwę jak SQL Server wystąpienie lokalne?**

Zmiana nazwy wystąpienia zarządzanego nie jest obsługiwana.

**Czy mogę zmienić prefiks strefy DNS?**

Tak, domyślna strefa DNS wystąpienia zarządzanego *. Database.Windows.NET* można zmienić. 

Aby użyć innej strefy DNS zamiast domyślnego, na przykład *. contoso.com*: 
- Użyj CliConfig, aby zdefiniować alias. To narzędzie jest tylko otoką ustawień rejestru, dlatego można ją wykonać przy użyciu zasad grupy lub skryptu.
- Użyj opcji *CNAME* z opcją *TrustServerCertificate = true* .

## <a name="migration-options"></a>Opcje migracji

**Jak przeprowadzić migrację z Azure SQL Database jednej lub elastycznej puli do wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane oferuje te same poziomy wydajności dla wielkości zasobów obliczeniowych i magazynu, co inne opcje wdrażania Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub po prostu potrzebna jest funkcja obsługiwana wyłącznie w wystąpieniu zarządzanym, można migrować dane przy użyciu funkcji eksportu/importu (BACPAC). Poniżej przedstawiono inne sposoby rozpatrywania SQL Database migracji do wystąpienia zarządzanego SQL: 
- Używanie [zewnętrznego źródła danych](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Korzystanie z [Sqlpackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Korzystanie z narzędzia [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Jak przeprowadzić migrację bazy danych wystąpienia do jednego Azure SQL Database?**

Jedną z opcji jest [wyeksportowanie bazy danych do BACPAC](../database/database-export.md) , a następnie [zaimportowanie pliku BACPAC](../database/database-import.md). Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB.

[Replikacji transakcyjnej](replication-two-instances-and-sql-server-configure-tutorial.md?view=sql-server-2017&preserve-view=true) można użyć, jeśli wszystkie tabele w bazie danych mają klucze *podstawowe* i w bazie danych nie ma obiektów OLTP w pamięci.

Nie można przywrócić natywnych kopii zapasowych COPY_ONLY pobranych z wystąpienia zarządzanego do SQL Server, ponieważ wystąpienie zarządzane ma nowszą wersję bazy danych w porównaniu do SQL Server. Aby uzyskać więcej informacji, zobacz [kopia zapasowa tylko do kopiowania](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Jak można migrować wystąpienie SQL Server do wystąpienia zarządzanego SQL?**

Aby przeprowadzić migrację wystąpienia SQL Server, zobacz [SQL Server migracji wystąpień do wystąpienia zarządzanego Azure SQL](migrate-to-instance-from-sql-server.md).

**Jak przeprowadzić migrację z innych platform do wystąpienia zarządzanego SQL?**

Informacje o migracji z innych platform znajdują się w temacie [Przewodnik po migracji bazy danych Azure](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Przełącz generowanie sprzętu 

**Czy mogę przełączać generowanie sprzętu wystąpienia zarządzanego między programem gen 4 i gen 5 online?**

Automatyczne przełączanie online z obliczenia do 5 rdzeń jest możliwe, jeśli sprzęt 5 rdzeń jest dostępny w regionie, w którym zainicjowano udostępnianie wystąpienia zarządzanego. W takim przypadku można sprawdzić na [stronie Przegląd modelu rdzeń wirtualny](../database/service-tiers-vcore.md) , jak przełączać się między generacjami sprzętowymi.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane zostanie udostępnione w tle, a bazy danych zostaną automatycznie przeniesione między starym i nowym wystąpieniem z szybką pracą w trybie failover na końcu procesu.

Uwaga: Sprzęt obliczenia jest wycofywany i nie jest już dostępny w przypadku nowych wdrożeń. Wszystkie nowe bazy danych należy wdrożyć na sprzęcie 5 rdzeń. Przełączanie z 5 rdzeń do obliczenia jest również niedostępne.

## <a name="performance"></a>Wydajność 

**Jak porównać wydajność wystąpienia zarządzanego w celu SQL Server wydajności?**

Aby porównać wydajność między wystąpieniem zarządzanym i SQL Server, dobrym punktem początkowym jest [najlepsze rozwiązanie w zakresie porównania wydajności między wystąpieniem zarządzanym usługi Azure SQL i](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) artykułem SQL Server.

**Co powoduje różnice w działaniu między wystąpieniem zarządzanym i SQL Server?**

Zobacz [najważniejsze przyczyny różnic wydajności między wystąpieniem zarządzanym SQL i SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Aby uzyskać więcej informacji na temat wpływu rozmiaru pliku dziennika na wydajność wystąpienia zarządzanego Ogólnego przeznaczenia, zobacz [wpływ rozmiaru pliku dziennika na ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Jak mogę dostosować wydajność wystąpienia zarządzanego?**

Wydajność wystąpienia zarządzanego można zoptymalizować, wykonując następujące działania:
- [Dostrajanie automatyczne](../database/automatic-tuning-overview.md) , które zapewnia wydajność szczytową i stabilną w ramach ciągłego dostrajania wydajności na podstawie systemu AI i uczenia maszynowego.
-   [OLTP w pamięci](../in-memory-oltp-overview.md) , które zwiększa przepływność i opóźnienie w przypadku obciążeń przetwarzania transakcyjnego i zapewnia szybszy wgląd w dane biznesowe. 

Aby jeszcze bardziej dostosować wydajność, należy rozważyć zastosowanie niektórych *najlepszych* rozwiązań w zakresie [dostrajania aplikacji i bazy danych](../database/performance-guidance.md#tune-your-database).
Jeśli obciążenie obejmuje wiele małych transakcji, należy rozważyć [przełączenie typu połączenia z serwera proxy do trybu przekierowywania](connection-types-overview.md#changing-connection-type) dla małych opóźnień i większej przepływności.

## <a name="monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty

**Jakie są opcje monitorowania i generowania alertów dla mojego wystąpienia zarządzanego?**

Aby uzyskać wszystkie opcje monitorowania i generowania alertów dotyczących użycia i wydajności wystąpienia zarządzanego przez usługę SQL, zobacz wpis w blogu dotyczący [opcji monitorowania wystąpienia zarządzanego Azure SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Aby monitorować wydajności w czasie rzeczywistym dla programu SQL, zobacz [monitorowanie wydajności w czasie rzeczywistym dla wystąpienia zarządzanego usługi Azure SQL DB](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Czy mogę używać programu SQL Profiler do śledzenia wydajności?**

Tak, program SQL Profiler jest obsługiwany lub wystąpienie zarządzane SQL. Aby uzyskać więcej informacji, zobacz [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Czy Database Advisor i Szczegółowe informacje o wydajności zapytań obsługiwane dla baz danych wystąpienia zarządzanego?**

Nie, nie są obsługiwane. Do monitorowania baz danych można używać [widoków DMV](../database/monitoring-with-dmvs.md) i [magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) wraz z [programem SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) i [XEvents](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) .

**Czy można tworzyć alerty metryk w wystąpieniu zarządzanym SQL?**

Tak. Aby uzyskać instrukcje, zobacz [tworzenie alertów dla wystąpienia zarządzanego SQL](alerts-create.md).

**Czy można tworzyć alerty metryk dla bazy danych w wystąpieniu zarządzanym?**

Metryki alertów są dostępne tylko dla wystąpienia zarządzanego. Metryki alertów dla poszczególnych baz danych w wystąpieniu zarządzanym są niedostępne.

## <a name="storage-size"></a>Rozmiar magazynu

**Jaki jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego SQL?**

Rozmiar magazynu zarządzanego przez SQL zależy od wybranej warstwy usług (Ogólnego przeznaczenia lub Krytyczne dla działania firmy). Aby uzyskać ograniczenia dotyczące magazynu tych warstw usług, zobacz [Charakterystyka warstwy usług](../database/service-tiers-general-purpose-business-critical.md).

**Jaki jest minimalny rozmiar magazynu dla wystąpienia zarządzanego?**

Minimalna ilość miejsca dostępnego w wystąpieniu wynosi 32 GB. Magazyn można dodać z przyrostem 32 GB do maksymalnego rozmiaru magazynu. Pierwsze 32 GB są bezpłatne.

**Czy mogę zwiększyć miejsce do magazynowania przypisane do wystąpienia, niezależnie od zasobów obliczeniowych?**

Tak, możesz kupić magazyn dodatków niezależnie od obliczeń, w pewnym zakresie. Zobacz *maks. zarezerwowany magazyn wystąpień* w [tabeli](resource-limits.md#hardware-generation-characteristics).

**Jak mogę zoptymalizować moją wydajność magazynu w Ogólnego przeznaczenia warstwie usług?**

Aby zoptymalizować wydajność magazynu, zobacz [najlepsze rozwiązania dotyczące magazynu w ogólnego przeznaczenia](https://techcommunity.microsoft.com).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

**Czy magazyn kopii zapasowych jest odejmowany od magazynu wystąpienia zarządzanego?**

Nie, magazyn kopii zapasowych nie jest odejmowany od przestrzeni dyskowej wystąpienia zarządzanego. Magazyn kopii zapasowych jest niezależny od ilości miejsca w magazynie i nie ma ograniczonego rozmiaru. Magazyn kopii zapasowych jest ograniczony przez okres, aby zachować kopię zapasową baz danych wystąpień, można skonfigurować do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [zautomatyzowane kopie zapasowe](../database/automated-backups-overview.md).

**Jak mogę zobaczyć, kiedy zautomatyzowane kopie zapasowe są tworzone na moim wystąpieniu zarządzanym?**

Aby śledzić, kiedy zautomatyzowane kopie zapasowe zostały wykonane na wystąpieniu zarządzanym, zobacz [jak śledzić automatyczne kopie zapasowe wystąpienia zarządzanego Azure SQL](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Czy jest dostępna kopia zapasowa na żądanie?**

Tak, możesz utworzyć pełną kopię zapasową w swoich Blob Storage platformy Azure, ale będzie ona dostępnych tylko w wystąpieniu zarządzanym. Aby uzyskać szczegółowe informacje, zobacz [kopia zapasowa tylko do kopiowania](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). Jednak kopia zapasowa tylko do kopiowania jest niemożliwa, jeśli baza danych została zaszyfrowana przez TDE zarządzaną przez usługę, ponieważ certyfikat używany do szyfrowania jest niedostępny. W takim przypadku należy użyć funkcji przywracania w czasie do przenoszenia bazy danych do innego wystąpienia zarządzanego SQL lub przełączyć się na klucz zarządzany przez klienta.

**Czy natywne odzyskiwanie (z plików. bak) do obsługiwanego wystąpienia zarządzanego?**

Tak, jest obsługiwana i dostępna dla wersji SQL Server 2005 +.  Aby użyć przywracania natywnego, Przekaż plik. bak do usługi Azure Blob Storage i wykonaj polecenia T-SQL. Aby uzyskać więcej informacji, zobacz [natywne odzyskiwanie z adresu URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Czy moje systemowe bazy danych zostały zreplikowane do wystąpienia dodatkowego w grupie trybu failover?**

Systemowe bazy danych nie są replikowane do wystąpienia dodatkowego w grupie trybu failover. W związku z tym scenariusze, które są zależne od obiektów z systemowych baz danych, będą niedostępne w wystąpieniu pomocniczym, chyba że obiekty są tworzone ręcznie na serwerze pomocniczym. Aby obejść ten temat, zobacz [Włączanie scenariuszy zależnych od obiektu z systemowych baz danych](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Wymagania dotyczące sieci 

**Jakie są bieżące ograniczenia sieciowej grupy zabezpieczeń ruchu przychodzącego/wychodzącego w podsieci wystąpienia zarządzanego?**

Wymagane reguły sieciowej grupy zabezpieczeń i UDR są udokumentowane w [tym miejscu](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)i automatycznie ustawiane przez usługę.
Należy pamiętać, że te reguły są tylko tymi, które są potrzebne do obsługi usługi. Aby nawiązać połączenie z wystąpieniem zarządzanym i korzystać z innych funkcji, należy ustawić dodatkowe, specyficzne dla funkcji reguły, które należy zachować.

**Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na portach zarządzania?**

Wystąpienie zarządzane SQL jest odpowiedzialne za ustawianie reguł na portach zarządzania. Jest to realizowane przy użyciu funkcji o nazwie [podsieć z obsługą usługi](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Ma to na celu zapewnienie nieprzerwanego przepływu ruchu związanego z zarządzaniem w celu spełnienia warunków umowy SLA.

**Czy mogę uzyskać źródłowe zakresy adresów IP, które są używane przez ruch przychodzący zarządzania?**

Tak. Ruch przychodzący przez grupę zabezpieczeń sieci można analizować przez [skonfigurowanie Network Watcher dzienników przepływu](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group).

**Czy mogę ustawić sieciowej grupy zabezpieczeń, aby kontrolować dostęp do punktu końcowego danych (port 1433)?**

Tak. Po aprowizacji wystąpienia zarządzanego można ustawić sieciowej grupy zabezpieczeń, który kontroluje dostęp przychodzący do portu 1433. Zalecane jest zawężenie zakresu adresów IP tak dużo, jak to możliwe.

**Czy można ustawić urządzenie WUS lub zaporę lokalną do filtrowania ruchu zarządzania wychodzącego na podstawie nazw FQDN?**

Nie. Nie jest to obsługiwane z kilku powodów:
-   Ruch związany z routingiem reprezentujący odpowiedź na przychodzące żądanie zarządzania mógłby być asymetryczny i nie mógł działać.
-   Ruch związany z kierowaniem do magazynu może mieć wpływ na ograniczenia przepływności i opóźnienia, dzięki czemu nie będzie można zapewnić oczekiwanej jakości i dostępności usługi.
-   W oparciu o środowisko te konfiguracje są podatne na błędy i nie są obsługiwane.

**Czy mogę ustawić urządzenie WUS lub zaporę dla wychodzącego ruchu niezwiązanego z zarządzaniem?**

Tak. Najprostszym sposobem osiągnięcia tego celu jest dodanie reguły 0/0 do UDR skojarzonej z podsiecią wystąpienia zarządzanego w celu kierowania ruchu przez urządzenie WUS.
 
**Ile adresów IP jest potrzebnych dla wystąpienia zarządzanego?**

Podsieć musi mieć wystarczającą liczbę dostępnych [adresów IP](connectivity-architecture-overview.md#network-requirements). Aby określić rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego SQL, zobacz [Określanie wymaganego rozmiaru podsieci i zakresu dla wystąpienia zarządzanego](./vnet-subnet-determine-size.md). 

**Co zrobić, jeśli nie ma wystarczającej liczby adresów IP na potrzeby wykonywania operacji aktualizacji wystąpienia?**

W przypadku braku wystarczającej liczby [adresów IP](connectivity-architecture-overview.md#network-requirements) w podsieci, w której jest inicjowane zarządzane wystąpienie, należy utworzyć nową podsieć i w niej nowe wystąpienie zarządzane. Zalecamy również utworzenie nowej podsieci przy użyciu większej liczby przydzielonych adresów IP, aby uniknąć takich sytuacji w przyszłych operacjach aktualizowania. Po aprowizacji nowego wystąpienia można ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub wykonać [przywracanie do określonego momentu w czasie](point-in-time-restore.md?tabs=azure-powershell).

**Czy muszę mieć pustą podsieć, aby utworzyć wystąpienie zarządzane?**

Nie. Można użyć pustej podsieci lub podsieci, która zawiera już wystąpienia zarządzane. 

**Czy mogę zmienić zakres adresów podsieci?**

Nie Jeśli w programie istnieją wystąpienia zarządzane. Jest to ograniczenie infrastruktury sieci platformy Azure. Można [dodać tylko dodatkową przestrzeń adresową do pustej podsieci](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Czy mogę przenieść wystąpienie zarządzane do innej podsieci?**

Nie. Jest to bieżące ograniczenie projektowania wystąpienia zarządzanego. Można jednak udostępnić nowe wystąpienie w innej podsieci i ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub przeprowadzić [przywracanie do punktu w czasie](point-in-time-restore.md?tabs=azure-powershell)między wystąpieniami.

**Czy potrzebuję pustej sieci wirtualnej, aby utworzyć wystąpienie zarządzane?**

Nie jest to wymagane. Można [utworzyć sieć wirtualną dla wystąpienia zarządzanego Azure SQL](./virtual-network-subnet-create-arm-template.md) lub [skonfigurować istniejącą sieć wirtualną dla wystąpienia zarządzanego Azure SQL](./vnet-existing-add-subnet.md).

**Czy można umieścić wystąpienie zarządzane z innymi usługami w podsieci?**

Nie. Obecnie nie obsługujemy umieszczania wystąpienia zarządzanego w podsieci, która zawiera już inne typy zasobów.

## <a name="connectivity"></a>Łączność 

**Czy mogę połączyć się z moim wystąpieniem zarządzanym przy użyciu adresu IP?**

Nie, to nie jest obsługiwane. Nazwa hosta wystąpienia zarządzanego jest mapowana na moduł równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego. Ponieważ jeden klaster wirtualny może obsługiwać wiele wystąpień zarządzanych, nie można kierować połączenia do właściwego wystąpienia zarządzanego bez określania jego nazwy.
Aby uzyskać więcej informacji na temat architektury klastra wirtualnego wystąpienia zarządzanego SQL, zobacz [Architektura łączności klastra wirtualnego](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Czy moje wystąpienie zarządzane ma statyczny adres IP?**

Nie jest to obecnie obsługiwane.

W rzadkich przypadkach, ale konieczne może być przeprowadzenie migracji w trybie online wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby migracja jest spowodowana zmianami w naszym stosie technologii, które mają na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP mapowanego na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie wiąże się z obsługą statycznego adresu IP i zastrzega sobie prawo do jego zmiany bez powiadomienia jako części zwykłych cykli konserwacyjnych.

Z tego powodu zdecydowanie odradzamy niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

**Czy zarządzane wystąpienie ma publiczny punkt końcowy?**

Tak. Wystąpienie zarządzane ma publiczny punkt końcowy, który jest domyślnie używany tylko do zarządzania usługami, ale klient może również włączyć go na potrzeby dostępu do danych. Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpienia zarządzanego SQL z publicznymi punktami końcowymi](./public-endpoint-overview.md). Aby skonfigurować publiczny punkt końcowy, przejdź do pozycji [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym SQL](public-endpoint-configure.md).

**Jak zarządzane wystąpienie zarządza dostępem do publicznego punktu końcowego?**

Wystąpienie zarządzane kontroluje dostęp do publicznego punktu końcowego zarówno na poziomie sieci, jak i aplikacji.

Usługi zarządzania i wdrażania nawiązują połączenie z wystąpieniem zarządzanym przy użyciu [punktu końcowego zarządzania](./connectivity-architecture-overview.md#management-endpoint) , który jest mapowany na zewnętrzny moduł równoważenia obciążenia. Ruch jest kierowany do węzłów tylko wtedy, gdy jest odbierany ze wstępnie zdefiniowanym zestawem portów, których używają tylko składniki zarządzania wystąpienia zarządzanego. Wbudowana zapora w węzłach jest skonfigurowana tak, aby zezwalała na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnie uwierzytelniają całą komunikację między składnikami zarządzania a płaszczyzną zarządzania. Aby uzyskać więcej informacji, zobacz [Architektura łączności dla wystąpienia zarządzanego SQL](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Czy mogę użyć publicznego punktu końcowego, aby uzyskać dostęp do danych w bazach danych wystąpienia zarządzanego?**

Tak. Klient będzie musiał włączyć dostęp do danych publicznego punktu końcowego z [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [programu Azure Portal PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM i skonfigurować sieciowej grupy zabezpieczeń do blokowania dostępu do portu danych (numer portu 3342). Aby uzyskać więcej informacji, zobacz [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym Azure SQL](public-endpoint-configure.md) i [bezpieczne używanie wystąpienia zarządzanego usługi Azure SQL z publicznym punktem końcowym](public-endpoint-overview.md). 

**Czy można określić niestandardowy port dla punktów końcowych danych SQL?**

Nie, ta opcja jest niedostępna.  W przypadku prywatnego punktu końcowego danych wystąpienie zarządzane używa domyślnego numeru portu 1433 i dla publicznego punktu końcowego danych, wystąpienie zarządzane używa domyślnego numeru portu 3342.

**Jaki jest zalecany sposób łączenia wystąpień zarządzanych umieszczonych w różnych regionach?**

Komunikacja równorzędna obwodu trasy Express jest preferowanym sposobem wykonania tej czynności. Globalne wirtualne sieci równorzędne są obsługiwane z ograniczeniami opisanymi w poniższej uwadze.  

> [!IMPORTANT]
> [W dniu 9/22/2020 ogłoszono globalne wirtualne sieci równorzędne dla nowo utworzonych klastrów wirtualnych](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Oznacza to, że globalne wirtualne sieci równorzędne są obsługiwane dla wystąpień zarządzanych SQL utworzonych w pustych podsieciach, a także dla wszystkich kolejnych wystąpień zarządzanych utworzonych w tych podsieciach. W przypadku wszystkich innych obsługi komunikacji równorzędnej usługi SQL Managed Instances jest ograniczone do sieci w tym samym regionie ze względu na [ograniczenia globalnej komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Więcej informacji można znaleźć w sekcji dotyczącej [często zadawanych pytań dotyczących usługi Azure Virtual Networks](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . 

Jeśli Komunikacja równorzędna obwodu usługi Express Route i globalna komunikacja wirtualna sieci wirtualnej nie jest możliwa, jedyną inną opcją jest utworzenie połączenia sieci VPN typu lokacja-lokacja ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [programu PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [interfejsu wiersza polecenia platformy Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Ograniczanie ryzyka związanego z eksfiltracji danych  

**Jak mogę ograniczyć ryzyko związane z eksfiltracji danych?**

Aby zmniejszyć ryzyko związane z eksfiltracji danych, klienci są zalecani do zastosowania zestawu ustawień zabezpieczeń i kontroli:

- Włącz [transparent Data Encryption (TDE)](../database/transparent-data-encryption-tde-overview.md) we wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to również zalecane lokalnie.
- Używaj tylko uwierzytelniania Azure Active Directory (Azure AD).
- Uzyskaj dostęp do wystąpienia przy użyciu konta DBA o niskim poziomie uprawnień.
- Skonfiguruj dostęp JIT serwera przesiadkowego dla konta sysadmin.
- Włącz [inspekcję SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)i Zintegruj ją z mechanizmami alertów.
- Włącz [wykrywanie zagrożeń](../database/threat-detection-configure.md) z poziomu [usługi Azure Defender dla pakietu SQL](../database/azure-defender-for-sql.md) .

## <a name="dns"></a>DNS

**Czy można skonfigurować niestandardowe DNS dla wystąpienia zarządzanego SQL?**

Tak. Zobacz [jak skonfigurować niestandardowy serwer DNS dla wystąpienia zarządzanego Azure SQL](./custom-dns-configure.md).

**Czy można odświeżyć DNS?**

Tak. Zobacz [Ustawienia synchronizacji serwerów DNS sieci wirtualnej w klastrze wirtualnym wystąpienia zarządzanego SQL](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Zmień strefę czasową

**Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?**

Konfigurację strefy czasowej można ustawić, gdy zarządzane wystąpienie jest inicjowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia strefy czasowej](timezones-overview.md#limitations).

Obejścia obejmują tworzenie nowego wystąpienia zarządzanego ze stosowną strefą czasową, a następnie wykonywanie ręcznego tworzenia kopii zapasowej i przywracania, a także to, co zalecamy, wykonywanie [przywracania do punktu w czasie między wystąpieniami](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance).


## <a name="security-and-database-encryption"></a>Zabezpieczenia i szyfrowanie bazy danych

**Czy rola serwera sysadmin jest dostępna dla wystąpienia zarządzanego SQL?**

Tak, klienci mogą tworzyć nazwy logowania należące do roli sysadmin.  Klienci, którzy zakładają uprawnienia administratora systemu, również zakładają odpowiedzialność za działanie tego wystąpienia, co może negatywnie wpłynąć na zobowiązania umowy SLA. Aby dodać logowanie do roli serwera sysadmin, zobacz [uwierzytelnianie w usłudze Azure AD](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Czy Transparent Data Encryption jest obsługiwana dla wystąpienia zarządzanego SQL?**

Tak, Transparent Data Encryption jest obsługiwana w przypadku wystąpienia zarządzanego SQL. Aby uzyskać szczegółowe informacje, zobacz [transparent Data Encryption dla wystąpienia zarządzanego SQL](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Czy mogę wykorzystać model "Przenieś własny klucz" dla TDE?**

Tak, Azure Key Vault scenariusz BYOK jest dostępny dla wystąpienia zarządzanego Azure SQL. Aby uzyskać szczegółowe informacje, zobacz [transparent Data Encryption z kluczem zarządzanym przez klienta](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Czy można przeprowadzić migrację zaszyfrowanej bazy danych SQL Server?**

Tak, możesz. Aby przeprowadzić migrację zaszyfrowanej bazy danych SQL Server, należy wyeksportować i zaimportować istniejące certyfikaty do wystąpienia zarządzanego, a następnie wykonać pełną kopię zapasową bazy danych i przywrócić ją w wystąpieniu zarządzanym. 

Można również użyć [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) do migracji zaszyfrowanych baz danych TDE.

**Jak mogę skonfigurować rotację funkcji ochrony TDE dla wystąpienia zarządzanego SQL?**

Funkcję ochrony TDE można obrócić dla wystąpienia zarządzanego przy użyciu Azure Cloud Shell. Aby uzyskać instrukcje, zobacz [transparent Data Encryption w wystąpieniu zarządzanym SQL przy użyciu własnego klucza z Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Czy można przywrócić zaszyfrowaną bazę danych do wystąpienia zarządzanego SQL?**

Tak, nie musisz odszyfrowywać bazy danych, aby przywrócić ją do wystąpienia zarządzanego SQL. Musisz podać certyfikat/klucz używany jako funkcja ochrony klucza szyfrowania w systemie źródłowym do wystąpienia zarządzanego SQL, aby można było odczytywać dane z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa sposoby, aby to zrobić:

- *Przekaż ochronę certyfikatu do wystąpienia zarządzanego SQL*. Można to zrobić tylko przy użyciu programu PowerShell. [Przykładowy skrypt](./tde-certificate-migrate.md) opisuje cały proces.
- *Przekaż funkcję ochrony klucza asymetrycznego do Azure Key Vault i wskaż do niej wystąpienie zarządzane przez SQL*. Takie podejście przypomina BYOK TDE użycie, który używa integracji Key Vault, która przechowuje klucz szyfrowania. Jeśli nie chcesz używać klucza jako ochrony klucza szyfrowania i po prostu chcesz udostępnić klucz dla wystąpienia zarządzanego SQL w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)i nie zaznaczaj pola wyboru **Oznacz wybrany klucz jako domyślną ochronę TDE**.

Po udostępnieniu funkcji ochrony szyfrowania wystąpieniem zarządzanym SQL można wykonać procedurę standardowego przywracania bazy danych.

## <a name="purchasing-models-and-benefits"></a>Kupowanie modeli i korzyści

**Jakie modele zakupów są dostępne dla wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane SQL oferuje [model zakupu oparty na rdzeń wirtualny](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Jakie korzyści z kosztów są dostępne dla wystąpienia zarządzanego SQL?**

Koszty związane z korzyściami z usługi Azure SQL można zaoszczędzić w następujący sposób:
-   Zmaksymalizuj istniejące inwestycje w licencje lokalne i Zaoszczędź nawet do 55% dzięki [korzyść użycia hybrydowego platformy Azure](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-   Zatwierdź do rezerwacji zasobów obliczeniowych i zaoszczędź do 33% z [korzyścią wystąpienia zarezerwowanego](../database/reserved-capacity-overview.md). Połącz ją z korzyścią użycia hybrydowego platformy Azure, aby zaoszczędzić do 82%. 
-   Oszczędź do 55 procent w porównaniu z cennikiem dzięki [korzyściom z tytułu](https://azure.microsoft.com/pricing/dev-test/) tworzenia i testowania platformy Azure, która oferuje obniżone stawki za bieżące obciążenia związane z tworzeniem i testowaniem.

**Kto jest uprawniony do skorzystania z korzyści z wystąpienia zarezerwowanego?**

Aby można było kwalifikować się do skorzystania z korzyści z wystąpienia zarezerwowanego, typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z płatnością zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Aby uzyskać więcej informacji na temat rezerwacji, zobacz [korzyść wystąpienia zarezerwowanego](../database/reserved-capacity-overview.md). 

**Czy istnieje możliwość anulowania, wymiany lub zwrotu pieniędzy?**

Istnieje możliwość anulowania rezerwacji, wymiany lub zwrotu pieniędzy z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Rozliczanie dla wystąpienia zarządzanego i magazynu kopii zapasowych

**Jakie są opcje cennika wystąpienia zarządzanego SQL?**

Aby poznać opcje cennika wystąpienia zarządzanego, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Jak mogę śledzić koszt rozliczeń dla mojego wystąpienia zarządzanego?**

Można to zrobić przy użyciu [rozwiązania Azure Cost Management](../../cost-management-billing/index.yml). Przejdź do **subskrypcji** w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Analiza kosztów**. 

Użyj opcji **skumulowane koszty** , a następnie Przefiltruj według **typu zasobu** jako `microsoft.sql/managedinstances` .

**Jaki jest koszt zautomatyzowanych kopii zapasowych?**

Ilość wolnego miejsca do magazynowania kopii zapasowej jest naliczana jako zarezerwowane miejsce do magazynowania danych zakupionych, niezależnie od ustawionego okresu przechowywania kopii zapasowych. Jeśli użycie magazynu kopii zapasowych znajduje się w ramach przydziału wolnego miejsca w magazynie kopii zapasowych, automatyczne kopie zapasowe w wystąpieniu zarządzanym nie będą miały żadnych dodatkowych kosztów, dlatego będą bezpłatne. Przekroczenie wykorzystania magazynu kopii zapasowych powyżej ilości wolnego miejsca spowoduje naliczenie kosztów o $0,20 – $0,24 za GB/miesiąc w regionach USA lub wyświetlenie strony z cennikiem w celu uzyskania szczegółowych informacji dotyczących regionu. Aby uzyskać więcej informacji, zobacz Omówienie [użycia magazynu kopii zapasowych](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Jak mogę monitorować koszt rozliczeń za użycie magazynu kopii zapasowych?**

Możesz monitorować koszt magazynu kopii zapasowych za pośrednictwem Azure Portal. Aby uzyskać instrukcje, zobacz [monitorowanie kosztów automatycznych kopii zapasowych](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Jak mogę zoptymalizować koszty magazynu kopii zapasowych w wystąpieniu zarządzanym?**

Aby zoptymalizować koszty magazynu kopii zapasowych, zobacz [precyzyjne dostrajanie kopii zapasowych w wystąpieniu zarządzanym SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Oszczędnościowe przypadki użycia

**Gdzie mogę znaleźć przypadki użycia i uzyskać oszczędności wynikające z wystąpienia zarządzanego SQL?**

Analizy przypadków wystąpienia zarządzanego SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Aby lepiej zrozumieć korzyści, koszty i ryzyko związane z wdrażaniem wystąpienia zarządzanego usługi Azure SQL, istnieje również analiza Forrestera: [Całkowity wpływ na gospodarcze wystąpienia zarządzanego Microsoft Azure SQL Database](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Zasady dotyczące haseł 

**Jakie zasady haseł są stosowane dla nazw logowania SQL wystąpienia zarządzanego SQL?**

Zasady haseł wystąpienia zarządzanego SQL dla nazw logowania SQL dziedziczą zasady platformy Azure, które są stosowane do maszyn wirtualnych tworzących klaster wirtualny przechowujący wystąpienie zarządzane. W tej chwili nie można zmienić któregokolwiek z tych ustawień, ponieważ te ustawienia są zdefiniowane przez platformę Azure i dziedziczone przez wystąpienie zarządzane.

 > [!IMPORTANT]
 > Platforma Azure może zmieniać wymagania dotyczące zasad bez powiadamiania usług korzystających z tych zasad.

**Co to są bieżące zasady platformy Azure?**

Każda nazwa logowania musi ustawić swoje hasło po zalogowaniu i zmienić hasło po osiągnięciu maksymalnego wieku.

| **Zasada** | **Ustawienie zabezpieczeń** |
| --- | --- |
| Maksymalny wiek hasła | 42 dni |
| Minimalny wiek hasła | 1 dzień |
| Minimalna długość hasła | 10 znaków |
| Hasło musi spełniać wymagania dotyczące złożoności | Enabled (Włączony) |

**Czy możliwe jest wyłączenie złożoności hasła i wygaśnięcia w wystąpieniu zarządzanym SQL na poziomie logowania?**

Tak, istnieje możliwość kontrolowania pól CHECK_POLICY i CHECK_EXPIRATION na poziomie logowania. Bieżące ustawienia można sprawdzić, wykonując następujące polecenie T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Następnie można zmodyfikować określone ustawienia logowania, wykonując następujące polecenie:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Zastąp element "test" pożądaną nazwą logowania i Dostosuj zasady i wartości wygaśnięcia)


## <a name="service-updates"></a>Aktualizacje usług

**Co to jest zmiana głównego urzędu certyfikacji dla Azure SQL Database & wystąpienia zarządzanego SQL?**

Zobacz [rotacja certyfikatów dla Azure SQL Database & wystąpienia zarządzanego SQL](../updates/ssl-root-certificate-expiring.md). 

**Co to jest planowane zdarzenie konserwacji dla wystąpienia zarządzanego SQL?**

Zobacz [Planowanie zdarzeń konserwacji platformy Azure w wystąpieniu zarządzanym SQL](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Opinie i pomoc techniczna dla platformy Azure

**Gdzie mogę pozostawić moje pomysły dotyczące ulepszeń wystąpienia zarządzanego SQL?**

Możesz głosować na nową funkcję wystąpienia zarządzanego lub wprowadzić nowy pomysł ulepszania na potrzeby głosowania na [forum opinii o wystąpieniu zarządzanym SQL](https://feedback.azure.com/forums/915676-sql-managed-instance). Dzięki temu można przyczynić się do rozwoju produktu i pomóc nam określić priorytety potencjalnych ulepszeń.

**Jak można utworzyć żądanie pomocy technicznej systemu Azure?**

Aby dowiedzieć się, jak utworzyć żądanie pomocy technicznej platformy Azure, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
