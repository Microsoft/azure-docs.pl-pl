---
title: Limity zasobów rdzenia wirtualnego dla pojedynczej bazy danych
description: Na tej stronie opisano niektóre typowe limity zasobów rdzeń wirtualny dla pojedynczej bazy danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/09/2020
ms.openlocfilehash: add2e0cc2852f9ab0b63565841f670ed6c53d9a7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206116"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu rdzeń wirtualny
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera szczegółowe limity zasobów dla pojedynczych baz danych w Azure SQL Database przy użyciu modelu zakupu rdzeń wirtualny.

Limity modelu zakupów jednostek DTU dla pojedynczych baz danych na serwerze można znaleźć w temacie [Omówienie limitów zasobów na serwerze](resource-limits-logical-server.md).

Możesz ustawić warstwę usług, rozmiar obliczeń (cel usługi) i ilość miejsca do magazynowania dla pojedynczej bazy danych przy użyciu [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), interfejsu [wiersza polecenia platformy Azure](single-database-manage.md#the-azure-cli)lub [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie pojedynczej bazy danych](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Ogólnego przeznaczenia — obliczenia bezserwerowe — 5 rdzeń

[Warstwa obliczeń bezserwerowych](serverless-tier-overview.md) jest obecnie dostępna tylko na sprzęcie 5 rdzeń.

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Min — maks. rdzeni wirtualnych|0.5-1|0.5-2|0,5 – 4|0,75-6|1.0-8|
|Min — maks. pamięć (GB)|2.02-3|2.05-6|2.10-12|2.25 – 18|3,00-24|
|Min-Maksymalne opóźnienie AutoPause (minuty)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|1024|1024|1024|1536|
|Maksymalny rozmiar dziennika (GB)|154|307|307|307|461|
|Maksymalny rozmiar danych TempDB (GB)|32|64|128|192|256|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|320|640|1280|1920|2560|
|Maksymalny współczynnik rejestrowania (MB/s)|3,8|7,5|15|22,5|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|75|150|300|450|600|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Min — maks. rdzeni wirtualnych|1,25-10|1.50 — 12|1,75-14|2,00-16|
|Min — maks. pamięć (GB)|3,75-30|4.50 – 36|5,25-42|6.00-48|
|Min-Maksymalne opóźnienie AutoPause (minuty)|60-10080|60-10080|60-10080|60-10080|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1536|3072|3072|3072|
|Maksymalny rozmiar dziennika (GB)|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|320|384|448|512|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|3200|3840|4480|5120|
|Maksymalny współczynnik rejestrowania (MB/s)|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|750|900|1050|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Generowanie obliczeń 5 rdzeń (część 3)

|Rozmiar obliczeń (cel usługi)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Min — maks. rdzeni wirtualnych|2.25 – 18|2.5 – 20|3-24|4-32|5-40|
|Min — maks. pamięć (GB)|6.75 — 54|7.5 — 60|9-72|12-96|15-120|
|Min-Maksymalne opóźnienie AutoPause (minuty)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|576|640|768|1024|1280|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|5760|6400|7680|10240|12800|
|Maksymalny współczynnik rejestrowania (MB/s)|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1350|1500|1800|2400|3000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Skalowanie tymczasowe — obliczenia

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń (cel usługi)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100|
|Maksymalny rozmiar dziennika (TB)|Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy lokalnego dysku SSD *|4000 |8000 |12000 |16000 |20000 |24000 |
|Maksymalny współczynnik rejestrowania (MB/s)|100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń (cel usługi)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy lokalnego dysku SSD *|28000 |32000 |36000 |40000 |64000 |76800 |
|Maksymalny współczynnik rejestrowania (MB/s)|100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Skalowanie tymczasowe — 5 rdzeń

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100|
|Maksymalny rozmiar dziennika (TB)|Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy lokalnego dysku SSD *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Maksymalny współczynnik rejestrowania (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy lokalnego dysku SSD *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Maksymalny współczynnik rejestrowania (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\*Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Uwagi

**Uwaga 1**: funkcja do skalowania jest architekturą wielowarstwową z oddzielnymi składnikami obliczeniowymi i magazynowanymi: [skalowanie warstwy usług](service-tier-hyperscale.md#distributed-functions-architecture)

**Uwaga 2**: opóźnienie to 1-2 ms dla danych na lokalnym dysku SSD repliki obliczeniowej, który przechowuje w pamięci podręcznej większość używanych stron danych. Wyższe opóźnienie dla danych pobieranych z serwerów stronicowania.

## <a name="general-purpose---provisioned-compute---gen4"></a>Obliczenia alokowane ogólnie do zastosowania — obliczenia

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|320|640|960|1280|1600|1920|
|Maksymalny współczynnik rejestrowania (MB/s)|3,75|7,5|11,25|15|18,75|22,5|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|922|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)
|Maksymalna liczba operacji we/wy danych *|2240|2560|2880|3200|5120|7680|
|Maksymalny współczynnik rejestrowania (MB/s)|26,3|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Obliczenia alokowane ogólnie do zastosowania — 5 rdzeń

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|640|1280|1920|2560|3200|3840|4480|
|Maksymalny współczynnik rejestrowania (MB/s)|7,5|15|22,5|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|5120|5760|6400|7680|10240|12800|12800|
|Maksymalny współczynnik rejestrowania (MB/s)|30|30|30|30|30|30|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Obliczenia alokowane z zastosowaniem ogólnym — seria Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Generowanie obliczeń serii Fsv2 (wersja zapoznawcza)

|Rozmiar obliczeń (cel usługi)|GP_Fsv2_72|
|:--- | --: |
|Generowanie obliczeń|Seria Fsv2|
|Rdzeni wirtualnych|72|
|Pamięć (GB)|136,2|
|Obsługa magazynu kolumn|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|1024|
|Maksymalny rozmiar danych TempDB (GB)|333|
|Typ magazynu|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|12 800|
|Maksymalny współczynnik rejestrowania (MB/s)|30|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|3600|
|Maksymalna liczba współbieżnych logowań|3600|
|Maksymalna liczba współbieżnych sesji|30 000|
|Liczba replik|1|
|Wiele-AZ|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Obliczenia o krytycznym znaczeniu dla firmy — obliczenia

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń (cel usługi)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|1|2|3|4|5|6|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|4000|8000|12 000|16 000|20 000|24 000|
|Maksymalny współczynnik rejestrowania (MB/s)|8|16|24|32|40|48|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń (cel usługi)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|7|8|9,5|11|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych |28 000|32 000|36 000|40 000|64 000|76 800|
|Maksymalny współczynnik rejestrowania (MB/s)|56|64|64|64|64|64|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych logowań (żądania)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Obliczenia o krytycznym znaczeniu dla firmy — 5 rdzeń

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|8000|16 000|24 000|32 000|40 000|48 000|56 000|
|Maksymalny współczynnik rejestrowania (MB/s)|24|48|72|96|96|96|96|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|64 000|72 000|80 000|96 000|128 000|160 000|204 800|
|Maksymalny współczynnik rejestrowania (MB/s)|96|96|96|96|96|96|96|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych logowań|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Krytyczne znaczenie biznesowe — Seria M

### <a name="m-series-compute-generation-preview"></a>Generowanie obliczeń serii M (wersja zapoznawcza)

|Rozmiar obliczeń (cel usługi)|BC_M_128|
|:--- | --: |
|Generowanie obliczeń|Seria M|
|Rdzeni wirtualnych|128|
|Pamięć (GB)|3767,1|
|Obsługa magazynu kolumn|Tak|
|Magazyn OLTP w pamięci (GB)|1768|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|2048|
|Maksymalny rozmiar danych TempDB (GB)|4096|
|Typ magazynu|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|160 000|
|Maksymalny współczynnik rejestrowania (MB/s)|264|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|12 800|
|Maksymalna liczba współbieżnych logowań|12 800|
|Maksymalna liczba współbieżnych sesji|30000|
|Liczba replik|4|
|Wiele-AZ|Tak|
|Skalowanie w górę odczytu|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|

\*Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Następne kroki

- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md) .
- Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-elastic-pools.md) .
- W przypadku limitów zasobów dla wystąpienia zarządzanego SQL zobacz [limity zasobów wystąpienia zarządzanego SQL](../managed-instance/resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze, zobacz [Omówienie limitów zasobów na serwerze](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
