---
title: Skalowanie zasobów elastycznej puli
description: Na tej stronie opisano skalowanie zasobów dla pul elastycznych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 1125ea0c6c625ece010b2acb416ad223e37aea84
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787129"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalowanie zasobów elastycznej puli w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla elastycznych pul i baz danych w puli w Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Zmienianie zasobów obliczeniowych (rdzeni wirtualnych lub jednostek DTU)

Po początkowym pobraniu liczby rdzeni wirtualnych lub eDKU można dynamicznie skalować pulę elastyczną w górę lub w dół na podstawie rzeczywistego doświadczenia przy użyciu:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Witryna Azure Portal](elastic-pool-manage.md#azure-portal)
* [Program PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)
* [Interfejs API REST](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Wpływ zmiany warstwy usługi lub ponownego skalowania rozmiaru obliczeniowego

Zmiana warstwy usługi lub rozmiaru obliczeniowego elastycznej puli jest zgodna z podobnym wzorcem jak w przypadku pojedynczych baz danych i obejmuje głównie usługę wykonującą następujące czynności:

1. Tworzenie nowego wystąpienia obliczeniowego dla elastycznej puli  

    Nowe wystąpienie obliczeniowe dla elastycznej puli jest tworzone z żądaną warstwą usługi i rozmiarem obliczeniowym. W przypadku niektórych kombinacji zmian warstwy usługi i rozmiaru zasobów obliczeniowych replika każdej bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, które obejmuje kopiowanie danych i może mieć duży wpływ na ogólne opóźnienie. Niezależnie od tego, bazy danych pozostają w trybie online w tym kroku, a połączenia są nadal kierowane do baz danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełączanie routingu połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazami danych w oryginalnym wystąpieniu obliczeniowym są porzucane. Wszelkie nowe połączenia są ustanowione z bazami danych w nowym wystąpieniu obliczeniowym. W przypadku niektórych kombinacji zmian warstwy usługi i rozmiaru zasobów obliczeniowych pliki bazy danych są odłączane i ponownie dopinane podczas przełączania.  Niezależnie od tego przełącznik może spowodować krótką przerwę w działaniu usługi, gdy bazy danych są ogólnie niedostępne przez mniej niż 30 sekund, a często tylko przez kilka sekund. Jeśli istnieją długotrwałe transakcje uruchomione po porzuceniu połączeń, czas trwania tego kroku może trwać dłużej, aby odzyskać przerwane transakcje. [przyspieszone odzyskiwanie bazy danych](../accelerated-database-recovery.md) może zmniejszyć wpływ przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie są utracone podczas dowolnego kroku przepływu pracy.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Opóźnienie zmiany warstwy usługi lub ponownego skalowania rozmiaru obliczeniowego

Szacowane opóźnienie zmiany warstwy usługi, skalowanie rozmiaru obliczeniowego pojedynczej bazy danych lub elastycznej puli, przenoszenie bazy danych do elastycznej puli lub przenoszenie bazy danych między elastycznymi pulami jest sparametryzowane w następujący sposób:

|Warstwa usług|Podstawowa pojedyncza baza danych,</br>Standardowa (S0–S1)|Podstawowa pula elastyczna,</br>Standardowa (S2–S12), </br>Ogólnego przeznaczenia pojedynczej bazy danych lub elastycznej puli|Premium lub Krytyczne dla działania firmy pojedyncza baza danych lub elastyczna pula|Hiperskala
|:---|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza </br> danych, Standardowa (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;&nbsp;Zwykle mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|
|**Podstawowa pula </br> elastyczna, Standardowa (S2-S12), </br> Ogólnego przeznaczenia pojedyncza baza danych lub pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;W przypadku pojedynczych baz danych stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;Zwykle mniej niż 5 minut w przypadku &nbsp; pojedynczych baz danych</br>&bull;&nbsp;W przypadku pul elastycznych proporcjonalnie do liczby baz danych|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|
|**Premium lub Krytyczne dla działania firmy pojedyncza baza danych lub elastyczna pula**|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|
|**Hiperskala**|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;&nbsp;Zwykle mniej niż 2 minuty|

> [!NOTE]
>
> - W przypadku zmiany warstwy usługi lub ponownego skalowania zasobów obliczeniowych dla elastycznej puli do obliczenia oszacowania należy użyć zsumowania miejsca używanego we wszystkich bazach danych w puli.
> - W przypadku przenoszenia bazy danych do i z elastycznej puli tylko miejsce używane przez bazę danych ma wpływ na opóźnienie, a nie na miejsce używane przez pulę elastyczną.
> - W przypadku pul Ogólnego przeznaczenia standardowych i elastycznych opóźnienie przenoszenia bazy danych do/z puli elastycznej lub między pulami elastycznymi będzie proporcjonalne do rozmiaru bazy danych, jeśli pula elastyczna używa magazynu udziału plików Premium[(PFS).](../../storage/files/storage-files-introduction.md) Aby określić, czy pula używa magazynu PFS, wykonaj następujące zapytanie w kontekście dowolnej bazy danych w puli. Jeśli wartość w kolumnie AccountType to `PremiumFileStorage` lub `PremiumFileStorage-ZRS` , pula używa magazynu PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Aby monitorować operacje w toku, zobacz: Zarządzanie operacjami przy użyciu interfejsu [API REST](/rest/api/sql/operations/list) [SQL,](/cli/azure/sql/db/op)Zarządzanie operacjami przy użyciu interfejsu wiersza polecenia, Monitorowanie operacji przy użyciu języka [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i następujące dwa polecenia programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Dodatkowe zagadnienia dotyczące zmieniania warstwy usługi lub ponownego skalowania rozmiaru obliczeniowego

- Podczas downsizing rdzeni wirtualnych lub eDTY dla elastycznej puli używane miejsce w puli musi być mniejsza niż maksymalny dozwolony rozmiar docelowej warstwy usług i eDKU puli.
- Podczas ponownego skalowania eDKU dla puli elastycznej obowiązuje dodatkowy koszt magazynu, jeśli (1) maksymalny rozmiar magazynu puli jest obsługiwany przez pulę docelową i (2) maksymalny rozmiar magazynu przekracza uwzględnioną ilość miejsca do magazynowania puli docelowej. Jeśli na przykład pula 100 eDTU Standardowa o maksymalnym rozmiarze 100 GB zostanie spakowana do puli 50 eDTU Standardowa, obowiązuje dodatkowy koszt magazynu, ponieważ pula docelowa obsługuje maksymalny rozmiar 100 GB, a uwzględniona ilość miejsca do magazynowania wynosi tylko 50 GB. Dlatego dodatkowa ilość miejsca do magazynowania wynosi 100 GB – 50 GB = 50 GB. Aby uzyskać informacje o cenach dodatkowego magazynu, [zobacz SQL Database cennik .](https://azure.microsoft.com/pricing/details/sql-database/) Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż uwzględniona ilość miejsca do magazynowania, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalny rozmiar bazy danych do uwzględnionej kwoty.

### <a name="billing-during-rescaling"></a>Rozliczenia podczas ponownego skalowania

Opłaty są naliczane za każdą godzinę, gdy baza danych istnieje, korzystając z najwyższej warstwy usługi i rozmiaru obliczeniowego, która została zastosowana w ciągu tej godziny, niezależnie od użycia lub tego, czy baza danych była aktywna przez mniej niż godzinę. Jeśli na przykład utworzysz pojedynczą bazę danych i usuniesz ją pięć minut później, rachunek odzwierciedla opłatę za jedną godzinę bazy danych.

## <a name="change-elastic-pool-storage-size"></a>Zmienianie rozmiaru magazynu puli elastycznej

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w programie Azure SQL Database](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn można aprowizować do maksymalnego limitu rozmiaru:

  - W przypadku magazynu w warstwie usługi Standardowa lub Ogólnego przeznaczenia zwiększ lub zmniejsz rozmiar o 10 GB
  - W przypadku magazynu w warstwach usług Premium lub Krytyczne dla działalności firmy zwiększ lub zmniejsz rozmiar o 250 GB
- Magazyn dla elastycznej puli można aprowizowany przez zwiększenie lub zmniejszenie maksymalnego rozmiaru.
- Cena magazynu dla elastycznej puli to wielkość magazynu pomnożona przez cenę jednostki magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, [zobacz cennik SQL Database magazynu](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w programie Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupów oparty na jednostkach DTU

- Cena eDTU dla elastycznej puli obejmuje pewną ilość miejsca bez dodatkowych kosztów. Dodatkowy magazyn, który przekracza uwzględnioną ilość, można aprowizować na dodatkowy koszt aż do maksymalnego limitu rozmiaru w przyrostach od 250 GB do 1 TB, a następnie w przyrostach 256 GB powyżej 1 TB. Aby uzyskać informacje na temat uwzględnionych kwot magazynu i limitów maksymalnego rozmiaru, zobacz Limity zasobów dla pul elastycznych korzystających z modelu zakupów [jednostek DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) lub Limity zasobów dla pul elastycznych korzystających z modelu zakupów opartych na [rdzeniu rdzeni wirtualnych.](resource-limits-vcore-elastic-pools.md)
- Dodatkowy magazyn dla elastycznej puli można aprowizował, zwiększając jego maksymalny rozmiar przy użyciu interfejsu [Azure Portal,](elastic-pool-manage.md#azure-portal) [programu PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)interfejsu wiersza polecenia platformy [Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)lub interfejsu [API REST.](/rest/api/sql/elasticpools/update)
- Cena dodatkowego magazynu dla elastycznej puli to dodatkowa ilość miejsca do magazynowania pomnożona przez dodatkową cenę jednostki magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, [zobacz cennik SQL Database magazynu](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne limity zasobów, [zobacz SQL Database](resource-limits-vcore-elastic-pools.md) limity zasobów oparte na rdzeniu SQL Database pule elastyczne i limity zasobów oparte na jednostkach [DTU — pule elastyczne.](resource-limits-dtu-elastic-pools.md)