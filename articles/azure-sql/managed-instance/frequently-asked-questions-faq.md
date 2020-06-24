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
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708862"
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

## <a name="backup-storage-cost"></a>Koszt magazynu kopii zapasowych 

**Czy magazyn kopii zapasowych jest potrącany z magazynu wystąpienia zarządzanego SQL?**

Nie, magazyn kopii zapasowych nie jest odejmowany od przestrzeni dyskowej wystąpienia zarządzanego SQL. Magazyn kopii zapasowych jest niezależny od ilości miejsca w magazynie i nie ma ograniczonego rozmiaru. Magazyn kopii zapasowych jest ograniczony przez okres, aby zachować kopię zapasową baz danych wystąpień, konfigurowalne od 7 do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [zautomatyzowane kopie zapasowe](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Śledź rozliczenia

**Czy istnieje sposób śledzenia kosztów rozliczeń dla wystąpienia zarządzanego SQL?**

Można to zrobić przy użyciu [rozwiązania Azure Cost Management](/azure/cost-management/). Przejdź do **subskrypcji** w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Analiza kosztów**. 

Użyj opcji **skumulowane koszty** , a następnie Przefiltruj według **typu zasobu** jako `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego

**Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na portach zarządzania?**

Płaszczyzna kontroli wystąpienia zarządzanego SQL utrzymuje reguły sieciowej grupy zabezpieczeń chroniące porty zarządzania.

Oto, z jakich portów zarządzania korzystasz:

Porty 9000 i 9003 są używane przez infrastrukturę usługi Azure Service Fabric. Podstawowa rola Service Fabric ma utrzymywać w dobrej kondycji klaster wirtualny i zachować stan celu w odniesieniu do liczby replik składników.

Porty 1438, 1440 i 1452 są używane przez agenta węzła. Agent węzła to aplikacja, która działa w klastrze i jest używana przez płaszczyznę kontroli do wykonywania poleceń zarządzania.

Oprócz reguł sieciowej grupy zabezpieczeń wbudowana zapora chroni wystąpienie w warstwie sieciowej. W warstwie aplikacji komunikacja jest chroniona przy użyciu certyfikatów.

Aby uzyskać więcej informacji i dowiedzieć się, jak sprawdzić wbudowaną zaporę, zobacz [wbudowana zapora wystąpienia zarządzanego Azure SQL](management-endpoint-verify-built-in-firewall.md).


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


## <a name="dns-refresh"></a>Odświeżanie DNS 

**Czy można odświeżyć DNS?**

Obecnie nie udostępniamy funkcji odświeżania konfiguracji serwera DNS dla wystąpienia zarządzanego SQL.

Konfiguracja DNS jest ostatecznie odświeżana:

- Po wygaśnięciu dzierżawy DHCP.
- Podczas uaktualniania platformy.

W ramach tego problemu można obniżyć wystąpienie zarządzanego programu SQL Server do 4 rdzeni wirtualnych i ponownie uaktualnić je. Ma to efekt po stronie odświeżenia konfiguracji DNS.


## <a name="ip-address"></a>Adres IP

**Czy można połączyć się z wystąpieniem zarządzanym SQL przy użyciu adresu IP?**

Nawiązywanie połączenia z wystąpieniem zarządzanym SQL przy użyciu adresu IP nie jest obsługiwane. Nazwa hosta wystąpienia zarządzanego SQL jest mapowana na moduł równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego SQL. Ponieważ jeden klaster wirtualny może obsługiwać wiele wystąpień zarządzanych, nie można kierować połączeń do właściwego wystąpienia zarządzanego bez jawnego określenia nazwy.

Aby uzyskać więcej informacji na temat architektury klastra wirtualnego wystąpienia zarządzanego SQL, zobacz [Architektura łączności klastra wirtualnego](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Czy wystąpienie zarządzane SQL ma statyczny adres IP?**

W rzadkich przypadkach, ale konieczne może być przeprowadzenie migracji w trybie online wystąpienia zarządzanego SQL do nowego klastra wirtualnego. W razie potrzeby migracja jest spowodowana zmianami w naszym stosie technologii, które mają na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP mapowanego na nazwę hosta wystąpienia zarządzanego SQL. Usługa wystąpienia zarządzanego SQL nie wiąże się z obsługą statycznych adresów IP i zastrzega sobie prawo do jego zmiany bez powiadomienia w ramach zwykłych cykli konserwacyjnych.

Z tego powodu zdecydowanie odradzamy niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

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

## <a name="migrate-from-sql-database"></a>Migrowanie z SQL Database 

**Jak przeprowadzić migrację z Azure SQL Database do wystąpienia zarządzanego SQL?**

Wystąpienie zarządzane SQL zapewnia te same poziomy wydajności dla wielkości zasobów obliczeniowych i magazynu, co Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub po prostu potrzebna jest funkcja obsługiwana wyłącznie w wystąpieniu zarządzanym SQL, można migrować dane przy użyciu funkcji eksportu/importu (BACPAC).

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
