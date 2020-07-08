---
title: Limity zasobów rdzenia wirtualnego dla elastycznej puli
description: Na tej stronie opisano niektóre typowe limity zasobów rdzeń wirtualny dla pul elastycznych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 06/10/2020
ms.openlocfilehash: 4ffd92c0641b74682a74ffd2898e226999ac2dd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668461"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Limity zasobów dla pul elastycznych przy użyciu modelu zakupu rdzeń wirtualny
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera szczegółowe limity zasobów Azure SQL Database pul elastycznych i baz danych w puli przy użyciu modelu zakupu rdzeń wirtualny.

Aby uzyskać ograniczenia modelu zakupu jednostek DTU, zobacz [SQL Database limitów zasobów jednostek DTU — pule elastyczne](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

Za pomocą [Azure Portal](elastic-pool-manage.md#azure-portal), [programu PowerShell](elastic-pool-manage.md#powershell), interfejsu [wiersza polecenia platformy Azure](elastic-pool-manage.md#azure-cli)lub [interfejsu API REST](elastic-pool-manage.md#rest-api)można ustawić warstwę usług, rozmiar obliczeń (cel usługi) i ilość miejsca do magazynowania.

> [!IMPORTANT]
> Aby uzyskać wskazówki dotyczące skalowania i zagadnienia, zobacz [skalowanie elastycznej puli](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Obliczenia alokowane ogólnie do zastosowania — obliczenia

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 4 (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|1|2|3|4|5|6|
|Pamięć (GB)|7|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|100|200|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|756|1536|1536|1536|2048|
|Maksymalny rozmiar dziennika|154|227|461|461|461|614|
|Maksymalny rozmiar danych TempDB (GB)|32|64|96|128|160|192|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|4,7|9.4|14,1|18,8|23,4|28,1|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup> |210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę <sup>3</sup> |210|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Warstwa usługi ogólnego przeznaczenia: platforma obliczeniowa 2 generacji (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|2048|2048|2048|2048|3584|4096|
|Maksymalny rozmiar dziennika (GB)|614|614|614|614|1075|1229|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|32,8|37,5|37,5|37,5|37,5|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań w puli (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Liczba replik|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="general-purpose---provisioned-compute---gen5"></a>Obliczenia alokowane ogólnie do zastosowania — 5 rdzeń

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 5 (część 1)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|2|4|6|8|10|12|14|
|Pamięć (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|100|200|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|512|756|1536|1536|1536|2048|2048|
|Maksymalny rozmiar dziennika (GB)|154|227|461|461|461|614|614|
|Maksymalny rozmiar danych TempDB (GB)|64|128|192|256|320|384|448|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|9.4|18,8|28,1|37,5|37,5|37,5|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Warstwa usług ogólnego przeznaczenia: platforma obliczeniowa generacji 5 (część 2)

|Rozmiar obliczeń (cel usługi)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|500|500|500|500|500|500|500|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|2048|3072|3072|3072|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|614|922|922|922|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|Magazyn Premium (zdalny)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup> |6 400|7 200|8000|9600|12 800|16 000|16 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Liczba replik|1|1|1|1|1|1|1|
|Wiele-AZ|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Obliczenia alokowane z zastosowaniem ogólnym — seria Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Generowanie obliczeń serii Fsv2 (wersja zapoznawcza)

|Rozmiar obliczeń (cel usługi)|GP_Fsv2_72|
|:--- | --: |
|Generowanie obliczeń|Seria Fsv2|
|Rdzeni wirtualnych|72|
|Pamięć (GB)|136,2|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|500|
|Obsługa magazynu kolumn|Tak|
|Magazyn OLTP w pamięci (GB)|Nie dotyczy|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|1024|
|Maksymalny rozmiar danych TempDB (GB)|333|
|Typ magazynu|Magazyn Premium (zdalny)|
|Opóźnienie we/wy (przybliżone)|5-7 ms (zapis)<br>5-10 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|16 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|37,5|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|3780|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|3780|
|Maksymalna liczba współbieżnych sesji|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0-72|
|Liczba replik|1|
|Wiele-AZ|Nie dotyczy|
|Skalowanie w górę odczytu|Nie dotyczy|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="business-critical---provisioned-compute---gen4"></a>Obliczenia o krytycznym znaczeniu dla firmy — obliczenia

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Warstwa usługi krytycznej dla działalności: platforma obliczeniowa w wersji 4 (część 1)

|Rozmiar obliczeń (cel usługi)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|2|3|4|5|6|
|Pamięć (GB)|14|21|28|35|42|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|50|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|2|3|4|5|6|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|64|96|128|160|192|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|9000|13 500|18 000|22 500|27 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|20|30|40|50|60|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Liczba replik|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Warstwa usługi krytycznej dla firm: platforma obliczeniowa 2 generacji (część 2)

|Rozmiar obliczeń (cel usługi)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generowanie obliczeń|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|Obliczenia|
|Rdzeni wirtualnych|7|8|9|10|16|24|
|Pamięć (GB)|49|56|63|70|112|159,5|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|Nie dotyczy|
|Magazyn OLTP w pamięci (GB)|7|8|9,5|11|20|36|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Maksymalny rozmiar danych (GB)|1024|1024|1024|1024|1024|1024|
|Maksymalny rozmiar dziennika (GB)|307|307|307|307|307|307|
|Maksymalny rozmiar danych TempDB (GB)|224|256|288|320|512|768|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|31 500|36 000|40 500|45 000|72 000|96 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|70|80|80|80|80|80|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="business-critical---provisioned-compute---gen5"></a>Obliczenia o krytycznym znaczeniu dla firmy — 5 rdzeń

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Warstwa usługi krytycznej dla działalności: platforma obliczeniowa w wersji 5 (część 1)

|Rozmiar obliczeń (cel usługi)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|4|6|8|10|12|14|
|Pamięć (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|50|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maksymalny rozmiar danych (GB)|1024|1536|1536|1536|3072|3072|
|Maksymalny rozmiar dziennika (GB)|307|307|461|461|922|922|
|Maksymalny rozmiar danych TempDB (GB)|128|192|256|320|384|448|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|18 000|27 000|36 000|45 000|54 000|63 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|60|90|120|120|120|120|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Liczba replik|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Warstwa usługi krytycznej dla działalności: platforma obliczeniowa w wersji 5 (część 2)

|Rozmiar obliczeń (cel usługi)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generowanie obliczeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|5 rdzeń|
|Rdzeni wirtualnych|16|18|20|24|32|40|80|
|Pamięć (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|100|100|100|100|100|100|100|
|Obsługa magazynu kolumn|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Magazyn OLTP w pamięci (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maksymalny rozmiar danych (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maksymalny rozmiar dziennika (GB)|922|922|922|1229|1229|1229|1229|
|Maksymalny rozmiar danych TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ magazynu|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|72 000|81 000|90 000|108 000|144 000|180 000|256 000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|120|120|120|120|120|120|120|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maksymalna liczba współbieżnych sesji|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Liczba replik|4|4|4|4|4|4|4|
|Wiele-AZ|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Skalowanie w górę odczytu|Tak|Tak|Tak|Tak|Tak|Tak|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

## <a name="business-critical---provisioned-compute---m-series"></a>Krytyczne znaczenie biznesowe — Seria M

### <a name="m-series-compute-generation-preview"></a>Generowanie obliczeń serii M (wersja zapoznawcza)

|Rozmiar obliczeń (cel usługi)|BC_M_128|
|:--- | --: |
|Generowanie obliczeń|Seria M|
|Rdzeni wirtualnych|128|
|Pamięć (GB)|3767,1|
|Maksymalna liczba baz danych na pulę <sup>1</sup>|100|
|Obsługa magazynu kolumn|Tak|
|Magazyn OLTP w pamięci (GB)|1768|
|Maksymalny rozmiar danych (GB)|4096|
|Maksymalny rozmiar dziennika (GB)|2048|
|Maksymalny rozmiar danych TempDB (GB)|4096|
|Typ magazynu|Lokalny dysk SSD|
|Opóźnienie we/wy (przybliżone)|1-2 ms (zapis)<br>1-2 ms (odczyt)|
|Maksymalna liczba operacji we/wy danych na pulę <sup>2</sup>|200,000|
|Maksymalna szybkość rejestrowania na pulę (MB/s)|333|
|Maksymalna liczba współbieżnych procesów roboczych na pulę (żądania) <sup>3</sup>|13 440|
|Maksymalna liczba współbieżnych logowań na pulę (żądania) <sup>3</sup>|13 440|
|Maksymalna liczba współbieżnych sesji|30 000|
|Minimalna/Maksymalna liczba opcji rdzeń wirtualny puli elastycznej na bazę danych|0-128|
|Liczba replik|4|
|Wiele-AZ|Tak|
|Skalowanie w górę odczytu|Tak|
|Uwzględniony magazyn kopii zapasowych|rozmiar bazy danych 1X|

<sup>1</sup> zobacz [Zarządzanie zasobami w gęstych pulach elastycznych](elastic-pool-resource-management.md) , aby uzyskać dodatkowe uwagi.

<sup>2</sup> maksymalna wartość dla wielkości we/wy z zakresu od 8 kb do 64 KB. Rzeczywiste operacje we/wy są zależne od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie we/wy danych](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> w przypadku maksymalnych współbieżnych procesów roboczych (żądań) dla każdej pojedynczej bazy danych zobacz [limity zasobów pojedynczej bazy danych](resource-limits-vcore-single-databases.md). Na przykład, jeśli Pula elastyczna korzysta z 5 rdzeń, a maksymalna rdzeń wirtualny na bazę danych jest ustawiona na 2, wartość maksymalna liczba współbieżnych procesów roboczych to 200.  Jeśli wartość maksymalna rdzeń wirtualny na bazę danych jest równa 0,5, Maksymalna liczba współbieżnych procesów roboczych wynosi 50, ponieważ na 5 rdzeń istnieje maksymalnie 100 współbieżnych procesów roboczych na rdzeń wirtualny. W przypadku innych maksymalnych ustawień rdzeń wirtualny dla bazy danych, które mają mniej niż 1 rdzeń wirtualny lub mniej, Maksymalna liczba współbieżnych procesów roboczych jest w podobnym stopniu skalowana.

Jeśli wszystkie rdzeni wirtualnych puli elastycznej są zajęte, każda baza danych w puli otrzymuje taką samą ilość zasobów obliczeniowych, aby przetwarzać zapytania. Azure SQL Database zapewnia sprawiedliwe udostępnianie zasobów między bazami danych przez zapewnienie równości wycinków czasu obliczeniowego. Sprawiedliwa współużytkowanie zasobów puli elastycznej jest uzupełnieniem dowolnej ilości zasobów, w przeciwnym razie zagwarantowane dla każdej bazy danych, gdy wartość rdzeń wirtualny min na bazę danych jest ustawiona na inną niż zero.

## <a name="database-properties-for-pooled-databases"></a>Właściwości bazy danych w puli

W poniższej tabeli opisano właściwości dla baz danych w puli.

> [!NOTE]
> Limity zasobów poszczególnych baz danych w pulach elastycznych są generalnie takie same jak w przypadku pojedynczych baz danych poza pulami o takim samym rozmiarze (cel usługi). Na przykład maksymalna liczba współbieżnych procesów roboczych dla bazy danych GP_Gen4_1 to 200 procesów roboczych. W związku z tym Maksymalna liczba współbieżnych procesów roboczych dla bazy danych w puli GP_Gen4_1 to również 200 procesów roboczych. Należy pamiętać, że łączna liczba współbieżnych procesów roboczych w puli GP_Gen4_1 wynosi 210.

| Właściwość | Opis |
|:--- |:--- |
| Maksymalna liczba rdzeni wirtualnych na bazę danych |Maksymalna liczba rdzeni wirtualnych, które mogą być używane w dowolnej bazie danych w puli, jeśli są dostępne na podstawie użycia przez inne bazy danych w puli. Maksymalna rdzeni wirtualnych na bazę danych nie jest gwarancją zasobów dla bazy danych. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Wartość maksymalna rdzeni wirtualnych na bazę danych jest wystarczająco wysoka, aby obsługiwała szczyty w wykorzystaniu bazy danych. Oczekiwany jest jakiś stopień nadmiernego zatwierdzania, ponieważ w puli ogólnie przyjęto wzorce użycia gorącą i zimną dla baz danych, w których wszystkie bazy danych nie są jednocześnie szczytowe.|
| Minimalna rdzeni wirtualnych na bazę danych |Minimalna liczba rdzeni wirtualnych, jaką gwarantuje każda baza danych w puli. To ustawienie jest ustawieniem globalnym, które ma zastosowanie do wszystkich baz danych w puli. Minimalna rdzeni wirtualnych na bazę danych może być ustawiona na 0 i jest również wartością domyślną. Ta właściwość jest ustawiona na dowolne miejsce między 0 a średnim wykorzystaniem rdzeni wirtualnych na bazę danych. Iloczyn liczby baz danych w puli i minimalnej rdzeni wirtualnych na bazę danych nie może przekroczyć rdzeni wirtualnych na pulę.|
| Maksymalny rozmiar magazynu na bazę danych |Maksymalny rozmiar bazy danych ustawiony przez użytkownika dla bazy danych w puli. Bazy danych w puli korzystają z przydzielonego magazynu puli, więc rozmiar bazy danych może dotrzeć do mniejszej ilości magazynu puli i rozmiaru bazy danych. Maksymalny rozmiar bazy danych odnosi się do maksymalnego rozmiaru plików danych i nie obejmuje przestrzeni używanej przez pliki dziennika. |
|||

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać limity zasobów rdzeń wirtualny dla pojedynczej bazy danych, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu rdzeń wirtualny](resource-limits-vcore-single-databases.md)
- W przypadku limitów zasobów jednostek DTU dla pojedynczej bazy danych zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md) .
- W przypadku limitów zasobów jednostek DTU dla pul elastycznych zobacz [limity zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-elastic-pools.md) .
- W przypadku limitów zasobów dla wystąpień zarządzanych zobacz [limity zasobów wystąpienia zarządzanego](../managed-instance/resource-limits.md).
- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat limitów zasobów na serwerze logicznym programu SQL Server, zobacz [Omówienie limitów zasobów na serwerze logicznym programu SQL Server](resource-limits-logical-server.md) , aby uzyskać informacje na temat limitów na poziomach serwera i subskrypcji.
