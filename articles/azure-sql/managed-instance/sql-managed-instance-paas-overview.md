---
title: Co to jest wystąpienie zarządzane Azure SQL?
description: Dowiedz się, jak wystąpienie zarządzane usługi Azure SQL zapewnia niemal 100% zgodność z najnowszym aparatem bazy danych SQL Server (Enterprise Edition)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 01/14/2021
ms.openlocfilehash: 5d49a5b57ff4b59005461f2bb13451822723b039
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644980"
---
# <a name="what-is-azure-sql-managed-instance"></a>Co to jest wystąpienie zarządzane Azure SQL?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane usługi Azure SQL to inteligentna, skalowalna usługa bazy danych w chmurze, która łączy najszerszą SQL Serverą zgodność aparatu bazy danych ze wszystkimi zaletami w pełni zarządzanej i Evergreen platformy jako usługi. Wystąpienie zarządzane SQL ma prawie 100% zgodność z najnowszym aparatem bazy danych SQL Server (Enterprise Edition), zapewniając natywną implementację [sieci wirtualnej (VNET)](../../virtual-network/virtual-networks-overview.md) , która rozwiązuje typowe problemy związane z bezpieczeństwem, oraz [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) preferowany dla istniejących SQL Server klientów. Wystąpienie zarządzane SQL umożliwia istniejącym klientom SQL Server na podniesienie i przesunięcia aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie wystąpienie zarządzane SQL zachowuje wszystkie możliwości PaaS (automatyczne stosowanie poprawek i aktualizacji wersji, [zautomatyzowane kopie zapasowe](../database/automated-backups-overview.md), [wysoka dostępność](../database/high-availability-sla.md)), które znacząco zmniejszają koszty zarządzania i całkowity koszt posiadania.

Jeśli dopiero zaczynasz skorzystać z usługi Azure SQL Managed instance, zapoznaj się z klipem wideo dotyczącym *wystąpienia zarządzanego usługi Azure SQL* z naszej szczegółowej [serii wideo usługi Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Aby zapoznać się z listą regionów, w których jest obecnie dostępne wystąpienie zarządzane SQL, zobacz [Obsługiwane regiony](resource-limits.md#supported-regions).

Na poniższym diagramie przedstawiono główne funkcje wystąpienia zarządzanego SQL:

![Najważniejsze funkcje](./media/sql-managed-instance-paas-overview/key-features.png)

Wystąpienie zarządzane usługi Azure SQL jest przeznaczone dla klientów, którzy chcą migrować dużą liczbę aplikacji z środowiska lokalnego lub IaaS, samodzielnego lub niezależnego dostawcy oprogramowania do w pełni zarządzanego środowiska chmury PaaS, z możliwie jak najniższym nakładem migracji. Korzystając z w pełni zautomatyzowanej [usługi migracji danych platformy Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance), klienci mogą przebudować istniejące wystąpienie SQL Server do wystąpienia zarządzanego SQL, które oferuje zgodność z SQL Server i pełną izolację wystąpień klientów dzięki natywnej obsłudze sieci wirtualnej. Aby uzyskać więcej informacji na temat opcji i narzędzi migracji, zobacz [Omówienie migracji: SQL Server do wystąpienia zarządzanego Azure SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).</br> Program Software Assurance umożliwia wymianę istniejących licencji dla obniżonych stawek w wystąpieniu zarządzanym SQL przy użyciu [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Wystąpienie zarządzane SQL to Najlepsza lokalizacja docelowa migracji w chmurze dla wystąpień SQL Server, które wymagają wysokiego poziomu zabezpieczeń i rozbudowanej powierzchni programistycznej.

> [!TIP]
> Jak możemy ulepszyć usługę Azure SQL? [Wypełnij ankietę](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="key-features-and-capabilities"></a>Najważniejsze funkcje i możliwości

Wystąpienie zarządzane SQL łączy najlepsze funkcje, które są dostępne zarówno w Azure SQL Database, jak i w aparacie bazy danych SQL Server.

> [!IMPORTANT]
> Wystąpienie zarządzane SQL działa ze wszystkimi funkcjami najnowszej wersji SQL Server, w tym operacji online, automatycznych korekt planu i innych ulepszeń wydajności przedsiębiorstwa. Porównanie dostępnych funkcji znajduje się w temacie [porównanie funkcji: wystąpienie zarządzane usługi Azure SQL Server a SQL Server](../database/features-comparison.md).

| **Korzyści z PaaS** | **Ciągłość działalności biznesowej** |
| --- | --- |
|Bez kupowania i zarządzania sprzętem <br>Brak obciążeń związanych z zarządzaniem podstawową infrastrukturą <br>Szybka obsługa i skalowanie usług <br>Automatyczne stosowanie poprawek i uaktualnianie wersji <br>Integracja z innymi usługami danych PaaS |Umowa SLA na 99,99% czasu  <br>Wbudowana [wysoka dostępność](../database/high-availability-sla.md) <br>Dane chronione za pomocą [zautomatyzowanych kopii zapasowych](../database/automated-backups-overview.md) <br>Okres przechowywania kopii zapasowych konfigurowalnych przez klienta <br>[Kopie zapasowe](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) inicjowane przez użytkownika <br>Możliwość [przywracania bazy danych do punktu w czasie](../database/recovery-using-backups.md#point-in-time-restore) |
|**Zabezpieczenia i zgodność** | **Zarządzanie**|
|Środowisko izolowane (Integracja z siecią[wirtualną](connectivity-architecture-overview.md), usługa pojedynczej dzierżawy, dedykowane zasoby obliczeniowe i magazyn) <br>[Przezroczyste szyfrowanie danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Uwierzytelnianie Azure Active Directory (Azure AD)](../database/authentication-aad-overview.md), obsługa logowania jednokrotnego <br> [Nazwy główne serwera usługi Azure AD (logowania)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>Stosuje się do standardów zgodności, takich jak Azure SQL Database <br>[Inspekcja SQL](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager interfejs API do automatyzowania aprowizacji i skalowania usługi <br>Azure Portal funkcje ręcznego inicjowania obsługi i skalowania usługi <br>Usługa Data Migration Service

> [!IMPORTANT]
> Wystąpienie zarządzane Azure SQL zostało certyfikowane z zastosowaniem wielu standardów zgodności. Aby uzyskać więcej informacji, zobacz [oferty zgodności Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), w których można znaleźć najbardziej aktualną listę certyfikatów zgodności wystąpienia zarządzanego SQL, które wymieniono w obszarze **SQL Database**.

W poniższej tabeli przedstawiono najważniejsze funkcje wystąpienia zarządzanego SQL:

|Cecha | Opis|
|---|---|
| Wersja SQL Server/kompilacja | Aparat bazy danych SQL Server (Najnowsza stabilna) |
| Zarządzane automatyczne kopie zapasowe | Tak |
| Wbudowane wystąpienie i monitorowanie bazy danych oraz metryki | Tak |
| Automatyczne stosowanie poprawek oprogramowania | Tak |
| Najnowsze funkcje aparatu bazy danych | Tak |
| Liczba plików danych (wierszy) na bazę danych | Wiele |
| Liczba plików dziennika (dzienników) na bazę danych | 1 |
| Wdrożenie Azure Resource Manager sieci wirtualnej | Tak |
| Model wdrażania klasycznego sieci wirtualnej | Nie |
| Obsługa portalu | Tak|
| Wbudowana usługa integracji (SSIS) | Nie — SSIS jest częścią [Azure Data Factory PaaS](../../data-factory/tutorial-deploy-ssis-packages-azure.md) |
| Wbudowana usługa analizy (SSAS) | Nie — SSAS jest osobnym [PaaS](../../analysis-services/analysis-services-overview.md) |
| Wbudowana usługa raportowania (SSRS) | Nie — zamiast tego użyj raportów z podziałem na strony [Power BI](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) lub HOSTOWANIE usług SSRS na maszynie wirtualnej platformy Azure. Chociaż wystąpienie zarządzane SQL nie może uruchamiać usług SSRS jako usługi, może hostować [bazy danych wykazu usług SSRS](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) dla serwera raportowania zainstalowanego na maszynie wirtualnej platformy Azure przy użyciu uwierzytelniania SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

[Model zakupu oparty na rdzeń wirtualny](../database/service-tiers-vcore.md) dla wystąpienia zarządzanego SQL zapewnia elastyczność, kontrolę, przejrzystość i prostą metodę tłumaczenia lokalnych wymagań obciążeń do chmury. Ten model umożliwia zmianę zasobów obliczeniowych, pamięci i magazynu w zależności od potrzeb związanych z obciążeniem. Model rdzeń wirtualny jest również uprawniony do 55% oszczędności przy użyciu [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Server.

W modelu rdzeń wirtualny można wybrać między generacjami sprzętu.

- Logiczne procesory **obliczenia** są oparte na procesorach Intel &reg; E5-2673 v3 (Haswell) 2,4 GHz, dołączonym SSD, rdzeniach fizycznych, 7 GB pamięci RAM na rdzeń i rozmiarach obliczeniowych od 8 do 24 rdzeni wirtualnych.
- Logiczne procesory **5 rdzeń** są oparte na procesorach Intel &reg; E5-2673 v4 (Broadwell) 2,3 GHz, Intel &reg; SP-8160 (Skylake) i Intel &reg; 8272CL (Kaskada Lake) 2,5 procesorów, szybkiego SSD interfejsu NVMe, rdzeniu logicznego funkcji Hyper-Threading i rozmiarów obliczeń między 4 i 80 rdzeniami.

Znajdź więcej informacji na temat różnic między generacjami sprzętowymi w [limitach zasobów wystąpienia zarządzanego SQL](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Warstwy usług

Wystąpienie zarządzane SQL jest dostępne w dwóch warstwach usług:

- **Ogólnego przeznaczenia**: zaprojektowana dla aplikacji z typowymi wymaganiami dotyczącymi opóźnień I operacji we/wy.
- **Krytyczne** dla działania firmy: zaprojektowano dla aplikacji z minimalnymi wymaganiami we/wy i minimalnego wpływu podstawowych operacji konserwacji w obciążeniu.

Obie warstwy usług gwarantują dostępność na 99,99% i umożliwiają niezależne Wybieranie rozmiaru magazynu i pojemności obliczeniowej. Aby uzyskać więcej informacji na temat architektury wysokiej dostępności wystąpienia zarządzanego Azure SQL, zobacz [wysoką dostępność i wystąpienie zarządzane usługi Azure SQL](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Ogólnego przeznaczenia warstwy usług

Poniższa lista zawiera opis kluczowych właściwości warstwy usługi Ogólnego przeznaczenia:

- Zaprojektowana dla większości aplikacji branżowych z typowymi wymaganiami dotyczącymi wydajności
- Wysoce wydajny magazyn obiektów blob platformy Azure (8 TB)
- Wbudowana [wysoka dostępność](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) na podstawie niezawodnego magazynu obiektów blob platformy Azure i [usługi Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Aby uzyskać więcej informacji, zobacz temat [warstwa magazynowania w warstwach ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) i [najlepszych rozwiązaniach dotyczących wydajności magazynu oraz zagadnieniach związanych z wystąpieniem zarządzanym SQL (ogólnego przeznaczenia)](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose).

Znajdź więcej informacji o różnicach między warstwami usług w [limitach zasobów wystąpienia zarządzanego SQL](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Krytyczne dla działania firmy warstwy usług

Warstwa usługi Krytyczne dla działania firmy jest tworzona dla aplikacji o wysokich wymaganiach we/wy. Zapewnia największą odporność na błędy przy użyciu kilku izolowanych replik.

Na poniższej liście przedstawiono kluczowe cechy warstwy usługi Krytyczne dla działania firmy:

- Przeznaczone dla aplikacji firmowych o najwyższej wydajności i wymaganiach dotyczących wysokiej dostępności
- Jest dostarczany z bardzo szybkim lokalnym magazynem SSD (do 1 TB w systemie obliczenia i do 4 TB na 5 rdzeń)
- Wbudowana [wysoka dostępność](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) na podstawie [zawsze dostępnych grup dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [platformy Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- Wbudowana dodatkowa [replika bazy danych tylko do odczytu](../database/read-scale-out.md) , która może być używana do raportowania i innych obciążeń tylko do odczytu
- [OLTP w pamięci](../in-memory-oltp-overview.md) , który może być używany do obciążeń z wymaganiami o wysokiej wydajności  

Znajdź więcej informacji o różnicach między warstwami usług w [limitach zasobów wystąpienia zarządzanego SQL](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operacje zarządzania

Wystąpienie zarządzane usługi Azure SQL udostępnia operacje zarządzania, których można użyć do automatycznego wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpienia i usuwania wystąpień, gdy nie są już potrzebne. Szczegółowe wyjaśnienie operacji zarządzania można znaleźć na stronie [Przegląd operacji zarządzania wystąpieniami zarządzanymi](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Wystąpienie zarządzane SQL zawiera zaawansowane funkcje zabezpieczeń zapewniane przez platformę Azure i aparat bazy danych SQL Server.

### <a name="security-isolation"></a>Izolacja zabezpieczeń

Wystąpienie zarządzane SQL zapewnia dodatkową izolację zabezpieczeń od innych dzierżawców na platformie Azure. Izolacja zabezpieczeń obejmuje:

- [Implementacja natywnej sieci wirtualnej](connectivity-architecture-overview.md) i łączność ze środowiskiem lokalnym za pomocą usługi Azure ExpressRoute lub VPN Gateway.
- W domyślnym wdrożeniu punkt końcowy SQL jest udostępniany tylko za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczną łączność z prywatnych platform Azure lub sieci hybrydowych.
- Pojedyncza dzierżawa z dedykowaną podstawową infrastrukturą (COMPUTE, Storage).

Na poniższym diagramie przedstawiono różne opcje łączności dla aplikacji:

![Wysoka dostępność](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Aby dowiedzieć się więcej na temat integracji sieci wirtualnej i wymuszania zasad sieciowych na poziomie podsieci, zobacz [Architektura sieci wirtualnej dla wystąpień zarządzanych](connectivity-architecture-overview.md) i [Połącz aplikację z wystąpieniem zarządzanym](connect-application-instance.md).

> [!IMPORTANT]
> Umieść wiele wystąpień zarządzanych w tej samej podsieci, wszędzie tam, gdzie jest to dozwolone w wymaganiach dotyczących zabezpieczeń, co spowoduje dodatkowe korzyści. Lokalizowanie wystąpień w tej samej podsieci znacznie upraszcza konserwację infrastruktury sieciowej i zmniejsza czas aprowizacji wystąpienia, ponieważ długi czas udostępniania jest skojarzony z kosztem wdrożenia pierwszego wystąpienia zarządzanego w podsieci.

### <a name="security-features"></a>Funkcje zabezpieczeń

Wystąpienie zarządzane usługi Azure SQL udostępnia zestaw zaawansowanych funkcji zabezpieczeń, których można użyć do ochrony danych.

- [Inspekcja wystąpienia zarządzanego SQL](auditing-configure.md) śledzi zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji umieszczonym na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, analizować aktywność bazy danych oraz uzyskać wgląd w odchylenia i anomalie, które mogą oznaczać problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Szyfrowanie danych w usłudze Motion — wystąpienie zarządzane SQL zabezpiecza dane, zapewniając szyfrowanie danych w ruchu przy użyciu Transport Layer Security. Oprócz Transport Layer Security wystąpienie zarządzane SQL oferuje ochronę poufnych danych w locie, w spoczynku i podczas przetwarzania zapytań przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted oferuje bezpieczeństwo danych przed naruszeniem, które wiążą się z kradzieżą ważnych danych. Na przykład w przypadku Always Encrypted numery kart kredytowych są przechowywane w bazie danych zawsze, nawet podczas przetwarzania zapytań, umożliwiając odszyfrowywanie w punkcie użytkowania przez autoryzowanego pracownika lub aplikacje, które muszą przetwarzać te dane.
- [Zaawansowana ochrona przed zagrożeniami](threat-detection-configure.md) uzupełnia [inspekcję](auditing-configure.md) , zapewniając dodatkową warstwę analizy zabezpieczeń wbudowaną w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Otrzymasz alerty o podejrzanych działaniach, potencjalnych lukach w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych. Alerty zaawansowanej ochrony przed zagrożeniami można przeglądać z poziomu [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dostarczają szczegółowych informacji o podejrzanych działaniach i zalecaną akcję dotyczącą badania i łagodzenia zagrożeń.  
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza narażenie na dane poufne przez zamaskowanie ich dla użytkowników bez uprawnień. Dynamiczne maskowanie danych ułatwia Zapobieganie nieautoryzowanemu dostępowi do poufnych danych przez umożliwienie wyznaczenia ilości poufnych danych do ujawnienia przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, podczas gdy dane w bazie danych nie są zmieniane.
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwiają kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (na przykład według członkostwa w grupie lub kontekstu wykonania). Zabezpieczenia na poziomie wiersza upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie pracownikom dostępu tylko do wierszy danych, które są istotne dla działu, lub ograniczenia dostępu do danych tylko do odpowiednich danych.
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje pliki danych wystąpienia zarządzanego SQL, znane jako szyfrowanie przechowywanych danych. TDE wykonuje szyfrowanie we/wy czasu rzeczywistego i odszyfrowywanie plików danych i dzienników. Szyfrowanie używa klucza szyfrowania bazy danych, który jest przechowywany w rekordzie rozruchowym bazy danych w celu zapewnienia dostępności podczas odzyskiwania. Można chronić wszystkie bazy danych w wystąpieniu zarządzanym z przezroczystym szyfrowaniem danych. TDE to sprawdzona technologia szyfrowania w usłudze REST w SQL Server, która jest wymagana przez wiele standardów zgodności, aby chronić przed kradzieżą nośników magazynu.

Migracja zaszyfrowanej bazy danych do wystąpienia zarządzanego SQL jest obsługiwana za pośrednictwem Azure Database Migration Service lub przywracania natywnego. Jeśli planujesz migrację zaszyfrowanej bazy danych przy użyciu przywracania natywnego, migracja istniejącego certyfikatu TDE z wystąpienia SQL Server do wystąpienia zarządzanego SQL jest wymaganym krokiem. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [SQL Server migracja do wystąpienia zarządzanego SQL](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integracja z usługą Azure Active Directory

Wystąpienie zarządzane SQL obsługuje tradycyjne logowania do aparatu bazy danych SQL Server i logowania zintegrowane z usługą Azure AD. Nazwy główne serwera usługi Azure AD (dane logowania) (**publiczna wersja zapoznawcza**) są w chmurze systemu Azure lokalnych nazw logowania, które są używane w środowisku lokalnym. Nazwy główne serwera usługi Azure AD umożliwiają określanie użytkowników i grup z dzierżawy usługi Azure AD jako wystąpień głównych o zakresie wystąpienia, które mogą wykonywać wszystkie operacje na poziomie wystąpienia, w tym zapytania między bazami danych w ramach tego samego wystąpienia zarządzanego.

Wprowadzono nową składnię do tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania) **od dostawcy zewnętrznego**. Aby uzyskać więcej informacji na temat składni, zobacz [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)i zapoznaj się z artykułem [inicjowanie obsługi administracyjnej Azure Active Directory dla wystąpienia zarządzanego SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Wystąpienie zarządzane SQL umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft z [integracją Azure Active Directory](../database/authentication-aad-overview.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](../database/authentication-mfa-ssms-configure.md) w celu zwiększenia bezpieczeństwa danych i aplikacji podczas obsługi logowania jednokrotnego.

### <a name="authentication"></a>Authentication

Uwierzytelnianie wystąpienia zarządzanego SQL dotyczy sposobu, w jaki użytkownicy udowadniają swoją tożsamość podczas łączenia się z bazą danych. Wystąpienie zarządzane SQL obsługuje dwa typy uwierzytelniania:  

- **Uwierzytelnianie SQL**:

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.
- **Uwierzytelnianie Azure Active Directory**:

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowanych. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w bazie danych w wystąpieniu zarządzanym usługi Azure SQL, i jest kontrolowany przez członkostwo roli bazy danych i uprawnienia na poziomie obiektów. Wystąpienie zarządzane SQL ma takie same funkcje autoryzacji jak SQL Server 2017.

## <a name="database-migration"></a>Migracja bazy danych

Wystąpienie zarządzane SQL kieruje scenariusze użytkownika z migracją masowej bazy danych z implementacji baz danych lokalnych lub IaaS. Wystąpienie zarządzane SQL obsługuje kilka opcji migracji bazy danych, które zostały omówione w przewodnikach migracji. Aby uzyskać więcej informacji [, zobacz Omówienie migracji: SQL Server do wystąpienia zarządzanego Azure SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md) .

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście do migracji wykorzystuje kopie zapasowe SQL do usługi Azure Blob Storage. Kopie zapasowe przechowywane w usłudze Azure Storage BLOB mogą być bezpośrednio przywracane do wystąpienia zarządzanego przy użyciu [polecenia Przywróć T-SQL](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current).

- Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak przywrócić plik kopii zapasowej o szerokim świecie standardowym, zobacz [przywracanie pliku kopii zapasowej do wystąpienia zarządzanego](restore-sample-database-quickstart.md). Ten przewodnik Szybki Start przedstawia, że musisz przekazać plik kopii zapasowej do usługi Azure Blob Storage i zabezpieczyć go przy użyciu klucza sygnatury dostępu współdzielonego (SAS).
- Aby uzyskać informacje o przywracaniu z adresu URL, zobacz [natywne przywracanie z adresu URL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore).

> [!IMPORTANT]
> Kopie zapasowe z wystąpienia zarządzanego można przywrócić tylko do innego wystąpienia zarządzanego. Nie można ich przywrócić do wystąpienia SQL Server lub Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących baz danych innych firm i SQL Server do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i SQL Server na maszynie wirtualnej platformy Azure. Zobacz [, jak migrować lokalną bazę danych do wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Wystąpienie zarządzane SQL ma na celu dostarczenie do 100% zgodności obszaru powierzchni z najnowszą wersją SQL Server za pomocą planu przygotowanego zlecenia. Aby uzyskać listę funkcji i porównywania, zobacz [porównanie funkcji wystąpienia zarządzanego SQL](../database/features-comparison.md)i listę różnic t-SQL w wystąpieniu zarządzanym sql a SQL Server, zobacz [różnice t-SQL wystąpienia zarządzanego przez sql](transact-sql-tsql-differences-sql-server.md)w programie SQL Server.

Wystąpienie zarządzane SQL obsługuje zgodność z poprzednimi wersjami z bazami danych SQL Server 2008. Obsługiwana jest bezpośrednia migracja z serwerów baz danych SQL Server 2005, a poziom zgodności dla zmigrowanych baz danych SQL Server 2005 został zaktualizowany do SQL Server 2008.
  
Na poniższym diagramie przedstawiono zgodność obszaru powierzchni w wystąpieniu zarządzanym SQL:  

![zgodność obszaru powierzchni](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Kluczowe różnice między SQL Server lokalnymi i wystąpieniem zarządzanym SQL

Wystąpienia zarządzane SQL są zawsze aktualne w chmurze, co oznacza, że niektóre funkcje w SQL Server mogą być przestarzałe, być wycofane lub mieć alternatywy. Istnieją określone przypadki, w których narzędzia muszą rozpoznać, że określona funkcja działa w nieco inny sposób lub że usługa działa w środowisku, w którym nie jest w pełni kontrolowany.

Niektóre kluczowe różnice:

- Wysoka dostępność jest wbudowana i wstępnie skonfigurowana przy użyciu technologii podobnej do [zawsze dostępnych grup dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Dostępne są tylko zautomatyzowane kopie zapasowe i przywracanie do punktu w czasie. Klienci mogą inicjować `copy-only` kopie zapasowe, które nie zakłócają automatycznego łańcucha kopii zapasowych.
- Określanie pełnych ścieżek fizycznych nie jest obsługiwane, więc wszystkie odpowiednie scenariusze muszą być obsługiwane inaczej: instrukcja RESTORE DB nie obsługuje funkcji MOVE, CREATE DB nie zezwala na ścieżki fizyczne, BULK INSERT działa tylko z obiektami blob platformy Azure itd.
- Wystąpienie zarządzane SQL obsługuje [uwierzytelnianie w usłudze Azure AD](../database/authentication-aad-overview.md) jako alternatywę dla uwierzytelniania systemu Windows w chmurze.
- Wystąpienie zarządzane SQL automatycznie zarządza grupami plików XTP i plikami dla baz danych zawierających In-Memory obiektów OLTP.
- Wystąpienie zarządzane SQL obsługuje SQL Server Integration Services (SSIS) i może hostować wykaz usług SSIS (SSISDB), który przechowuje pakiety SSIS, ale są wykonywane na zarządzanym Azure-SSIS Integration Runtime (IR) w Azure Data Factory. Zobacz [tworzenie Azure-SSIS IR w Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md). Aby porównać funkcje usług SSIS, zobacz [porównanie SQL Database z wystąpieniem zarządzanym SQL](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Funkcje administracyjne

Wystąpienie zarządzane SQL pozwala administratorom systemów poświęcać mniej czasu na zadania administracyjne, ponieważ usługa wykonuje je dla Ciebie lub znacznie upraszcza te zadania. Na przykład [Instalacja systemu operacyjnego/RDBMS i stosowanie poprawek](../database/high-availability-sla.md), [dynamiczne zmienianie rozmiarów i konfiguracji](../database/single-database-scale.md), [kopie zapasowe](../database/automated-backups-overview.md), [Replikacja bazy danych](replication-between-two-instances-configure-tutorial.md) (w tym systemowe bazy danych), [Konfiguracja wysokiej dostępności](../database/high-availability-sla.md)oraz konfiguracja strumieni danych monitorowania kondycji i [wydajności](../../azure-monitor/insights/azure-sql.md) .

Aby uzyskać więcej informacji, zobacz [listę obsługiwanych i nieobsługiwanych funkcji wystąpienia zarządzanego SQL](../database/features-comparison.md)oraz [różnice w języku T-SQL między wystąpieniem zarządzanym SQL i SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Programowo Zidentyfikuj wystąpienie zarządzane

W poniższej tabeli przedstawiono kilka właściwości, które są dostępne za pomocą języka Transact-SQL, za pomocą których można wykryć, że aplikacja pracuje z wystąpieniem zarządzanym SQL i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest taka sama jak w SQL Database. Nie **oznacza to,** że aparat SQL w wersji 12 (SQL Server 2014). W wystąpieniu zarządzanym SQL zawsze jest uruchomiona najnowsza stabilna wersja aparatu SQL, która jest równa lub większa niż najnowsza dostępna wersja RTM programu SQL Server.  |
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest taka sama jak w SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnej instancji w następującym formacie: `<instanceName>` . `<dnsPrefix>` . database.windows.net, gdzie `<instanceName>` jest nazwą dostarczoną przez klienta, podczas gdy `<dnsPrefix>` jest automatycznie generowana część nazwy gwarantującej unikalność globalnej nazwy DNS (na przykład "wcus17662feb9ce98")|Przykład: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](../database/features-comparison.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego SQL](connectivity-architecture-overview.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Aby zapoznać się z samouczkiem dotyczącym korzystania z Azure Database Migration Service migracji, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przez usługę SQL z wbudowaną analizą rozwiązywania problemów, zobacz temat [monitorowanie wystąpienia zarządzanego Azure SQL przy użyciu Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).