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
ms.date: 09/16/2020
ms.openlocfilehash: 2792a93748600d71c37972058c8e496928543c9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330710"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skalowanie zasobów puli elastycznej w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe dostępne dla pul elastycznych i baz danych w puli w Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Zmień zasoby obliczeniowe (rdzeni wirtualnych lub DTU)

Po początkowym wybraniu liczby rdzeni wirtualnych lub jednostek eDTU można dynamicznie skalować pulę elastyczną w górę lub w dół na podstawie rzeczywistego środowiska przy użyciu [Azure Portal](elastic-pool-manage.md#azure-portal), [programu PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Wpływ zmiany warstwy usług lub skalowanie w poziomie

Zmiana warstwy usług lub rozmiaru obliczeniowego puli elastycznej odbywa się zgodnie z podobnym wzorcem, co w przypadku pojedynczych baz danych, a głównie obejmuje usługę wykonującą następujące czynności:

1. Utwórz nowe wystąpienie obliczeniowe dla elastycznej puli  

    Nowe wystąpienie obliczeniowe puli elastycznej jest tworzone z żądaną warstwą usług i rozmiarem obliczeniowym. W przypadku niektórych kombinacji warstwy usług i rozmiaru obliczeń replika każdej bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, które obejmuje kopiowanie danych i może mieć silny wpływ na ogólne opóźnienie. Bez względu na to, że bazy danych pozostaną w trybie online w tym kroku, a połączenia będą nadal kierowane do baz danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełącz routing połączeń do nowego wystąpienia obliczeniowego

    Istniejące połączenia z bazami danych w oryginalnym wystąpieniu obliczeniowym są usuwane. Wszystkie nowe połączenia są ustanawiane do baz danych w nowym wystąpieniu obliczeniowym. W przypadku niektórych kombinacji warstwy usług i rozmiaru obliczeń pliki bazy danych są odłączone i ponownie dołączone podczas przełączania.  Bez względu na to, że przełączenie może spowodować, że usługa bazy danych jest niedostępna na ogół przez mniej niż 30 sekund i często tylko przez kilka sekund. W przypadku długotrwałych transakcji uruchomionych w przypadku porzucenia połączeń czas trwania tego kroku może trwać dłużej w celu odzyskania przerwanych transakcji. [Szybsze odzyskiwanie bazy danych](../accelerated-database-recovery.md) może zmniejszyć wpływ przerwań długotrwałych transakcji.

> [!IMPORTANT]
> W żadnym kroku przepływu pracy nie są tracone żadne dane.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Opóźnienie zmiany warstwy usług lub ponowne skalowanie rozmiaru obliczeń

Szacowane opóźnienie zmiany warstwy usług, skalowanie wielkości obliczeniowej pojedynczej bazy danych lub puli elastycznej, przenoszenie bazy danych do puli elastycznej lub przenoszenie bazy danych między pulami elastycznymi jest opisane w następujący sposób:

|Warstwa usługi|Podstawowa pojedyncza baza danych,</br>Standard (S0-S1)|Podstawowa Pula elastyczna,</br>Standardowa (S2-S12), </br>Ogólnego przeznaczenia pojedynczej bazy danych lub puli elastycznej|Premium lub Krytyczne dla działania firmy pojedynczą bazę danych lub pulę elastyczną|Hiperskala
|:---|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza danych, </br> Standard (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle, mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Podstawowa Pula elastyczna, </br> standardowa (S2-S12), </br> ogólnego przeznaczenia pojedyncza baza danych lub Pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Dla pojedynczych baz danych, stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle mniej niż 5 minut dla pojedynczych baz danych</br>&bull;&nbsp;W przypadku pul elastycznych proporcjonalnie do liczby baz danych|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Premium lub Krytyczne dla działania firmy pojedynczą bazę danych lub pulę elastyczną**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Hiperskala**|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|&bull;&nbsp;Stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle, mniej niż 2 minuty|

> [!NOTE]
>
> - W przypadku zmiany warstwy usług lub ponownego skalowania obliczeń dla puli elastycznej w celu obliczenia oszacowania należy użyć podsumowania miejsca używanego dla wszystkich baz danych w puli.
> - W przypadku przeniesienia bazy danych do/z puli elastycznej tylko miejsce używane przez bazę danych wpływa na opóźnienie, a nie miejsce używane przez pulę elastyczną.
> - W przypadku pul elastycznych w warstwie Standardowa i Ogólnego przeznaczenia opóźnienie przeniesienia bazy danych do puli elastycznej lub między pulami elastycznymi będzie proporcjonalne do rozmiaru bazy danych, jeśli Pula elastyczna korzysta z magazynu Premium File Share ([PFS](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)). Aby określić, czy pula korzysta z magazynu PFS, wykonaj następujące zapytanie w kontekście każdej bazy danych w puli. Jeśli wartość w kolumnie AccountType to `PremiumFileStorage` lub `PremiumFileStorage-ZRS` , Pula korzysta z magazynu PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Aby monitorować operacje w toku, zobacz: [Zarządzanie operacjami przy użyciu interfejsu API REST usługi SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [Zarządzanie operacjami przy użyciu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i tych dwóch poleceń programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Dodatkowe zagadnienia dotyczące zmiany warstwy usług lub skalowanie zmian rozmiaru

- Gdy downsizing rdzeni wirtualnych lub jednostek eDTU dla puli elastycznej, zajęte miejsce w puli musi być mniejsze niż maksymalny dozwolony rozmiar docelowej warstwy usług i puli jednostek eDTU.
- Podczas ponownego skalowania jednostek eDTU dla puli elastycznej obowiązuje dodatkowy koszt magazynu, jeśli (1) maksymalny rozmiar magazynu puli jest obsługiwany przez pulę docelową, a (2) maksymalny rozmiar magazynu przekracza dołączoną ilość miejsca w puli docelowej. Jeśli na przykład liczba 100 jednostek eDTU w warstwie Standardowa o maksymalnym rozmiarze 100 GB jest downsized do 50 puli jednostek eDTU w warstwie Standardowa, wówczas obowiązuje dodatkowy koszt magazynu, ponieważ docelowa Pula obsługuje maksymalny rozmiar wynoszący 100 GB, a uwzględniona ilość miejsca w magazynie wynosi tylko 50 GB. W związku z tym dodatkowa kwota magazynu wynosi 100 GB – 50 GB = 50 GB. Aby uzyskać cennik dodatkowego magazynu, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość użytego miejsca jest mniejsza niż uwzględniona ilość miejsca w magazynie, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalną wielkość bazy danych do uwzględnionej kwoty.

### <a name="billing-during-rescaling"></a>Rozliczanie podczas ponownego skalowania

Opłaty są naliczane za każdą godzinę, gdy baza danych istnieje przy użyciu najwyższej wartości warstwy usług + rozmiaru obliczeń, która została zastosowana w tej godzinie, niezależnie od użycia lub od tego, czy baza danych była aktywna przez czas krótszy niż godzina. Jeśli na przykład utworzysz pojedynczą bazę danych i usuniesz ją 5 minut później, opłata zostanie naliczona za jedną godzinę bazy danych.

## <a name="change-elastic-pool-storage-size"></a>Zmień rozmiar magazynu elastycznej puli

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Obsługa magazynu może należeć do maksymalnego limitu rozmiaru:

  - W przypadku magazynu w warstwach usługi w warstwie Standardowa lub ogólnego przeznaczenia Zwiększ lub Zmniejsz rozmiar w przyrostach 10 GB
  - W przypadku magazynu w warstwach usługi krytycznej w wersji Premium lub biznesowej Zwiększ lub Zmniejsz rozmiar w przyrostach 250 GB
- Aby można było zainicjować magazyn dla puli elastycznej, można zwiększyć lub zmniejszyć jej maksymalny rozmiar.
- Cena magazynu dla puli elastycznej to wielkość magazynu pomnożona przez cenę jednostkową magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupu oparty na jednostkach DTU

- Cena jednostek eDTU dla puli elastycznej obejmuje pewną ilość miejsca w magazynie bez dodatkowych kosztów. Dodatkowy magazyn poza uwzględnioną ilością można zainicjować w celu uzyskania dodatkowego kosztu do maksymalnego limitu rozmiaru w przyrostach wynoszących 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB poza 1 TB. W przypadku uwzględnionych kwot magazynu i maksymalnych limitów rozmiaru zobacz [Pula elastyczna: rozmiary magazynu i rozmiary obliczeń](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla puli elastycznej można zainicjować przez zwiększenie jego maksymalnego rozmiaru przy użyciu [Azure Portal](elastic-pool-manage.md#azure-portal), [programu PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Cena dodatkowego magazynu dla puli elastycznej to dodatkowa kwota magazynu pomnożona przez dodatkową cenę jednostkową magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Następne kroki

Ogólne limity zasobów można znaleźć w temacie [SQL Database limity zasobów opartych na rdzeń wirtualny — pule elastyczne](resource-limits-vcore-elastic-pools.md) i [SQL Database limitów zasobów opartych na jednostkach DTU — w przypadku pul elastycznych](resource-limits-dtu-elastic-pools.md).
