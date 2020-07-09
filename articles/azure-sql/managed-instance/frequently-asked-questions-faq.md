---
title: Często zadawane pytania
titleSuffix: Azure SQL Managed Instance
description: Często zadawane pytania dotyczące wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 88f92117dc07fc241ca714851956e386cd10d617
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135029"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera najbardziej typowe pytania dotyczące [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Obsługiwane funkcje

**Gdzie mogę znaleźć listę funkcji obsługiwanych w wystąpieniu zarządzanym SQL?**

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym SQL, zobacz [funkcje wystąpienia zarządzanego usługi Azure SQL](../database/features-comparison.md).

Różnice dotyczące składni i zachowań między wystąpieniem zarządzanym i SQL Server usługi Azure SQL można znaleźć w temacie [różnice w języku T-SQL z SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Specyfikacja techniczna & limity zasobów
 
**Gdzie można znaleźć cechy techniczne i limity zasobów dla wystąpienia zarządzanego SQL?**

Aby uzyskać dostęp do charakterystyki generowania sprzętu, zobacz [różnice techniczne w generacjach sprzętowych](resource-limits.md#hardware-generation-characteristics).
Aby uzyskać dostęp do dostępnych warstw usług i ich cech, zobacz [różnice techniczne między warstwami usług](resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Znane problemy & usterki

**Gdzie mogę znaleźć znane problemy i usterki?**

Aby zapoznać się z usterkami i znanymi problemami, zobacz [znane problemy](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nowe funkcje

**Gdzie mogę znaleźć najnowsze funkcje i funkcje dostępne w publicznej wersji zapoznawczej?**

Aby zapoznać się z nowymi funkcjami i wersjami zapoznawczymi, zobacz [Informacje o wersji](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Czasy wdrożenia 

**Ile czasu zajmuje utworzenie lub zaktualizowanie wystąpienia lub przywrócenie bazy danych?**

Oczekiwany czas utworzenia wystąpienia zarządzanego lub zmiany warstwy usług (rdzeni wirtualnych, Storage) zależy od kilku czynników. Zapoznaj się z [operacjami zarządzania](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Konwencje nazewnictwa

**Czy wystąpienie zarządzane może mieć taką samą nazwę jak SQL Server wystąpienie lokalne?**

Zmiana nazwy wystąpienia zarządzanego nie jest obsługiwana.

Można zmienić domyślną strefę DNS *. Database.Windows.NET* dla wystąpienia zarządzanego. 

Aby użyć innej strefy DNS zamiast domyślnego, na przykład *. contoso.com*: 
- Użyj CliConfig, aby zdefiniować alias. To narzędzie jest tylko otoką ustawień rejestru, dlatego można ją wykonać przy użyciu zasad grupy lub skryptu.
- Użyj opcji *CNAME* z opcją *TrustServerCertificate = true* .

## <a name="move-a-database-from-sql-managed-instance"></a>Przenoszenie bazy danych z wystąpienia zarządzanego SQL 

**Jak przenieść bazę danych z wystąpienia zarządzanego SQL z powrotem do SQL Server lub Azure SQL Database?**

[Bazę danych można wyeksportować do BACPAC](../database/database-export.md) , a następnie [zaimportować plik BACPAC](../database/database-import.md). Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB.

Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

Nie można przywrócić natywnych `COPY_ONLY` kopii zapasowych wykonanych z wystąpienia zarządzanego SQL do SQL Server, ponieważ wystąpienie zarządzane SQL ma nowszą wersję bazy danych porównaną z SQL Server.

## <a name="migrate-an-instance-database"></a>Migrowanie bazy danych wystąpienia

**Jak przeprowadzić migrację bazy danych wystąpienia do Azure SQL Database?**

Jedną z opcji jest [wyeksportowanie bazy danych do BACPAC](../database/database-export.md) , a następnie [zaimportowanie pliku BACPAC](../database/database-import.md). 

Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB. Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

## <a name="switch-hardware-generation"></a>Przełącz generowanie sprzętu 

**Czy mogę przełączać generowanie sprzętu wystąpienia zarządzanego SQL między programem gen 4 i gen 5 online?**

Automatyczne przełączanie do trybu online między generacjami sprzętowymi jest możliwe, jeśli w regionie, w którym Zainicjowano obsługę wystąpienia zarządzanego SQL, są dostępne oba generacji sprzętowe. W takim przypadku można sprawdzić [stronę przegląd modelu rdzeń wirtualny](../database/service-tiers-vcore.md), która wyjaśnia, jak przełączać się między generacjami sprzętowymi.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane zostanie udostępnione w tle, a bazy danych są automatycznie transferowane między starymi i nowymi wystąpieniami, z szybką pracą w trybie failover na końcu procesu. 

**Co zrobić, jeśli oba generacje sprzętowe nie są obsługiwane w tym samym regionie?**

Jeśli oba generacje sprzętowe nie są obsługiwane w tym samym regionie, zmiana generacji sprzętu jest możliwa, ale należy ją wykonać ręcznie. Wymaga to zainicjowania obsługi nowego wystąpienia w regionie, w którym jest dostępna żądana generacja sprzętu, a następnie ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem.

**Co zrobić, jeśli nie ma wystarczającej liczby adresów IP do wykonania operacji aktualizacji?**

W przypadku braku wystarczającej liczby adresów IP w podsieci, w której jest inicjowane zarządzane wystąpienie, należy utworzyć nową podsieć i w niej nowe wystąpienie zarządzane. Sugerujemy również, że Nowa podsieć jest tworzona przy użyciu większej liczby adresów IP przyznanych, aby przyszłe operacje aktualizowania zastąpią podobne sytuacje. (W przypadku odpowiedniego rozmiaru podsieci Sprawdź, [jak określić rozmiar podsieci sieci wirtualnej](vnet-subnet-determine-size.md)). Po aprowizacji nowego wystąpienia można ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub wykonać [przywracanie do określonego momentu w czasie](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Dostosowywanie wydajności

**Jak mogę dostosować wydajność wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane SQL w warstwie Ogólnego przeznaczenia używa magazynu zdalnego, więc rozmiar danych i plików dzienników jest istotny dla wydajności. Aby uzyskać więcej informacji, zobacz [wpływ rozmiaru pliku dziennika na ogólnego przeznaczenia wydajności wystąpienia zarządzanego SQL](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Jeśli obciążenie obejmuje wiele małych transakcji, rozważ przełączenie typu połączenia z serwera proxy do trybu przekierowania.

## <a name="maximum-storage-size"></a>Maksymalny rozmiar magazynu

**Jaki jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego SQL?**

Rozmiar magazynu zarządzanego przez SQL zależy od wybranej warstwy usług (Ogólnego przeznaczenia lub Krytyczne dla działania firmy). Aby uzyskać ograniczenia dotyczące magazynu tych warstw usług, zobacz [Charakterystyka warstwy usług](../database/service-tiers-general-purpose-business-critical.md).

  
## <a name="networking-requirements"></a>Wymagania dotyczące sieci 

**Jakie są bieżące ograniczenia sieciowej grupy zabezpieczeń ruchu przychodzącego/wychodzącego w podsieci wystąpienia zarządzanego?**

Wymagane reguły sieciowej grupy zabezpieczeń i UDR są udokumentowane w [tym miejscu](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)i automatycznie ustawiane przez usługę.
Należy pamiętać, że te reguły są tylko tymi, które są potrzebne do obsługi usługi. Aby nawiązać połączenie z wystąpieniem zarządzanym i korzystać z innych funkcji, należy ustawić dodatkowe, specyficzne dla funkcji reguły, które należy zachować.

**Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na portach zarządzania?**

Wystąpienie zarządzane SQL jest odpowiedzialne za ustawianie reguł na portach zarządzania. Jest to realizowane przy użyciu funkcji o nazwie [podsieć z obsługą usługi](connectivity-architecture-overview.md#service-aided-subnet-configuration).
Ma to na celu zapewnienie nieprzerwanego przepływu ruchu związanego z zarządzaniem w celu spełnienia warunków umowy SLA.

**Czy mogę uzyskać źródłowe zakresy adresów IP, które są używane przez ruch przychodzący zarządzania?**

Tak. Ruch przychodzący przez grupę zabezpieczeń sieci można analizować przez [skonfigurowanie Network Watcher dzienników przepływu](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#analyze-traffic-to-or-from-a-network-security-group).

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

Podsieć musi mieć wystarczającą liczbę dostępnych [adresów IP](connectivity-architecture-overview.md#network-requirements). Aby określić rozmiar podsieci sieci wirtualnej dla wystąpienia zarządzanego SQL, zobacz [Określanie wymaganego rozmiaru podsieci i zakresu dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-determine-size-vnet-subnet). 

**Co zrobić, jeśli nie ma wystarczającej liczby adresów IP na potrzeby wykonywania operacji aktualizacji wystąpienia?**

W przypadku braku wystarczającej liczby [adresów IP](connectivity-architecture-overview.md#network-requirements) w podsieci, w której jest inicjowane zarządzane wystąpienie, należy utworzyć nową podsieć i w niej nowe wystąpienie zarządzane. Sugerujemy również, że Nowa podsieć jest tworzona przy użyciu większej liczby adresów IP przyznanych, aby przyszłe operacje aktualizowania zastąpią podobne sytuacje. Po aprowizacji nowego wystąpienia można ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub wykonać [przywracanie do określonego momentu w czasie](point-in-time-restore.md?tabs=azure-powershell).

**Czy muszę mieć pustą podsieć, aby utworzyć wystąpienie zarządzane?**

Nie. Można użyć pustej podsieci lub podsieci, która zawiera już wystąpienia zarządzane. 

**Czy mogę zmienić zakres adresów podsieci?**

Nie Jeśli w programie istnieją wystąpienia zarządzane. Jest to ograniczenie infrastruktury sieci platformy Azure. Można [dodać tylko dodatkową przestrzeń adresową do pustej podsieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet#change-subnet-settings). 

**Czy mogę przenieść wystąpienie zarządzane do innej podsieci?**

Nie. Jest to bieżące ograniczenie projektowania wystąpienia zarządzanego. Można jednak udostępnić nowe wystąpienie w innej podsieci i ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem lub przeprowadzić [przywracanie do punktu w czasie](point-in-time-restore.md?tabs=azure-powershell)między wystąpieniami.

**Czy potrzebuję pustej sieci wirtualnej, aby utworzyć wystąpienie zarządzane?**

Nie jest to wymagane. Można [utworzyć sieć wirtualną dla wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-vnet-subnet) lub [skonfigurować istniejącą sieć wirtualną dla wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vnet-subnet).

**Czy można umieścić wystąpienie zarządzane z innymi usługami w podsieci?**

Nie. Obecnie nie obsługujemy umieszczania wystąpienia zarządzanego w podsieci, która zawiera już inne typy zasobów.

## <a name="connectivity"></a>Łączność 

**Czy mogę połączyć się z moim wystąpieniem zarządzanym przy użyciu adresu IP?**

Nie, to nie jest obsługiwane. Nazwa hosta wystąpienia zarządzanego jest mapowana na moduł równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego. Ponieważ jeden klaster wirtualny może obsługiwać wiele wystąpień zarządzanych, nie można kierować połączenia do właściwego wystąpienia zarządzanego bez określania jego nazwy.
Aby uzyskać więcej informacji na temat architektury klastra wirtualnego wystąpienia zarządzanego SQL, zobacz [Architektura łączności klastra wirtualnego](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Czy zarządzane wystąpienie ma statyczny adres IP?**

Nie jest to obecnie obsługiwane.

W rzadkich przypadkach, ale konieczne może być przeprowadzenie migracji w trybie online wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby migracja jest spowodowana zmianami w naszym stosie technologii, które mają na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP mapowanego na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie wiąże się z obsługą statycznego adresu IP i zastrzega sobie prawo do jego zmiany bez powiadomienia jako części zwykłych cykli konserwacyjnych.

Z tego powodu zdecydowanie odradzamy niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

**Czy zarządzane wystąpienie ma publiczny punkt końcowy?**

Tak. Wystąpienie zarządzane ma publiczny punkt końcowy, który jest domyślnie używany tylko do zarządzania usługami, ale klient może również włączyć go na potrzeby dostępu do danych. Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpienia zarządzanego SQL z publicznymi punktami końcowymi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-securely). Aby skonfigurować publiczny punkt końcowy, przejdź do pozycji [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym SQL](public-endpoint-configure.md).

**Jak zarządzane wystąpienie zarządza dostępem do publicznego punktu końcowego?**

Wystąpienie zarządzane kontroluje dostęp do publicznego punktu końcowego zarówno na poziomie sieci, jak i aplikacji.

Usługi zarządzania i wdrażania nawiązują połączenie z wystąpieniem zarządzanym przy użyciu [punktu końcowego zarządzania](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#management-endpoint) , który jest mapowany na zewnętrzny moduł równoważenia obciążenia. Ruch jest kierowany do węzłów tylko wtedy, gdy jest odbierany ze wstępnie zdefiniowanym zestawem portów, których używają tylko składniki zarządzania wystąpienia zarządzanego. Wbudowana zapora w węzłach jest skonfigurowana tak, aby zezwalała na ruch tylko z zakresów adresów IP firmy Microsoft. Certyfikaty wzajemnie uwierzytelniają całą komunikację między składnikami zarządzania a płaszczyzną zarządzania. Aby uzyskać więcej informacji, zobacz [Architektura łączności dla wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connectivity-architecture#virtual-cluster-connectivity-architecture).

**Czy mogę użyć publicznego punktu końcowego, aby uzyskać dostęp do danych w bazach danych wystąpienia zarządzanego?**

Tak. Klient będzie musiał włączyć dostęp do danych publicznego punktu końcowego z poziomu programu PowerShell/ARM w [witrynie Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) i skonfigurować sieciowej grupy zabezpieczeń, aby zablokować dostęp do portu danych (numer portu 3342). Aby uzyskać więcej informacji, zobacz [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym Azure SQL](public-endpoint-configure.md) i [bezpieczne używanie wystąpienia zarządzanego usługi Azure SQL z publicznym punktem końcowym](public-endpoint-overview.md). 

**Czy można określić niestandardowy port dla punktów końcowych danych SQL?**

Nie, ta opcja jest niedostępna.  W przypadku prywatnego punktu końcowego danych wystąpienie zarządzane używa domyślnego numeru portu 1433 i dla publicznego punktu końcowego danych, wystąpienie zarządzane używa domyślnego numeru portu 3342.

**Jaki jest zalecany sposób łączenia wystąpień zarządzanych umieszczonych w różnych regionach?**

Komunikacja równorzędna obwodu trasy Express jest preferowanym sposobem wykonania tej czynności. Nie ma to być mieszane w przypadku komunikacji równorzędnej sieci wirtualnych między regionami, która nie jest obsługiwana z powodu [ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)dotyczącego wewnętrznego modułu równoważenia obciążenia.

Jeśli Komunikacja równorzędna obwodu trasy Express nie jest możliwa, jedyną inną opcją jest utworzenie połączenia sieci VPN typu lokacja-lokacja ([Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal), [programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)).


## <a name="mitigate-data-exfiltration-risks"></a>Ograniczanie ryzyka związanego z eksfiltracji danych  

**Jak mogę ograniczyć ryzyko związane z eksfiltracji danych?**

Aby zmniejszyć ryzyko związane z eksfiltracji danych, klienci są zalecani do zastosowania zestawu ustawień zabezpieczeń i kontroli:

- Włącz [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) we wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to również zalecane lokalnie.
- Używaj tylko uwierzytelniania Azure Active Directory (Azure AD).
- Uzyskaj dostęp do wystąpienia przy użyciu konta DBA o niskim poziomie uprawnień.
- Skonfiguruj dostęp JIT serwera przesiadkowego dla konta sysadmin.
- Włącz [inspekcję SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)i Zintegruj ją z mechanizmami alertów.
- Włącz [wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) z poziomu zestawu [Advanced Data Security (AD)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Oszczędnościowe przypadki użycia

**Gdzie mogę znaleźć przypadki użycia i uzyskać oszczędności wynikające z wystąpienia zarządzanego SQL?**

Analizy przypadków wystąpienia zarządzanego SQL:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Aby lepiej zrozumieć korzyści, koszty i ryzyko związane z wdrażaniem wystąpienia zarządzanego usługi Azure SQL, istnieje również analiza Forrestera: [Całkowity wpływ na gospodarcze wystąpienia zarządzanego Microsoft Azure SQL Database](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns"></a>DNS

**Czy można skonfigurować niestandardowe DNS dla wystąpienia zarządzanego SQL?**

Tak. Zobacz [jak skonfigurować niestandardowy serwer DNS dla wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

**Czy można odświeżyć DNS?**

Obecnie nie udostępniamy funkcji odświeżania konfiguracji serwera DNS dla wystąpienia zarządzanego SQL.

Konfiguracja DNS jest ostatecznie odświeżana:

- Po wygaśnięciu dzierżawy DHCP.
- Podczas uaktualniania platformy.

W ramach tego problemu można obniżyć wystąpienie zarządzanego programu SQL Server do 4 rdzeni wirtualnych i ponownie uaktualnić je. Ma to efekt po stronie odświeżenia konfiguracji DNS.


## <a name="change-time-zone"></a>Zmień strefę czasową

**Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?**

Konfigurację strefy czasowej można ustawić, gdy zarządzane wystąpienie jest inicjowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia strefy czasowej](timezones-overview.md#limitations).

Obejścia obejmują tworzenie nowego wystąpienia zarządzanego ze stosowną strefą czasową, a następnie wykonywanie ręcznego tworzenia kopii zapasowej i przywracania, a także to, co zalecamy, wykonywanie [przywracania do punktu w czasie między wystąpieniami](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Rozwiązywanie problemów z wydajnością

**Jak mogę rozwiązać problemy z wydajnością w wystąpieniu zarządzanym SQL?**

W celu porównania wydajności między wystąpieniem zarządzanym SQL i SQL Server, dobrym punktem początkowym jest [najlepsze rozwiązanie dotyczące porównania wydajności między wystąpieniem zarządzanym usługi Azure SQL i SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Ładowanie danych jest często wolniejsze w wystąpieniu zarządzanym SQL niż w SQL Server z powodu obowiązkowego pełnego modelu odzyskiwania i [limitów](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) przepływności zapisu w dzienniku transakcji. Czasami może to być spowodowane załadowaniem danych przejściowych do bazy danych tempdb, a nie z jej bazą


## <a name="restore-encrypted-backup"></a>Przywracanie zaszyfrowanej kopii zapasowej

**Czy można przywrócić zaszyfrowaną bazę danych do wystąpienia zarządzanego SQL?**

Tak, nie musisz odszyfrowywać bazy danych, aby przywrócić ją do wystąpienia zarządzanego SQL. Musisz podać certyfikat/klucz używany jako funkcja ochrony klucza szyfrowania w systemie źródłowym do wystąpienia zarządzanego SQL, aby można było odczytywać dane z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa sposoby, aby to zrobić:

- *Przekaż ochronę certyfikatu do wystąpienia zarządzanego SQL*. Można to zrobić tylko przy użyciu programu PowerShell. [Przykładowy skrypt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) opisuje cały proces.
- *Przekaż funkcję ochrony klucza asymetrycznego do Azure Key Vault i wskaż do niej wystąpienie zarządzane przez SQL*. Takie podejście przypomina BYOK TDE użycie, który używa integracji Key Vault, która przechowuje klucz szyfrowania. Jeśli nie chcesz używać klucza jako ochrony klucza szyfrowania i po prostu chcesz udostępnić klucz dla wystąpienia zarządzanego SQL w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)i nie zaznaczaj pola wyboru **Oznacz wybrany klucz jako domyślną ochronę TDE**.

Po udostępnieniu funkcji ochrony szyfrowania wystąpieniem zarządzanym SQL można wykonać procedurę standardowego przywracania bazy danych.

## <a name="purchasing-models-and-benefits"></a>Kupowanie modeli i korzyści

**Jakie modele zakupów są dostępne dla wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane SQL oferuje [model zakupu oparty na rdzeń wirtualny](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model).

**Jakie korzyści z kosztów są dostępne dla wystąpienia zarządzanego SQL?**

Koszty związane z korzyściami z usługi Azure SQL można zaoszczędzić w następujący sposób:
-   Zmaksymalizuj istniejące inwestycje w licencje lokalne i Zaoszczędź nawet do 55% dzięki [korzyść użycia hybrydowego platformy Azure](https://docs.microsoft.com/azure/azure-sql/azure-hybrid-benefit?tabs=azure-powershell). 
-   Zatwierdź do rezerwacji zasobów obliczeniowych i zaoszczędź do 33% z [korzyścią wystąpienia zarezerwowanego](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). Połącz ją z korzyścią użycia hybrydowego platformy Azure, aby zaoszczędzić do 82%. 
-   Oszczędź do 55 procent w porównaniu z cennikiem dzięki [korzyściom z tytułu](https://azure.microsoft.com/pricing/dev-test/) tworzenia i testowania platformy Azure, która oferuje obniżone stawki za bieżące obciążenia związane z tworzeniem i testowaniem.

**Kto jest uprawniony do skorzystania z korzyści z wystąpienia zarezerwowanego?**

Aby można było kwalifikować się do skorzystania z korzyści z wystąpienia zarezerwowanego, typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub indywidualna umowa z cennikiem z płatnością zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Aby uzyskać więcej informacji na temat rezerwacji, zobacz [korzyść wystąpienia zarezerwowanego](https://docs.microsoft.com/azure/sql-database/sql-database-reserved-capacity). 

**Czy istnieje możliwość anulowania, wymiany lub zwrotu pieniędzy?**

Istnieje możliwość anulowania rezerwacji, wymiany lub zwrotu pieniędzy z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Rozliczanie dla wystąpienia zarządzanego i magazynu kopii zapasowych

**Jakie są opcje cennika wystąpienia zarządzanego SQL?**

Aby poznać opcje cennika wystąpienia zarządzanego, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Jak mogę śledzić koszt rozliczeń dla mojego wystąpienia zarządzanego?**

Można to zrobić przy użyciu [rozwiązania Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/). Przejdź do **subskrypcji** w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Analiza kosztów**. 

Użyj opcji **skumulowane koszty** , a następnie Przefiltruj według **typu zasobu** jako `microsoft.sql/managedinstances` .

**Jaki jest koszt zautomatyzowanych kopii zapasowych?**

Ilość wolnego miejsca do magazynowania kopii zapasowej jest naliczana jako zarezerwowane miejsce do magazynowania danych zakupionych, niezależnie od ustawionego okresu przechowywania kopii zapasowych. Jeśli użycie magazynu kopii zapasowych znajduje się w ramach przydziału wolnego miejsca w magazynie kopii zapasowych, automatyczne kopie zapasowe w wystąpieniu zarządzanym nie będą miały żadnych dodatkowych kosztów, dlatego będą bezpłatne. Przekroczenie wykorzystania magazynu kopii zapasowych powyżej ilości wolnego miejsca spowoduje naliczenie kosztów o $0,20 – $0,24 za GB/miesiąc w regionach USA lub wyświetlenie strony z cennikiem w celu uzyskania szczegółowych informacji dotyczących regionu. Aby uzyskać więcej informacji, zobacz Omówienie [użycia magazynu kopii zapasowych](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Jak mogę monitorować koszt rozliczeń za użycie magazynu kopii zapasowych?**

Możesz monitorować koszt magazynu kopii zapasowych za pośrednictwem witryny Azure Portal. Aby uzyskać instrukcje, zobacz [monitorowanie kosztów automatycznych kopii zapasowych](https://docs.microsoft.com/azure/azure-sql/database/automated-backups-overview?tabs=managed-instance#monitor-costs). 

**Jak mogę zoptymalizować koszty magazynu kopii zapasowych w wystąpieniu zarządzanym?**

Aby zoptymalizować koszty magazynu kopii zapasowych, zobacz [precyzyjne dostrajanie kopii zapasowych w wystąpieniu zarządzanym SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935).

## <a name="password-policy"></a>Zasady dotyczące haseł 

**Jakie zasady haseł są stosowane dla nazw logowania SQL wystąpienia zarządzanego SQL?**

Zasady haseł wystąpienia zarządzanego SQL dla nazw logowania SQL dziedziczą zasady platformy Azure, które są stosowane do maszyn wirtualnych tworzących klaster wirtualny przechowujący wystąpienie zarządzane. W tej chwili nie można zmienić któregokolwiek z tych ustawień, ponieważ te ustawienia są zdefiniowane przez platformę Azure i dziedziczone przez wystąpienie zarządzane.

 > [!IMPORTANT]
 > Platforma Azure może zmieniać wymagania dotyczące zasad bez powiadamiania usług korzystających z tych zasad.

**Co to są bieżące zasady platformy Azure?**

Każda nazwa logowania musi ustawić swoje hasło po zalogowaniu i zmienić hasło po osiągnięciu maksymalnego wieku.

| **Zasady** | **Ustawienie zabezpieczeń** |
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

## <a name="azure-feedback-and-support"></a>Opinie i pomoc techniczna dla platformy Azure

**Gdzie mogę pozostawić moje pomysły dotyczące ulepszeń wystąpienia zarządzanego SQL?**

Możesz głosować na nową funkcję wystąpienia zarządzanego lub wprowadzić nowy pomysł ulepszania na potrzeby głosowania na [forum opinii o wystąpieniu zarządzanym SQL](https://feedback.azure.com/forums/915676-sql-managed-instance). Dzięki temu można przyczynić się do rozwoju produktu i pomóc nam określić priorytety potencjalnych ulepszeń.

**Jak można utworzyć żądanie pomocy technicznej systemu Azure?**

Aby dowiedzieć się, jak utworzyć żądanie pomocy technicznej platformy Azure, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

