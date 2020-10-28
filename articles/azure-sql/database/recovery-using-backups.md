---
title: Przywracanie bazy danych z kopii zapasowej
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się więcej o przywracaniu do punktu w czasie, dzięki któremu można wycofać bazę danych w Azure SQL Database lub wystąpienie w wystąpieniu zarządzanym Azure SQL o maksymalnie 35 dni.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 09/26/2019
ms.openlocfilehash: 334495eeef410c42fb45445c400a86ff1b777061
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790342"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Odzyskiwanie przy użyciu zautomatyzowanych kopii zapasowych bazy danych — Azure SQL Database & wystąpienie zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Poniższe opcje są dostępne do odzyskiwania bazy danych przy użyciu [zautomatyzowanych kopii zapasowych bazy danych](automated-backups-overview.md). Oto co możesz zrobić:

- Utwórz nową bazę danych na tym samym serwerze, a następnie Odzyskaj ją do określonego punktu w czasie w okresie przechowywania.
- Utwórz bazę danych na tym samym serwerze, a następnie Odzyskaj do czasu usunięcia usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze w tym samym regionie, a następnie Odzyskaj do punktu najnowszych kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze w innym regionie, Odzyskaj do punktu najnowszych zreplikowanych kopii zapasowych.

Jeśli skonfigurowano [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md), można także utworzyć nową bazę danych na podstawie wszelkich długoterminowych kopii zapasowych przechowywania na dowolnym serwerze.

> [!IMPORTANT]
> Nie można zastąpić istniejącej bazy danych podczas przywracania.

W przypadku korzystania z warstwy usługi w warstwie Standardowa lub Premium przywrócenie bazy danych może pociągnąć za sobą dodatkowy koszt magazynu. Dodatkowy koszt jest naliczany, gdy maksymalny rozmiar przywróconej bazy danych jest większy niż ilość miejsca do magazynowania dołączona do warstwy usługi i poziomu wydajności docelowej bazy danych. Szczegóły cennika dodatkowego magazynu znajdują się na [stronie cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Jeśli rzeczywista ilość zajętego miejsca jest mniejsza niż ilość dostępnego magazynu, możesz uniknąć tego dodatkowego kosztu, ustawiając wartość w polu Maksymalny rozmiar bazy danych na uwzględnioną kwotę.

## <a name="recovery-time"></a>Czas odzyskiwania

Czas odzyskiwania do przywrócenia bazy danych przy użyciu zautomatyzowanych kopii zapasowych bazy danych ma wpływ na kilka czynników:

- Rozmiar bazy danych.
- Rozmiar obliczeń bazy danych.
- Liczba użytych dzienników transakcji.
- Ilość działań, które muszą być odtwarzane w celu odzyskania do punktu przywracania.
- Przepustowość sieci, jeśli przywracanie jest w innym regionie.
- Liczba współbieżnych żądań przywracania w regionie docelowym.

W przypadku dużej lub bardzo aktywnej bazy danych Przywracanie może potrwać kilka godzin. Jeśli w regionie występuje długotrwała awaria, możliwe jest zainicjowanie dużej liczby żądań przywracania geograficznego na potrzeby odzyskiwania po awarii. Jeśli istnieje wiele żądań, można zwiększyć czas odzyskiwania poszczególnych baz danych. Większość przywracania bazy danych kończy się w czasie krótszym niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia liczby współbieżnych żądań przywracania. Te ograniczenia mają zastosowanie do dowolnej kombinacji przywracania do punktu w czasie, przywracania geograficznego i przywracania z kopii zapasowej długoterminowego przechowywania.

| **Opcja wdrażania** | **Maksymalna liczba przetwarzanych żądań współbieżnych** | **Maksymalna liczba przesyłanych żądań współbieżnych** |
| :--- | --: | --: |
|**Pojedyncza baza danych (na subskrypcję)**|10|60|
|**Pula elastyczna (na pulę)**|4|200|


Nie ma wbudowanej metody przywracania całego serwera. Aby zapoznać się z przykładem wykonywania tego zadania, zobacz [Azure SQL Database: pełne odzyskiwanie serwera](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Aby odzyskać sprawność przy użyciu zautomatyzowanych kopii zapasowych, musisz być członkiem roli współautora roli współautor SQL Server lub wystąpienia zarządzanego SQL (w zależności od lokalizacji docelowej odzyskiwania) w subskrypcji lub musi być właścicielem subskrypcji. Aby uzyskać więcej informacji, zobacz [RBAC: role wbudowane](../../role-based-access-control/built-in-roles.md). Odzyskiwanie można przeprowadzić za pomocą Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Za pomocą Azure Portal, [programu PowerShell](/powershell/module/az.sql/restore-azsqldatabase)lub [interfejsu API REST](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)można przywrócić autonomiczną, w puli lub bazę danych wystąpienia do wcześniejszego punktu w czasie. Żądanie może określać dowolną warstwę usług lub rozmiar obliczeń dla przywróconej bazy danych. Upewnij się, że na serwerze, na którym jest przywracana baza danych, jest wystarczająca ilość zasobów. 

Po zakończeniu przywracania tworzy nową bazę danych na tym samym serwerze, na którym znajduje się oryginalna baza danych. Przywrócona baza danych jest naliczana według normalnych stawek na podstawie jej warstwy usług i rozmiaru obliczeniowego. Nie są naliczane opłaty do momentu zakończenia przywracania bazy danych.

Na ogół przywracana jest baza danych do wcześniejszego punktu na potrzeby odzyskiwania. Przywróconą bazę danych można traktować jako zamiennik do oryginalnej bazy danych lub użyć jej jako źródła danych, aby zaktualizować oryginalną bazę danych.

- **Zastąpienie bazy danych**

  Jeśli chcesz, aby przywrócona baza danych była zamiennikiem oryginalnej bazy danych, należy określić rozmiar i warstwę usługi oryginalnej bazy danych. Następnie można zmienić nazwę oryginalnej bazy danych i nadać przywróconą bazę danych oryginalną nazwę przy użyciu polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) w języku T-SQL.

- **Odzyskiwanie danych**

  Jeśli planujesz pobrać dane z przywróconej bazy danych w celu odzyskania sprawności po błędzie użytkownika lub aplikacji, musisz napisać i wykonać skrypt odzyskiwania danych, który wyodrębnia dane z przywróconej bazy danych i ma zastosowanie do oryginalnej bazy danych. Mimo że operacja przywracania może zająć dużo czasu, przywracanie bazy danych jest widoczne na liście baz danych w ramach procesu przywracania. Jeśli baza danych zostanie usunięta podczas przywracania, operacja przywracania zostanie anulowana i nie zostanie naliczona opłata za bazę danych, która nie ukończyła przywracania.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Przywracanie do punktu w czasie za pomocą Azure Portal

Można odzyskać pojedynczą lub wystąpienie bazy danych do punktu w czasie z bloku przegląd bazy danych, która ma zostać przywrócona w Azure Portal.

#### <a name="sql-database"></a>Baza danych SQL

Aby odzyskać bazę danych do punktu w czasie za pomocą Azure Portal, Otwórz stronę przegląd bazy danych i wybierz pozycję **Przywróć** na pasku narzędzi. Wybierz źródło kopii zapasowej, a następnie wybierz punkt kopii zapasowej do punktu w czasie, z którego zostanie utworzona nowa baza danych.

  ![Zrzut ekranu przedstawiający Opcje przywracania bazy danych dla SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby odzyskać bazę danych wystąpienia zarządzanego do punktu w czasie za pomocą Azure Portal, Otwórz stronę przegląd bazy danych i wybierz pozycję **Przywróć** na pasku narzędzi. Wybierz punkt w czasie, z którego zostanie utworzona nowa baza danych.

  ![Zrzut ekranu przedstawiający Opcje przywracania bazy danych dla wystąpienia zarządzanego SQL.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Aby programowo przywrócić bazę danych z kopii zapasowej, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Usuniętą bazę danych można przywrócić do czasu usunięcia lub do wcześniejszego punktu w czasie na tym samym serwerze lub w tym samym wystąpieniu zarządzanym. Można to zrobić za pomocą Azure Portal, [programu PowerShell](/powershell/module/az.sql/restore-azsqldatabase)lub [reszty (createmode = Restore)](/rest/api/sql/databases/createorupdate). Usunięta baza danych zostanie przywrócona przez utworzenie nowej bazy danych z kopii zapasowej.

> [!IMPORTANT]
> Usunięcie serwera lub wystąpienia zarządzanego spowoduje również usunięcie wszystkich jego baz danych i nie można go odzyskać. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Przywracanie bazy danych zostało usunięte przy użyciu Azure Portal

Przywrócono usunięte bazy danych z Azure Portal z serwera lub zasobu wystąpienia zarządzanego.

#### <a name="sql-database"></a>Baza danych SQL

Aby odzyskać usuniętą bazę danych do czasu usunięcia przy użyciu Azure Portal, Otwórz stronę przegląd serwera i wybierz pozycję **usunięte bazy danych** . Wybierz usuniętą bazę danych, którą chcesz przywrócić, a następnie wpisz nazwę nowej bazy danych, która zostanie utworzona z przywróconymi danymi z kopii zapasowej.

  ![Zrzut ekranu przywracania usuniętej bazy danych](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby odzyskać zarządzaną bazę danych przy użyciu Azure Portal, Otwórz stronę Omówienie wystąpienia zarządzanego i wybierz pozycję **usunięte bazy danych** . Wybierz usuniętą bazę danych, którą chcesz przywrócić, a następnie wpisz nazwę nowej bazy danych, która zostanie utworzona z przywróconymi danymi z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych wystąpienia zarządzanego Azure SQL](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Przywracanie bazy danych zostało usunięte przy użyciu programu PowerShell

Poniższe przykładowe skrypty służą do przywracania usuniętej bazy danych dla wystąpienia zarządzanego SQL Database lub SQL za pomocą programu PowerShell.

#### <a name="sql-database"></a>Baza danych SQL

Przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych w Azure SQL Database można znaleźć w temacie [przywracanie bazy danych przy użyciu programu PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych wystąpienia można znaleźć w temacie [przywracanie usuniętej bazy danych wystąpienia przy użyciu programu PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Aby programowo przywrócić usuniętą bazę danych, zobacz programowe [wykonywanie odzyskiwania przy użyciu zautomatyzowanych kopii zapasowych](recovery-using-backups.md).

## <a name="geo-restore"></a>Przywracanie geograficzne

> [!IMPORTANT]
> Przywracanie geograficzne jest dostępne tylko dla baz danych SQL lub wystąpień zarządzanych skonfigurowanych przy użyciu geograficznie nadmiarowego [magazynu kopii zapasowych](automated-backups-overview.md#backup-storage-redundancy).

Bazę danych można przywrócić na SQL Database dowolnym wystąpieniu zarządzanym w dowolnym regionie platformy Azure, na dowolnym z nich, na dowolnym z nich, na dowolnym z nich, na podstawie najnowszych kopii zapasowych replikowanych geograficznie. Przywracanie geograficzne używa kopii zapasowej replikowanej geograficznie jako źródła. Możesz zażądać przywracania geograficznego, nawet jeśli baza danych lub Datacenter jest niedostępna z powodu przestoju.

Przywracanie geograficzne jest domyślną opcją odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Bazę danych można przywrócić na serwerze w dowolnym innym regionie. Istnieje opóźnienie między wykonaniem kopii zapasowej a replikacją geograficzną do obiektu blob platformy Azure w innym regionie. W związku z tym przywrócona baza danych może należeć do jednej godziny w stosunku do oryginalnej bazy danych. Na poniższej ilustracji przedstawiono przywracanie bazy danych z ostatniej dostępnej kopii zapasowej w innym regionie.

![Ilustracja dotycząca przywracania geograficznego](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Przywracanie geograficzne przy użyciu Azure Portal

Z Azure Portal można utworzyć nową bazę danych wystąpienia pojedynczego lub zarządzanego i wybrać dostępną kopię zapasową przywracania geograficznego. Nowo utworzona baza danych zawiera dane kopii zapasowej przywróconej do lokalizacji geograficznej.

#### <a name="sql-database"></a>Baza danych SQL

Aby przywrócić geograficzną pojedynczą bazę danych z Azure Portal w wybranym regionie i serwerze, wykonaj następujące kroki:

1. Z poziomu **pulpitu nawigacyjnego** wybierz pozycję **Dodaj**  >  **Utwórz SQL Database** . Na karcie **podstawowe** wprowadź wymagane informacje.
2. Wybierz **dodatkowe ustawienia** .
3. W obszarze **Użyj istniejących danych** wybierz pozycję **kopia zapasowa** .
4. W obszarze **kopia zapasowa** wybierz pozycję Kopia zapasowa z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje tworzenia SQL Database](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych z kopii zapasowej. Podczas tworzenia bazy danych w Azure SQL Database znajduje się przywrócona kopia zapasowa przywracania geograficznego.

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby przywrócić geograficznie bazę danych wystąpienia zarządzanego z Azure Portal do istniejącego wystąpienia zarządzanego w wybranym regionie, wybierz wystąpienie zarządzane, na którym ma zostać przywrócona baza danych. Wykonaj następujące kroki:

1. Wybierz pozycję **Nowa baza danych** .
2. Wpisz żądaną nazwę bazy danych.
3. W obszarze **Użyj istniejących danych** wybierz pozycję **kopia zapasowa** .
4. Wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje nowej bazy danych](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych. Po utworzeniu bazy danych wystąpienia zawiera przywróconą kopię zapasową przywracania geograficznego.

### <a name="geo-restore-by-using-powershell"></a>Przywracanie geograficzne przy użyciu programu PowerShell

#### <a name="sql-database"></a>Baza danych SQL

Skrypt programu PowerShell pokazujący sposób wykonywania przywracania geograficznego dla pojedynczej bazy danych znajduje się w temacie [Używanie programu PowerShell do przywracania pojedynczej bazy danych do wcześniejszego punktu w czasie](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

W przypadku skryptu programu PowerShell, który pokazuje, jak przeprowadzić przywracanie geograficzne dla bazy danych wystąpienia zarządzanego, zobacz temat [Używanie programu PowerShell do przywracania bazy danych wystąpienia zarządzanego w innym regionie geograficznym](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Zagadnienia dotyczące przywracania geograficznego

Nie można wykonać przywracania do punktu w czasie dla pomocniczej bazy danych. Można to zrobić tylko w podstawowej bazie danych. Aby uzyskać szczegółowe informacje o używaniu przywracania geograficznego w celu odzyskania sprawności przed awarią, zobacz [odzyskiwanie po awarii](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> Przywracanie geograficzne to najbardziej podstawowe rozwiązanie do odzyskiwania po awarii dostępne w przypadku wystąpienia zarządzanego SQL Database i SQL. Polega on na automatycznym tworzeniu kopii zapasowych replikowanych geograficznie z celem punktu odzyskiwania (RPO) do 1 godziny i szacowanym czasem odzyskiwania do 12 godzin. Nie gwarantuje to, że region docelowy będzie miał zdolność do przywrócenia baz danych po awarii regionalnej, ponieważ prawdopodobnie jest to gwałtowny wzrost popytu. Jeśli aplikacja używa stosunkowo małych baz danych i nie ma znaczenia dla działalności firmy, Funkcja przywracania geograficznego jest odpowiednim rozwiązaniem do odzyskiwania po awarii. 
>
> W przypadku aplikacji o krytycznym znaczeniu dla firmy, które wymagają dużych baz danych i muszą zapewnić ciągłość działania, należy użyć [grup z obsługą trybu failover](auto-failover-group-overview.md). Oferuje on znacznie niższy cel punktu odzyskiwania i odzyskania, a pojemność jest zawsze gwarantowana. 
>
> Więcej informacji o opcjach ciągłości biznesowej znajduje się w temacie [Omówienie ciągłości działania](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Odzyskiwanie programistyczne przy użyciu zautomatyzowanych kopii zapasowych

Można również użyć Azure PowerShell lub interfejsu API REST do odzyskiwania. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez wystąpienia zarządzane SQL Database i SQL, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach Azure Resource Manager są dokładnie takie same.

#### <a name="sql-database"></a>Baza danych SQL

Aby przywrócić bazę danych autonomiczną lub w puli, zobacz [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych. |

  > [!TIP]
  > Przykładowy skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie do punktu w czasie dla bazy danych, zobacz [przywracanie bazy danych za pomocą programu PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby przywrócić bazę danych wystąpienia zarządzanego, zobacz [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jedno wystąpienie zarządzane. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera bazę danych wystąpienia. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić bazę danych przy użyciu interfejsu API REST:

| Interfejs API | Opis |
| --- | --- |
| [REST (createmode = Recovery)](/rest/api/sql/databases) |Przywraca bazę danych. |
| [Pobieranie lub aktualizowanie stanu bazy danych](/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania. |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

#### <a name="sql-database"></a>Baza danych SQL

Aby przywrócić bazę danych za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby przywrócić bazę danych wystąpienia zarządzanego przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronią bazy danych przed błędami użytkowników i aplikacji, przypadkowym usunięciem bazy danych i długotrwałym przestojem. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów obliczeniowych.

## <a name="next-steps"></a>Następne kroki

- [Omówienie ciągłości działania](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md)
- [Długoterminowe przechowywanie](long-term-retention-overview.md)
- Aby dowiedzieć się więcej na temat szybszych opcji odzyskiwania, zobacz [aktywne replikacje geograficzne](active-geo-replication-overview.md) lub [grupy autopracy awaryjnej](auto-failover-group-overview.md).