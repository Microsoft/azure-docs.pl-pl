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
ms.openlocfilehash: 16f937286b967aaea8ec6a16e97835b2de5a0331
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765507"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Usługa Azure SQL Managed Instance (często zadawane pytania)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera najczęściej zadawane pytania [dotyczące](sql-managed-instance-paas-overview.md)Azure SQL Managed Instance .

## <a name="supported-features"></a>Obsługiwane funkcje

**Gdzie mogę znaleźć listę funkcji obsługiwanych na SQL Managed Instance?**

Aby uzyskać listę obsługiwanych funkcji w programie SQL Managed Instance, [zobacz Azure SQL Managed Instance funkcji](../database/features-comparison.md).

Aby uzyskać informacje o różnicach składni i zachowania Azure SQL Managed Instance i SQL Server, zobacz Różnice między językami [T-SQL SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Specyfikacja techniczna, limity zasobów i inne ograniczenia
 
**Gdzie można znaleźć charakterystyki techniczne i limity zasobów dla SQL Managed Instance?**

Aby uzyskać informacje o dostępnych charakterystykach generacji sprzętu, [zobacz Różnice techniczne w generacjach sprzętu.](resource-limits.md#hardware-generation-characteristics)
Aby uzyskać informacje o dostępnych warstwach usług i ich cechach, zobacz [Różnice techniczne między warstwami usług](resource-limits.md#service-tier-characteristics).

**Do jakiej warstwy usługi mogę się zakwalifikować?**

Każdy klient jest uprawniony do korzystania z dowolnej warstwy usług. Jeśli jednak chcesz wymienić istniejące licencje na obniżone stawki na platformę Azure SQL Managed Instance przy użyciu usługi [Korzyść użycia hybrydowego platformy Azure,](https://azure.microsoft.com/pricing/hybrid-benefit/)pamiętaj, że klienci wersji SQL Server Enterprise Edition z programem pakiet Software Assurance są uprawnieni do korzystania z warstw wydajności [Ogólnego przeznaczenia](../database/service-tier-general-purpose.md) lub [Krytyczne dla działania firmy, a](../database/service-tier-business-critical.md) klienci wersji SQL Server Standard Edition z programem pakiet Software Assurance są uprawnieni tylko do warstwy wydajności Ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [Określone prawa AHB](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Jakie typy subskrypcji są obsługiwane w SQL Managed Instance?**

Aby uzyskać listę obsługiwanych typów subskrypcji, zobacz [Obsługiwane typy subskrypcji.](resource-limits.md#supported-subscription-types) 

**Które regiony platformy Azure są obsługiwane?**

Wystąpienia zarządzane można tworzyć w większości regionów świadczenia usługi Azure. Zobacz [Obsługiwane regiony dla SQL Managed Instance](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Jeśli potrzebujesz wystąpienia zarządzanego w regionie, który nie jest obecnie obsługiwany, wyślij żądanie pomocy technicznej za [pośrednictwem Azure Portal](../database/quota-increase-request.md).

**Czy istnieją ograniczenia limitu przydziału SQL Managed Instance wdrożeniach?**

Wystąpienie zarządzane ma dwa domyślne limity: limit liczby podsieci, których można użyć, i limit liczby rdzeni wirtualnych, które można aprowizuje. Limity różnią się w zależności od typów subskrypcji i regionów. Aby uzyskać listę regionalnych ograniczeń zasobów według typu subskrypcji, zobacz tabelę [z tematu Ograniczenia zasobów regionalnych](resource-limits.md#regional-resource-limitations). Są to limity programowe, które można zwiększyć na żądanie. Jeśli chcesz aprowizować więcej wystąpień zarządzanych w bieżących regionach, wyślij żądanie pomocy technicznej w celu zwiększenia limitu przydziału przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [Żądanie zwiększenia limitu przydziału dla Azure SQL Database](../database/quota-increase-request.md).

**Czy mogę zwiększyć limit baz danych (100) dla mojego wystąpienia zarządzanego na żądanie?**

Nie, a obecnie nie ma żadnych planów zwiększania liczby baz danych na SQL Managed Instance. 

**Gdzie można przeprowadzić migrację, jeśli mam więcej niż 8 TB danych?**
Możesz rozważyć migrowanie do innych wersji platformy Azure, które odpowiadają Twojeowi obciążeniu: Azure SQL Database [hiperskala](../database/service-tier-hyperscale.md) lub SQL Server [na platformie Azure Virtual Machines.](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

**Gdzie można przeprowadzić migrację, jeśli mam określone wymagania sprzętowe, takie jak większy stosunek pamięci RAM do liczby rdzeni wirtualnych lub większa liczba procesorów CPU?**
Możesz rozważyć migrowanie do usługi [SQL Server na platformie Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) lub Azure SQL Database zoptymalizowane pod kątem pamięci/procesora CPU. [](../database/sql-database-paas-overview.md)

## <a name="known-issues-and-defects"></a>Znane problemy i wady

**Gdzie można znaleźć znane problemy i wady?**

Aby uzyskać informacje o wadach produktów i znanych problemach, zobacz [Znane problemy.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Nowe funkcje

**Gdzie można znaleźć najnowsze funkcje i funkcje w publicznej wersji zapoznawczej?**

Aby uzyskać informacje o nowych funkcjach i wersjach zapoznawczych, zobacz [Informacje o wersji](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Tworzenie, aktualizowanie, usuwanie lub przenoszenie SQL Managed Instance

**Jak aprowizować SQL Managed Instance?**

Wystąpienie można aprowizować z [Azure Portal](instance-create-quickstart.md), [programu PowerShell,](scripts/create-configure-managed-instance-powershell.md)interfejsu [wiersza polecenia](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) platformy Azure i szablonów [usługi ARM.](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)

**Czy mogę aprowizć wystąpienia zarządzane w istniejącej subskrypcji?**

Tak, możesz aprowizować wystąpienie zarządzane w istniejącej subskrypcji, jeśli ta subskrypcja należy do [obsługiwanych typów subskrypcji.](resource-limits.md#supported-subscription-types)

**Dlaczego nie można aprowizuje wystąpienia zarządzanego w podsieci, której nazwa zaczyna się cyfrą?**

Jest to bieżące ograniczenie dotyczące składnika bazowego, które weryfikuje nazwę podsieci względem wyrażenia regularnego ^[a-zA-Z_][^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ]*(?<![ \. \s])$. Obecnie obsługiwane są wszystkie nazwy, które przechodzą wyrażenia regularne i są prawidłowymi nazwami podsieci.

**Jak mogę skalować moje wystąpienie zarządzane?**

Wystąpienie zarządzane można skalować przy użyciu szablonów [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), [PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell) [interfejsu wiersza polecenia](/cli/azure/sql/mi#az_sql_mi_update) platformy Azure [lub usługi ARM.](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)

**Czy mogę przenieść wystąpienie zarządzane z jednego regionu do innego?**

Tak, możesz. Aby uzyskać instrukcje, [zobacz Przenoszenie zasobów między regionami.](../database/move-resources-across-regions.md)

**Jak mogę usunąć moje wystąpienie zarządzane?**

Wystąpienia zarządzane można usuwać za pomocą programu Azure Portal, [programu PowerShell,](/powershell/module/az.sql/remove-azsqlinstance)interfejsu wiersza polecenia platformy [Azure](/cli/azure/sql/mi#az_sql_mi_delete) [Resource Manager interfejsów API REST.](/rest/api/sql/managedinstances/delete)

**Ile czasu trwa tworzenie lub aktualizowanie wystąpienia albo przywracanie bazy danych?**

Oczekiwany czas na utworzenie nowego wystąpienia zarządzanego lub zmianę warstw usług (rdzeni wirtualnych, magazynu) zależy od kilku czynników. Zobacz [Operacje zarządzania](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Konwencje nazewnictwa

**Czy wystąpienie zarządzane może mieć taką samą nazwę jak SQL Server lokalnego?**

Zmiana nazwy wystąpienia zarządzanego nie jest obsługiwana.

**Czy mogę zmienić prefiks strefy DNS?**

Tak, można zmienić domyślną strefę DNS *database.windows.net* DNS wystąpienia zarządzanego. 

Aby użyć innej strefy DNS zamiast domyślnej, na przykład *.contoso.com*: 
- Użyj polecenia CliConfig, aby zdefiniować alias. Narzędzie jest po prostu otoką ustawień rejestru, więc można to zrobić przy użyciu zasad grupy lub skryptu.
- Użyj *rekordu CNAME* z *opcją TrustServerCertificate=true.*

## <a name="migration-options"></a>Opcje migracji

**Jak przeprowadzić migrację z puli Azure SQL Database elastycznej do SQL Managed Instance?**

Wystąpienie zarządzane oferuje te same poziomy wydajności na zasoby obliczeniowe i rozmiar magazynu co inne opcje wdrażania Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub potrzebujesz funkcji obsługiwanej wyłącznie w wystąpieniu zarządzanym, możesz migrować dane przy użyciu funkcji eksportowania/importowania (BACPAC). Poniżej podano inne sposoby, które należy wziąć pod uwagę SQL Database migracji do SQL Managed Instance: 
- Używanie [zewnętrznego źródła danych](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Korzystanie z [narzędzia SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- Korzystanie z [narzędzia BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Jak przeprowadzić migrację bazy danych wystąpienia do jednego Azure SQL Database?**

Jedną z opcji jest [wyeksportowanie bazy danych do pliku BACPAC, a](../database/database-export.md) następnie [zaimportowanie pliku BACPAC.](../database/database-import.md) Jest to zalecane podejście, jeśli rozmiar bazy danych jest mniejszy niż 100 GB.

[Replikacja transakcyjna](replication-two-instances-and-sql-server-configure-tutorial.md) może być używana,  jeśli wszystkie tabele w bazie danych mają klucze podstawowe i w bazie danych nie ma obiektów OLTP w pamięci.

Natywnych COPY_ONLY kopii zapasowych z wystąpienia zarządzanego nie można przywrócić do SQL Server ponieważ wystąpienie zarządzane ma wyższą wersję bazy danych niż SQL Server. Aby uzyskać więcej informacji, zobacz [Kopia zapasowa tylko do kopiowania.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Jak przeprowadzić migrację mojego SQL Server do SQL Managed Instance?**

Aby przeprowadzić migrację SQL Server, zobacz [SQL Server migracji wystąpienia do Azure SQL Managed Instance](migrate-to-instance-from-sql-server.md).

**Jak przeprowadzić migrację z innych platform do SQL Managed Instance?**

Aby uzyskać informacje o migracji z innych platform, zobacz [Przewodnik po migracji bazy danych platformy Azure.](https://datamigration.microsoft.com/)

## <a name="switch-hardware-generation"></a>Przełączanie generacji sprzętu 

**Czy mogę przełączyć generację sprzętu wystąpienia zarządzanego między 4. i 5. generacji w trybie online?**

Automatyczne przełączanie w trybie online z wersji Gen4 do Gen5 jest możliwe, jeśli sprzęt 5. generacji jest dostępny w regionie, w którym aprowizowane jest wystąpienie zarządzane. W takim przypadku możesz sprawdzić stronę przeglądu modelu [z rdzeniami](../database/service-tiers-vcore.md) wirtualnych, na których wyjaśniono, jak przełączać się między generacjami sprzętu.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane będzie aprowizowane w tle, a bazy danych będą automatycznie przenoszone między starym i nowym wystąpieniem z szybkim trybem failover na końcu procesu.

Uwaga: sprzęt 4. generacji jest wywłaszowyty i nie jest już dostępny dla nowych wdrożeń. Wszystkie nowe bazy danych muszą zostać wdrożone na sprzęcie Gen5. Przełączanie z wersji Gen5 do Gen4 również nie jest dostępne.

## <a name="performance"></a>Wydajność 

**Jak porównać wydajność wystąpienia zarządzanego z SQL Server wydajnością?**

Aby uzyskać porównanie wydajności między wystąpieniem zarządzanym i SQL Server, dobrym punktem wyjścia jest najlepsze rozwiązania dotyczące porównania wydajności między wystąpieniem zarządzanym Azure SQL a SQL Server [artykułu.](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210)

**Co powoduje różnice w wydajności między wystąpieniem zarządzanym a SQL Server?**

Zobacz [Kluczowe przyczyny różnic w wydajności między wystąpieniem zarządzanym SQL i SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). Aby uzyskać więcej informacji na temat wpływu rozmiaru pliku dziennika na wydajność Ogólnego przeznaczenia zarządzanego, zobacz Wpływ rozmiaru pliku dziennika [na Ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Jak mogę dostrajanie wydajności mojego wystąpienia zarządzanego?**

Wydajność wystąpienia zarządzanego można zoptymalizować przez:
- [Automatyczne dostrajanie,](../database/automatic-tuning-overview.md) które zapewnia maksymalną wydajność i stabilność obciążeń dzięki ciągłemu dostrajania wydajności w oparciu o AI i uczenie maszynowe.
-    [Przetwarzanie OLTP w pamięci,](../in-memory-oltp-overview.md) które poprawia przepływność i opóźnienia obciążeń przetwarzania transakcyjnego i zapewnia szybsze szczegółowe informacje biznesowe. 

Aby jeszcze bardziej dostosować wydajność, rozważ  zastosowanie niektórych najlepszych rozwiązań dostrajania aplikacji [i bazy danych.](../database/performance-guidance.md#tune-your-database)
Jeśli obciążenie składa się z wielu małych transakcji, rozważ przełączenie typu połączenia z serwera [proxy](connection-types-overview.md#changing-connection-type) na tryb przekierowania, aby uzyskać mniejsze opóźnienia i wyższą przepływność.

## <a name="monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty

**Jakie są opcje monitorowania i alertów dla mojego wystąpienia zarządzanego?**

Aby uzyskać informacje o wszystkich możliwych opcjach monitorowania zużycia SQL Managed Instance wydajności, zobacz wpis w blogu Azure SQL Managed Instance [opcje monitorowania.](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416) Aby uzyskać informacje na temat monitorowania wydajności wystąpienia zarządzanego SQL w czasie rzeczywistym, zobacz Monitorowanie wydajności w czasie rzeczywistym [dla wystąpienia zarządzanego Azure SQL DB.](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)

**Czy mogę używać narzędzia SQL Profiler do śledzenia wydajności?**

Tak, program SQL Profiler jest obsługiwany lub SQL Managed Instance. Aby uzyskać więcej informacji, zobacz [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Czy Database Advisor i Szczegółowe informacje o wydajności zapytań baz danych wystąpienia zarządzanego?**

Nie, nie są obsługiwane. Do monitorowania baz danych można [używać](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) widoków [DMV](../database/monitoring-with-dmvs.md) i magazynu zapytań wraz z [narzędziami SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) i [XEvents.](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15)

**Czy można tworzyć alerty dotyczące metryk SQL Managed Instance?**

Tak. Aby uzyskać instrukcje, [zobacz Tworzenie alertów dla SQL Managed Instance](alerts-create.md).

**Czy mogę tworzyć alerty dotyczące metryk w bazie danych w wystąpieniu zarządzanym?**

Nie można, metryki alertów są dostępne tylko dla wystąpienia zarządzanego. Metryki alertów dla poszczególnych baz danych w wystąpieniu zarządzanym nie są dostępne.

## <a name="storage-size"></a>Rozmiar magazynu

**Jaki jest maksymalny rozmiar magazynu dla SQL Managed Instance?**

Rozmiar magazynu dla SQL Managed Instance zależy od wybranej warstwy usługi (Ogólnego przeznaczenia lub Krytyczne dla działania firmy). Aby uzyskać informacje o ograniczeniach magazynu dla tych warstw usług, zobacz [Charakterystyka warstw usług](../database/service-tiers-general-purpose-business-critical.md).

**Jaki jest minimalny rozmiar magazynu dostępny dla wystąpienia zarządzanego?**

Minimalna ilość miejsca dostępnego w wystąpieniu to 32 GB. Magazyn można dodawać w przyrostach od 32 GB do maksymalnego rozmiaru magazynu. Pierwsze 32 GB jest bezpłatne.

**Czy mogę zwiększyć miejsce do magazynowania przypisane do wystąpienia niezależnie od zasobów obliczeniowych?**

Tak, do pewnego stopnia możesz kupić magazyn dodatków, niezależnie od zasobów obliczeniowych. Zobacz *Max instance reserved storage (Maksymalna* liczba wystąpień zarezerwowanych magazynu) w [tabeli](resource-limits.md#hardware-generation-characteristics).

**Jak mogę zoptymalizować wydajność magazynu w Ogólnego przeznaczenia usługi?**

Aby zoptymalizować wydajność magazynu, zobacz [Najlepsze rozwiązania dotyczące magazynu w Ogólnego przeznaczenia](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525).

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

**Czy magazyn kopii zapasowych jest odejmowany od magazynu wystąpienia zarządzanego?**

Nie, magazyn kopii zapasowych nie jest odejmowany od miejsca w magazynie wystąpienia zarządzanego. Magazyn kopii zapasowych jest niezależny od przestrzeni dyskowej wystąpienia i nie ma ograniczonego rozmiaru. Magazyn kopii zapasowych jest ograniczony przez okres przechowywania kopii zapasowych baz danych wystąpienia, który można skonfigurować do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [Automatyczne kopie zapasowe.](../database/automated-backups-overview.md)

**Jak sprawdzić, kiedy są automatycznie tworzyć kopie zapasowe w wystąpieniu zarządzanym?**

Aby śledzić, kiedy automatyczne kopie zapasowe były wykonywane w wystąpieniu zarządzanym, zobacz Jak śledzić automatyczne kopie zapasowe dla [Azure SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355).

**Czy jest obsługiwana kopia zapasowa na żądanie?**

Tak, możesz utworzyć pełną kopię zapasową tylko do kopiowania w Azure Blob Storage, ale będzie można ją przywrócić tylko w wystąpieniu zarządzanym. Aby uzyskać szczegółowe informacje, zobacz [Kopia zapasowa tylko do kopiowania.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) Jednak tworzenie kopii zapasowej tylko do kopiowania jest niemożliwe, jeśli baza danych jest szyfrowana za pomocą funkcji TDE zarządzanej przez usługę, ponieważ certyfikat używany do szyfrowania jest niedostępny. W takim przypadku użyj funkcji przywracania do punktu w czasie, aby przenieść bazę danych do innej SQL Managed Instance lub przełączyć się na klucz zarządzany przez klienta.

**Czy przywracanie natywne (z plików bak) do wystąpienia zarządzanego jest obsługiwane?**

Tak, jest ona obsługiwana i dostępna SQL Server wersji 2005+.  Aby użyć przywracania natywnego, przekaż plik bak do usługi Azure Blob Storage i wykonaj polecenia języka T-SQL. Aby uzyskać więcej informacji, zobacz [Natywne przywracanie z adresu URL](./migrate-to-instance-from-sql-server.md#native-restore-from-url).

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

**Czy moje systemowe bazy danych są replikowane do wystąpienia pomocniczego w grupie trybu failover?**

Systemowe bazy danych nie są replikowane do wystąpienia pomocniczego w grupie trybu failover. W związku z tym scenariusze zależne od obiektów z systemowych baz danych będą niemożliwe w wystąpieniu pomocniczym, chyba że obiekty zostaną utworzone ręcznie w pomocniczym wystąpieniu. Aby obejść ten problem, zobacz [Włączanie scenariuszy zależnych od obiektu z systemowych baz danych](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Wymagania dotyczące sieci 

**Jakie są bieżące ograniczenia sieciowej organizacji danych dla ruchu przychodzącego/wychodzącego w podsieci wystąpienia zarządzanego?**

Wymagane reguły sieciowej i UDR są udokumentowane tutaj [i](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)automatycznie ustawiane przez usługę.
Należy pamiętać, że te reguły są tylko tymi, których potrzebujemy do obsługi usługi. Aby nawiązać połączenie z wystąpieniem zarządzanym i korzystać z różnych funkcji, należy ustawić dodatkowe, specyficzne dla funkcji reguły, które należy zachować.

**Jak można ustawić reguły sieciowej grupy danych dla ruchu przychodzącego na portach zarządzania?**

SQL Managed Instance odpowiada za ustawianie reguł na portach zarządzania. Można to osiągnąć za pomocą funkcji o nazwie konfiguracja podsieci [wspomaganej przez usługę](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Ma to na celu zapewnienie nieprzerwanego przepływu ruchu zarządzania w celu spełnienia warunków umowy SLA.

**Czy mogę uzyskać źródłowe zakresy adresów IP, które są używane dla ruchu przychodzącego zarządzania?**

Tak. Możesz analizować ruch przez grupę zabezpieczeń sieci, [konfigurując dzienniki Network Watcher przepływu.](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)

**Czy mogę ustawić sieciowa nr 5, aby kontrolować dostęp do punktu końcowego danych (port 1433)?**

Tak. Po aprowieniu wystąpienia zarządzanego można ustawić sieciowej 5.0, która kontroluje dostęp przychodzący do portu 1433. Zaleca się zawężenie zakresu adresów IP tak, jak to możliwe.

**Czy można ustawić urządzenie WUS lub zaporę lokalną, aby filtrować ruch zarządzania ruchem wychodzącym na podstawie sieci FQDN?**

Nie. Nie jest to obsługiwane z kilku powodów:
-    Ruch routingu, który reprezentuje odpowiedź na przychodzące żądanie zarządzania, będzie asymetryczny i nie będzie działać.
-    Ograniczenia przepływności i opóźnienie wpływają na kierowanie ruchu do magazynu, dlatego w ten sposób nie będziemy mogli zapewnić oczekiwanej jakości i dostępności usługi.
-    W zależności od doświadczenia te konfiguracje są podatne na błędy i nie obsługują obsługi.

**Czy można ustawić urządzenie WUS lub zaporę dla wychodzącego ruchu niepowiązanego z zarządzaniem?**

Tak. Najprostszym sposobem osiągnięcia tego celu jest dodanie reguły 0/0 do trasy UDR skojarzonej z podsiecią wystąpienia zarządzanego w celu rozsyłania ruchu przez urządzenie WUS.
 
**Ile adresów IP jest potrzebnych dla wystąpienia zarządzanego?**

Podsieć musi mieć wystarczającą liczbę [dostępnych adresów IP.](connectivity-architecture-overview.md#network-requirements) Aby określić rozmiar podsieci sieci wirtualnej dla SQL Managed Instance, zobacz Określanie wymaganego rozmiaru podsieci i [zakresu dla wystąpienia zarządzanego](./vnet-subnet-determine-size.md). 

**Co zrobić, jeśli nie ma wystarczającej ilości adresów IP do wykonania operacji aktualizacji wystąpienia?**

Jeśli w podsieci, w której aprowizowane jest wystąpienie zarządzane, nie ma wystarczającej ilości adresów [IP,](connectivity-architecture-overview.md#network-requirements) należy utworzyć nową podsieć i nowe wystąpienie zarządzane w tej podsieci. Zalecamy również utworzenie nowej podsieci przy użyciu większej liczby przydzielonych adresów IP, aby uniknąć takich sytuacji w przyszłych operacjach aktualizowania. Po aproweniu nowego wystąpienia można ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub wykonać przywracanie do punktu w czasie między [wystąpieniami.](point-in-time-restore.md?tabs=azure-powershell)

**Czy do utworzenia wystąpienia zarządzanego potrzebna jest pusta podsieć?**

Nie. Możesz użyć pustej podsieci lub podsieci, która już zawiera wystąpienia zarządzane. 

**Czy mogę zmienić zakres adresów podsieci?**

Nie, jeśli wewnątrz znajdują się wystąpienia zarządzane. Jest to ograniczenie infrastruktury sieci platformy Azure. Możesz tylko dodać [dodatkową przestrzeń adresową do pustej podsieci](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Czy mogę przenieść wystąpienie zarządzane do innej podsieci?**

Nie. Jest to bieżące ograniczenie projektowe wystąpienia zarządzanego. Można jednak aprowizować nowe wystąpienie w innej podsieci oraz ręcznie tworzyć kopię zapasową i przywracać dane między starym i nowym wystąpieniem lub wykonać przywracanie do punktu w czasie między [wystąpieniami.](point-in-time-restore.md?tabs=azure-powershell)

**Czy do utworzenia wystąpienia zarządzanego potrzebna jest pusta sieć wirtualna?**

Nie jest to wymagane. Możesz utworzyć [sieć wirtualną dla usługi Azure SQL Managed Instance](./virtual-network-subnet-create-arm-template.md) lub skonfigurować [istniejącą sieć wirtualną](./vnet-existing-add-subnet.md)dla Azure SQL Managed Instance .

**Czy mogę umieścić wystąpienie zarządzane z innymi usługami w podsieci?**

Nie. Obecnie nie obsługujemy umieszczania wystąpienia zarządzanego w podsieci, która zawiera już inne typy zasobów.

## <a name="connectivity"></a>Łączność 

**Czy mogę nawiązać połączenie z wystąpieniem zarządzanym przy użyciu adresu IP?**

Nie, nie jest to obsługiwane. Nazwa hosta wystąpienia zarządzanego jest mapowana na usługę równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego. Ponieważ jeden klaster wirtualny może hostować wiele wystąpień zarządzanych, połączenie nie może być kierowane do odpowiedniego wystąpienia zarządzanego bez określenia jego nazwy.
Aby uzyskać więcej informacji na temat SQL Managed Instance architektury klastra wirtualnego, zobacz [Architektura łączności klastra wirtualnego](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Czy moje wystąpienie zarządzane może mieć statyczny adres IP?**

Nie jest to obecnie obsługiwane.

W rzadkich, ale niezbędnych sytuacjach może być konieczne migracja wystąpienia zarządzanego w trybie online do nowego klastra wirtualnego. W razie potrzeby ta migracja wynika ze zmian w stosie technologii mających na celu zwiększenie bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP, który jest mapowany na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie obsługuje statycznych adresów IP i zastrzega sobie prawo do jej zmiany bez powiadomienia w ramach regularnych cykli konserwacji.

Z tego powodu zdecydowanie odradzamy poleganie na niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

**Czy wystąpienie zarządzane ma publiczny punkt końcowy?**

Tak. Wystąpienie zarządzane ma publiczny punkt końcowy, który jest domyślnie używany tylko w usłudze Service Management, ale klient może również włączyć go na potrzeby dostępu do danych. Aby uzyskać więcej informacji, zobacz Use SQL Managed Instance with public endpoints (Używanie usługi [SQL Managed Instance z publicznymi punktami końcowymi).](./public-endpoint-overview.md) Aby skonfigurować publiczny punkt końcowy, przejdź do [konfigurowania publicznego punktu końcowego w SQL Managed Instance](public-endpoint-configure.md).

**Jak wystąpienie zarządzane kontroluje dostęp do publicznego punktu końcowego?**

Wystąpienie zarządzane kontroluje dostęp do publicznego punktu końcowego zarówno na poziomie sieci, jak i aplikacji.

Usługi zarządzania i wdrażania łączą się z wystąpieniem zarządzanym przy użyciu punktu [końcowego zarządzania,](./connectivity-architecture-overview.md#management-endpoint) który jest mapowany na zewnętrzny usługę równoważenia obciążenia. Ruch jest przekierowyny do węzłów tylko wtedy, gdy jest odbierany na wstępnie zdefiniowanym zestawie portów, z których korzystają tylko składniki zarządzania wystąpienia zarządzanego. Wbudowana zapora w węzłach jest ustawiana w celu zezwalania na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnie uwierzytelniają całą komunikację między składnikami zarządzania a płaszczyzną zarządzania. Aby uzyskać więcej informacji, zobacz [Connectivity architecture for SQL Managed Instance](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)(Architektura łączności dla SQL Managed Instance ).

**Czy mogę użyć publicznego punktu końcowego, aby uzyskać dostęp do danych w bazach danych wystąpienia zarządzanego?**

Tak. Klient będzie musiał włączyć dostęp do danych publicznego punktu końcowego z programu [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShell/ARM i skonfigurować sieciową grupy zarządzania w celu zablokowania dostępu do portu danych  /  [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) (numer portu 3342). Aby uzyskać więcej informacji, zobacz Configure public endpoint in Azure SQL Managed Instance and Use Azure SQL Managed Instance securely with public endpoint (Konfigurowanie publicznego punktu końcowego w [programie Azure SQL Managed Instance)](public-endpoint-configure.md) i [Use Azure SQL Managed Instance securely with public endpoint (Bezpieczne](public-endpoint-overview.md)używanie Azure SQL Managed Instance z publicznym punktem końcowym). 

**Czy mogę określić port niestandardowy dla punktów końcowych danych SQL?**

Nie, ta opcja nie jest dostępna.  W przypadku prywatnego punktu końcowego danych wystąpienie zarządzane używa domyślnego numeru portu 1433, a dla publicznego punktu końcowego danych wystąpienie zarządzane używa domyślnego numeru portu 3342.

**Jaki jest zalecany sposób łączenia wystąpień zarządzanych umieszczonych w różnych regionach?**

Preferowanym sposobem na to jest komunikacja równorzędna obwodu express Route. Globalna komunikacja równorzędna sieci wirtualnych jest obsługiwana z ograniczeniem opisanym w poniższej uwagach.  

> [!IMPORTANT]
> [W dniu 22.09.2020 ogłoszono globalną komunikację równorzędna sieci wirtualnych dla nowo utworzonych klastrów wirtualnych](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Oznacza to, że globalna komunikacja równorzędna sieci wirtualnych jest obsługiwana dla wystąpień zarządzanych SQL utworzonych w pustych podsieciach, a także dla wszystkich kolejnych wystąpień zarządzanych utworzonych w tych podsieciach. W przypadku wszystkich innych usług SQL Managed Instances obsługa komunikacji równorzędnej jest ograniczona do sieci w tym samym regionie ze względu na ograniczenia globalnej komunikacji [równorzędnej sieci wirtualnych.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Zobacz również odpowiednią sekcję artykułu [Azure Virtual Networks frequently asked questions (Często zadawane](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) pytania dotyczące sieci wirtualnych platformy Azure), aby uzyskać więcej szczegółów. 

Jeśli komunikacja równorzędna obwodu usługi Express Route i globalna komunikacja równorzędna sieci wirtualnych nie są możliwe, jedyną inną opcją jest utworzenie połączenia sieci VPN typu lokacja-lokacja ([Azure Portal,](../../vpn-gateway/tutorial-site-to-site-portal.md) [PowerShell,](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)interfejs wiersza polecenia [platformy Azure).](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="mitigate-data-exfiltration-risks"></a>Ograniczanie ryzyka związanego z eksfiltracją danych  

**Jak ograniczyć ryzyko związane z eksfiltracją danych?**

Aby ograniczyć ryzyko związane z eksfiltracją danych, klientom zaleca się zastosowanie zestawu ustawień zabezpieczeń i kontroli:

- Włącz Transparent Data Encryption [(TDE) we](../database/transparent-data-encryption-tde-overview.md) wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to zalecane również w środowisku lokalnym.
- Używaj Azure Active Directory (Azure AD).
- Uzyskaj dostęp do wystąpienia przy użyciu konta administratora bazy danych z niskimi uprawnieniami.
- Skonfiguruj dostęp JIT dla konta administratora systemu.
- Włącz [inspekcję SQL i](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)zintegruj ją z mechanizmami alertów.
- Włącz wykrywanie [zagrożeń z](../database/threat-detection-configure.md) Azure Defender [pakietu SQL.](../database/azure-defender-for-sql.md)

## <a name="dns"></a>DNS

**Czy można skonfigurować niestandardowy serwer DNS dla SQL Managed Instance?**

Tak. Zobacz [How to configure a Custom DNS for Azure SQL Managed Instance](./custom-dns-configure.md).

**Czy mogę odświeżyć system DNS?**

Tak. Zobacz Synchronize virtual network DNS servers setting on SQL Managed Instance virtual cluster (Synchronizowanie ustawień serwerów DNS sieci [wirtualnej SQL Managed Instance klastrze wirtualnym).](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)

## <a name="change-time-zone"></a>Zmiana strefy czasowej

**Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?**

Konfigurację strefy czasowej można ustawić podczas aprowizowania wystąpienia zarządzanego po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [Time zone limitations (Ograniczenia strefy czasowej).](timezones-overview.md#limitations)

Obejścia obejmują tworzenie nowego wystąpienia zarządzanego z odpowiednią strefą czasową, a następnie ręczne wykonywanie kopii zapasowej i przywracanie lub zalecane wykonanie przywracania do punktu w czasie między [wystąpieniami.](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)


## <a name="security-and-database-encryption"></a>Zabezpieczenia i szyfrowanie bazy danych

**Czy rola serwera sysadmin jest dostępna dla SQL Managed Instance?**

Tak, klienci mogą tworzyć identyfikatory logowania, które są członkami roli sysadmin.  Klienci, którzy przejmują uprawnienie administratora systemu, są również odpowiedzialni za obsługa wystąpienia, co może mieć negatywny wpływ na zobowiązanie dotyczące umowy SLA. Aby dodać nazwę logowania do roli serwera sysadmin, zobacz [Uwierzytelnianie usługi Azure AD.](./aad-security-configure-tutorial.md#azure-ad-authentication)

**Czy Transparent Data Encryption dla SQL Managed Instance?**

Tak, Transparent Data Encryption są obsługiwane w SQL Managed Instance. Aby uzyskać szczegółowe informacje, [zobacz Transparent Data Encryption for SQL Managed Instance](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Czy mogę korzystać z modelu "bring your own key" dla TDE?**

Tak, Azure Key Vault scenariusz BYOK jest dostępny dla Azure SQL Managed Instance. Aby uzyskać szczegółowe informacje, [zobacz Transparent Data Encryption z kluczem zarządzanym przez klienta.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

**Czy można migrować zaszyfrowaną SQL Server bazy danych?**

Tak, możesz. Aby przeprowadzić migrację zaszyfrowanej SQL Server bazy danych, należy wyeksportować i zaimportować istniejące certyfikaty do wystąpienia zarządzanego, a następnie wykonać pełną kopię zapasową bazy danych i przywrócić ją w wystąpieniu zarządzanym. 

Możesz również użyć funkcji [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) do migrowania baz danych zaszyfrowanych przy użyciu funkcji TDE.

**Jak skonfigurować rotację funkcji ochrony TDE dla SQL Managed Instance?**

Można obracać funkcji ochrony TDE dla wystąpienia zarządzanego przy użyciu Azure Cloud Shell. Aby uzyskać instrukcje, [Transparent Data Encryption w SQL Managed Instance użyciu własnego klucza z Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Czy mogę przywrócić zaszyfrowaną bazę danych do SQL Managed Instance?**

Tak, nie musisz odszyfrowywać bazy danych, aby przywrócić ją do SQL Managed Instance. Musisz podać certyfikat/klucz używany jako ochrona klucza szyfrowania w systemie źródłowym, aby móc SQL Managed Instance odczytać dane z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa możliwe sposoby, aby to zrobić:

- *Przekaż ochrony certyfikatu do SQL Managed Instance*. Można to zrobić tylko przy użyciu programu PowerShell. Przykładowy [skrypt](./tde-certificate-migrate.md) opisuje cały proces.
- *Przekaż asymetryczną ochrony klucza do Azure Key Vault i wskaż SQL Managed Instance do niego*. Takie podejście przypomina przypadek użycia funkcji TDE "bring your own-key" (BYOK), który również używa integracji Key Vault do przechowywania klucza szyfrowania. Jeśli nie chcesz używać klucza jako ochrony klucza szyfrowania i po prostu chcesz udostępnić klucz dla programu SQL Managed Instance w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami dotyczącymi konfigurowania funkcji TDE funkcji [BYOK](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)i nie zaznacz pola wyboru Przywróć wybranego klucza domyślną funkcją ochrony **TDE.**

Po udostępniniu ochrony szyfrowania dla SQL Managed Instance można przejść do standardowej procedury przywracania bazy danych.

## <a name="purchasing-models-and-benefits"></a>Modele zakupów i korzyści

**Jakie modele zakupów są dostępne dla SQL Managed Instance?**

SQL Managed Instance oferuje [model zakupów oparty na rdzeńach wirtualnych.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Jakie koszty są dostępne dla SQL Managed Instance?**

Możesz zaoszczędzić koszty dzięki korzyściom Azure SQL na następujące sposoby:
-    Maksymalizuj istniejące inwestycje w licencje lokalne i oszczędzaj do 55% dzięki [Korzyść użycia hybrydowego platformy Azure.](../azure-hybrid-benefit.md?tabs=azure-powershell) 
-    Zatowadź rezerwację zasobów obliczeniowych i zaoszczędź do 33% dzięki [korzyści wystąpienia zarezerwowanego.](../database/reserved-capacity-overview.md) Połącz to z korzyścią dla hybrydowej platformy Azure, aby uzyskać oszczędności do 82 procent. 
-    Zaoszczędź do 55% w porównaniu z cenami list dzięki korzyści z cennika usługi [Azure Dev/Test,](https://azure.microsoft.com/pricing/dev-test/) która oferuje obniżone stawki dla bieżących obciążeń programistyczych i testowych.

**Kto kwalifikuje się do skorzystania z korzyści wystąpienia zarezerwowanego?**

Aby kwalifikować się do korzyści wystąpienia zarezerwowanego, typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualną umową z cennikiem płatności zgodnie z potrzebami (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Aby uzyskać więcej informacji na temat rezerwacji, zobacz [Korzyść wystąpienia zarezerwowanego](../database/reserved-capacity-overview.md). 

**Czy można anulować, wymienić lub zwrócić rezerwacje?**

Rezerwacje można anulować, wymienić lub uzyskać zwrot kosztów z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Rozliczenia dla wystąpienia zarządzanego i magazynu kopii zapasowych

**Jakie są SQL Managed Instance cen?**

Aby poznać opcje cenowe wystąpienia zarządzanego, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Jak mogę śledzić koszt rozliczeń dla mojego wystąpienia zarządzanego?**

Można to zrobić przy użyciu [Azure Cost Management rozwiązania](../../cost-management-billing/index.yml). Przejdź do **opcji Subskrypcje** w [Azure Portal](https://portal.azure.com) a następnie wybierz **pozycję Analiza kosztów.** 

Użyj opcji **Skumulowane koszty,** a następnie przefiltruj według **typu zasobu** jako `microsoft.sql/managedinstances` .

**Ile kosztują automatyczne kopie zapasowe?**

Możesz uzyskać tyle samo wolnego miejsca do magazynowania kopii zapasowych, ile zarezerwowano zakupione miejsce do magazynowania danych, niezależnie od ustawionego okresu przechowywania kopii zapasowych. Jeśli magazyn kopii zapasowych znajduje się w przydzielonym bezpłatnym miejscu do magazynowania kopii zapasowych, automatyczne kopie zapasowe w wystąpieniu zarządzanym nie będą kosztować żadnych dodatkowych kosztów, dlatego będą bezpłatne. Przekroczenie użycia magazynu kopii zapasowej powyżej wolnego miejsca spowoduje koszty w wysokości około 0,20 –0,24 USD/miesiąc w regionach STANÓW Zjednoczonych. Szczegóły dotyczące regionu można też znaleźć na stronie cennika. Aby uzyskać więcej informacji, zobacz [Backup storage consumption explained (Objaśniono zużycie magazynu kopii zapasowych).](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Jak mogę monitorować koszt rozliczeń za zużycie magazynu kopii zapasowych?**

Koszt magazynu kopii zapasowych można monitorować za pośrednictwem Azure Portal. Aby uzyskać instrukcje, [zobacz Monitorowanie kosztów automatycznych kopii zapasowych.](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs) 

**Jak mogę zoptymalizować koszty magazynu kopii zapasowych w wystąpieniu zarządzanym?**

Aby zoptymalizować koszty magazynu kopii zapasowych, zobacz [Dostrajanie kopii zapasowych w SQL Managed Instance](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="cost-saving-use-cases"></a>Przypadki użycia oszczędnia kosztów

**Gdzie można znaleźć przypadki użycia i wynikające z tego oszczędności dzięki SQL Managed Instance?**

SQL Managed Instance przypadków:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Aby lepiej zrozumieć korzyści, koszty i ryzyka związane z wdrażaniem usługi Azure SQL Managed Instance, istnieje również badanie firmy Forrester: [The Total Economic Impact of Microsoft Azure SQL Database Managed Instance](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)(Całkowity wpływ ekonomiczny wystąpienia zarządzanego Microsoft Azure SQL Database).

## <a name="password-policy"></a>Zasady haseł 

**Jakie zasady haseł są stosowane w przypadku SQL Managed Instance logowania SQL?**

SQL Managed Instance haseł dla identyfikatorów logowania SQL dziedziczą zasady platformy Azure stosowane do maszyn wirtualnych tworzących klaster wirtualny, w którym jest zarządzane wystąpienie. W tej chwili nie można zmienić żadnego z tych ustawień, ponieważ te ustawienia są definiowane przez platformę Azure i dziedziczone przez wystąpienie zarządzane.

 > [!IMPORTANT]
 > Platforma Azure może zmieniać wymagania dotyczące zasad bez powiadamiania usług, które korzystają z tych zasad.

**Co to są bieżące zasady platformy Azure?**

Każde logowanie musi ustawić swoje hasło podczas logowania i zmienić hasło po osiągnięciu maksymalnego wieku.

| **Zasada** | **Ustawienie zabezpieczeń** |
| --- | --- |
| Maksymalny wiek hasła | 42 dni |
| Minimalny wiek hasła | 1 dzień |
| Minimalna długość hasła | 10 znaków |
| Hasło musi spełniać wymagania dotyczące złożoności | Enabled (Włączony) |

**Czy można wyłączyć złożoność i wygaśnięcie hasła w SQL Managed Instance na poziomie logowania?**

Tak, można kontrolować pola CHECK_POLICY i CHECK_EXPIRATION na poziomie logowania. Bieżące ustawienia można sprawdzić, wykonując następujące polecenie języka T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Następnie można zmodyfikować określone ustawienia logowania, wykonując :

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(zastąp wartość "test" żądaną nazwą logowania i dostosuj wartości zasad i wygaśnięcia)


## <a name="service-updates"></a>Aktualizacje usług

**Jaka jest zmiana głównego urzędu certyfikacji dla Azure SQL Database & SQL Managed Instance?**

Zobacz [Rotacja certyfikatów dla Azure SQL Database & SQL Managed Instance](../updates/ssl-root-certificate-expiring.md). 

**Co to jest zdarzenie planowanej konserwacji dla SQL Managed Instance?**

Zobacz [Planowanie zdarzeń konserwacji platformy Azure w SQL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Opinie i pomoc techniczna platformy Azure

**Gdzie mogę pozostawić moje pomysły na SQL Managed Instance ulepszenia?**

Możesz zagłosować na nową funkcję wystąpienia zarządzanego lub wprowadzić nowy pomysł na ulepszenie podczas głosowania SQL Managed Instance [Forum opinii.](https://feedback.azure.com/forums/915676-sql-managed-instance) W ten sposób możesz przyczynić się do rozwoju produktu i pomóc nam ustalić priorytety naszych potencjalnych ulepszeń.

**Jak utworzyć wniosek o pomoc techniczną platformy Azure?**

Aby dowiedzieć się, jak utworzyć wniosek o pomoc techniczną platformy Azure, zobacz [Jak utworzyć żądanie pomocy technicznej platformy Azure.](../../azure-portal/supportability/how-to-create-azure-support-request.md)
