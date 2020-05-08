---
title: Omówienie wystąpienia zarządzanego SQL
description: W tym artykule opisano Azure SQL Database wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 65bce50665b6dd99662e99ca57569f906f3af208
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598542"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Co to jest Azure SQL Database wystąpienie zarządzane?

Wystąpienie zarządzane to nowa opcja wdrażania Azure SQL Database, która zapewnia niemal 100% zgodność z najnowszym SQL Server lokalnym (Enterprise Edition) aparat bazy danych, zapewniając natywną implementację [sieci wirtualnej (VNET)](../virtual-network/virtual-networks-overview.md) , która rozwiązuje typowe problemy związane z bezpieczeństwem, oraz [model biznesowy](https://azure.microsoft.com/pricing/details/sql-database/) preferowany dla klientów lokalnych SQL Server. Model wdrażania wystąpienia zarządzanego umożliwia istniejącym klientom SQL Server na podniesienie i przemieszczenie aplikacji lokalnych do chmury przy minimalnych zmianach aplikacji i baz danych. W tym samym czasie opcja wdrożenia wystąpienia zarządzanego zachowuje wszystkie możliwości PaaS (automatyczne stosowanie poprawek i aktualizacji wersji, [zautomatyzowane kopie zapasowe](sql-database-automated-backups.md), [wysoka dostępność](sql-database-high-availability.md) ), które znacząco zmniejszają koszty związane z zarządzaniem i całkowitym kosztem posiadania.

> [!IMPORTANT]
> Aby zapoznać się z listą regionów, w których opcja wdrażania wystąpienia zarządzanego jest obecnie dostępna, zobacz [Obsługiwane regiony](sql-database-managed-instance-resource-limits.md#supported-regions).

Na poniższym diagramie przedstawiono główne funkcje wystąpień zarządzanych:

![Najważniejsze funkcje](./media/sql-database-managed-instance/key-features.png)

Model wdrażania wystąpienia zarządzanego jest przeznaczony dla klientów, którzy chcą migrować dużą liczbę aplikacji z środowiska lokalnego lub IaaS, samodzielnego lub niezależnego dostawcy oprogramowania do w pełni zarządzanego środowiska chmury PaaS, z możliwie jak najniższym nakładem migracji. Korzystając z w pełni zautomatyzowanej [usługi migracji danych (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) na platformie Azure, klienci mogą podnoszenia i przesunięć SQL Server lokalnych do wystąpienia zarządzanego, które oferuje zgodność z SQL Server lokalnie i pełną izolacją wystąpień klientów dzięki natywnej obsłudze sieci wirtualnej.  Program Software Assurance umożliwia wymianę istniejących licencji dla obniżonych stawek w wystąpieniu zarządzanym przy użyciu [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Wystąpienie zarządzane to Najlepsza lokalizacja docelowa migracji w chmurze dla wystąpień SQL Server, które wymagają wysokiego poziomu zabezpieczeń i rozbudowanej powierzchni programistycznej.

Opcja wdrożenia wystąpienia zarządzanego oferuje bliską 100% zgodność obszaru powierzchni z najnowszą SQL Server wersji lokalnej w ramach planu planowanej wersji.

Aby podjąć decyzję między Azure SQL Databaseymi opcjami wdrażania: pojedyncza baza danych, baza danych w puli i wystąpienie zarządzane i SQL Server hostowana na maszynie wirtualnej, zobacz [jak wybrać odpowiednią wersję SQL Server na platformie Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Najważniejsze funkcje i możliwości

Wystąpienie zarządzane łączy najlepsze funkcje, które są dostępne zarówno w Azure SQL Database, jak i SQL Server aparacie bazy danych.

> [!IMPORTANT]
> Wystąpienie zarządzane jest uruchamiane ze wszystkimi funkcjami najnowszej wersji SQL Server, w tym operacji online, automatycznych korekt planu i innych ulepszeń wydajności przedsiębiorstwa. Porównanie dostępnych funkcji znajduje się w temacie [porównanie funkcji: Azure SQL Database i SQL Server](sql-database-features.md).

| **Korzyści z PaaS** | **Ciągłość działalności biznesowej** |
| --- | --- |
|Bez kupowania i zarządzania sprzętem <br>Brak obciążeń związanych z zarządzaniem podstawową infrastrukturą <br>Szybka obsługa i skalowanie usług <br>Automatyczne stosowanie poprawek i uaktualnianie wersji <br>Integracja z innymi usługami danych PaaS |Umowa SLA na 99,99% czasu  <br>Wbudowana [wysoka dostępność](sql-database-high-availability.md) <br>Dane chronione za pomocą [zautomatyzowanych kopii zapasowych](sql-database-automated-backups.md) <br>Okres przechowywania kopii zapasowych konfigurowalnych przez klienta <br>[Kopie zapasowe](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) inicjowane przez użytkownika <br>Możliwość [przywracania bazy danych do punktu w czasie](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Zabezpieczenia i zgodność** | **Zarządzanie**|
|Środowisko izolowane (Integracja z siecią[wirtualną](sql-database-managed-instance-connectivity-architecture.md), usługa pojedynczej dzierżawy, dedykowane zasoby obliczeniowe i magazyn) <br>[Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Uwierzytelnianie usługi Azure AD](sql-database-aad-authentication.md), obsługa logowania jednokrotnego <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Nazwy główne serwera usługi Azure AD (logowania)</a>  <br>Zgodność ze standardami zgodności, takimi jak usługa Azure SQL Database <br>[Inspekcja SQL](sql-database-managed-instance-auditing.md) <br>[Zaawansowana ochrona przed zagrożeniami](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager interfejs API do automatyzowania aprowizacji i skalowania usługi <br>Azure Portal funkcje ręcznego inicjowania obsługi i skalowania usługi <br>Usługa Data Migration Service

> [!IMPORTANT]
> Azure SQL Database (wszystkie opcje wdrażania) mają certyfikat dla wielu standardów zgodności. Aby uzyskać więcej informacji, zobacz [Centrum zaufania Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , w którym można znaleźć najbardziej aktualną listę SQL Database certyfikatów zgodności.

W poniższej tabeli przedstawiono najważniejsze funkcje wystąpień zarządzanych:

|Funkcja | Opis|
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
| Wbudowana usługa integracji (SSIS) | Nie — SSIS jest częścią [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Wbudowana usługa analizy (SSAS) | Nie — SSAS jest osobnym [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Wbudowana usługa raportowania (SSRS) | Nie używaj raportów z podziałem na strony [Power BI](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) lub HOSTOWANIE usług SSRS na maszynie wirtualnej platformy Azure. Chociaż zarządzane wystąpienie nie może uruchamiać usług SSRS jako usługi, może hostować bazy danych wykazu usług SSRS 2019 dla zewnętrznego serwera raportowania przy użyciu uwierzytelniania SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

[Model zakupu oparty na rdzeń wirtualny](sql-database-service-tiers-vcore.md) dla wystąpień zarządzanych zapewnia elastyczność, kontrolę, przejrzystość i prostą metodę tłumaczenia lokalnych wymagań obciążeń do chmury. Ten model umożliwia zmianę zasobów obliczeniowych, pamięci i magazynu w zależności od potrzeb związanych z obciążeniem. Model rdzeń wirtualny jest również uprawniony do 55% oszczędności przy użyciu [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

W modelu rdzeń wirtualny można wybrać między generacjami sprzętu.

- **Obliczenia** Logiczne procesory CPU bazują na procesorach Intel E5-2673 v3 (Haswell) 2,4-GHz, podłączonym SSD, rdzeniach fizycznych, 7 GB pamięci RAM na rdzeń i rozmiarach obliczeniowych od 8 do 24 rdzeni wirtualnych.
- **5 rdzeń** Logiczne procesory CPU są oparte na procesorach Intel E5-2673 v4 (Broadwell) 2,3-GHz i Intel SP-8160 (Skylake), szybkim SSD interfejsu NVMe, rdzeni logicznej funkcji Hyper-threaded i rozmiarach obliczeniowych z zakresu od 4 do 80 rdzeni.

Znajdź więcej informacji na temat różnic między generacjami sprzętowymi w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Warstwy usługi wystąpienia zarządzanego

Wystąpienie zarządzane jest dostępne w dwóch warstwach usług:

- **Ogólnego przeznaczenia**: zaprojektowana dla aplikacji z typowymi wymaganiami dotyczącymi wydajności i opóźnień we/wy.
- **Krytyczne**dla działania firmy: przeznaczone dla aplikacji z niskimi wymaganiami dotyczącymi opóźnień we/wy i minimalnym wpływem podstawowych operacji konserwacyjnych w obciążeniu.

Obie warstwy usług gwarantują dostępność na 99,99% i umożliwiają niezależne Wybieranie rozmiaru magazynu i pojemności obliczeniowej. Aby uzyskać więcej informacji na temat architektury Azure SQL Database o wysokiej dostępności, zobacz [wysoka dostępność i Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Warstwa usługi ogólnego przeznaczenia

Na poniższej liście opisano kluczowe cechy warstwy usługi Ogólnego przeznaczenia:

- Projektowanie dla większości aplikacji branżowych z typowymi wymaganiami dotyczącymi wydajności
- Wysoce wydajny magazyn obiektów blob platformy Azure (8 TB)
- Wbudowana [wysoka dostępność](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) oparta na niezawodnej usłudze Azure Blob Storage i [platformie Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Aby uzyskać więcej informacji, zobacz temat [warstwa magazynowania w warstwie ogólnego przeznaczenia](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) i [najlepsze praktyki dotyczące wydajności magazynu oraz zagadnienia dotyczące wystąpień zarządzanych (ogólnego przeznaczenia)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Znajdź więcej informacji o różnicach między warstwami usług w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Krytyczne dla działania firmy warstwy usług

Krytyczne dla działania firmy warstwa usług jest tworzona dla aplikacji o wysokim poziomie operacji we/wy. Zapewnia największą odporność na błędy przy użyciu kilku izolowanych replik.

Na poniższej liście przedstawiono kluczowe cechy warstwy usługi Krytyczne dla działania firmy:

- Przeznaczone dla aplikacji firmowych o najwyższej wydajności i wymaganiach dotyczących wysokiej dostępności
- Jest dostarczany z bardzo szybkim lokalnym magazynem SSD (do 1 TB w systemie obliczenia i do 4 TB na 5 rdzeń)
- Wbudowana [wysoka dostępność](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) na podstawie [zawsze dostępnych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) i [platformy Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Wbudowana dodatkowa [replika bazy danych tylko do odczytu](sql-database-read-scale-out.md) , która może być używana do raportowania i innych obciążeń tylko do odczytu
- [OLTP w pamięci](sql-database-in-memory.md) , który może być używany do obciążeń z wymaganiami o wysokiej wydajności  

Znajdź więcej informacji o różnicach między warstwami usług w [limitach zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operacje zarządzania wystąpieniami zarządzanymi

Usługa Azure SQL Database udostępnia operacje zarządzania, których można używać do automatycznego wdrażania nowych wystąpień zarządzanych, aktualizowania właściwości wystąpień i usuwania wystąpień, gdy nie są już potrzebne. Ta sekcja zawiera informacje na temat operacji zarządzania i ich typowych czasów trwania.

Aby obsługiwać [wdrożenia w ramach usługi Azure Virtual Networks (sieci wirtualnych)](../virtual-network/virtual-network-for-azure-services.md) i zapewnić izolację i bezpieczeństwo klientów, zarządzane wystąpienie korzysta z [klastrów wirtualnych](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), które reprezentują dedykowany zestaw izolowanych maszyn wirtualnych wdrożonych w podsieci sieci wirtualnej klienta. Zasadniczo każde wdrożenie wystąpienia zarządzanego w pustej podsieci powoduje wystąpienie nowego klastra wirtualnego buildout.

Kolejne operacje na wdrożonych wystąpieniach zarządzanych mogą również mieć wpływ na jego źródłowy klaster wirtualny. Ma to wpływ na czas trwania operacji zarządzania, ponieważ wdrożenie dodatkowych maszyn wirtualnych wiąże się z obciążeniem, które należy wziąć pod uwagę podczas planowania nowych wdrożeń lub aktualizacji istniejących wystąpień zarządzanych.

Wszystkie operacje zarządzania można klasyfikować w następujący sposób:

- Wdrożenie wystąpienia (nowe wystąpienie). 
- Aktualizacja wystąpienia (zmiana właściwości wystąpienia, takich jak rdzeni wirtualnych lub zarezerwowany magazyn).
- Usuwanie wystąpienia.

Zazwyczaj operacje w klastrach wirtualnych trwają najdłużej. Czas trwania operacji na klastrach wirtualnych jest różny — poniżej znajdują się wartości, których można zwykle oczekiwać na podstawie istniejących danych telemetrycznych usługi:

- Tworzenie klastra wirtualnego. Jest to synchroniczny krok operacji zarządzania wystąpieniami. **90% operacji zakończonych w ciągu 4 godzin**.
- Zmienianie rozmiaru klastra wirtualnego (rozszerzanie lub zmniejszanie). Rozszerzanie jest krokiem synchronicznym, podczas gdy zmniejszanie jest wykonywane asynchronicznie (bez wpływu na czas trwania operacji zarządzania wystąpieniami). **90% rozszerzeń klastra kończy się w czasie krótszym niż 2,5 godzin**.
- Usuwanie klastra wirtualnego. Usuwanie jest krokiem asynchronicznym, ale można go również [zainicjować ręcznie](sql-database-managed-instance-delete-virtual-cluster.md) w pustym klastrze wirtualnym, w tym przypadku jest wykonywane synchronicznie. **90% usunięć klastrów wirtualnych kończy się w 1,5 godz**.

Ponadto Zarządzanie wystąpieniami może również obejmować jedną z operacji w hostowanych bazach danych, co powoduje dłuższe czasy trwania:

- Dołączanie plików bazy danych z usługi Azure Storage. Jest to synchroniczny krok, taki jak COMPUTE (rdzeń wirtualny) lub skalowanie w górę lub w dół w warstwie usług Ogólnego przeznaczenia. **90% tych operacji kończy się za 5 minut**.
- Umieszczanie w zawsze włączonym rozrzutu grupy dostępności. Jest to synchroniczny krok, taki jak COMPUTE (rdzeń wirtualny) lub skalowanie magazynu w warstwie usług Krytyczne dla działania firmy oraz zmiana warstwy usług z Ogólnego przeznaczenia na Krytyczne dla działania firmy (lub odwrotnie). Czas trwania tej operacji jest proporcjonalny do całkowitego rozmiaru bazy danych, a także bieżącego działania bazy danych (liczba aktywnych transakcji). Działanie bazy danych podczas aktualizowania wystąpienia może wprowadzić znaczącą wariancję do całkowitego czasu trwania. **90% tych operacji wykonywanych o godz. 220 GB/godz. lub wyższych**.

W poniższej tabeli zestawiono operacje i typowe ogólne czasy trwania:

|Kategoria  |Operacja  |Segment długotrwały  |Szacowany czas trwania  |
|---------|---------|---------|---------|
|**Wdrożenie** |Pierwsze wystąpienie w pustej podsieci|Tworzenie klastra wirtualnego|90% operacji zakończonych w ciągu 4 godzin|
|Wdrożenie |Pierwsze wystąpienie innej generacji sprzętu w niepustej podsieci (na przykład pierwsze wystąpienie generacji 5 w podsieci z wystąpieniami generacji 4)|Tworzenie klastra wirtualnego *|90% operacji zakończonych w ciągu 4 godzin|
|Wdrożenie |Pierwsze utworzenie wystąpienia 4 rdzeni wirtualnych w pustej lub niepustej podsieci|Tworzenie klastra wirtualnego * *|90% operacji zakończonych w ciągu 4 godzin|
|Wdrożenie |Kolejne Tworzenie wystąpienia w niepustej podsieci (drugi, trzeci itp. wystąpienie)|Zmienianie rozmiarów klastra wirtualnego|90% operacji zakończonych w ciągu 2,5 godzin|
|**Aktualizacja** |Zmiana właściwości wystąpienia (hasło administratora, logowanie w usłudze AAD, flaga Korzyść użycia hybrydowego platformy Azure)|Brak|Do 1 minuty|
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia warstwy usług)|Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 5 minut|
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy warstwy usług)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.)|
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia)|— Zmienianie rozmiarów klastra wirtualnego<br>— Dołączanie plików bazy danych|90% operacji zakończonych w ciągu 2,5 godzin|
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.)|
|Aktualizowanie |Skalowanie wystąpienia w dół do 4 rdzeni wirtualnych (Ogólnego przeznaczenia)|— Zmienianie rozmiarów klastra wirtualnego (jeśli jest wykonywane po raz pierwszy, może to wymagać utworzenia klastra wirtualnego * *)<br>— Dołączanie plików bazy danych|90% operacji zakończonych w 4 h 5 min * *|
|Aktualizowanie |Skalowanie wystąpienia w dół do 4 rdzeni wirtualnych (Krytyczne dla działania firmy)|— Zmienianie rozmiarów klastra wirtualnego (jeśli jest wykonywane po raz pierwszy, może to wymagać utworzenia klastra wirtualnego * *)<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 4 godzin od czasu do wypełniania wszystkich baz danych (220 GB/godz.)|
|Aktualizowanie |Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót)|— Zmienianie rozmiarów klastra wirtualnego<br>-Zawsze włączone Określanie rozrzutu grupy dostępności|90% operacji zakończonych w ciągu 2,5 godzin + Time do wypełniania wszystkich baz danych (220 GB/godz.)|
|**Usunięcie**|Usunięcie wystąpienia|Kopia zapasowa dziennika dla wszystkich baz danych|90% operacji zakończonych w ciągu do 1 minuty.<br>Uwaga: Jeśli ostatnie wystąpienie w podsieci zostanie usunięte, ta operacja spowoduje zaplanowanie usunięcia klastra wirtualnego po upływie 12 godzin * * * *|
|Usunięcie|Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika)|Usuwanie klastra wirtualnego|90% operacji zakończonych w maksymalnie 1,5 godzinach|

\*Klaster wirtualny jest oparty na generowaniu sprzętu.

\*\*Opcja wdrożenia 4 rdzeni wirtualnych została wydana w czerwcu 2019 i wymaga nowej wersji klastra wirtualnego. Jeśli wystąpiły wystąpienia w podsieci docelowej, które zostały utworzone przed 12 czerwca, nowy klaster wirtualny zostanie automatycznie wdrożony do rdzeń wirtualny wystąpień hosta 4.

\*\*\*Bieżąca konfiguracja to 12 godzin, ale może ona ulec zmianie w przyszłości, dlatego nie należy na niej korzystać. Jeśli musisz usunąć klaster wirtualny wcześniej (aby zwolnić podsieć na przykład), zobacz [usuwanie podsieci po usunięciu Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management-operations"></a>Dostępność wystąpienia podczas operacji zarządzania

Wystąpienie zarządzane nie jest dostępne dla aplikacji klienckich podczas operacji wdrażania i usuwania.

Wystąpienie zarządzane jest dostępne podczas operacji aktualizacji, z wyjątkiem krótkich przestojów spowodowanych przez pracę w trybie failover, które są wykonywane po zakończeniu aktualizacji. Zwykle trwa to 10 sekund nawet w przypadku przerwanych długotrwałych transakcji, dzięki czemu możliwe jest [szybsze odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md).

> [!TIP]
> Aktualizacja zarezerwowanego miejsca do magazynowania w Ogólnego przeznaczenia warstwie usług nie wiąże się z trybem failover ani nie wpływa na dostępność wystąpienia.

> [!IMPORTANT]
> Nie zaleca się skalowania zasobów obliczeniowych i magazynu Azure SQL Database wystąpienia zarządzanego lub zmiany warstwy usług w tym samym czasie z długotrwałymi transakcjami (Importowanie danych, zadania przetwarzania danych, ponowne kompilowanie indeksu itp.). Praca w trybie failover bazy danych, która zostanie wykonana na końcu operacji, spowoduje anulowanie wszystkich bieżących transakcji.


### <a name="management-operations-cross-impact"></a>Wpływ operacji zarządzania

Operacje zarządzania wystąpieniami zarządzanymi mogą mieć wpływ na inne operacje zarządzania wystąpieniami znajdującymi się w tym samym klastrze wirtualnym. Obejmuje to następujące elementy:

- **Długotrwałe operacje przywracania** w klastrze wirtualnym zostaną wstrzymane w ramach innych operacji tworzenia wystąpienia lub skalowania w tej samej podsieci.<br/>**Przykład:** Jeśli masz długotrwałą operację przywracania i istnieje żądanie utworzenia lub skalowania w tej samej podsieci, żądanie będzie trwać dłużej, ponieważ będzie oczekiwać na ukończenie operacji przywracania przed kontynuowaniem.
    
- **Kolejne operacje tworzenia wystąpienia lub skalowania** są umieszczane w blokadzie przez poprzednio zainicjowane wystąpienie lub skalowanie wystąpienia, które zainicjowało zmianę rozmiaru klastra wirtualnego.<br/>**Przykład:** Jeśli istnieje wiele żądań tworzenia i/lub skalowania w tej samej podsieci w ramach tego samego klastra wirtualnego, a jedna z nich inicjuje zmianę rozmiaru klastra wirtualnego, wszystkie żądania, które zostały przesłane 5 minut po tym, że zmiana rozmiaru klastra wirtualnego będzie trwać dłużej niż oczekiwano, ponieważ te żądania będą musiały upłynąć przed wznowieniem.

- **Operacje tworzenia/skalowania przesłane w 5-minutowych oknach** zostaną wykonane w partii i wykonywane równolegle.<br/>**Przykład:** Dla wszystkich operacji przesyłanych w oknie 5-minutowe zostanie wykonane tylko jedno Zmienianie rozmiaru klastra wirtualnego (mierzenie od momentu wykonania pierwszego żądania operacji). Jeśli kolejne żądanie zostało przesłane więcej niż 5 minut po przesłaniu pierwszego z nich, czeka na ukończenie zmiany rozmiaru klastra wirtualnego przed rozpoczęciem wykonywania.

> [!IMPORTANT]
> Operacje zarządzania, które zostały wstrzymane, ponieważ inna operacja, która jest w toku, zostanie automatycznie wznowiona po spełnieniu warunków do kontynuowania. Nie trzeba wykonywać żadnych czynności przez użytkownika w celu wznowienia tymczasowej operacji zarządzania.

### <a name="canceling-management-operations"></a>Anulowanie operacji zarządzania

Poniższa tabela zawiera podsumowanie możliwości anulowania określonych operacji zarządzania i typowych ogólnych czasów trwania:

Kategoria  |Operacja  |Można anulować  |Szacowany czas trwania anulowania  |
|---------|---------|---------|---------|
|Wdrożenie |Tworzenie wystąpienia |Nie |  |
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Ogólnego przeznaczenia) |Nie |  |
|Aktualizowanie |Skalowanie magazynu wystąpień w górę/w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut |
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Ogólnego przeznaczenia) |Tak |90% operacji zakończonych w ciągu 5 minut |
|Aktualizowanie |Skalowanie wystąpienia obliczeniowego (rdzeni wirtualnych) w górę i w dół (Krytyczne dla działania firmy) |Tak |90% operacji zakończonych w ciągu 5 minut |
|Aktualizowanie |Zmiana warstwy usługi wystąpienia (Ogólnego przeznaczenia do Krytyczne dla działania firmy i na odwrót) |Tak |90% operacji zakończonych w ciągu 5 minut |
|Usuń |Usunięcie wystąpienia |Nie |  |
|Usuń |Usuwanie klastra wirtualnego (jako operacja zainicjowana przez użytkownika) |Nie |  |

Aby anulować operację zarządzania, przejdź do bloku przegląd i kliknij pole powiadomień o trwającej operacji. Po prawej stronie pojawi się ekran z trwającą operacją i zostanie wyświetlony przycisk anulowania operacji. Po pierwszym kliknięciu zostanie wyświetlony monit o ponowne kliknięcie i potwierdzenie, że chcesz anulować operację.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Gdy żądanie anulowania zostało przesłane i przetworzone, otrzymasz powiadomienie w przypadku pomyślnego przesłania żądania anulowania. 

W przypadku powodzenia anulowania operacja zarządzania zostanie anulowana w ciągu kilku minut z powodu błędu.

![Anulowanie wyniku operacji](./media/sql-database-managed-instance/canceling-operation-result.png)

Jeśli żądanie anulowania nie powiedzie się lub przycisk Anuluj nie jest aktywny, oznacza to, że operacja zarządzania nie jest w stanie anulowania i zostanie zakończona w ciągu kilku minut. Operacja zarządzania będzie kontynuować wykonywanie, dopóki nie zostanie ukończona.

> [!IMPORTANT]
> Anulowanie operacji jest obecnie obsługiwane tylko w portalu.

## <a name="advanced-security-and-compliance"></a>Zaawansowane zabezpieczenia i zgodność

Opcja wdrażania wystąpienia zarządzanego łączy zaawansowane funkcje zabezpieczeń zapewniane przez usługę Azure Cloud i aparat bazy danych SQL Server.

### <a name="managed-instance-security-isolation"></a>Izolacja zabezpieczeń wystąpienia zarządzanego

Wystąpienie zarządzane zapewnia dodatkową izolację zabezpieczeń od innych dzierżawców w chmurze platformy Azure. Izolacja zabezpieczeń obejmuje:

- [Implementacja natywnej sieci wirtualnej](sql-database-managed-instance-connectivity-architecture.md) i łączność ze środowiskiem lokalnym za pomocą usługi Azure Express Route lub VPN Gateway.
- W domyślnym wdrożeniu punkt końcowy SQL jest udostępniany tylko za pośrednictwem prywatnego adresu IP, umożliwiając bezpieczną łączność z prywatnych platform Azure lub sieci hybrydowych.
- Pojedyncza dzierżawa z dedykowaną podstawową infrastrukturą (COMPUTE, Storage).

Na poniższym diagramie przedstawiono różne opcje łączności dla aplikacji:

![Wysoka dostępność](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Aby dowiedzieć się więcej na temat integracji sieci wirtualnej i wymuszania zasad sieciowych na poziomie podsieci, zobacz [Architektura sieci wirtualnej dla wystąpień zarządzanych](sql-database-managed-instance-connectivity-architecture.md) i [Połącz aplikację z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Umieść wiele wystąpień zarządzanych w tej samej podsieci, wszędzie tam, gdzie jest to dozwolone w wymaganiach dotyczących zabezpieczeń, co spowoduje dodatkowe korzyści. Rozmieszczenie wystąpień w tej samej podsieci znacznie upraszcza konserwację infrastruktury sieciowej i zmniejsza czas aprowizacji wystąpienia, ponieważ długi czas aprowizacji jest skojarzony z kosztem wdrożenia pierwszego wystąpienia zarządzanego w podsieci.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database funkcje zabezpieczeń

Azure SQL Database udostępnia zestaw zaawansowanych funkcji zabezpieczeń, których można użyć do ochrony danych.

- [Inspekcja wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) śledzi zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji umieszczonym na koncie usługi Azure Storage. Inspekcja pomaga zachować zgodność z przepisami, zrozumieć aktywność bazy danych oraz uzyskać wgląd w niezgodności i anomalie, które mogą wskazywać na problemy biznesowe lub podejrzane naruszenia zabezpieczeń.
- Szyfrowanie danych w ruchu — wystąpienie zarządzane zabezpiecza dane, zapewniając szyfrowanie danych w ruchu przy użyciu Transport Layer Security. Oprócz zabezpieczeń warstwy transportu opcja wdrażania wystąpienia zarządzanego oferuje ochronę poufnych danych w locie, w czasie spoczynku i podczas przetwarzania zapytań przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkcja Always Encrypted to pierwsze w branży rozwiązanie, które oferuje bezkonkurencyjne zabezpieczenie danych przed naruszeniami, w tym przed kradzieżą danych o kluczowym znaczeniu. Na przykład w przypadku Always Encrypted numery kart kredytowych są przechowywane w bazie danych zawsze, nawet podczas przetwarzania zapytań, umożliwiając odszyfrowywanie w punkcie użytkowania przez autoryzowanego pracownika lub aplikacje, które muszą przetwarzać te dane.
- [Zaawansowana ochrona przed zagrożeniami](sql-database-managed-instance-threat-detection.md) uzupełnia [inspekcję](sql-database-managed-instance-auditing.md) , zapewniając dodatkową warstwę analizy zabezpieczeń wbudowaną w usługę, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Otrzymasz alerty o podejrzanych działaniach, potencjalnych lukach w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych. Alerty zaawansowanej ochrony przed zagrożeniami można przeglądać z poziomu [Azure Security Center](https://azure.microsoft.com/services/security-center/) i zapewniać szczegółowe informacje o podejrzanych działaniach i zalecać podejmowanie działań w celu zbadania i ograniczenia zagrożenia.  
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) ogranicza narażenie na dane poufne przez zamaskowanie ich dla użytkowników bez uprawnień. Dynamiczne maskowanie danych ułatwia Zapobieganie nieautoryzowanemu dostępowi do poufnych danych przez umożliwienie wyznaczenia ilości poufnych danych do ujawnienia przy minimalnym wpływie na warstwę aplikacji. Jest to funkcja zabezpieczeń oparta na zasadach, która ukrywa dane poufne w zestawie wyników zapytania w wyznaczonych polach bazy danych, podczas gdy dane w bazie danych nie są zmieniane.
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwiają kontrolowanie dostępu do wierszy w tabeli bazy danych na podstawie właściwości użytkownika wykonującego zapytanie (na przykład według członkostwa w grupie lub kontekstu wykonania). Zabezpieczenia na poziomie wiersza (RLS, Row-Level Security) upraszczają projektowanie i kodowanie zabezpieczeń w aplikacji. Zabezpieczenia na poziomie wiersza umożliwiają zaimplementowanie ograniczeń w dostępie do wiersza danych. Na przykład zapewnienie pracownikom dostępu tylko do wierszy danych, które są istotne dla działu, lub ograniczenia dostępu do danych tylko do odpowiednich danych.
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) szyfruje pliki danych wystąpienia zarządzanego, znane jako szyfrowanie przechowywanych danych. TDE wykonuje szyfrowanie we/wy czasu rzeczywistego i odszyfrowywanie plików danych i dzienników. Szyfrowanie używa klucza szyfrowania bazy danych, który jest przechowywany w rekordzie rozruchowym bazy danych w celu zapewnienia dostępności podczas odzyskiwania. Można chronić wszystkie bazy danych w wystąpieniu zarządzanym z przezroczystym szyfrowaniem danych. TDE SQL Server to sprawdzona technologia szyfrowania w technologii REST, która jest wymagana przez wiele standardów zgodności, aby chronić przed kradzieżą nośników magazynu.

Migracja zaszyfrowanej bazy danych do wystąpienia zarządzanego jest obsługiwana za pośrednictwem Azure Database Migration Service (DMS) lub przywracania natywnego. Jeśli planujesz migrację zaszyfrowanej bazy danych przy użyciu funkcji przywracania natywnego, migracja istniejącego certyfikatu TDE z SQL Server lokalnego lub SQL Server na maszynie wirtualnej do wystąpienia zarządzanego jest wymagana. Aby uzyskać więcej informacji na temat opcji migracji, zobacz [SQL Server migracji wystąpień do wystąpienia zarządzanego](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integracja Azure Active Directory

Opcja wdrażania wystąpienia zarządzanego obsługuje tradycyjne logowania aparatu bazy danych programu SQL Server i logowania zintegrowane z usługą Azure Active Directory (AAD). Nazwy główne serwera usługi Azure AD (dane logowania) (**publiczna wersja zapoznawcza**) są lokalnymi nazwami logowania w chmurze w systemie Azure, które są używane w środowisku lokalnym. Nazwy główne serwera usługi Azure AD umożliwiają określanie użytkowników i grup z dzierżawy Azure Active Directory jako wystąpień głównych o zakresie wystąpienia, które mogą wykonywać wszystkie operacje na poziomie wystąpienia, w tym zapytania między bazami danych w ramach tego samego wystąpienia zarządzanego.

Wprowadzono nową składnię do tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania) **od dostawcy zewnętrznego**. Aby uzyskać więcej informacji na temat składni, zobacz <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Tworzenie nazwy logowania</a>i zapoznaj się z tematem [inicjowanie obsługi administracyjnej Azure Active Directory administratora wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Opcja wdrożenia wystąpienia zarządzanego umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft z [integracją Azure Active Directory](sql-database-aad-authentication.md). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory obsługuje [uwierzytelnianie wieloskładnikowe](sql-database-ssms-mfa-authentication-configure.md) (MFA, Multi-Factor Authentication) w celu zwiększenia bezpieczeństwa danych i aplikacji, korzystając z procesu jednokrotnego logowania.

### <a name="authentication"></a>Authentication

Uwierzytelnianie wystąpienia zarządzanego dotyczy sposobu, w jaki użytkownicy udowadniają swoją tożsamość podczas łączenia się z bazą danych. Usługa SQL Database obsługuje dwa typy uwierzytelniania:  

- **Uwierzytelnianie SQL**:

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.
- **Uwierzytelnianie Azure Active Directory**:

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez Azure Active Directory i jest obsługiwana w przypadku domen zarządzanych i zintegrowanych. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w ramach Azure SQL Database i jest kontrolowane przez członkostwo w roli bazy danych konta użytkownika i uprawnienia na poziomie obiektów. Wystąpienie zarządzane ma takie same możliwości autoryzacji jak SQL Server 2017.

## <a name="database-migration"></a>Migracja bazy danych

Opcja wdrażania wystąpienia zarządzanego kieruje scenariusze użytkownika z migracją masowej bazy danych z implementacji lokalnych lub IaaS baz danych. Wystąpienie zarządzane obsługuje kilka opcji migracji bazy danych:

### <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie  

Podejście do migracji wykorzystuje kopie zapasowe SQL do usługi Azure Blob Storage. Kopie zapasowe przechowywane w usłudze Azure Storage BLOB mogą być przywracane bezpośrednio do wystąpienia zarządzanego przy użyciu [polecenia przywracania T-SQL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak przywrócić plik kopii zapasowej o szerokim świecie standardowym, zobacz [przywracanie pliku kopii zapasowej do wystąpienia zarządzanego](sql-database-managed-instance-get-started-restore.md). W tym przewodniku szybki start przedstawiono, jak przesłać plik kopii zapasowej do usługi Azure Blob Storage i zabezpieczyć go przy użyciu klucza sygnatury dostępu współdzielonego (SAS).
- Aby uzyskać informacje o przywracaniu z adresu URL, zobacz [natywne przywracanie z adresu URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Kopie zapasowe z wystąpienia zarządzanego można przywrócić tylko do innego wystąpienia zarządzanego. Nie można ich przywrócić do SQL Server lokalnego lub pojedynczej bazy danych/elastycznej puli.

### <a name="data-migration-service"></a>Usługa Data Migration Service

Azure Database Migration Service to w pełni zarządzana usługa, która umożliwia bezproblemowe Migrowanie z wielu źródeł baz danych do platform danych platformy Azure z minimalnym czasem przestoju. Ta usługa usprawnia zadania wymagane do przeniesienia istniejących baz danych innych firm i SQL Server do Azure SQL Database (pojedyncze bazy danych, pule baz danych w pulach elastycznych i wystąpienia baz danych w wystąpieniu zarządzanym) oraz SQL Server na maszynie wirtualnej platformy Azure. Zobacz [, jak migrować lokalną bazę danych do wystąpienia zarządzanego przy użyciu usługi DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Obsługiwane funkcje SQL

Opcja wdrażania wystąpienia zarządzanego pozwala zapewnić bliską 100% zgodność obszaru powierzchni z lokalnymi SQL Servermi w fazie do momentu ogólnego udostępnienia usługi. Aby uzyskać listę funkcji i porównywania, zobacz [SQL Database porównanie funkcji](sql-database-features.md)i listę różnic t-SQL w wystąpieniach zarządzanych i SQL Server, zobacz temat [różnice w języku t-SQL wystąpienia zarządzanego w SQL Server](sql-database-managed-instance-transact-sql-information.md).

Opcja wdrażania wystąpienia zarządzanego obsługuje zgodność z poprzednimi wersjami z bazami danych SQL 2008. Obsługiwana jest bezpośrednia migracja z serwerów baz danych SQL 2005. poziom zgodności dla zmigrowanych baz danych SQL 2005 został zaktualizowany do wersji SQL 2008.
  
Na poniższym diagramie przedstawiono zgodność obszaru powierzchni w wystąpieniu zarządzanym:  

![migracji](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Kluczowe różnice między SQL Server lokalnymi i w wystąpieniu zarządzanym

Opcja wdrażania wystąpienia zarządzanego oferuje zawsze aktualność w chmurze, co oznacza, że niektóre funkcje w SQL Server lokalnym mogą być przestarzałe, wycofane lub mieć alternatywy. Istnieją określone przypadki, w których narzędzia muszą rozpoznać, że określona funkcja działa w nieco inny sposób lub że usługa działa w środowisku, w którym nie jest w pełni kontrolowany:

- Wysoka dostępność jest wbudowana i wstępnie skonfigurowana przy użyciu technologii podobnej do [zawsze dostępnych grup dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatyczne kopie zapasowe i przywracanie do punktu w czasie. Klient może inicjować `copy-only` kopie zapasowe, które nie zakłócają automatycznego łańcucha kopii zapasowych.
- Wystąpienie zarządzane nie pozwala na określanie pełnych ścieżek fizycznych, więc wszystkie odpowiednie scenariusze muszą być obsługiwane inaczej: instrukcja RESTORE DB nie obsługuje funkcji MOVE, tworzenie bazy danych nie zezwala na ścieżki fizyczne, BULK INSERT działa tylko z obiektami blob platformy Azure itd.
- Wystąpienie zarządzane obsługuje [uwierzytelnianie w usłudze Azure AD](sql-database-aad-authentication.md) jako alternatywę dla uwierzytelniania systemu Windows w chmurze.
- Wystąpienie zarządzane automatycznie zarządza grupą plików i plikami XTP dla baz danych zawierających obiekty OLTP w pamięci
- Wystąpienie zarządzane obsługuje SQL Server Integration Services (SSIS) i może hostować wykaz usług SSIS (SSISDB), który przechowuje pakiety SSIS, ale są wykonywane na zarządzanym Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF), zobacz [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Aby porównać funkcje usług SSIS w SQL Database, zobacz [porównanie Azure SQL Database pojedynczej bazy danych, elastycznej puli i wystąpienia zarządzanego](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Funkcje administrowania wystąpieniem zarządzanym

Opcja wdrożenia wystąpienia zarządzanego pozwala administratorowi systemu poświęcać mniej czasu na zadania administracyjne, ponieważ usługa SQL Database wykonuje je dla Ciebie lub znacznie upraszcza te zadania. Na przykład [Instalacja systemu operacyjnego/RDBMS i stosowanie poprawek](sql-database-high-availability.md), [dynamiczne zmienianie rozmiarów i konfiguracji](sql-database-single-database-scale.md), [kopie zapasowe](sql-database-automated-backups.md), [Replikacja bazy danych](replication-with-sql-database-managed-instance.md) (w tym systemowe bazy danych), [Konfiguracja wysokiej dostępności](sql-database-high-availability.md)oraz konfiguracja strumieni danych monitorowania kondycji i [wydajności](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> Listę obsługiwanych, częściowo obsługiwanych i nieobsługiwanych funkcji można znaleźć w temacie [SQL Database Features](sql-database-features.md). Aby zapoznać się z listą różnic T-SQL w wystąpieniach zarządzanych i SQL Server, zobacz [różnice w wystąpieniu zarządzanym t-SQL z SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programowo zidentyfikować wystąpienie zarządzane

W poniższej tabeli przedstawiono kilka właściwości, które są dostępne za pomocą języka Transact SQL, za pomocą których można wykryć, że aplikacja pracuje z wystąpieniem zarządzanym i pobrać ważne właściwości.

|Właściwość|Wartość|Komentarz|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) — 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ta wartość jest taka sama jak w SQL Database. Nie **oznacza to,** że aparat SQL w wersji 12 (SQL Server 2014). W przypadku wystąpienia zarządzanego zawsze jest uruchomiona najnowsza stabilna wersja aparatu SQL, która jest równa lub większa niż najnowsza dostępna wersja RTM programu SQL Server.  |
|`SERVERPROPERTY ('Edition')`|Usługi SQL Azure|Ta wartość jest taka sama jak w SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Ta wartość jednoznacznie identyfikuje wystąpienie zarządzane.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nazwa DNS pełnej instancji w następującym formacie:`<instanceName>`. `<dnsPrefix>`. Database.Windows.NET, gdzie `<instanceName>` jest nazwą dostarczoną przez klienta, podczas `<dnsPrefix>` gdy jest automatycznie generowana część nazwy gwarantującej unikalność globalnej nazwy DNS (na przykład "wcus17662feb9ce98")|Przykład: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [Przewodnik Szybki Start](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z funkcjami i listą porównania, zobacz [funkcje wspólne SQL](sql-database-features.md).
- Aby uzyskać więcej informacji na temat konfiguracji sieci wirtualnej, zobacz [Konfiguracja sieci wirtualnej wystąpienia zarządzanego](sql-database-managed-instance-connectivity-architecture.md).
- W przypadku szybkiego startu, który tworzy wystąpienie zarządzane i przywraca bazę danych z pliku kopii zapasowej, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- Aby zapoznać się z samouczkiem korzystającym z Azure Database Migration Service (DMS) do migracji, zobacz [migracja wystąpienia zarządzanego przy użyciu usługi DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Aby uzyskać zaawansowane monitorowanie wydajności bazy danych wystąpień zarządzanych przy użyciu wbudowanej analizy rozwiązywania problemów, zobacz [monitorowanie Azure SQL Database przy użyciu Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Aby uzyskać informacje o cenach, zobacz [SQL Database cenach wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/sql-database/managed/).
