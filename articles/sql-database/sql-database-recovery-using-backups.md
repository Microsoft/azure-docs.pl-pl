---
title: Przywracanie bazy danych z kopii zapasowej
description: Dowiedz się więcej o przywracaniu do punktu w czasie, który umożliwia wycofanie bazy danych Azure SQL Database do 35 dni.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268745"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Odzyskiwanie bazy danych Azure SQL Database przy użyciu zautomatyzowanych kopii zapasowych bazy danych

Domyślnie kopie zapasowe Azure SQL Database są przechowywane w replikowanym geograficznie magazynie obiektów BLOB (typ magazynu RA-GRS). Poniższe opcje są dostępne do odzyskiwania bazy danych przy użyciu [zautomatyzowanych kopii zapasowych bazy danych](sql-database-automated-backups.md). Można:

- Utwórz nową bazę danych na tym samym serwerze SQL Database, a następnie Odzyskaj ją do określonego punktu w czasie w okresie przechowywania.
- Utwórz bazę danych na tym samym serwerze SQL Database, Odzyskaj do czasu usunięcia usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze SQL Database w tym samym regionie, a następnie Odzyskaj do punktu najnowszych kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze SQL Database w innym regionie, Odzyskaj do punktu najnowszych zreplikowanych kopii zapasowych.

Jeśli skonfigurowano [długoterminowe przechowywanie kopii zapasowych](sql-database-long-term-retention.md), można także utworzyć nową bazę danych na podstawie dowolnego długoterminowego przechowywania kopii zapasowych na dowolnym serwerze SQL Database.

> [!IMPORTANT]
> Nie można zastąpić istniejącej bazy danych podczas przywracania.

W przypadku korzystania z warstw usług warstwy Standardowa lub Premium przywrócenie bazy danych może pociągnąć za sobą dodatkowy koszt magazynu. Dodatkowy koszt jest naliczany, gdy maksymalny rozmiar przywróconej bazy danych jest większy niż ilość miejsca do magazynowania dołączona do warstwy usługi i poziomu wydajności docelowej bazy danych. Szczegóły cennika dodatkowego magazynu znajdują się na [stronie cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość zajętego miejsca jest mniejsza niż ilość dostępnego magazynu, możesz uniknąć tego dodatkowego kosztu, ustawiając wartość w polu Maksymalny rozmiar bazy danych na uwzględnioną kwotę.

## <a name="recovery-time"></a>Czas odzyskiwania

Czas odzyskiwania do przywrócenia bazy danych przy użyciu zautomatyzowanych kopii zapasowych bazy danych ma wpływ na kilka czynników:

- Rozmiar bazy danych.
- Rozmiar obliczeń bazy danych.
- Liczba użytych dzienników transakcji.
- Ilość działań, które muszą być odtwarzane w celu odzyskania do punktu przywracania.
- Przepustowość sieci, jeśli przywracanie jest w innym regionie.
- Liczba współbieżnych żądań przywracania w regionie docelowym.

W przypadku dużej lub bardzo aktywnej bazy danych Przywracanie może potrwać kilka godzin. Jeśli w regionie występuje długotrwała awaria, możliwe jest zainicjowanie dużej liczby żądań przywracania geograficznego na potrzeby odzyskiwania po awarii. Jeśli istnieje wiele żądań, można zwiększyć czas odzyskiwania poszczególnych baz danych. Większość operacji przywracania bazy danych jest przeprowadzana w czasie krótszym niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia liczby współbieżnych żądań przywracania. Te ograniczenia mają zastosowanie do dowolnej kombinacji przywracania do punktu w czasie, przywracania geograficznego i przywracania z kopii zapasowej długoterminowego przechowywania.

| | **Maksymalna liczba przetwarzanych żądań współbieżnych** | **Maksymalna liczba przesyłanych żądań współbieżnych** |
| :--- | --: | --: |
|Pojedyncza baza danych (na subskrypcję)|10|60|
|Pula elastyczna (na pulę)|4|200|
||||

Nie ma wbudowanej metody przywracania całego serwera. Aby zapoznać się z przykładem wykonywania tego zadania, zobacz [Azure SQL Database: pełne odzyskiwanie serwera](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Aby odzyskać przy użyciu zautomatyzowanych kopii zapasowych, musisz być członkiem roli współautor SQL Server w subskrypcji lub być właścicielem subskrypcji. Aby uzyskać więcej informacji, zobacz [RBAC: role wbudowane](../role-based-access-control/built-in-roles.md). Odzyskiwanie można przeprowadzić za pomocą Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Za pomocą Azure Portal, [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/databases)można przywrócić autonomiczną, w puli lub bazę danych wystąpienia do wcześniejszego punktu w czasie. Żądanie może określać dowolną warstwę usług lub rozmiar obliczeń dla przywróconej bazy danych. Upewnij się, że na serwerze, na którym jest przywracana baza danych, jest wystarczająca ilość zasobów. Po zakończeniu przywracania tworzy nową bazę danych na tym samym serwerze, na którym znajduje się oryginalna baza danych. Przywrócona baza danych jest naliczana według normalnych stawek na podstawie jej warstwy usług i rozmiaru obliczeniowego. Nie są naliczane opłaty do momentu zakończenia przywracania bazy danych.

Na ogół przywracana jest baza danych do wcześniejszego punktu na potrzeby odzyskiwania. Przywróconą bazę danych można traktować jako zamiennik do oryginalnej bazy danych lub użyć jej jako źródła danych, aby zaktualizować oryginalną bazę danych.

- **Zastąpienie bazy danych**

  Jeśli chcesz, aby przywrócona baza danych była zamiennikiem oryginalnej bazy danych, należy określić rozmiar i warstwę usługi oryginalnej bazy danych. Następnie można zmienić nazwę oryginalnej bazy danych i nadać przywróconą bazę danych oryginalną nazwę przy użyciu polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) w języku T-SQL.

- **Odzyskiwanie danych**

  Jeśli planujesz pobrać dane z przywróconej bazy danych w celu odzyskania sprawności po błędzie użytkownika lub aplikacji, musisz napisać i wykonać skrypt odzyskiwania danych, który wyodrębnia dane z przywróconej bazy danych i ma zastosowanie do oryginalnej bazy danych. Mimo że operacja przywracania może zająć dużo czasu, przywracanie bazy danych jest widoczne na liście baz danych w ramach procesu przywracania. Jeśli baza danych zostanie usunięta podczas przywracania, operacja przywracania zostanie anulowana i nie zostanie naliczona opłata za bazę danych, która nie ukończyła przywracania.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Przywracanie do punktu w czasie za pomocą Azure Portal

Możesz odzyskać pojedynczą bazę danych SQL lub wystąpienie do punktu w czasie z bloku przegląd bazy danych, która ma zostać przywrócona w Azure Portal.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Aby odzyskać bazę danych z jednej lub puli do punktu w czasie za pomocą Azure Portal, Otwórz stronę przegląd bazy danych i wybierz pozycję **Przywróć** na pasku narzędzi. Wybierz źródło kopii zapasowej, a następnie wybierz punkt kopii zapasowej do punktu w czasie, z którego zostanie utworzona nowa baza danych. 

  ![Zrzut ekranu przedstawiający Opcje przywracania bazy danych](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby odzyskać bazę danych wystąpienia zarządzanego do punktu w czasie za pomocą Azure Portal, Otwórz stronę przegląd bazy danych i wybierz pozycję **Przywróć** na pasku narzędzi. Wybierz punkt w czasie, z którego zostanie utworzona nowa baza danych. 

  ![Zrzut ekranu przedstawiający Opcje przywracania bazy danych](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Aby programowo przywrócić bazę danych z kopii zapasowej, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Usuniętą bazę danych można przywrócić do czasu usunięcia lub do wcześniejszego punktu w czasie na tym samym serwerze SQL Database lub w tym samym wystąpieniu zarządzanym. Można to zrobić za pomocą Azure Portal, [programu PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)lub [reszty (createmode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Usunięta baza danych zostanie przywrócona przez utworzenie nowej bazy danych z kopii zapasowej.

> [!IMPORTANT]
> Jeśli usuniesz serwer Azure SQL Database lub wystąpienie zarządzane, wszystkie jego bazy danych również zostaną usunięte i nie będzie można go odzyskać. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Przywracanie bazy danych zostało usunięte przy użyciu Azure Portal

Przywrócono usunięte bazy danych z Azure Portal z zasobów serwera i wystąpienia.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Aby odzyskać usuniętą bazę danych z jednej lub puli do czasu usuwania przy użyciu Azure Portal, Otwórz stronę przegląd serwera i wybierz pozycję **usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, a następnie wpisz nazwę nowej bazy danych, która zostanie utworzona z przywróconymi danymi z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych Azure SQL Database](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby odzyskać zarządzaną bazę danych przy użyciu Azure Portal, Otwórz stronę Omówienie wystąpienia zarządzanego i wybierz pozycję **usunięte bazy danych**. Wybierz usuniętą bazę danych, którą chcesz przywrócić, a następnie wpisz nazwę nowej bazy danych, która zostanie utworzona z przywróconymi danymi z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych wystąpienia usługi Azure SQL](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Przywracanie bazy danych zostało usunięte przy użyciu programu PowerShell

Poniższe przykładowe skrypty służą do przywracania usuniętej bazy danych dla Azure SQL Database i wystąpienia zarządzanego przy użyciu programu PowerShell.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych Azure SQL Database można znaleźć w temacie [przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych wystąpienia znajduje się w temacie [przywracanie usuniętej bazy danych na zarządzanym wystąpieniu przy użyciu programu PowerShell](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Aby programowo przywrócić usuniętą bazę danych, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Przywracanie geograficzne

Bazę danych SQL można przywrócić na dowolnym serwerze w dowolnym regionie świadczenia usługi Azure z najnowszych kopii zapasowych replikowanych geograficznie. Przywracanie geograficzne używa kopii zapasowej replikowanej geograficznie jako źródła. Możesz zażądać przywracania geograficznego, nawet jeśli baza danych lub Datacenter jest niedostępna z powodu przestoju.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Bazę danych można przywrócić na serwerze w dowolnym innym regionie. Istnieje opóźnienie między wykonaniem kopii zapasowej a replikacją geograficzną do obiektu blob platformy Azure w innym regionie. W związku z tym przywrócona baza danych może należeć do jednej godziny w stosunku do oryginalnej bazy danych. Na poniższej ilustracji przedstawiono przywracanie bazy danych z ostatniej dostępnej kopii zapasowej w innym regionie.

![Ilustracja dotycząca przywracania geograficznego](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Przywracanie geograficzne przy użyciu Azure Portal

Z Azure Portal można utworzyć nową bazę danych wystąpienia z jedną lub zarządzaną, a następnie wybrać dostępną kopię zapasową przywracania geograficznego. Nowo utworzona baza danych zawiera dane kopii zapasowej przywróconej do lokalizacji geograficznej.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Aby przywrócić geograficzną pojedynczą bazę danych SQL z Azure Portal w wybranym regionie i serwerze, wykonaj następujące kroki:

1. Z poziomu **pulpitu nawigacyjnego**wybierz pozycję **Dodaj** > **Utwórz SQL Database**. Na karcie **podstawowe** wprowadź wymagane informacje.
2. Wybierz **dodatkowe ustawienia**.
3. W obszarze **Użyj istniejących danych**wybierz pozycję **kopia zapasowa**.
4. W obszarze **kopia zapasowa**wybierz pozycję Kopia zapasowa z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje tworzenia SQL Database](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych z kopii zapasowej. Po utworzeniu pojedynczej bazy danych Azure SQL Database zawiera przywróconą kopię zapasową przywracania geograficznego.

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić geograficznie bazę danych wystąpienia zarządzanego z Azure Portal do istniejącego wystąpienia zarządzanego w wybranym regionie, wybierz wystąpienie zarządzane, na którym ma zostać przywrócona baza danych. Wykonaj następujące kroki:

1. Wybierz pozycję **Nowa baza danych**.
2. Wpisz żądaną nazwę bazy danych.
3. W obszarze **Użyj istniejących danych**wybierz pozycję **kopia zapasowa**.
4. Wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje nowej bazy danych](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych. Po utworzeniu bazy danych wystąpienia zawiera przywróconą kopię zapasową przywracania geograficznego.

### <a name="geo-restore-by-using-powershell"></a>Przywracanie geograficzne przy użyciu programu PowerShell

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

W przypadku skryptu programu PowerShell, który pokazuje, jak przeprowadzić przywracanie geograficzne dla pojedynczej bazy danych SQL, zobacz temat [Używanie programu PowerShell do przywracania pojedynczej bazy danych SQL Azure do wcześniejszego punktu w czasie](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

W przypadku skryptu programu PowerShell, który pokazuje, jak przeprowadzić przywracanie geograficzne dla bazy danych wystąpienia zarządzanego, zobacz temat [Używanie programu PowerShell do przywracania bazy danych wystąpienia zarządzanego w innym regionie geograficznym](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Zagadnienia dotyczące przywracania geograficznego

Nie można wykonać przywracania do punktu w czasie dla pomocniczej bazy danych. Można to zrobić tylko w podstawowej bazie danych. Aby uzyskać szczegółowe informacje o używaniu przywracania geograficznego w celu odzyskania sprawności przed awarią, zobacz [odzyskiwanie po awarii](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Przywracanie geograficzne to najbardziej podstawowe rozwiązanie do odzyskiwania po awarii dostępne w SQL Database. Polega on na automatycznym tworzeniu kopii zapasowych replikowanych geograficznie z celem punktu odzyskiwania (RPO) równym 1 godz. oraz szacowanym czasem odzyskiwania wynoszącym maksymalnie 12 godzin. Nie gwarantuje to, że region docelowy będzie miał zdolność do przywrócenia baz danych po awarii regionalnej, ponieważ prawdopodobnie jest to gwałtowny wzrost popytu. Jeśli aplikacja używa stosunkowo małych baz danych i nie ma znaczenia dla działalności firmy, Funkcja przywracania geograficznego jest odpowiednim rozwiązaniem do odzyskiwania po awarii. W przypadku aplikacji o krytycznym znaczeniu dla firmy, które wymagają dużych baz danych i muszą zapewnić ciągłość działania, należy użyć [grup z obsługą trybu failover](sql-database-auto-failover-group.md). Oferuje on znacznie niższy cel punktu odzyskiwania i odzyskania, a pojemność jest zawsze gwarantowana. Aby uzyskać więcej informacji o opcjach ciągłości biznesowej, zobacz [Omówienie ciągłości działania](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programowe wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych

Można również użyć Azure PowerShell lub interfejsu API REST do odzyskiwania. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są dokładnie takie same.

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Aby przywrócić bazę danych autonomiczną lub w puli, zobacz [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych SQL. |

  > [!TIP]
  > Przykładowy skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie do punktu w czasie dla bazy danych, zobacz [przywracanie bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić bazę danych wystąpienia zarządzanego, zobacz [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jedno wystąpienie zarządzane. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera bazę danych wystąpienia. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić pojedynczą lub w puli bazę danych przy użyciu interfejsu API REST:

| Interfejs API | Opis |
| --- | --- |
| [REST (createmode = Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Przywraca bazę danych. |
| [Pobieranie lub aktualizowanie stanu bazy danych](https://docs.microsoft.com/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania. |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

#### <a name="single-azure-sql-database"></a>Pojedyncza baza danych SQL Azure

Aby przywrócić pojedynczą lub pulę baz danych za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Baza danych wystąpienia zarządzanego

Aby przywrócić bazę danych wystąpienia zarządzanego przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronią bazy danych przed błędami użytkowników i aplikacji, przypadkowym usunięciem bazy danych i długotrwałym przestojem. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów obliczeniowych.

## <a name="next-steps"></a>Następne kroki

- [Przegląd ciągłości działania firmy](sql-database-business-continuity.md)
- [SQL Database zautomatyzowane kopie zapasowe](sql-database-automated-backups.md)
- [Długoterminowe przechowywanie](sql-database-long-term-retention.md)
- Aby dowiedzieć się więcej na temat szybszych opcji odzyskiwania, zobacz [aktywne replikacje geograficzne](sql-database-active-geo-replication.md) lub [grupy autopracy awaryjnej](sql-database-auto-failover-group.md).
