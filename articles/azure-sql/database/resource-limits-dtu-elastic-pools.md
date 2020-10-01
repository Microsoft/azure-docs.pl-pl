---
title: Limity zasobów jednostek DTU ograniczają pule elastyczne
description: Ta strona zawiera opis niektórych typowych limitów zasobów jednostek DTU dla pul elastycznych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 72d50cadcc9b0f913c66f00ebc16d5e12a39de70
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619104"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera szczegółowe limity zasobów dla baz danych w Azure SQL Database, które znajdują się w puli elastycznej przy użyciu modelu zakupu jednostek DTU.

* Limity zasobów modelu zakupu jednostek DTU dla Azure SQL Database można znaleźć w temacie [limity zasobów jednostek DTU — Azure SQL Database](resource-limits-dtu-single-databases.md).
* Aby uzyskać limity zasobów rdzeń wirtualny, zobacz [limity zasobów rdzeń wirtualny-Azure SQL Database](resource-limits-vcore-single-databases.md) i [rdzeń wirtualny — pule elastyczne](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pula elastyczna: rozmiary magazynu i rozmiary obliczeń

W przypadku pul elastycznych Azure SQL Database w poniższych tabelach przedstawiono zasoby dostępne w każdej warstwie usług i rozmiarze obliczeniowym. Możesz ustawić warstwę usług, rozmiar obliczeń i ilość miejsca do magazynowania, korzystając z:

* [Witryna Azure Portal](elastic-pool-manage.md#azure-portal)
* [Program PowerShell](elastic-pool-manage.md#powershell)
* [Interfejs wiersza polecenia platformy Azure](elastic-pool-manage.md#azure-cli)
* [Interfejs API REST](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie elastycznej puli](elastic-pool-scale.md)

Limity zasobów poszczególnych baz danych w pulach elastycznych są generalnie takie same jak w przypadku pojedynczych baz danych poza pulami na podstawie DTU i warstwy usług. Na przykład maksymalna liczba współbieżnych procesów roboczych dla bazy danych S2 to 120 procesów roboczych. W związku z tym Maksymalna liczba współbieżnych procesów roboczych dla bazy danych w puli standardowej to 120 pracowników, jeśli Maksymalna liczba jednostek DTU na bazę danych w puli to 50 DTU (co jest równoważne S2).
 
W przypadku tej samej liczby DTU zasoby udostępniane puli elastycznej mogą przekroczyć zasoby udostępniane pojedynczej bazie danych spoza puli elastycznej. Oznacza to, że możliwe jest, że użycie jednostek eDTU w puli elastycznej jest mniejsze niż suma użycia jednostek DTU w obrębie baz danych w puli, w zależności od wzorców obciążenia. Na przykład w skrajnym przypadku z tylko jedną bazą danych w puli elastycznej, w której wykorzystanie jednostek DTU bazy danych wynosi 100%, możliwe jest użycie jednostek eDTU puli do 50% dla niektórych wzorców obciążenia. Może się to zdarzyć nawet wtedy, gdy maksymalna wartość liczby jednostek DTU na bazę danych pozostanie maksymalną obsługiwaną wartością dla danego rozmiaru puli.

> [!NOTE]
> Limit zasobów magazynu na pulę w każdej z poniższych tabel nie obejmuje bazy danych tempdb ani magazynu dzienników.

### <a name="basic-elastic-pool-limits"></a>Limity podstawowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Uwzględniony magazyn na pulę (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalny rozmiar magazynu na pulę (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |
| Maksymalna liczba baz danych na pulę <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maksymalna liczba współbieżnych sesji na pulę <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek DTU na bazę danych | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Maksymalna liczba jednostek DTU na bazę danych | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="standard-elastic-pool-limits"></a>Limity standardowych pul elastycznych

| Jednostki eDTU na pulę | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Uwzględniony magazyn na pulę (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Maksymalny rozmiar magazynu na pulę (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |
| Maksymalna liczba baz danych na pulę <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maksymalna liczba współbieżnych sesji na pulę <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek DTU na bazę danych | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maksymalna liczba jednostek DTU na bazę danych | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> zobacz [opcje cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) , aby uzyskać szczegółowe informacje o dodatkowych kosztach związanych z obsługą dodatkowego magazynu.

<sup>2</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="standard-elastic-pool-limits-continued"></a>Limity standardowych pul elastycznych (ciąg dalszy)

| Jednostki eDTU na pulę | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maksymalny rozmiar magazynu na pulę (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY | NIE DOTYCZY |
| Maksymalna liczba baz danych na pulę <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maksymalna liczba współbieżnych sesji na pulę <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek DTU na bazę danych | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maksymalna liczba jednostek DTU na bazę danych | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> zobacz [opcje cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) , aby uzyskać szczegółowe informacje o dodatkowych kosztach związanych z obsługą dodatkowego magazynu.

<sup>2</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="premium-elastic-pool-limits"></a>Limity pul elastycznych Premium

| Jednostki eDTU na pulę | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Maksymalny rozmiar magazynu na pulę (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | 1 | 2 | 4 | 10 | 12 |
| Maksymalna liczba baz danych na pulę <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maksymalna liczba współbieżnych sesji na pulę <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek eDTU na bazę danych | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maksymalna liczba jednostek eDTU na bazę danych | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> zobacz [opcje cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) , aby uzyskać szczegółowe informacje o dodatkowych kosztach związanych z obsługą dodatkowego magazynu.

<sup>2</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="premium-elastic-pool-limits-continued"></a>Limity pul elastycznych Premium (ciąg dalszy)

| Jednostki eDTU na pulę | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Uwzględniony magazyn na pulę (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maksymalny rozmiar magazynu na pulę (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maksymalna pojemność magazynu OLTP w pamięci na pulę (GB) | 16 | 20 | 24 | 28 | 32 |
| Maksymalna liczba baz danych na pulę <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Maksymalna liczba współbieżnych procesów roboczych (żądań) na pulę <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maksymalna liczba współbieżnych sesji na pulę <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalna liczba jednostek DTU na bazę danych | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maksymalna liczba jednostek DTU na bazę danych | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maksymalny rozmiar magazynu na bazę danych (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> zobacz [opcje cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/elastic/) , aby uzyskać szczegółowe informacje o dodatkowych kosztach związanych z obsługą dodatkowego magazynu.

<sup>2</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy środkowe i Niemcy północno-wschodnie. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Jeśli używane są wszystkie jednostki DTU puli elastycznej, każda baza danych w puli otrzymuje taką samą ilość zasobów do przetwarzania zapytań. Usługa SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równych okresów czasu obliczeń. Sprawiedliwe udostępnianie zasobów puli elastycznej jest wykonywane oprócz zapewniania dowolnej ilości zasobów w przeciwnym razie gwarantowanej dla każdej bazy danych, gdy minimalna liczba jednostek DTU na bazę danych jest ustawiona na wartość różną od zera.

> [!NOTE]
> Aby uzyskać `tempdb` limity, zobacz [limity tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych w puli

W poniższej tabeli opisano właściwości dla baz danych w puli.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba jednostek eDTU na bazę danych |Maksymalna liczba jednostek eDTU, z których może korzystać dowolna baza danych w puli, jeśli są dostępne na podstawie użycia innych baz danych w puli. Maksymalna liczba jednostek eDTU na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Należy ustawić odpowiednio wysoką maksymalną liczbę jednostek eDTU na bazę danych, aby obsłużyć szczytowe użycia baz danych. Oczekiwany jest pewien stopień nadmiernego przydzielania, ponieważ pula ogólnie zakłada wzorce gorącego i zimnego użycia dla baz danych, w których nie wszystkie bazy danych jednocześnie osiągają szczytowe użycie. Załóżmy na przykład, że użycie szczytowe na bazę danych wynosi 20 eDTU i tylko 20% ze 100 baz danych w puli osiąga szczytowe użycie w tym samym czasie. Jeśli maksymalna liczba jednostek eDTU na bazę danych wynosi 20 eDTU, zasadne jest nadmiarowe przydzielenie pięciokrotnie większej liczby eDTU dla puli i ustawienie liczby 400 jednostek eDTU na pulę. |
| Minimalna liczba jednostek eDTU na bazę danych |Minimalna liczba jednostek eDTU gwarantowana dla każdej bazy danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna liczba jednostek eDTU na bazę danych może być ustawiona na 0 i jest to również wartość domyślna. Tę właściwość można ustawić na dowolną wartość między 0 a średnim użyciem jednostek eDTU na bazę danych. Iloczyn liczby baz danych w puli i minimalnej liczby jednostek eDTU na bazę danych nie może przekraczać liczby jednostek eDTU na pulę. Na przykład jeśli pula zawiera 20 baz danych, a minimalna liczba jednostek eDTU na bazę danych wynosi 10 eDTU, liczba jednostek eDTU na pulę musi wynosić co najmniej 200 eDTU. |
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Jednak bazy danych w puli korzystają z przydzielony magazyn pul. Nawet jeśli łączny maksymalny rozmiar magazynu *na bazę danych* jest ustawiony na wartość większą niż całkowita ilość dostępnego *miejsca do*magazynowania w puli, całkowite miejsce używane przez wszystkie bazy danych nie będzie mogło przekroczyć limitu dostępnej puli. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać limity zasobów rdzeń wirtualny dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-single-databases.md)
* W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md) .
* Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)
* W przypadku limitów zasobów dla wystąpień zarządzanych w wystąpieniu zarządzanym usługi Azure SQL zobacz [limity zasobów wystąpienia zarządzanego SQL](../managed-instance/resource-limits.md).
* Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Aby uzyskać informacje na temat limitów zasobów na serwerze logicznym programu SQL Server, zobacz [Omówienie limitów zasobów na serwerze logicznym programu SQL Server](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
