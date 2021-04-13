---
title: Skalowanie zasobów pojedynczej bazy danych
description: W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe dostępne dla pojedynczej bazy danych w Azure SQL Database.
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
ms.openlocfilehash: 47686f457e2579ca8a643de6671c886effefa6f1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313525"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Skalowanie zasobów pojedynczej bazy danych w usłudze Azure SQL Database

W tym artykule opisano, jak skalować zasoby obliczeniowe i magazynowe dostępne dla Azure SQL Database w warstwie obliczeniowej zainicjowanej. Alternatywnie [warstwa obliczeń bezserwerowych](serverless-tier-overview.md) oferuje Skalowanie automatyczne i opłaty za użycie obliczeń na sekundę.

Po początkowym wybraniu liczby rdzeni wirtualnych lub DTU można dynamicznie skalować pojedynczą bazę danych w górę lub w dół na podstawie rzeczywistego środowiska przy użyciu:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Witryna Azure Portal](single-database-manage.md#the-azure-portal)
* [Program PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)
* [Interfejs API REST](/rest/api/sql/databases/update)


Poniższy film wideo pokazuje dynamicznie zmieniające się warstwy usług i rozmiar obliczeń, aby zwiększyć dostępne DTU dla pojedynczej bazy danych.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="impact"></a>Wpływ

Zmiana warstwy usług lub rozmiaru obliczeniowego głównie obejmuje usługę wykonującą następujące czynności:

1. Utwórz nowe wystąpienie obliczeniowe dla bazy danych. 

    Nowe wystąpienie obliczeniowe jest tworzone z żądaną warstwą usługi i rozmiarem obliczeniowym. W przypadku niektórych kombinacji warstwy usług i rozmiaru obliczeń replika bazy danych musi zostać utworzona w nowym wystąpieniu obliczeniowym, które obejmuje kopiowanie danych i może mieć silny wpływ na ogólne opóźnienie. Bez względu na to, że baza danych pozostanie w trybie online w tym kroku, a połączenia będą nadal kierowane do bazy danych w oryginalnym wystąpieniu obliczeniowym.

2. Przełącz routing połączeń do nowego wystąpienia obliczeniowego.

    Istniejące połączenia z bazą danych w oryginalnym wystąpieniu obliczeniowym są usuwane. Wszystkie nowe połączenia są ustanawiane do bazy danych w nowym wystąpieniu obliczeniowym. W przypadku niektórych kombinacji warstwy usług i rozmiaru obliczeń pliki bazy danych są odłączone i ponownie dołączone podczas przełączania.  Bez względu na to, że przełącznik może spowodować powstanie krótkiej przerwy w działaniu usługi, gdy baza danych jest niedostępna na ogół przez mniej niż 30 sekund i często tylko przez kilka sekund. Jeśli istnieją długotrwałe transakcje, które są uruchamiane w przypadku porzucenia połączeń, czas trwania tego kroku może trwać dłużej w celu odzyskania przerwanych transakcji. [Szybsze odzyskiwanie bazy danych](../accelerated-database-recovery.md) może zmniejszyć wpływ przerwań długotrwałych transakcji.

> [!IMPORTANT]
> W żadnym kroku przepływu pracy nie są tracone żadne dane. Upewnij się, że zaimplementowano kilka [logiki ponawiania](troubleshoot-common-connectivity-issues.md) w aplikacjach i składnikach, które używają Azure SQL Database podczas zmiany warstwy usług.

## <a name="latency"></a>Opóźnienie

Szacowane opóźnienie zmiany warstwy usług, skalowanie wielkości obliczeniowej pojedynczej bazy danych lub puli elastycznej, przenoszenie bazy danych do puli elastycznej lub przenoszenie bazy danych między pulami elastycznymi jest opisane w następujący sposób:

|Warstwa usług|Podstawowa pojedyncza baza danych,</br>Standard (S0-S1)|Podstawowa Pula elastyczna,</br>Standardowa (S2-S12), </br>Ogólnego przeznaczenia pojedynczej bazy danych lub puli elastycznej|Premium lub Krytyczne dla działania firmy pojedynczą bazę danych lub pulę elastyczną|Hiperskala
|:---|:---|:---|:---|:---|
|**Podstawowa pojedyncza baza danych, </br> Standard (S0-S1)**|&bull;&nbsp;Stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle, mniej niż 5 minut|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Podstawowa Pula elastyczna, </br> standardowa (S2-S12), </br> ogólnego przeznaczenia pojedyncza baza danych lub Pula elastyczna**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Dla pojedynczych baz danych, stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle mniej niż 5 minut dla pojedynczych baz danych</br>&bull;&nbsp;W przypadku pul elastycznych proporcjonalnie do liczby baz danych|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Premium lub Krytyczne dla działania firmy pojedynczą bazę danych lub pulę elastyczną**|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|&bull;&nbsp;Opóźnienie proporcjonalne do miejsca bazy danych używane z powodu kopiowania danych</br>&bull;&nbsp;Zwykle jest to mniej niż 1 minuta na GB zajętego miejsca|
|**Hiperskala**|NIE DOTYCZY|NIE DOTYCZY|NIE DOTYCZY|&bull;&nbsp;Stałe opóźnienie czasu niezależne od użytego miejsca</br>&bull;&nbsp;Zwykle, mniej niż 2 minuty|

> [!NOTE]
> Ponadto w przypadku warstwy Standardowa (S2-S12) i Ogólnego przeznaczenia bazy danych opóźnienie przeniesienia bazy danych z puli elastycznej lub między pulami elastycznymi będzie proporcjonalne do rozmiaru bazy danych, jeśli baza danych korzysta z magazynu Premium File Share ([PFS](../../storage/files/storage-files-introduction.md)).
>
> Aby określić, czy baza danych korzysta z magazynu PFS, wykonaj następujące zapytanie w kontekście bazy danych. Jeśli wartość w kolumnie AccountType to `PremiumFileStorage` lub `PremiumFileStorage-ZRS` , baza danych korzysta z magazynu PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> Właściwość nadmiarowa strefy będzie domyślnie taka sama w przypadku skalowania z Krytyczne dla działania firmy do warstwy Ogólnego przeznaczenia. Opóźnienie tej zmiany w przypadku włączenia nadmiarowości strefy, a opóźnienie przełączenia nadmiarowości strefy dla warstwy Ogólnego przeznaczenia będzie proporcjonalne do rozmiaru bazy danych.

> [!TIP]
> Aby monitorować operacje w toku, zobacz: [Zarządzanie operacjami przy użyciu interfejsu API REST usługi SQL](/rest/api/sql/operations/list), [Zarządzanie operacjami przy użyciu wiersza polecenia](/cli/azure/sql/db/op), [monitorowanie operacji przy użyciu języka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) i tych dwóch poleceń programu PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) i [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Anulowanie zmian

Operacja zmiany warstwy usług lub ponownego skalowania obliczeń może zostać anulowana.

### <a name="the-azure-portal"></a>Witryna Azure Portal

W bloku przegląd bazy danych przejdź do **powiadomień** i kliknij kafelek z informacją o trwającej operacji:

![Bieżąca operacja](./media/single-database-scale/ongoing-operations.png)

Następnie kliknij przycisk z etykietą **Anuluj tę operację**.

![Anuluj trwającą operację](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

W wierszu polecenia programu PowerShell Ustaw `$resourceGroupName` polecenie, `$serverName` , i `$databaseName` , a następnie uruchom następujące polecenie:

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

- Jeśli przeprowadzasz uaktualnienie do wyższej warstwy usług lub rozmiaru obliczeń, maksymalny rozmiar bazy danych nie zostanie zwiększony, chyba że jawnie określisz większy rozmiar (wartość maksymalna).
- Aby można było obniżyć wersję bazy danych, ilość używanej bazy danych musi być mniejsza niż maksymalny dozwolony rozmiar docelowej warstwy usługi i rozmiaru.
- W przypadku obniżenia poziomu wersji **Premium** do warstwy **standardowa** obowiązuje dodatkowy koszt magazynu, jeśli oba (1) maksymalny rozmiar bazy danych jest obsługiwany w docelowym rozmiarze obliczeniowym i (2) maksymalny rozmiar przekracza ilość dołączonego miejsca docelowego. Jeśli na przykład baza danych P1 o maksymalnym rozmiarze 500 GB to S3, nastąpi zastosowanie dodatkowego kosztu magazynu, ponieważ S3 obsługuje maksymalny rozmiar 1 TB, a uwzględniona ilość miejsca w magazynie wynosi tylko 250 GB. W związku z tym dodatkowa kwota magazynu wynosi 500 GB – 250 GB = 250 GB. Aby uzyskać cennik dodatkowego magazynu, zobacz [Cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość użytego miejsca jest mniejsza niż uwzględniona ilość miejsca w magazynie, można uniknąć tego dodatkowego kosztu, zmniejszając maksymalną wielkość bazy danych do uwzględnionej kwoty.
- Podczas uaktualniania bazy danych z włączoną [replikacją geograficzną](active-geo-replication-configure-portal.md) należy uaktualnić jej pomocnicze bazy danych do żądanej warstwy usług i rozmiaru obliczeń przed uaktualnieniem podstawowej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). W przypadku uaktualniania do innej wersji należy najpierw uaktualnić pomocniczą bazę danych.
- W przypadku obniżenia poziomu bazy danych z włączoną [replikacją geograficzną](active-geo-replication-configure-portal.md) należy zmienić jej podstawowe bazy danych na żądaną warstwę usługi i rozmiar obliczeń przed obniżeniem poziomu pomocniczej bazy danych (ogólne wskazówki dotyczące najlepszej wydajności). W przypadku zmiany wersji na starszą należy najpierw zmienić wersję podstawowej bazy danych.
- Oferowane usługi przywracania różnią się w zależności do warstwy usługi. W przypadku obniżenia poziomu do warstwy **podstawowa** istnieje dolny okres przechowywania kopii zapasowej. Zobacz [Azure SQL Database kopii zapasowych](automated-backups-overview.md).
- Nowe właściwości bazy danych nie są stosowane do momentu zakończenia wprowadzania zmian.
- Gdy kopiowanie danych jest wymagane do skalowania bazy danych (zobacz [opóźnienie](#latency)) podczas zmiany warstwy usług, duże wykorzystanie zasobów współbieżne dla operacji skalowania może spowodować dłuższe czasy skalowania. Dzięki [przyspieszonemu odzyskiwaniu bazy danych (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)wycofywanie długotrwałych transakcji nie jest znaczącym źródłem opóźnień, ale wysokie użycie zasobów może spowodować zmniejszenie zasobów obliczeniowych, magazynu i przepustowości sieci na potrzeby skalowania, szczególnie w przypadku mniejszych rozmiarów obliczeniowych.

## <a name="billing"></a>Rozliczenia

Opłaty są naliczane za każdą godzinę, gdy baza danych istnieje przy użyciu najwyższej wartości warstwy usług + rozmiaru obliczeń, która została zastosowana w tej godzinie, niezależnie od użycia lub od tego, czy baza danych była aktywna przez czas krótszy niż godzina. Jeśli na przykład utworzysz pojedynczą bazę danych i usuniesz ją 5 minut później, opłata zostanie naliczona za jedną godzinę bazy danych.

## <a name="change-storage-size"></a>Zmień rozmiar magazynu

### <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

- Magazyn może być inicjowany do limitu maksymalnego rozmiaru magazynu danych przy użyciu przyrostów 1 GB. Minimalny konfigurowalny magazyn danych to 1 GB. W przypadku maksymalnych limitów rozmiaru magazynu danych w każdym celu usługi Zobacz strony dokumentacji limit zasobów dla [limitów zasobów dla pojedynczych baz danych przy użyciu modelu zakupów rdzeń wirtualny](resource-limits-vcore-single-databases.md) i [limitów zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md).
- Przechowywanie danych dla pojedynczej bazy danych może być obsługiwane przez zwiększenie lub zmniejszenie maksymalnego rozmiaru przy użyciu [Azure Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), interfejsu [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)lub [API REST](/rest/api/sql/databases/update). Jeśli wartość maksymalnego rozmiaru jest określona w bajtach, musi być wielokrotnością 1 GB (1073741824 bajtów).
- Ilość danych, które mogą być przechowywane w plikach danych bazy danych, jest ograniczona przez skonfigurowany maksymalny rozmiar magazynu danych. Oprócz tego magazynu Azure SQL Database automatycznie przydziela więcej niż 30% więcej miejsca do użycia w dzienniku transakcji.
- Azure SQL Database automatycznie przydzieli 32 GB na rdzeń wirtualny dla `tempdb` bazy danych. `tempdb` znajduje się w lokalnym magazynie dysków SSD we wszystkich warstwach usługi.
- Cena magazynu dla pojedynczej bazy danych lub puli elastycznej to suma ilości magazynu danych i magazynu dzienników transakcji pomnożona przez cenę jednostkową magazynu dla warstwy usług. Koszt `tempdb` jest uwzględniony w cenie. Aby uzyskać szczegółowe informacje na temat ceny magazynu, zobacz [Cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Model zakupu oparty na jednostkach DTU

- Cena jednostek DTU dla pojedynczej bazy danych obejmuje pewną ilość miejsca w magazynie bez dodatkowych kosztów. Dodatkowy magazyn poza uwzględnioną ilością można zainicjować w celu uzyskania dodatkowego kosztu do maksymalnego limitu rozmiaru w przyrostach wynoszących 250 GB do 1 TB, a następnie w przyrostach wynoszących 256 GB poza 1 TB. W przypadku uwzględnionych kwot magazynu i maksymalnych limitów rozmiaru zobacz [pojedyncza baza danych: rozmiary magazynu i rozmiary obliczeń](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Dodatkowy magazyn dla pojedynczej bazy danych może być inicjowany przez zwiększenie jego maksymalnego rozmiaru przy użyciu Azure Portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), interfejsu [wiersza polecenia platformy Azure](/cli/azure/sql/db#az-sql-db-update)lub [API REST](/rest/api/sql/databases/update).
- Cena dodatkowego magazynu dla pojedynczej bazy danych to ilość dodatkowego magazynu pomnożona przez dodatkową cenę jednostkową magazynu warstwy usług. Aby uzyskać szczegółowe informacje na temat ceny dodatkowego magazynu, zobacz [Cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

### <a name="geo-replicated-database"></a>Baza danych z replikacją geograficzną

Aby zmienić rozmiar bazy danych zreplikowanej pomocniczej bazy danych, Zmień rozmiar podstawowej bazy danych. Ta zmiana zostanie również zreplikowana i wdrożona w pomocniczej bazie danych.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 i P15, gdy maksymalny rozmiar jest większy niż 1 TB

Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy środkowe i Niemcy północno-wschodnie. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB. Następujące zagadnienia i ograniczenia mają zastosowanie do baz danych P11 i P15 o maksymalnym rozmiarze większym niż 1 TB:

- Jeśli maksymalny rozmiar bazy danych P11 lub P15 został kiedykolwiek ustawiony na wartość większą niż 1 TB, można go przywrócić lub skopiować do bazy danych P11 lub P15.  Następnie można ponownie przeskalować bazę danych do innego rozmiaru obliczeniowego, o ile ilość miejsca przydzieloną w czasie operacji ponownego skalowania nie przekracza maksymalnego limitu rozmiaru nowego rozmiaru obliczenia.
- W przypadku aktywnych scenariuszy replikacji geograficznej:
  - Konfigurowanie relacji replikacji geograficznej: Jeśli podstawowa baza danych to P11 lub P15, muszą być one również P11 lub P15. Mniejsze rozmiary obliczeń są odrzucane jako serwery pomocnicze, ponieważ nie mogą obsługiwać więcej niż 1 TB.
  - Uaktualnianie podstawowej bazy danych w relacji replikacji geograficznej: zmiana maksymalnego rozmiaru na więcej niż 1 TB w podstawowej bazie danych wyzwala tę samą zmianę w pomocniczej bazie danych. Aby zmiana dotycząca podstawowego zaczęła obowiązywać, należy przeprowadzić obie uaktualnienia. Ograniczenia regionów dla opcji ponad 1 TB mają zastosowanie. Jeśli pomocnicza znajduje się w regionie, który nie obsługuje więcej niż 1 TB, podstawowy nie zostanie uaktualniony.
- Korzystanie z usługi Import/Export do ładowania baz danych P11/P15 o więcej niż 1 TB nie jest obsługiwane. Użyj SqlPackage.exe, aby [importować](database-import.md) i [eksportować](database-export.md) dane.

## <a name="next-steps"></a>Następne kroki

Ogólne limity zasobów można znaleźć w temacie [Azure SQL Database limity zasobów opartych na rdzeń wirtualny — pojedyncze bazy danych](resource-limits-vcore-single-databases.md) i [Azure SQL Database limitów zasobów opartych na jednostkach DTU — pojedyncze bazy danych](resource-limits-dtu-single-databases.md).
