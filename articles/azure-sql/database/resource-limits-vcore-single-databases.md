---
title: Limity zasobów rdzenia wirtualnego dla pojedynczej bazy danych
description: Na tej stronie opisano niektóre typowe limity zasobów rdzeń wirtualny dla pojedynczej bazy danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/15/2021
ms.openlocfilehash: db3b168826223e4eb958f7700e65623a115e5779
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251475"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limity zasobów dla pojedynczych baz danych podczas używania modelu zakupu opartego na rdzeniach wirtualnych
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
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|512|1024|1024|1024|1536|
|Maksymalny rozmiar dziennika (GB)|154|307|307|307|461|
|Maksymalny rozmiar danych TempDB (GB)|32|64|128|192|256|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|320|640|1280|1920|2560|
|Maksymalny współczynnik rejestrowania (MB/s)|4.5|9|18|27|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|75|150|300|450|600|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Min — maks. rdzeni wirtualnych|1,25-10|1.50 — 12|1,75-14|2,00-16|
|Min — maks. pamięć (GB)|3,75-30|4.50 – 36|5,25-42|6.00-48|
|Min-Maksymalne opóźnienie AutoPause (minuty)|60-10080|60-10080|60-10080|60-10080|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1536|3072|3072|3072|
|Maksymalny rozmiar dziennika (GB)|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|320|384|448|512|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|3200|3840|4480|5120|
|Maksymalny współczynnik rejestrowania (MB/s)|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|750|900|1050|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Generowanie obliczeń 5 rdzeń (część 3)

|Rozmiar obliczeń (cel usługi)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Min — maks. rdzeni wirtualnych|2.25 – 18|2.5 – 20|3-24|4-32|5-40|
|Min — maks. pamięć (GB)|6.75 — 54|7.5 — 60|9-72|12-96|15-120|
|Min-Maksymalne opóźnienie AutoPause (minuty)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|1024|1024|1024|
|Maksymalny rozmiar danych TempDB (GB)|576|640|768|1024|1280|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|5760|6400|7680|10240|12800|
|Maksymalny współczynnik rejestrowania (MB/s)|36|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1350|1500|1800|2400|3000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Skalowanie tymczasowe — obliczenia

### <a name="gen4-compute-generation-part-1"></a>Generowanie obliczeń obliczenia (część 1)

|Rozmiar obliczeń (cel usługi)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
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
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\* Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń (cel usługi)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
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
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\* Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Skalowanie tymczasowe — 5 rdzeń

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
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
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\* Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
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
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|7 dni|
|||

\* Oprócz lokalnego dysku SSD we/wy obciążenia będą używać zdalnego [serwera stron](service-tier-hyperscale.md#page-server) . Efektywne operacje we/wy będą zależeć od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance)i [operacje we/wy danych w statystyce użycia zasobów](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Uwagi

**Uwaga 1**: funkcja do skalowania jest architekturą wielowarstwową z oddzielnymi składnikami obliczeniowymi i magazynowanymi: [skalowanie warstwy usług](service-tier-hyperscale.md#distributed-functions-architecture)

**Uwaga 2**: opóźnienie to 1-2 ms dla danych na lokalnym dysku SSD repliki obliczeniowej, który przechowuje w pamięci podręcznej większość używanych stron danych. Wyższe opóźnienie dla danych pobieranych z serwerów stronicowania.

## <a name="hyperscale---provisioned-compute---dc-series"></a>Skalowanie wstępne — Seria DC

|Rozmiar obliczeń (cel usługi)|HS_DC_2|HS_DC_4|HS_DC_6|HS_DC_8|
|:--- | --: |--: |--: |--: |---: | 
|Generowanie obliczeń|Seria DC|Seria DC|Seria DC|Seria DC|
|Rdzeni wirtualnych|2|4|6|8|
|Pamięć (GB)|9|18|27|36|
|[RBPEX](service-tier-hyperscale.md#compute) Zmienia|3. pamięć|3. pamięć|3. pamięć|3. pamięć|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (TB)|100 |100 |100 |100 |
|Maksymalny rozmiar dziennika (TB)|Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |Nieograniczona liczba |
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|
|Typ magazynu| [Uwaga 1](#notes) |[Uwaga 1](#notes)|[Uwaga 1](#notes) |[Uwaga 1](#notes) |
|Maksymalna liczba operacji we/wy lokalnego dysku SSD *|8000 |16000 |24000 |32000 |
|Maksymalny współczynnik rejestrowania (MB/s)|100 |100 |100 |100 |
|Opóźnienie we/wy (przybliżone)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|[Uwaga 2](#notes)|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|160|320|480|640|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|
|Repliki pomocnicze|0-4|0-4|0-4|0-4|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|
|Przechowywanie kopii zapasowej magazynu|7 dni|7 dni|7 dni|7 dni|
|||

### <a name="notes"></a>Uwagi

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
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|320|640|960|1280|1600|1920|
|Maksymalny współczynnik rejestrowania (MB/s)|4.5|9|13,5|18|22,5|27|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generowanie obliczeń obliczenia (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|3072|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|922|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)
|Maksymalna liczba operacji we/wy danych *|2240|2560|2880|3200|5120|7680|
|Maksymalny współczynnik rejestrowania (MB/s)|31,5|36|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1400|1600|1800|2000|3200|4800|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Obliczenia alokowane ogólnie do zastosowania — 5 rdzeń

### <a name="gen5-compute-generation-part-1"></a>Generowanie obliczeń 5 rdzeń (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|384|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|640|1280|1920|2560|3200|3840|4480|
|Maksymalny współczynnik rejestrowania (MB/s)|9|18|27|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|1400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1024|1024|1024|1024|
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|5120|5760|6400|7680|10240|12800|12800|
|Maksymalny współczynnik rejestrowania (MB/s)|36|36|36|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|1600|1800|2000|2400|3200|4000|8000|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Dostępne w wersji zapoznawczej](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Obliczenia alokowane z zastosowaniem ogólnym — seria Fsv2

### <a name="fsv2-series-compute-generation-part-1"></a>Generowanie obliczeń serii Fsv2 (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Generowanie obliczeń|Seria Fsv2|Seria Fsv2|Seria Fsv2|Seria Fsv2|Seria Fsv2|
|Rdzeni wirtualnych|8|10|12|14|16|
|Pamięć (GB)|15.1|18,9|22,7|26,5|30,2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1536|
|Maksymalny rozmiar dziennika (GB)|336|336|336|336|512|
|Maksymalny rozmiar danych TempDB (GB)|333|333|333|333|333|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|2560|3200|3840|4480|5120|
|Maksymalny współczynnik rejestrowania (MB/s)|36|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|400|500|600|700|800|
|Maksymalna liczba współbieżnych logowań|800|1000|1200|1400|1600|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Generowanie obliczeń serii Fsv2 (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Generowanie obliczeń|Seria Fsv2|Seria Fsv2|Seria Fsv2|Seria Fsv2|Seria Fsv2|Seria Fsv2|
|Rdzeni wirtualnych|18|20|24|32|36|72|
|Pamięć (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1536|1536|1536|3072|3072|4096|
|Maksymalny rozmiar dziennika (GB)|512|512|512|1024|1024|1024|
|Maksymalny rozmiar danych TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|5760|6400|7680|10240|11520|23040|
|Maksymalny współczynnik rejestrowania (MB/s)|36|36|36|36|36|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|900|1000|1200|1600|1800|3600|
|Maksymalna liczba współbieżnych logowań|1800|2000|2400|3200|3600|7200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---dc-series"></a>Przeznaczenie ogólne — obsługa administracyjna — Seria DC

|Rozmiar obliczeń (cel usługi)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8| 
|:---| ---:|---:|---:|---:|
|Generowanie obliczeń|Seria DC|Seria DC|Seria DC|Seria DC|
|Rdzeni wirtualnych|2|4|6|8|
|Pamięć (GB)|9|18|27|36|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Maksymalny rozmiar danych (GB)|1024|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|
|Typ magazynu|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|Zdalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|640|1280|1920|2560|
|Maksymalny współczynnik rejestrowania (MB/s)|9|18|27|36|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|160|320|480|640|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|
|Liczba replik|1|1|1|1|
|Wiele-AZ|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Skalowanie w górę odczytu|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|


\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

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
|Maksymalna liczba operacji we/wy danych *|4000|8000|12 000|16 000|20 000|24 000|
|Maksymalny współczynnik rejestrowania (MB/s)|8|16|24|32|40|48|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|1000|1200|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

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

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

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

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generowanie obliczeń 5 rdzeń (część 2)

|Rozmiar obliczeń (cel usługi)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1024|1024|1024|1024|
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

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Krytyczne znaczenie biznesowe — Seria M

### <a name="m-series-compute-generation-part-1"></a>Generowanie obliczeń serii M (część 1)

|Rozmiar obliczeń (cel usługi)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Generowanie obliczeń|Seria M|Seria M|Seria M|Seria M|Seria M|Seria M|
|Rdzeni wirtualnych|8|10|12|14|16|18|
|Pamięć (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|64|80|96|112|128|150|
|Maksymalny rozmiar danych (GB)|512|640|768|896|1024|1152|
|Maksymalny rozmiar dziennika (GB)|171|213|256|299|341|384|
|Maksymalny rozmiar danych TempDB (GB)|256|320|384|448|512|576|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|12 499|15 624|18 748|21 873|24 998|28 123|
|Maksymalny współczynnik rejestrowania (MB/s)|48|60|72|84|96|108|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|800|1000|1200|1400|1600|1800|
|Maksymalna liczba współbieżnych logowań|800|1000|1200|1400|1600|1800|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Nie|Nie|Nie|Nie|Nie|Nie|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>Generowanie obliczeń serii M (część 2)

|Rozmiar obliczeń (cel usługi)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Generowanie obliczeń|Seria M|Seria M|Seria M|Seria M|Seria M|
|Rdzeni wirtualnych|20|24|32|64|128|
|Pamięć (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|172|216|304|704|1768|
|Maksymalny rozmiar danych (GB)|1280|1536|2048|4096|4096|
|Maksymalny rozmiar dziennika (GB)|427|512|683|1024|1024|
|Maksymalny rozmiar danych TempDB (GB)|4096|2048|1024|768|640|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|31 248|37 497|49 996|99 993|160 000|
|Maksymalny współczynnik rejestrowania (MB/s)|120|144|192|264|264|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|2000|2 400|3 200|6 400|12 800|
|Maksymalna liczba współbieżnych logowań|2000|2 400|3 200|6 400|12 800|
|Maksymalna liczba współbieżnych sesji|30000|30000|30000|30000|30000|
|Liczba replik|4|4|4|4|4|
|Wiele-AZ|Nie|Nie|Nie|Nie|Nie|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="business-critical---provisioned-compute---dc-series"></a>Krytyczne dla działalności biznesowe — seria z obsługą administracyjną

|Rozmiar obliczeń (cel usługi)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Generowanie obliczeń|Seria DC|Seria DC|Seria DC|Seria DC|
|Rdzeni wirtualnych|2|4|6|8|
|Pamięć (GB)|9|18|27|36|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|1,7|3.7|5.9|8.2|
|Maksymalny rozmiar danych (GB)|768|768|768|768|
|Maksymalny rozmiar dziennika (GB)|230|230|230|230|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych *|14000|28000|42000|56000|
|Maksymalny współczynnik rejestrowania (MB/s)|24|48|72|96|
|Maksymalna liczba współbieżnych procesów roboczych (żądań)|200|400|600|800|
|Maksymalna liczba współbieżnych logowań|200|400|600|800|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|
|Liczba replik|4|4|4|4|
|Wiele-AZ|Nie|Nie|Nie|Nie|
|Skalowanie w górę odczytu|Nie|Nie|Nie|Nie|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

\* Maksymalna wartość dla wielkości we/wy z zakresu od 8 KB do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).


## <a name="next-steps"></a>Następne kroki

- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md) .
- Aby uzyskać limity zasobów rdzeń wirtualny dla pul elastycznych, zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-elastic-pools.md) .
- W przypadku limitów zasobów dla wystąpienia zarządzanego SQL zobacz [limity zasobów wystąpienia zarządzanego SQL](../managed-instance/resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze, zobacz [Omówienie limitów zasobów na serwerze](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
