---
title: Wycofanie warstwy usług Premium RS
description: Warstwa usługi Premium RS jest wycofywana, a jej obsługa kończy się — Zobacz opcje migracji.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821043"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS warstwy usług (wersja zapoznawcza) jest wycofywana-opcje migracji

W lutym 2018 firma Microsoft ogłosiła, że warstwa usługi Premium RS w Azure SQL Database nie będzie dostępna do ogólnej dostępności i nie będzie już obsługiwana po 31 stycznia 2019. Termin ostateczny tego końca wsparcia został rozszerzony do 30 czerwca 2019. W tym artykule opisano opcje migrowania z warstwy usługi Premium RS do innej warstwy usług. Po 30 czerwca 2019 firma Microsoft automatycznie przeprowadzi migrację baz danych Premium RS do ogólnie dostępnej warstwy usług, która najlepiej odpowiada wymaganiom dotyczącym wydajności bazy danych Premium RS.

Poniżej przedstawiono opcje dotyczące lokalizacji docelowych migracji i cennika, które mogą być odpowiednie dla Premium RS klientów:

- warstwy usług rdzeń wirtualny

  **Ogólnego przeznaczenia** i **krytyczne dla działania firmy** warstwy usług w [modelu zakupu opartym na rdzeń wirtualny](sql-database-service-tiers-vcore.md). Te dwie warstwy usług są ogólnie dostępne. Model zakupów opartych na rdzeń wirtualny oferuje również warstwę usługi w ramach **skalowania** , która dostosowuje zapotrzebowanie na żądanie do potrzeb obciążenia dzięki automatycznemu skalowaniu do 100 TB na bazę danych. Warstwa usługi do skalowania zapewnia wydajność we/wy porównywalną z warstwą usługi Premium w [modelu zakupu opartego](sql-database-service-tiers-dtu.md) na jednostkach DTU pod kątem ceny zbliżonej do Premium RS warstwy usług.
- Cennik dotyczący tworzenia i testowania

  Cennik dotyczący tworzenia [i testowania](https://azure.microsoft.com/pricing/dev-test/) zapewnia oszczędności do 55% w porównaniu z opłatami za licencję w ramach subskrypcji programu Visual Studio.
- Ceny Korzyść użycia hybrydowego platformy Azure i zarezerwowanej pojemności

  [Ceny korzyść użycia hybrydowego platformy Azure i zarezerwowanej pojemności](https://azure.microsoft.com/pricing/details/sql-database/) zapewniają oszczędności do 80% w porównaniu do stawek uwzględnionych w ramach licencji. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) i [Azure SQL Database zarezerwowanej pojemności](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Wykonaj teraz czynności w celu migrowania baz danych Premium RS do alternatywnych SQL Database warstw usług

Zapoznaj się ze wskazówkami w tym artykule wraz z naszymi cenami i dokumentacją, aby ustalić odpowiednie miejsca docelowe migracji dla obciążeń Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrowanie obciążeń intensywnie korzystających z obliczeń i zapisywanie

W przypadku obciążeń Premium RS intensywnie korzystających z obliczeń zalecamy przeprowadzenie migracji do naszej ogólnie dostępnej warstwy usługi Ogólnego przeznaczenia opartej na rdzeń wirtualny, a następnie zaoszczędzenie stawki za licencje za pomocą Korzyść użycia hybrydowego platformy Azure dla ofert SQL Server i zarezerwowanych zdolności produkcyjnych. Jeśli wolisz korzystać z opcji kupowania opartego na jednostkach DTU, możesz migrować bazy danych Premium RS intensywnie korzystające z mocy obliczeniowej do warstwy Standardowa usługi i nadal zapisywać Premium RS w porównaniu z ogólnymi cenami dostępności (jeśli były ogólnie dostępne).

> [!WARNING]
> Migrowanie obciążeń Premium RS do warstw usług premium opartych na jednostkach DTU może zwiększyć miesięczne koszty w porównaniu z bieżącymi Premium RS cenami. Zalecamy uwzględnienie warstw ze skalą lub Krytyczne dla działania firmy przy użyciu cen Korzyść użycia hybrydowego platformy Azure i zarezerwowanych zdolności produkcyjnych w celu utrzymania podobnych lub niższych kosztów niż w Premium RS.

### <a name="premium-rs-databases"></a>Bazy danych Premium RS

|**Jeśli jesteś teraz...**|**Migruj do porównywalnej opartej na rdzeń wirtualny...**|**Migruj do porównywalnych opartych na jednostkach DTU...**|
|---|---|---|
|Premium RS 1|Ogólnego przeznaczenia 1 rdzeń wirtualny (obliczenia)|Standardowa 3|
|Premium RS 2|Ogólnego przeznaczenia 2 rdzeni wirtualnych (obliczenia)|Standardowa 4|
|Premium RS 4|Ogólnego przeznaczenia 4 rdzeni wirtualnych (obliczenia)|Standard 6|
|Premium RS 6|Ogólnego przeznaczenia 6 rdzeni wirtualnych (obliczenia)|Standard 7|

### <a name="premium-rs-pools"></a>Pule Premium RS

|**Jeśli jesteś teraz...**|**Migruj do porównywalnej opartej na rdzeń wirtualny...**|**Migruj do porównywalnych opartych na jednostkach DTU...**|
|---|---|---|
|Premium RS puli jednostek DTU 125|Ogólnego przeznaczenia 1 rdzeń wirtualny (obliczenia)|Standardowa pula 100 jednostek eDTU|
|Premium RS puli jednostek DTU 250|Ogólnego przeznaczenia 2 rdzeni wirtualnych (obliczenia)|Standardowa Pula 250 jednostek eDTU|
|Premium RS puli jednostek DTU 500|Ogólnego przeznaczenia 4 rdzeni wirtualnych (obliczenia)|Standardowa Pula 500 jednostek eDTU|
|Premium RS puli jednostek DTU 1000|Ogólnego przeznaczenia 8 rdzeni wirtualnych (obliczenia)|Standardowa Pula 1000 jednostek eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optymalizowanie oszczędności i wydajności dla obciążeń intensywnie korzystających z operacji we/wy

Zalecamy Migrowanie pojedynczych baz danych z dużą ilością operacji we/wy do naszego rdzeń wirtualnyej warstwy skalowania, obecnie w wersji zapoznawczej oraz pul baz danych intensywnie korzystających z operacji we/wy do naszej ogólnie dostępnej warstwy Krytyczne dla działania firmy, co zapewnia optymalną kombinację wydajności i kosztów.  Następujące opcje oparte na rdzeń wirtualny będą zachować lub poprawić bieżącą wydajność i mogą zaoszczędzić pieniądze w połączeniu z Korzyść użycia hybrydowego platformy Azure i zastrzeżonymi cenami pojemności.

|**Jeśli jesteś teraz...**|**Migruj do porównywalnej opartej na rdzeń wirtualny...**|**Migruj do porównywalnych opartych na jednostkach DTU...**|
|---|---|---|
|Premium RS 1| Rdzeń wirtualny 1 (obliczenia) lub Krytyczne dla działania firmy 1 rdzeń wirtualny (obliczenia)|Premium 1|
|Premium RS 2| Rdzeni wirtualnych 2 (obliczenia) lub Krytyczne dla działania firmy 2 rdzeni wirtualnych (obliczenia|Premium 2|
|Premium RS 4| Rdzeni wirtualnych 4 (obliczenia) lub Krytyczne dla działania firmy 4 rdzeni wirtualnych (obliczenia)|Premium 4
|Premium RS 6| Rdzeni wirtualnych 6 (obliczenia) lub Krytyczne dla działania firmy 6 rdzeni wirtualnych (obliczenia)|Premium 6|

|**Jeśli jesteś teraz...**|**Migruj do porównywalnej opartej na rdzeń wirtualny...**|**Migruj do porównywalnych opartych na jednostkach DTU...**|
|---|---|---|
|Premium RS puli jednostek DTU 125|Krytyczne dla działania firmy 2 rdzeni wirtualnych (obliczenia)|Pula Premium 125 jednostek eDTU|
|Premium RS puli jednostek DTU 250|Krytyczne dla działania firmy 2 rdzeni wirtualnych (obliczenia)|Pula Premium 250 jednostek eDTU|
|Premium RS puli jednostek DTU 500|Krytyczne dla działania firmy 4 rdzeni wirtualnych (obliczenia)|Pula Premium 500 jednostek eDTU|
|Premium RS puli jednostek DTU 1000|Krytyczne dla działania firmy 8 rdzeni wirtualnych (obliczenia)|Pula Premium 1000 jednostek eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Skorzystaj z naszych nowych ofert

Nasze warstwy usług w modelu zakupu opartego na rdzeń wirtualny są uprawnione do ofert specjalnych, które mogą zaoszczędzić do 80% w porównaniu z cenami uwzględnionymi w ramach licencji. Użyj licencji na SQL Server Standard lub Enterprise Edition z aktywnym programem Software Assurance, aby zaoszczędzić do 55% w porównaniu z uwzględnieniem licencji z [korzyść użycia hybrydowego platformy Azure dla SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Możesz połączyć korzyść hybrydową, korzystając z [Azure SQL Database zarezerwowanych cen pojemności](sql-database-reserved-capacity.md) i zaoszczędzić do 80% podczas zatwierdzania z góry do jednego lub trzeciego okresu.  Aktywuj oba korzyści dzisiaj od Azure Portal.

Jeśli masz jakieś pytania lub wątpliwości dotyczące tej zmiany lub potrzebujesz pomocy dotyczącej migracji, skontaktuj się z [firmą Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migracja z warstwy usługi Premium RS do warstwy usługi w modelu jednostek DTU lub rdzeń wirtualny

### <a name="migration-of-a-database"></a>Migracja bazy danych

Migrowanie bazy danych z Premium RS warstwy usług do warstwy usługi w modelu DTU lub rdzeń wirtualny jest podobne do uaktualniania lub obniżenia poziomu między warstwami usług w Premium RSj warstwie usług.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Konwertowanie Premium RS Database przy użyciu kopii bazy danych w bazie danych opartych na jednostkach DTU lub rdzeń wirtualny

Możesz skopiować dowolną bazę danych o rozmiarze Premium RS do bazy danych z rozmiarem obliczeniowym opartym na jednostkach DTU lub rdzeń wirtualny bez ograniczeń lub specjalnej sekwencjonowania, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie przeprowadza synchronizacji danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczej bazy danych, zobacz [SQL Database limitów zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pul elastycznych, zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
