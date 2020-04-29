---
title: Często zadawane pytania dotyczące wystąpień zarządzanych
description: SQL Database często zadawane pytania dotyczące wystąpienia zarządzanego
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80364156"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database często zadawane pytania dotyczące wystąpienia zarządzanego

Ten artykuł zawiera wiele typowych pytań dotyczących [SQL Database wystąpienia zarządzanego](sql-database-managed-instance.md).

## <a name="supported-features"></a>Obsługiwane funkcje

**Gdzie mogę znaleźć listę funkcji obsługiwanych na wystąpieniu zarządzanym?**

Aby uzyskać listę obsługiwanych funkcji w wystąpieniu zarządzanym, zobacz [Azure SQL Database i SQL Server](sql-database-features.md).

Różnice między składnią i zachowaniem Azure SQL Database wystąpienia zarządzanego i SQL Server lokalnych znajdują się w temacie [różnice w języku T-SQL z SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Specyfikacja techniczna & limity zasobów
 
**Gdzie można znaleźć cechy techniczne i limity zasobów dla wystąpienia zarządzanego?**

Aby uzyskać dostęp do charakterystyki generowania sprzętu, zobacz [różnice techniczne w generacjach sprzętowych](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Aby uzyskać dostęp do dostępnych warstw usług i ich cech, zobacz [różnice techniczne między warstwami usług](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Znane problemy & usterki

**Gdzie mogę znaleźć znane problemy i usterki?**

Aby poznać usterki i znane problemy, zobacz [znane problemy](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nowe funkcje

**Gdzie mogę znaleźć najnowsze funkcje i funkcje dostępne w publicznej wersji zapoznawczej?**

Aby poznać nowe funkcje i w wersji zapoznawczej, zobacz [Informacje o wersji](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Czasy wdrożenia 

**Ile czasu zajmuje utworzenie lub zaktualizowanie wystąpienia lub przywrócenie bazy danych?**

Oczekiwany czas tworzenia nowego wystąpienia zarządzanego lub zmiany warstwy usług (rdzeni wirtualnych, Storage) zależy od kilku czynników. Zapoznaj się z [operacjami zarządzania](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Konwencja nazewnictwa

**Czy wystąpienie zarządzane może mieć taką samą nazwę jak SQL Server lokalnego?**

Zmiana nazwy wystąpienia zarządzanego nie jest obsługiwana.

Domyślna strefa DNS wystąpienia zarządzanego *. Database.Windows.NET* można zmienić. 

Aby użyć innej strefy DNS zamiast domyślnego, na przykład *. contoso.com*: 
- Użyj CliConfig, aby zdefiniować alias. To narzędzie jest tylko otoką ustawień rejestru, dlatego można ją wykonać przy użyciu zasad grupy lub skryptu.
- Użyj opcji *CNAME* z *TrustServerCertificate = true* .

## <a name="move-db-from-mi"></a>Przenieś bazę danych z MI 

**Jak przenieść bazę danych z wystąpienia zarządzanego z powrotem do SQL Server lub Azure SQL Database?**

[Bazę danych można wyeksportować do BACPAC](sql-database-export.md) , a następnie [zaimportować plik BACPAC]( sql-database-import.md). Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB.

Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

Nie `COPY_ONLY` można przywrócić natywnych kopii zapasowych wykonanych z wystąpienia zarządzanego do SQL Server, ponieważ wystąpienie zarządzane ma nowszą wersję bazy danych porównaną z SQL Server.

## <a name="migrate-instance-db"></a>Migrowanie bazy danych wystąpień

**Jak przeprowadzić migrację bazy danych wystąpienia do jednego Azure SQL Database?**

Jedną z opcji jest [wyeksportowanie bazy danych do BACPAC](sql-database-export.md) , a następnie [zaimportowanie pliku BACPAC](sql-database-import.md). 

Jest to zalecane rozwiązanie, jeśli baza danych jest mniejsza niż 100 GB. Jeśli wszystkie tabele w bazie danych mają klucze podstawowe, można użyć replikacji transakcyjnej.

## <a name="switch-hardware-generation"></a>Przełącz generowanie sprzętu 

**Czy mogę przełączać generowanie sprzętu wystąpienia zarządzanego między programem gen 4 i gen 5 online?**

Automatyczne przełączanie do trybu online między generacjami sprzętowymi jest możliwe, jeśli w regionie, w którym zainicjowano wystąpienie zarządzane, są dostępne oba generacji sprzętowe. W takim przypadku można sprawdzić na [stronie Przegląd modelu rdzeń wirtualny](sql-database-service-tiers-vcore.md) , jak przełączać się między generacjami sprzętowymi.

Jest to długotrwała operacja, ponieważ nowe wystąpienie zarządzane zostanie udostępnione w tle, a bazy danych zostaną automatycznie przeniesione między starym i nowym wystąpieniem z szybką pracą w trybie failover na końcu procesu. 

Jeśli oba generacje sprzętowe nie są obsługiwane w tym samym regionie, zmiana generacji sprzętu jest możliwa, ale należy ją wykonać ręcznie. Wymaga to zainicjowania obsługi nowego wystąpienia w regionie, w którym jest dostępna żądana generacja sprzętu, a następnie ręcznie utworzyć kopię zapasową i przywrócić dane między starym i nowym wystąpieniem.


## <a name="tune-performance"></a>Dostosowywanie wydajności

**Jak mogę dostosować wydajność wystąpienia zarządzanego?**

Ogólnego przeznaczenia zarządzanym wystąpieniem używa magazynu zdalnego, ponieważ rozmiar danych i plików dziennika jest istotny dla wydajności. Aby uzyskać więcej informacji, zobacz [wpływ rozmiaru pliku dziennika na wydajność wystąpienia zarządzanego ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Jeśli obciążenie obejmuje wiele małych transakcji, rozważ przełączenie typu połączenia z serwera proxy do trybu przekierowania.

## <a name="maximum-storage-size"></a>Maksymalny rozmiar magazynu

**Jaki jest maksymalny rozmiar magazynu dla wystąpienia zarządzanego?**

Rozmiar magazynu dla wystąpienia zarządzanego zależy od wybranej warstwy usług (Ogólnego przeznaczenia lub Krytyczne dla działania firmy). Aby uzyskać ograniczenia dotyczące magazynu tych warstw usług, zobacz [Charakterystyka warstwy usług](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Tworzenie kopii zapasowej kosztów magazynu 

**Czy magazyn kopii zapasowych jest odejmowany od magazynu wystąpienia zarządzanego?**

Nie, magazyn kopii zapasowych nie jest odejmowany od przestrzeni dyskowej wystąpienia zarządzanego. Magazyn kopii zapasowych jest niezależny od ilości miejsca w magazynie i nie ma ograniczonego rozmiaru. Magazyn kopii zapasowych jest ograniczony przez okres, aby zachować kopię zapasową baz danych wystąpień, konfigurowalne od 7 do 35 dni. Aby uzyskać szczegółowe informacje, zobacz [zautomatyzowane kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Śledź rozliczenia

**Czy istnieje sposób śledzenia kosztów rozliczeń dla mojego wystąpienia zarządzanego?**

Można to zrobić przy użyciu [rozwiązania Azure Cost Management](/azure/cost-management/). Przejdź do **subskrypcji** w [Azure Portal](https://portal.azure.com) i wybierz pozycję **Analiza kosztów**. 

Użyj opcji **skumulowane koszty** , a następnie Przefiltruj według **typu zasobu** jako `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego

**Jak ustawić reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego na portach zarządzania?**

Płaszczyzna kontroli wystąpienia zarządzanego utrzymuje reguły sieciowej grupy zabezpieczeń chroniące porty zarządzania.

Oto, z jakich portów zarządzania korzystasz:

Porty 9000 i 9003 są używane przez infrastrukturę Service Fabric. Service Fabric rolą podstawową jest utrzymywanie w dobrej kondycji klastra wirtualnego i zachowanie stanu celu w odniesieniu do liczby replik składników.

Porty 1438, 1440 i 1452 są używane przez agenta węzła. Node Agent to aplikacja, która działa w klastrze i jest używana przez płaszczyznę kontroli do wykonywania poleceń zarządzania.

Oprócz reguł sieciowej grupy zabezpieczeń wbudowana zapora chroni wystąpienie w warstwie sieciowej. Komunikacja warstwy aplikacji jest chroniona przy użyciu certyfikatów.
  
Aby uzyskać więcej informacji i jak sprawdzić wbudowaną zaporę, zobacz [Azure SQL Database wbudowanej zapory wystąpienia zarządzanego](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Ograniczanie ryzyka związanego z eksfiltracji danych  

**Jak mogę ograniczyć ryzyko związane z eksfiltracji danych?**

Aby zmniejszyć ryzyko związane z eksfiltracji danych, klienci są zalecani do zastosowania zestawu ustawień zabezpieczeń i kontroli:

- Włącz [transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) we wszystkich bazach danych.
- Wyłącz środowisko uruchomieniowe języka wspólnego (CLR). Jest to również zalecane lokalnie.
- Używaj tylko uwierzytelniania Azure Active Directory (AAD).
- Wystąpienie dostępu przy użyciu konta z niskim poziomem uprawnień DBA.
- Skonfiguruj dostęp JiT serwera przesiadkowego dla konta sysadmin.
- Włącz [inspekcję SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)i Zintegruj ją z mechanizmami alertów.
- Włącz [wykrywanie zagrożeń](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) z poziomu zestawu [Advanced Data Security (AD)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Oszczędność kosztów — przypadki użycia

**Gdzie mogę znaleźć przypadki użycia i uzyskać oszczędności wynikające z wystąpienia zarządzanego?**

Analizy przypadków wystąpienia zarządzanego:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Aby lepiej zrozumieć korzyści, koszty i ryzyko związane z wdrażaniem Azure SQL Database wystąpienia zarządzanego, istnieje również badanie Forrester: [Całkowity wpływ na](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)to, co jest ekonomiczne.


## <a name="dns-refresh"></a>Odświeżanie DNS 

**Czy można odświeżyć DNS?**

Obecnie nie udostępniamy funkcji odświeżania konfiguracji serwera DNS dla wystąpienia zarządzanego.

Konfiguracja DNS jest ostatecznie odświeżana:

- Po wygaśnięciu dzierżawy DHCP.
- Podczas uaktualniania platformy.

Obejście tego problemu powoduje obniżenie poziomu zarządzanego wystąpienia do 4 rdzeń wirtualny i ponowne uaktualnienie. Ma to efekt po stronie odświeżenia konfiguracji DNS.


## <a name="ip-address"></a>Adres IP

**Czy można połączyć się z wystąpieniem zarządzanym przy użyciu adresu IP?**

Nawiązywanie połączenia z wystąpieniem zarządzanym przy użyciu adresu IP nie jest obsługiwane. Nazwa hosta wystąpienia zarządzanego mapuje do modułu równoważenia obciążenia przed klastrem wirtualnym wystąpienia zarządzanego. Ponieważ jeden klaster wirtualny może obsługiwać wiele połączeń wystąpień zarządzanych, nie można było skierować do właściwego wystąpienia zarządzanego bez określenia jego nazwy.

Aby uzyskać więcej informacji na temat architektury klastra wirtualnego wystąpienia zarządzanego, zobacz [Architektura łączności klastra wirtualnego](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Czy zarządzane wystąpienie ma statyczny adres IP?**

W rzadkich przypadkach, ale konieczne może być przeprowadzenie migracji w trybie online wystąpienia zarządzanego do nowego klastra wirtualnego. W razie potrzeby migracja jest spowodowana zmianami w naszym stosie technologii, które mają na celu poprawę bezpieczeństwa i niezawodności usługi. Migracja do nowego klastra wirtualnego powoduje zmianę adresu IP mapowanego na nazwę hosta wystąpienia zarządzanego. Usługa wystąpienia zarządzanego nie wiąże się z obsługą statycznego adresu IP i zastrzega sobie prawo do jego zmiany bez powiadomienia jako części zwykłych cykli konserwacyjnych.

Z tego powodu zdecydowanie odradzamy niezmienności adresu IP, ponieważ może to spowodować niepotrzebne przestoje.

## <a name="change-time-zone"></a>Zmień strefę czasową

**Czy mogę zmienić strefę czasową dla istniejącego wystąpienia zarządzanego?**

Konfigurację strefy czasowej można ustawić, gdy zarządzane wystąpienie jest inicjowane po raz pierwszy. Zmiana strefy czasowej istniejącego wystąpienia zarządzanego nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia strefy czasowej](sql-database-managed-instance-timezone.md#limitations).

Obejścia obejmują tworzenie nowego wystąpienia zarządzanego ze stosowną strefą czasową, a następnie wykonanie ręcznej kopii zapasowej i przywracania albo to, co jest zalecane, wykonywanie [przywracania do punktu w czasie między wystąpieniami](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Rozwiązywanie problemów z wydajnością

**Jak mogę rozwiązać problemy z wydajnością z moim wystąpieniem zarządzanym?**

Aby porównać wydajność między wystąpieniem zarządzanym i SQL Server, dobrym punktem początkowym jest [najlepsze rozwiązanie w zakresie porównania wydajności między wystąpieniem zarządzanym usługi Azure SQL i](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) artykułem SQL Server.

Ładowanie danych jest często wolniejsze na wystąpieniu zarządzanym niż w SQL Server z powodu obowiązkowego pełnego modelu odzyskiwania i [limitów](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) przepływności zapisu w dzienniku transakcji. Czasami może to być spowodowane załadowaniem danych przejściowych do bazy danych tempdb, a nie z użyciem klastrowanej magazynu kolumn lub tabel zoptymalizowanych pod kątem pamięci.


## <a name="restore-encrypted-backup"></a>Przywracanie zaszyfrowanej kopii zapasowej

**Czy można przywrócić zaszyfrowaną bazę danych do wystąpienia zarządzanego?**

Tak, nie musisz odszyfrowywać bazy danych, aby móc przywrócić ją do wystąpienia zarządzanego. Należy podać certyfikat/klucz używany jako funkcja ochrony klucza szyfrowania w systemie źródłowym do wystąpienia zarządzanego, aby można było odczytywać dane z zaszyfrowanego pliku kopii zapasowej. Istnieją dwa sposoby, aby to zrobić:

- *Przekaż ochronę certyfikatu do wystąpienia zarządzanego*. Można to zrobić tylko przy użyciu programu PowerShell. [Przykładowy skrypt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) opisuje cały proces.
- *Przekaż funkcję ochrony klucza asymetrycznego do Azure Key Vault (AKV) i wskaż do niej wystąpienie zarządzane*. Takie podejście przypomina BYOK TDE użycie, który używa integracji AKV w celu przechowywania klucza szyfrowania. Jeśli nie chcesz używać klucza jako ochrony klucza szyfrowania i po prostu chcesz udostępnić klucz dla wystąpienia zarządzanego w celu przywrócenia zaszyfrowanych baz danych, postępuj zgodnie z instrukcjami dotyczącymi [KONFIGUROWANIA BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)i nie zaznaczaj pola wyboru *Oznacz wybrany klucz jako domyślną ochronę TDE*.

Po udostępnieniu funkcji ochrony szyfrowania wystąpieniem zarządzanemu można wykonać procedurę standardowego przywracania bazy danych.

## <a name="migrate-from-single-db"></a>Migrowanie z pojedynczej bazy danych 

**Jak przeprowadzić migrację z Azure SQL Database jednej lub elastycznej puli do wystąpienia zarządzanego?**

Wystąpienie zarządzane oferuje te same poziomy wydajności dla wielkości zasobów obliczeniowych i magazynu, co inne opcje wdrażania Azure SQL Database. Jeśli chcesz skonsolidować dane w jednym wystąpieniu lub po prostu potrzebna jest funkcja obsługiwana wyłącznie w wystąpieniu zarządzanym, można migrować dane przy użyciu funkcji eksportu/importu (BACPAC).
