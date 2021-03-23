---
title: Limity zasobów
titleSuffix: Azure SQL Managed Instance
description: Ten artykuł zawiera omówienie limitów zasobów dla wystąpienia zarządzanego usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: ee0c673027892e6d3ee55b6ec5f67a30d88be683
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780348"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Omówienie limitów zasobów usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera omówienie parametrów technicznych i limitów zasobów dla wystąpienia zarządzanego usługi Azure SQL i zawiera informacje o sposobach żądania wzrostu do tych limitów.

> [!NOTE]
> Aby uzyskać różnice w obsługiwanych funkcjach i instrukcjach języka T-SQL, zobacz temat [różnice w funkcjach](../database/features-comparison.md) i [Obsługa instrukcji języka t-SQL](transact-sql-tsql-differences-sql-server.md). Aby uzyskać ogólne różnice między warstwami usługi dla Azure SQL Database i wystąpienia zarządzanego SQL, zobacz [porównanie warstwy usług](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Charakterystyki generowania sprzętu

Wystąpienie zarządzane SQL ma cechy i limity zasobów, które są zależne od podstawowej infrastruktury i architektury. Wystąpienie zarządzane SQL można wdrożyć na dwóch generacjach sprzętu: obliczenia i 5 rdzeń. Generacji sprzętu mają różne cechy, zgodnie z opisem w poniższej tabeli:

|   | **Obliczenia** | **5 rdzeń** |
| --- | --- | --- |
| **Sprzęt** | Procesor Intel® E5-2673 v3 (Haswell) 2,4 GHz, dołączony dysk SSD rdzeń wirtualny = 1 PP (rdzeń fizyczny) | Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) i Intel® 8272CL (Kaskada Lake) 2,5 procesorów, Fast NVMe SSD, rdzeń wirtualny = 1 LP (Hyper-Thread) |
| **Liczba rdzeni wirtualnych** | 8, 16, 24 rdzeni wirtualnych | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| **Maksymalna ilość pamięci (stosunek pamięci/rdzeni)** | 7 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | 5,1 GB na rdzeń wirtualny<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| **Maksymalna ilość pamięci w In-Memory OLTP** | Limit wystąpień: 1 – 1,5 GB na rdzeń wirtualny| Limit wystąpień: 0,8 – 1,65 GB na rdzeń wirtualny |
| **Maksymalna ilość zarezerwowanego wystąpienia** |  Ogólnego przeznaczenia: 8 TB<br/>Krytyczne dla działania firmy: 1 TB | Ogólnego przeznaczenia: 8 TB<br/> Krytyczne dla działania firmy 1 TB, 2 TB lub 4 TB w zależności od liczby rdzeni |

> [!IMPORTANT]
> - Sprzęt obliczenia jest wycofywany i nie jest już dostępny w przypadku nowych wdrożeń. Wszystkie nowe wystąpienia wystąpienia zarządzanego SQL należy wdrożyć na sprzęcie 5 rdzeń.
> - Rozważ [przeniesienie wystąpienia wystąpienia zarządzanego SQL do ogólnego 5](../database/service-tiers-vcore.md) sprzętu w celu uzyskania szerszego zakresu skalowalności rdzeń wirtualny i pamięci masowej, przyspieszonej sieci, najlepszej wydajności operacji we/wy i minimalnego opóźnienia.

### <a name="in-memory-oltp-available-space"></a>Dostępne miejsce w pamięci OLTP 

Ilość miejsca OLTP w pamięci w [krytyczne dla działania firmy](../database/service-tier-business-critical.md) warstwy usług zależy od liczby rdzeni wirtualnych i generowania sprzętu. W poniższej tabeli wymieniono limity pamięci, które mogą być używane dla obiektów OLTP w pamięci.

| Przestrzeń OLTP w pamięci  | **5 rdzeń** | **Obliczenia** |
| --- | --- | --- |
| 4 rdzenie wirtualne  | 3,14 GB | |   
| 8 rdzeni wirtualnych  | 6,28 GB | 8 GB |
| 16 rdzeni wirtualnych | 15,77 GB | 20 GB |
| 24 rdzeni wirtualnych | 25,25 GB | 36 GB |
| 32 rdzeni wirtualnych | 37,94 GB | |
| 40 rdzeni wirtualnych | 52,23 GB | |
| 64 rdzeni wirtualnych | 99,9 GB    | |
| 80 rdzeni wirtualnych | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Charakterystyki warstwy usług

Wystąpienie zarządzane SQL ma dwie warstwy usług: [ogólnego przeznaczenia](../database/service-tier-general-purpose.md) i [krytyczne dla działania firmy](../database/service-tier-business-critical.md). Te warstwy zapewniają [różne możliwości](../database/service-tiers-general-purpose-business-critical.md), zgodnie z opisem w poniższej tabeli.

> [!Important]
> Krytyczne dla działania firmy warstwa usługi oferuje dodatkową wbudowaną kopię wystąpienia zarządzanego SQL (replikę pomocniczą), która może być używana do obsługi obciążeń tylko do odczytu. Jeśli możesz oddzielić zapytania odczytu i zapisu oraz zapytania tylko do odczytu/analizy/raportowania, otrzymujesz dwa razy rdzeni wirtualnych i pamięć za tę samą cenę. Replika pomocnicza może potrwać kilka sekund za wystąpieniem podstawowym, więc jest przeznaczona do odciążania obciążeń raportowania/analiz, które nie wymagają dokładnego bieżącego stanu danych. W poniższej tabeli **zapytania tylko do odczytu** są zapytania, które są wykonywane w replice pomocniczej.

| **Funkcja** | **Ogólnego przeznaczenia** | **Krytyczne dla działania firmy** |
| --- | --- | --- |
| Liczba rdzeni wirtualnych\* | Obliczenia: 8, 16, 24<br/>5 rdzeń: 4, 8, 16, 24, 32, 40, 64, 80 | Obliczenia: 8, 16, 24 <br/> 5 rdzeń: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Ta sama liczba rdzeni wirtualnych jest przeznaczona dla zapytań tylko do odczytu. |
| Maksymalna pamięć | Obliczenia: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń: 20,4 GB – 408 GB (5.1 GB/rdzeń wirtualny)<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. | Obliczenia: 56 GB – 168 GB (7GB/rdzeń wirtualny)<br/>5 rdzeń: 20,4 GB-408 GB (5.1 GB/rdzeń wirtualny) dla zapytań do odczytu i zapisu<br/>+ dodatkowe 20,4 GB – 408 GB (5.1 GB/rdzeń wirtualny) dla zapytań tylko do odczytu.<br/>Aby uzyskać więcej pamięci, Dodaj więcej rdzeni wirtualnych. |
| Maksymalny rozmiar magazynu wystąpienia (zarezerwowany) | -2 TB dla 4 rdzeni wirtualnych (tylko 5 rdzeń)<br/>-8 TB dla innych rozmiarów | Obliczenia: 1 TB <br/> 5 rdzeń <br/>-1 TB dla 4, 8, 16 rdzeni wirtualnych<br/>-2 TB przez 24 rdzeni wirtualnych<br/>-4 TB dla 32, 40, 64, 80 rdzeni wirtualnych |
| Maksymalny rozmiar bazy danych | Do aktualnie dostępnego rozmiaru wystąpienia (maksymalnie 2 TB – 8 TB w zależności od liczby rdzeni wirtualnych). | Do aktualnie dostępnego rozmiaru wystąpienia (maksymalnie 1 TB — 4 TB w zależności od liczby rdzeni wirtualnych). |
| Maksymalny rozmiar bazy danych tempDB | Ograniczone do 24 GB/rdzeń wirtualny (96 – 1 920 GB) i aktualnie dostępnego rozmiaru magazynu wystąpień.<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać więcej przestrzeni TempDB.<br/> Rozmiar pliku dziennika jest ograniczony do 120 GB.| Do aktualnie dostępnego rozmiaru magazynu wystąpień. |
| Maksymalna liczba baz danych na wystąpienie | bazy danych użytkowników 100, chyba że osiągnięto limit rozmiaru magazynu wystąpień. | bazy danych użytkowników 100, chyba że osiągnięto limit rozmiaru magazynu wystąpień. |
| Maksymalna liczba plików bazy danych na wystąpienie | Do 280, o ile nie osiągnięto rozmiaru magazynu wystąpienia lub limitu [przestrzeni dyskowej usługi Azure Premium](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) . | 32 767 plików na bazę danych, o ile nie osiągnięto limitu rozmiaru magazynu wystąpień. |
| Maksymalny rozmiar pliku danych | Ograniczone do aktualnie dostępnego rozmiaru magazynu wystąpień (maksymalnie 2 TB-8 TB) i [miejsca alokacji dysku Azure Premium Storage](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). | Ograniczone do aktualnie dostępnego rozmiaru magazynu wystąpień (do 1 TB – 4 TB). |
| Maksymalny rozmiar pliku dziennika | Ograniczone do 2 TB i aktualnie dostępnego rozmiaru magazynu wystąpień. | Ograniczone do 2 TB i aktualnie dostępnego rozmiaru magazynu wystąpień. |
| Operacje we/wy danych/dziennika (przybliżone) | Do 30-40 K operacji we/wy na wystąpienie *, 500-7500 na plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać więcej operacji we/wy na sekundę](#file-io-characteristics-in-general-purpose-tier)| 16 k-320 K (4000 IOPS/rdzeń wirtualny)<br/>Dodaj więcej rdzeni wirtualnych, aby uzyskać lepszą wydajność operacji we/wy. |
| Limit przepływności zapisu dziennika (na wystąpienie) | 3 MB/s na rdzeń wirtualny<br/>Maks 120 MB/s na wystąpienie<br/>22-65 MB/s na bazę danych<br/>\*[Zwiększ rozmiar pliku, aby uzyskać lepszą wydajność we/wy](#file-io-characteristics-in-general-purpose-tier) | 4 MB/s na rdzeń wirtualny<br/>Maks 96 MB/s |
| Przepływność danych (przybliżona) | 100 – 250 MB/s na plik<br/>\*[Zwiększ rozmiar pliku, aby uzyskać lepszą wydajność we/wy](#file-io-characteristics-in-general-purpose-tier) | Nieograniczone. |
| Opóźnienie operacji we/wy magazynu (w przybliżeniu) | 5-10 ms | 1-2 MS |
| Przetwarzanie OLTP w pamięci | Nieobsługiwane | Dostępne, [rozmiar zależy od liczby rdzeń wirtualny](#in-memory-oltp-available-space) |
| Maksymalna liczba sesji | 30000 | 30000 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) | 4\. generacji: 210 * liczba rdzeni wirtualnych + 800<br>5\. generacji: 105 * liczba rdzeni wirtualnych + 800 | 4\. generacji: 210 * liczba rdzeni wirtualnych + 800<br>5\. generacji: 105 * liczba rdzeni wirtualnych + 800 |
| [Repliki tylko do odczytu](../database/read-scale-out.md) | 0 | 1 (wliczone w cenę) |
| Izolacja obliczeniowa | 5 rdzeń nie jest obsługiwana, ponieważ wystąpienia Ogólnego przeznaczenia mogą współużytkować fizyczny sprzęt z innymi wystąpieniami<br/>Obliczenia nie jest obsługiwana z powodu wycofania|5 rdzeń<br/>-obsługiwane przez 40, 64, 80 rdzeni wirtualnych<br/>-nieobsługiwane w przypadku innych rozmiarów<br/><br/>Obliczenia nie jest obsługiwana z powodu wycofania|


Kilka dodatkowych zagadnień: 

- **Obecnie dostępny rozmiar magazynu wystąpienia** to różnica między zarezerwowanym rozmiarem wystąpienia i ilością miejsca do magazynowania.
- Rozmiar pliku danych i dziennika w bazach danych użytkownika i systemu jest uwzględniany w rozmiarze magazynu wystąpienia, który jest porównywany z maksymalnym limitem rozmiaru magazynu. Użyj widoku system [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) , aby określić łączną ilość miejsca używanego przez bazy danych. Dzienniki błędów nie są utrwalane i nie zostały uwzględnione w rozmiarze. Kopie zapasowe nie są uwzględniane w rozmiarze magazynu.
- Przepływność i operacje we/wy w warstwie Ogólnego przeznaczenia również zależą od [rozmiaru pliku](#file-io-characteristics-in-general-purpose-tier) , który nie jest jawnie ograniczony przez wystąpienie zarządzane SQL.
  Można utworzyć kolejną replikę do odczytu w innym regionie świadczenia usługi Azure przy użyciu [grup Autotryb failover](../database/auto-failover-group-configure.md)
- Maksymalna liczba operacji we/wy wystąpienia zależy od układu pliku i rozkładu obciążenia. Jeśli na przykład utworzysz 7 x 1 TB plików z maksymalną 5 K IOPS każdy i 7 małych plików (mniejszym niż 128 GB) z 500 IOPS każdy, możesz uzyskać 38500 operacji we/wy na wystąpienie (7x5000 + 7x500), jeśli obciążenie może korzystać ze wszystkich plików. Należy zauważyć, że niektóre IOPS są również używane do tworzenia kopii zapasowych.

Więcej informacji na temat [limitów zasobów w pulach wystąpień zarządzanych SQL znajduje się w tym artykule](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Charakterystyka we/wy pliku w warstwie Ogólnego przeznaczenia

W warstwie usług Ogólnego przeznaczenia każdy plik bazy danych uzyskuje dedykowane operacje we/wy na sekundę, które zależą od rozmiaru pliku. Większe pliki zwiększają liczbę operacji we/wy i przepływność. W poniższej tabeli przedstawiono charakterystykę we/wy plików bazy danych:

| Rozmiar pliku | >= 0 i <= 128 GiB | >128 i <= 512 GiB | >0,5 i <= 1 TiB    | >1 i <= 2 TiB    | >2 i <= 4 TiB | >4 i <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Liczba operacji we/wy na plik       | 500   | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Przepływność na plik | 100 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Jeśli zauważysz wysokie opóźnienia we/wy dla niektórych plików bazy danych lub widzisz, że liczba IOPS/przepływność osiąga limit, można zwiększyć wydajność, [zwiększając rozmiar pliku](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Istnieje również limit na poziomie wystąpienia maksymalnej przepływności zapisu dziennika (patrz powyżej dla wartości, np. 22 MB/s), dzięki czemu możesz nie mieć możliwości dotarcia do maksymalnego pliku w pliku dziennika, ponieważ osiągasz limit przepływności wystąpienia.

## <a name="supported-regions"></a>Obsługiwane regiony

Wystąpienie zarządzane SQL można utworzyć tylko w [obsługiwanych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane SQL w regionie, który nie jest obecnie obsługiwany, można [wysłać żądanie pomocy technicznej za pośrednictwem Azure Portal](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Obsługiwane typy subskrypcji

Wystąpienie zarządzane SQL obecnie obsługuje wdrażanie tylko w następujących typach subskrypcji:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Subskrypcje z miesięcznymi środkami platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Ograniczenia zasobów regionalnych

> [!Note]
> Aby uzyskać najnowsze informacje o dostępności regionów dla subskrypcji, najpierw [zaznacz region](https://aka.ms/sqlcapacity).

Obsługiwane typy subskrypcji mogą zawierać ograniczoną liczbę zasobów na region. Wystąpienie zarządzane SQL ma dwa domyślne limity dla regionu platformy Azure (które można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w Azure Portal w](../database/quota-increase-request.md) zależności od typu subskrypcji:

- **Limit podsieci**: Maksymalna liczba podsieci, w których wystąpienia wystąpienia zarządzanego SQL są wdrażane w jednym regionie.
- **limit jednostek rdzeń wirtualny**: Maksymalna liczba jednostek rdzeń wirtualny, które mogą zostać wdrożone we wszystkich wystąpieniach w jednym regionie. Jedna z zasad GP rdzeń wirtualny korzysta z jednej jednostki rdzeń wirtualny, a jedna z nich ma rdzeń wirtualny rdzeń wirtualny jednostek. Łączna liczba wystąpień nie jest ograniczona, o ile mieści się w limicie jednostek rdzeń wirtualny.

> [!Note]
> Te limity to ustawienia domyślne, a nie ograniczenia techniczne. Limity można zwiększyć na żądanie, tworząc specjalne [żądanie pomocy technicznej w Azure Portal,](../database/quota-increase-request.md) Jeśli potrzebujesz więcej wystąpień w bieżącym regionie. Alternatywnie można utworzyć nowe wystąpienia wystąpienia zarządzanego SQL w innym regionie świadczenia usługi Azure bez wysyłania żądań obsługi.

W poniższej tabeli przedstawiono **domyślne limity** dla obsługiwanych typów subskrypcji (domyślne limity można rozszerzyć przy użyciu żądania pomocy technicznej opisanego poniżej):

|Typ subskrypcji| Maksymalna liczba podsieci wystąpień zarządzanych przez program SQL | Maksymalna liczba jednostek rdzeń wirtualny * |
| :---| :--- | :--- |
|Płatność zgodnie z rzeczywistym użyciem|3|320|
|CSP |8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie|3|320|
|Enterprise — tworzenie i testowanie|3|320|
|EA|8 (15 w niektórych regionach * *)|960 (1440 w niektórych regionach * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional i Platformy MSDN|2|32|

\* W obszarze Planowanie wdrożeń należy wziąć pod uwagę, że warstwa usług Krytyczne dla działania firmy (BC) wymaga czterech (4) razy więcej rdzeń wirtualny pojemności niż Ogólnego przeznaczenia (GP). Na przykład: 1 GP rdzeń wirtualny = 1 rdzeń wirtualny Unit i 1 BC rdzeń wirtualny = 4 rdzeń wirtualny. Aby uprościć analizę zużycia pod kątem domyślnych limitów, należy podsumować jednostki rdzeń wirtualny we wszystkich podsieciach w regionie, w którym wdrożono wystąpienie zarządzane SQL, i porównać wyniki z limitami jednostek wystąpienia dla danego typu subskrypcji. **Maksymalna liczba jednostek rdzeń wirtualny** dotyczy każdej subskrypcji w regionie. Nie ma żadnego limitu dla poszczególnych podsieci, z tą różnicą, że suma wszystkich rdzeni wirtualnych wdrożonych w wielu podsieciach musi być mniejsza lub równa **maksymalnej liczbie jednostek rdzeń wirtualny**.

\*\* Większe limity podsieci i rdzeń wirtualny są dostępne w następujących regionach: Australia Wschodnia, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia Południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA 2.

> [!IMPORTANT]
> Jeśli limit rdzeń wirtualny i podsieci wynosi 0, oznacza to, że domyślny limit regionalny dla danego typu subskrypcji nie jest ustawiony. Można również użyć żądania zwiększenia przydziału w celu uzyskania dostępu do subskrypcji w określonym regionie, zgodnie z tą samą procedurą — dostarczając wymagane wartości rdzeń wirtualny i podsieci.

## <a name="request-a-quota-increase"></a>Poproś o zwiększenie limitu przydziału

Jeśli potrzebujesz więcej wystąpień w bieżących regionach, Wyślij żądanie obsługi, aby zwiększyć przydział przy użyciu Azure Portal. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](../database/quota-increase-request.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o wystąpieniu zarządzanym SQL, zobacz [co to jest wystąpienie zarządzane SQL?](sql-managed-instance-paas-overview.md).
- Aby uzyskać informacje o cenach, zobacz [Cennik wystąpienia zarządzanego SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane SQL, zobacz [Przewodnik Szybki Start](instance-create-quickstart.md).
