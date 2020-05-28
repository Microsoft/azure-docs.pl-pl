---
title: Długoterminowe przechowywanie kopii zapasowych
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, w jaki sposób usługi Azure SQL Database i wystąpienia zarządzane SQL przechowują pełne kopie zapasowe bazy danych przez okres do 10 lat przy użyciu zasad długoterminowego przechowywania.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 0e562b92db16456956ff2fe1cbec0f1addde87ef
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048287"
---
# <a name="long-term-retention---azure-sql-database--sql-managed-instance"></a>Długoterminowe przechowywanie — Azure SQL Database & wystąpienia zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wiele aplikacji ma przepisy prawne, zgodność lub inne cele biznesowe, które wymagają zachowania kopii zapasowych bazy danych ponad 7-35 dni udostępnionych przez Azure SQL Database i [Automatyczne kopie zapasowe](automated-backups-overview.md)wystąpienia zarządzanego usługi Azure SQL. Za pomocą funkcji długoterminowego przechowywania (LTR) można przechowywać określone SQL Database i wystąpienia zarządzane SQL pełne kopie zapasowe w usłudze Azure Blob Storage z magazynem geograficznie nadmiarowym z dostępem do odczytu przez maksymalnie 10 lat. Następnie można przywrócić wszystkie kopie zapasowe jako nową bazę danych. Aby uzyskać więcej informacji o nadmiarowości usługi Azure Storage, zobacz [nadmiarowość usługi Azure Storage](../../storage/common/storage-redundancy.md). 

Przechowywanie w czasie długim można włączyć dla Azure SQL Database i jest w ograniczonej publicznej wersji zapoznawczej wystąpienia zarządzanego Azure SQL. Ten artykuł zawiera omówienie pojęć dotyczących przechowywania długoterminowego. Aby skonfigurować długoterminowe przechowywanie, zobacz [konfigurowanie Azure SQL Database ltr](long-term-backup-retention-configure.md) i [Konfigurowanie programu Azure SQL Managed instance ltr](../managed-instance/long-term-backup-retention-configure.md). 

> [!NOTE]
> Zadań programu SQL Agent można użyć do zaplanowania [kopii zapasowych bazy danych tylko do kopiowania](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) jako alternatywy dla listy odwołującej więcej niż 35 dni.


## <a name="how-long-term-retention-works"></a>Jak działa długoterminowe przechowywanie
     
Długoterminowe przechowywanie kopii zapasowych (LTR) wykorzystuje pełne kopie zapasowe bazy danych, które są [tworzone automatycznie](automated-backups-overview.md) , aby włączyć przywracanie do punktu w czasie (kopie). Jeśli skonfigurowano zasady LTR, te kopie zapasowe są kopiowane do różnych obiektów BLOB w celu przechowywania długoterminowego. Kopia to zadanie w tle, które nie ma wpływu na wydajność bazy danych. Zasady LTR dla każdej bazy danych SQL mogą również określać częstotliwość tworzenia kopii zapasowych LTR.

Aby włączyć funkcję LTR, można zdefiniować zasady przy użyciu kombinacji czterech parametrów: cotygodniowe przechowywanie kopii zapasowych (W), miesięczne przechowywanie kopii zapasowych (M), roczne przechowywanie kopii zapasowych (Y) i tydzień roku (WeekOfYear). Jeśli określisz W, jedna kopia zapasowa co tydzień zostanie skopiowana do magazynu długoterminowego. W przypadku określenia typu M pierwsza kopia zapasowa każdego miesiąca zostanie skopiowana do magazynu długoterminowego. Jeśli określisz wartość Y, jedna kopia zapasowa w tygodniu określona przez WeekOfYear zostanie skopiowana do magazynu długoterminowego. Jeśli określona WeekOfYear jest w przeszłości podczas konfigurowania zasad, pierwsza kopia zapasowa LTR zostanie utworzona w następnym roku. Każda kopia zapasowa będzie przechowywana w magazynie długoterminowym zgodnie z parametrami zasad skonfigurowanymi podczas tworzenia kopii zapasowej LTR.

> [!NOTE]
> Wszelkie zmiany zasad odliczenia odnoszą się tylko do przyszłych kopii zapasowych. Na przykład jeśli cotygodniowe przechowywanie kopii zapasowych (W), miesięczne przechowywanie kopii zapasowych (M) lub coroczne przechowywanie kopii zapasowych (Y) jest modyfikowane, nowe ustawienie przechowywania będzie stosowane tylko do nowych kopii zapasowych. Przechowywanie istniejących kopii zapasowych nie będzie modyfikowane. Jeśli zachodzi potrzeba usunięcia starych kopii zapasowych w celu wygaśnięcia okresu przechowywania, należy [ręcznie usunąć kopie zapasowe](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Przykłady zasad LTR:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Trzecia pełna kopia zapasowa każdego roku będzie utrzymywana przez pięć lat.
   
- W = 0, M = 3, Y = 0

   Pierwsza pełna kopia zapasowa każdego miesiąca będzie utrzymywana przez trzy miesiące.

- W = 12, M = 0, Y = 0

   Każde cotygodniowe pełne kopie zapasowe będą przechowywane przez 12 tygodni.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Każde cotygodniowe pełne kopie zapasowe będą przechowywane przez sześć tygodni. Oprócz pierwszej pełnej kopii zapasowej każdego miesiąca, która będzie przechowywana przez 12 miesięcy. Z wyjątkiem pełnej kopii zapasowej wykonanej w szesnastym tygodniu roku, która będzie przechowywana przez 10 lat. 

W poniższej tabeli przedstawiono erze i okres ważności długoterminowych kopii zapasowych dla następujących zasad:

W = 12 tygodni (84 dni), M = 12 miesięcy (365 dni), Y = 10 lat (3650 dni), WeekOfYear = 15 (tydzień po 15 kwietnia)

   ![przykład ltr](./media/long-term-retention-overview/ltr-example.png)


Jeśli zmodyfikujesz powyższe zasady i ustawisz wartość z = 0 (bez cotygodniowych kopii zapasowych), erze kopii zapasowych zmienią się tak, jak pokazano w powyższej tabeli według wyróżnionych dat. Ilość miejsca do magazynowania wymagana do zachowania tych kopii zapasowych zostanie odpowiednio zredukowana. 

> [!IMPORTANT]
> Czas poszczególnych kopii zapasowych pojedynczych oddziałów jest kontrolowany przez platformę Azure. Nie można ręcznie utworzyć kopii zapasowej LTR ani kontrolować chronometrażu tworzenia kopii zapasowej. Po skonfigurowaniu zasad LTR może upłynąć do 7 dni, zanim pierwsza kopia zapasowa LTR zostanie wyświetlona na liście dostępnych kopii zapasowych.  


## <a name="geo-replication-and-long-term-backup-retention"></a>Replikacja geograficzna i długoterminowe przechowywanie kopii zapasowych

Jeśli używasz aktywnej replikacji geograficznej lub grup trybu failover jako rozwiązania do ciągłej ciągłości biznesowej, należy przygotować się do przejścia do trybu failover i skonfigurować te same zasady LTR w pomocniczej bazie danych lub wystąpieniu. Koszt magazynu LTR nie zostanie zwiększony, ponieważ kopie zapasowe nie są generowane na podstawie serwerów pomocniczych. Kopie zapasowe są tworzone tylko wtedy, gdy pomocniczy staną się podstawowe, tworzone są kopie zapasowe. Zapewnia to nieprzerwaną generację kopii zapasowych w przypadku wyzwolenia trybu failover, a podstawowy przenosi do regionu pomocniczego. 

> [!NOTE]
> Gdy pierwotna podstawowa baza danych odzyska się z awarii, która spowodowała przejście w tryb failover, stanie się nowym serwerem pomocniczym. W związku z tym tworzenie kopii zapasowej nie zostanie wznowione, a istniejące zasady LTR nie zaczną obowiązywać, dopóki nie staną się pierwotne. 

## <a name="managed-instance-support"></a>Obsługa wystąpienia zarządzanego

Korzystanie z długoterminowego przechowywania kopii zapasowych z wystąpieniem zarządzanym usługi Azure SQL ma następujące ograniczenia:

- **Ograniczona publiczna wersja zapoznawcza** — ta wersja zapoznawcza jest dostępna tylko dla subskrypcji EA i CSP i podlega ograniczonej dostępności.  
- [**Tylko program PowerShell**](../managed-instance/long-term-backup-retention-configure.md) — obecnie nie ma obsługi Azure Portal. Należy włączyć funkcję LTR przy użyciu programu PowerShell. 

Aby zażądać rejestracji, Utwórz [bilet pomocy technicznej systemu Azure](https://azure.microsoft.com/support/create-ticket/). W polu Typ problemu wybierz pozycję problem techniczny, dla opcji usługa wybierz SQL Database wystąpienie zarządzane, a w polu Typ problemu wybierz pozycję **kopia zapasowa, przywracanie i ciągłość działania oraz przechowywanie długoterminowych kopii zapasowych**. W żądaniu Określ, czy chcesz zarejestrować się w ograniczonej publicznej wersji zapoznawczej LTR dla wystąpienia zarządzanego.

## <a name="configure-long-term-backup-retention"></a>Konfigurowanie długoterminowego przechowywania kopii zapasowych

Długoterminowe przechowywanie kopii zapasowych można skonfigurować przy użyciu Azure Portal i programu PowerShell dla Azure SQL Database i programu PowerShell dla wystąpienia zarządzanego Azure SQL. Aby przywrócić bazę danych z magazynu LTR, można wybrać określoną kopię zapasową w oparciu o sygnaturę czasową. Bazę danych można przywrócić na dowolnym istniejącym serwerze lub wystąpieniu zarządzanym w ramach tej samej subskrypcji, w której znajduje się oryginalna baza danych.

Aby dowiedzieć się, jak skonfigurować przechowywanie długoterminowe lub przywrócić bazę danych z kopii zapasowej dla Azure SQL Database przy użyciu Azure Portal lub programu PowerShell, zobacz [zarządzanie Azure SQL Database długoterminowym przechowywaniem kopii zapasowych](long-term-backup-retention-configure.md)

Aby dowiedzieć się, jak skonfigurować przechowywanie długoterminowe lub przywrócić bazę danych z kopii zapasowej dla wystąpienia zarządzanego usługi Azure SQL przy użyciu programu PowerShell, zobacz [Zarządzanie długoterminowym przechowywaniem kopii zapasowych wystąpienia zarządzanego Azure SQL](../managed-instance/long-term-backup-retention-configure.md). 

Aby przywrócić bazę danych z magazynu LTR, można wybrać określoną kopię zapasową w oparciu o sygnaturę czasową. Bazę danych można przywrócić na dowolnym istniejącym serwerze w ramach tej samej subskrypcji, w której znajduje się oryginalna baza danych. Aby dowiedzieć się, jak przywrócić bazę danych z kopii zapasowej LTR przy użyciu Azure Portal lub programu PowerShell, zobacz [zarządzanie Azure SQL Database długoterminowym przechowywaniem kopii zapasowych](long-term-backup-retention-configure.md). W żądaniu Określ, czy chcesz zarejestrować się w ograniczonej publicznej wersji zapoznawczej LTR dla wystąpienia zarządzanego.

## <a name="next-steps"></a>Następne kroki

Ponieważ kopie zapasowe bazy danych chronią dane przed przypadkowym uszkodzeniem lub usunięciem, są one istotną częścią strategii ciągłości działania i odzyskiwania po awarii. Aby dowiedzieć się więcej na temat innych SQL Database rozwiązań zapewniających ciągłość działania firmy, zobacz temat [ciągłość](business-continuity-high-availability-disaster-recover-hadr-overview.md)działania — Omówienie.
