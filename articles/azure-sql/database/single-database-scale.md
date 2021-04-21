---
title: Skalowanie zasobów pojedynczej bazy danych
description: W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla pojedynczej bazy danych w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779079"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalowanie zasobów pojedynczej bazy danych w usłudze Azure SQL Database

W tym artykule opisano sposób skalowania zasobów obliczeniowych i magazynowych dostępnych dla Azure SQL Database w aprowizowanych warstwach obliczeniowych. Alternatywnie warstwa [obliczeniowa bez](serverless-tier-overview.md) serwera zapewnia automatyczne skalowanie obliczeń i rachunki za sekundę dla używanych zasobów obliczeniowych.

Po początkowymbraniu liczby rdzeni wirtualnych lub jednostek DTU można dynamicznie skalować pojedynczą bazę danych w górę lub w dół na podstawie rzeczywistego doświadczenia przy użyciu:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Witryna Azure Portal](single-database-manage.md#the-azure-portal)
* [Program PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/sql/db#az_sql_db_update)
* [Interfejs API REST](/rest/api/sql/databases/update)


Poniższy klip wideo pokazuje dynamiczne zmienianie warstwy usługi i rozmiaru obliczeniowego w celu zwiększenia liczby dostępnych jednostek DTU dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w programie Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Wpływ

Zmiana warstwy usługi lub rozmiaru obliczeniowego głównie obejmuje usługę wykonującą następujące kroki:

1. Utwórz nowe wystąpienie obliczeniowe dla bazy danych. 

    Nowe wystąpienie obliczeniowe jest tworzone z żądaną warstwą usługi i rozmiarem obliczeniowym. W przypadku niektórych kombinacji zmian warstwy usługi i rozmiaru zasobów obliczeniowych replika bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, co obejmuje kopiowanie danych i może mieć duży wpływ na całkowite opóźnienie. Niezależnie od tego baza danych pozostaje w trybie online na tym etapie, a połączenia są nadal kierowane do bazy danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełącz routing połączeń do nowego wystąpienia obliczeniowego.

    Istniejące połączenia z bazą danych w oryginalnym wystąpieniu obliczeniowym są porzucane. Wszystkie nowe połączenia są nawiązane z bazą danych w nowym wystąpieniu obliczeniowym. W przypadku niektórych kombinacji zmian warstwy usługi i rozmiaru zasobów obliczeniowych pliki bazy danych są odłączane i ponownie dopinane podczas przełączania.  Niezależnie od tego przełącznik może spowodować krótką przerwę w działaniu usługi, gdy baza danych będzie ogólnie niedostępna przez mniej niż 30 sekund, a często tylko przez kilka sekund. Jeśli istnieją długotrwałe transakcje uruchomione po porzuceniu połączeń, czas trwania tego kroku może trwać dłużej, aby odzyskać przerwane transakcje. [przyspieszone odzyskiwanie bazy danych](../accelerated-database-recovery.md) może zmniejszyć wpływ przerywania długotrwałych transakcji.

> [!IMPORTANT]
> Żadne dane nie są utracone podczas dowolnego kroku przepływu pracy. Upewnij się, że zaimplementowano logikę ponawiania [prób](troubleshoot-common-connectivity-issues.md) w aplikacjach i składnikach, które Azure SQL Database podczas zmiany warstwy usługi.

## <a name="latency"></a>Opóźnienie

Szacowane opóźnienie zmiany warstwy usługi, skalowanie rozmiaru obliczeniowego pojedynczej bazy danych lub elastycznej puli, przenoszenie bazy danych do elastycznej puli lub przenoszenie bazy danych między elastycznymi pulami jest sparametryzowane w następujący sposób:

|Warstwa usług|Podstawowa pojedyncza baza danych,</br>Standardowa (S0–S1)|Podstawowa pula elastyczna,</br>Standardowa (S2–S12), </br>Ogólnego przeznaczenia pojedynczej bazy danych lub elastycznej puli|Premium lub Krytyczne dla działania firmy pojedyncza baza danych lub elastyczna pula|Hiperskala
|:---|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza </br> danych, Standardowa (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;&nbsp;Zwykle mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minuta na GB używanego miejsca|
|**Podstawowa elastyczna </br> pula, Standardowa (S2-S12), </br> Ogólnego przeznaczenia pojedyncza baza danych lub elastyczna pula**|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;W przypadku pojedynczych baz danych stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;Zwykle mniej niż 5 minut w przypadku &nbsp; pojedynczych baz danych</br>&bull;&nbsp;W przypadku pul elastycznych proporcjonalnie do liczby baz danych|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|
|**Premium lub Krytyczne dla działania firmy pojedyncza baza danych lub elastyczna pula**|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do przestrzeni bazy danych używanej z powodu kopiowania danych</br>&bull;&nbsp;Zwykle mniej niż 1 minutę na GB używanego miejsca|
|**Hiperskala**|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|&bull;&nbsp;Stałe opóźnienie czasu niezależne od używanego miejsca</br>&bull;&nbsp;Zwykle mniej niż 2 minuty|

> [!NOTE]
> Ponadto w przypadku baz danych w standardowych (S2-S12) i Ogólnego przeznaczenia opóźnienie przenoszenia bazy danych do/z elastycznej puli lub między pulami elastycznymi będzie proporcjonalne do rozmiaru bazy danych, jeśli baza danych korzysta z magazynu z udziałami plików w chmurze Premium[(PFS).](../../storage/files/storage-files-introduction.md)
>
> Aby określić, czy baza danych używa magazynu PFS, wykonaj następujące zapytanie w kontekście bazy danych. Jeśli wartość w kolumnie AccountType to `PremiumFileStorage` lub , baza danych używa magazynu `PremiumFileStorage-ZRS` PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> Właściwość strefowo nadmiarowa domyślnie pozostanie taka sama podczas skalowania z warstwy Krytyczne dla działania firmy do Ogólnego przeznaczenia warstwy. Opóźnienie dla tej obniżania poziomu po włączeniu nadmiarowości strefy, a także opóźnienie przełączania na nadmiarowość strefową dla warstwy Ogólnego przeznaczenia będzie proporcjonalne do rozmiaru bazy danych.

> [!TIP]
> Aby monitorować operacje w toku, zobacz: Zarządzanie operacjami przy użyciu interfejsu [API REST](/rest/api/sql/operations/list) [SQL,](/cli/azure/sql/db/op)Zarządzanie operacjami przy użyciu interfejsu wiersza polecenia, Monitorowanie operacji przy użyciu języka [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i następujące dwa polecenia programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

## <a name="cancelling-changes"></a>Anulowanie zmian

Operację zmiany warstwy usługi lub operacji ponownego skalowania obliczeń można anulować.

### <a name="the-azure-portal"></a>Witryna Azure Portal

W bloku przeglądu bazy danych przejdź do **powiadomienia** i kliknij kafelek wskazujący, że trwa operacja:

![Bieżąca operacja](./media/single-database-scale/ongoing-operations.png)

Następnie kliknij przycisk z etykietą **Anuluj tę operację.**

![Anulowanie trwającej operacji](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

W wierszu polecenia programu PowerShell ustaw `$resourceGroupName` , i , a następnie uruchom następujące `$serverName` `$databaseName` polecenie:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

- Jeśli uaktualniasz do wyższej warstwy usługi lub rozmiaru obliczeniowego, maksymalny rozmiar bazy danych nie zwiększa się, chyba że jawnie określisz większy rozmiar (maxsize).
- Aby obniżyć poziom bazy danych, używane miejsce w bazie danych musi być mniejsze niż maksymalny dozwolony rozmiar docelowej warstwy usługi i rozmiaru obliczeniowego.
- W przypadku spadku z warstwy **Premium** do warstwy **Standardowa** obowiązuje dodatkowy koszt magazynu, jeśli zarówno (1) maksymalny rozmiar bazy danych jest obsługiwany w docelowym rozmiarze obliczeniowym, jak i (2) maksymalny rozmiar przekracza uwzględnioną ilość miejsca do magazynowania docelowego rozmiaru obliczeniowego. Jeśli na przykład baza danych P1 o maksymalnym rozmiarze 500 GB zostanie w dół do S3, będzie mieć zastosowanie dodatkowy koszt magazynu, ponieważ magazyn S3 obsługuje maksymalny rozmiar 1 TB, a jego uwzględniona ilość miejsca do magazynowania wynosi tylko 250 GB. Dlatego dodatkowa ilość miejsca do magazynowania wynosi 500 GB – 250 GB = 250 GB. Aby uzyskać informacje o cenach dodatkowego magazynu, [zobacz Azure SQL Database cennik .](https://azure.microsoft.com/pricing/details/sql-database/) Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż uwzględniona ilość miejsca w magazynie, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalny rozmiar bazy danych do uwzględnionej kwoty.
- Podczas uaktualniania bazy [](active-geo-replication-configure-portal.md) danych z włączoną replikacją geograficzną uaktualnij pomocnicze bazy danych do żądanej warstwy usługi i rozmiaru obliczeniowego przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). Podczas uaktualniania do innej wersji należy najpierw uaktualnić pomocniczą bazę danych.
- W przypadku obniżania [](active-geo-replication-configure-portal.md) poziomu bazy danych z włączoną replikacją geograficzną należy obniżyć poziom podstawowej bazy danych do żądanej warstwy usługi i rozmiaru obliczeniowego przed obniżeniem warstwy pomocniczej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). W przypadku obniżania poziomu do innej wersji należy najpierw obniżyć poziom podstawowej bazy danych.
- Oferowane usługi przywracania różnią się w zależności do warstwy usługi. W przypadku obniżania poziomu do warstwy **Podstawowa** istnieje niższy okres przechowywania kopii zapasowych. Zobacz [Azure SQL Database Kopii zapasowych.](automated-backups-overview.md)
- Nowe właściwości bazy danych są stosowane dopiero po zakończeniu zmian.
- Gdy kopiowanie danych jest wymagane do skalowania bazy danych (zobacz Opóźnienie [)](#latency)podczas zmiany warstwy usługi, wysokie wykorzystanie zasobów współbieżne z operacją skalowania może powodować dłuższe czasy skalowania. W [przyspieszone odzyskiwanie bazy danych (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)wycofywanie długotrwałych transakcji nie jest znaczącym źródłem opóźnień, ale duże współbieżne użycie zasobów może pozostawić mniej zasobów obliczeniowych, magazynu i przepustowości sieci na skalowanie, szczególnie w przypadku mniejszych rozmiarów zasobów obliczeniowych.

## <a name="billing"></a>Rozliczenia

Opłaty są naliczane za każdą godzinę, gdy baza danych istnieje przy użyciu najwyższej warstwy usługi i rozmiaru zasobów obliczeniowych zastosowanej w ciągu tej godziny, niezależnie od użycia lub tego, czy baza danych była aktywna przez mniej niż godzinę. Jeśli na przykład utworzysz pojedynczą bazę danych i usuniesz ją pięć minut później, rachunek odzwierciedla opłatę za jedną godzinę bazy danych.

## <a name="change-storage-size"></a>Zmienianie rozmiaru magazynu

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn można aprowizować do maksymalnego limitu rozmiaru magazynu danych przy użyciu przyrostów 1 GB. Minimalny konfigurowalny magazyn danych to 1 GB. Aby uzyskać informacje na temat limitów maksymalnego rozmiaru [](resource-limits-vcore-single-databases.md) magazynu danych w każdym celu usługi, zobacz strony dokumentacji limitów zasobów dla limitów zasobów dla pojedynczych baz danych korzystających z modelu zakupów rdzeni wirtualnych i Limity zasobów dla pojedynczych baz danych korzystających z modelu zakupów [jednostek DTU.](resource-limits-dtu-single-databases.md)
- Magazyn danych dla pojedynczej bazy danych można aprowizowany przez zwiększenie lub zmniejszenie maksymalnego rozmiaru przy użyciu poleceń [Azure Portal,](https://portal.azure.com) [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [interfejsu](/cli/azure/sql/db#az_sql_db_update)wiersza polecenia platformy Azure lub interfejsu [API REST.](/rest/api/sql/databases/update) Jeśli wartość maksymalnego rozmiaru jest określona w bajtach, musi być wielokrotnością 1 GB (1073741824 bajtów).
- Ilość danych, które mogą być przechowywane w plikach danych bazy danych, jest ograniczona przez skonfigurowany maksymalny rozmiar magazynu danych. Oprócz tego magazynu program Azure SQL Database automatycznie przydziela o 30% więcej miejsca do magazynowania na dziennik transakcji.
- Azure SQL Database automatycznie przydziela 32 GB na rdzeń wirtualnych dla bazy `tempdb` danych. `tempdb` znajduje się w lokalnym magazynie SSD we wszystkich warstwach usług.
- Cena magazynu dla pojedynczej bazy danych lub elastycznej puli to suma ilości magazynu danych i dziennika transakcji pomnożona przez cenę jednostki magazynu warstwy usług. Koszt jest `tempdb` uwzględniany w cenie. Aby uzyskać szczegółowe informacje na temat ceny magazynu, [zobacz Azure SQL Database cennik.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w programie Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupów oparty na jednostkach DTU

- Cena jednostek DTU dla pojedynczej bazy danych obejmuje określoną ilość miejsca do magazynowania bez dodatkowych kosztów. Dodatkowy magazyn, który przekracza uwzględnioną ilość, można aprowizować na dodatkowy koszt aż do maksymalnego limitu rozmiaru w przyrostach od 250 GB do 1 TB, a następnie w przyrostach 256 GB powyżej 1 TB. Aby uzyskać informacje na temat uwzględnionych ilości magazynów i limitów maksymalnego rozmiaru, zobacz [Pojedyncza baza danych: rozmiary magazynu i rozmiary obliczeniowe.](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Dodatkowy magazyn dla pojedynczej bazy danych można aprowizował, zwiększając jego maksymalny rozmiar przy użyciu języka Azure Portal, [języka Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1)programu [PowerShell,](/powershell/module/az.sql/set-azsqldatabase)interfejsu wiersza polecenia platformy [Azure](/cli/azure/sql/db#az_sql_db_update)lub interfejsu [API REST.](/rest/api/sql/databases/update)
- Cena dodatkowego magazynu dla pojedynczej bazy danych to dodatkowa ilość miejsca do magazynowania pomnożona przez dodatkową cenę jednostki magazynu warstwy usługi. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, [zobacz cennik Azure SQL Database magazynu.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu odzyskania nieużywanego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w programie Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Baza danych z replikacją geograficzną

Aby zmienić rozmiar bazy danych replikowanej pomocniczej bazy danych, zmień rozmiar podstawowej bazy danych. Ta zmiana zostanie następnie zreplikowana i zaimplementowana w pomocniczej bazie danych.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Ograniczenia P11 i P15, gdy maksymalny rozmiar jest większy niż 1 TB

Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy Środkowe i Niemcy Północno-Wschodnie. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Następujące zagadnienia i ograniczenia dotyczą baz danych P11 i P15 o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli maksymalny rozmiar bazy danych P11 lub P15 był kiedykolwiek ustawiony na wartość większą niż 1 TB, można ją przywrócić lub skopiować tylko do bazy danych P11 lub P15.  Następnie bazę danych można przeskalować do innego rozmiaru obliczeniowego, pod warunkiem że ilość miejsca przydzielonego w czasie operacji ponownego skalowania nie przekracza limitów maksymalnego rozmiaru nowego rozmiaru obliczeniowego.
- W przypadku aktywnych scenariuszy replikacji geograficznej:
  - Konfigurowanie relacji replikacji geograficznej: jeśli podstawowa baza danych to P11 lub P15, pomocniczą bazą danych musi być również P11 lub P15. Mniejszy rozmiar obliczeniowy jest odrzucany jako pliki sekundowe, ponieważ nie obsługują one więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: zmiana maksymalnego rozmiaru na ponad 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Oba uaktualnienia muszą zostać pomyślnie wprowadzone, aby zmiana w podstawowej wersji obowiązywała. Obowiązują ograniczenia regionu dla opcji o pojemności większej niż 1 TB. Jeśli pomocniczy region znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowa nie jest uaktualniana.
- Ładowanie baz danych P11/P15 o pojemności większej niż 1 TB przy użyciu usługi Import/Export nie jest obsługiwane. Użyj SqlPackage.exe, aby [zaimportować](database-import.md) [i wyeksportować](database-export.md) dane.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne limity zasobów, [zobacz Azure SQL Database](resource-limits-vcore-single-databases.md) limity zasobów oparte na rdzeniu rdzeni wirtualnych — pojedyncze bazy danych i limity zasobów oparte Azure SQL Database [dtu — pojedyncze bazy danych.](resource-limits-dtu-single-databases.md)
