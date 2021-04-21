---
title: Przywracanie bazy danych z kopii zapasowej
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się więcej na temat przywracania do punktu w czasie, który umożliwia wycofywanie bazy danych w programie Azure SQL Database lub wystąpienia w ciągu Azure SQL Managed Instance do 35 dni.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762087"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Odzyskiwanie przy użyciu automatycznych kopii zapasowych bazy danych — Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Następujące opcje są dostępne dla odzyskiwania bazy danych przy użyciu [automatycznych kopii zapasowych bazy danych.](automated-backups-overview.md) Oto co możesz zrobić:

- Utwórz nową bazę danych na tym samym serwerze odzyskaną do określonego punktu w czasie w okresie przechowywania.
- Utwórz bazę danych na tym samym serwerze odzyskaną do czasu usunięcia usuniętej bazy danych.
- Utwórz nową bazę danych na dowolnym serwerze w tym samym regionie odzyskaną do momentu utworzenia najnowszych kopii zapasowych.
- Utwórz nową bazę danych na dowolnym serwerze w dowolnym innym regionie, odzyskaną do punktu najnowszych replikowanych kopii zapasowych.

Jeśli skonfigurowano długoterminowe [przechowywanie](long-term-retention-overview.md)kopii zapasowych, można również utworzyć nową bazę danych z dowolnej kopii zapasowej przechowywania długoterminowego na dowolnym serwerze.

> [!IMPORTANT]
> Nie można zastąpić istniejącej bazy danych podczas przywracania.

W przypadku korzystania z warstwy usługi Standardowa lub Premium przywracanie bazy danych może wiązać się z dodatkowymi kosztami magazynu. Dodatkowy koszt jest ponoszony, gdy maksymalny rozmiar przywróconej bazy danych jest większy niż ilość miejsca w magazynie uwzględniona w warstwie usługi i poziomie wydajności docelowej bazy danych. Aby uzyskać szczegółowe informacje o cenach dodatkowego magazynu, zobacz stronę [SQL Database cennika.](https://azure.microsoft.com/pricing/details/sql-database/) Jeśli rzeczywista ilość używanego miejsca jest mniejsza niż ilość miejsca do magazynowania, możesz uniknąć tego dodatkowego kosztu, ustawiając maksymalny rozmiar bazy danych na uwzględnioną ilość.

## <a name="recovery-time"></a>Czas odzyskiwania

Na czas odzyskiwania bazy danych przy użyciu automatycznych kopii zapasowych bazy danych ma wpływ kilka czynników:

- Rozmiar bazy danych.
- Rozmiar obliczeniowy bazy danych.
- Liczba zaangażowanych dzienników transakcji.
- Ilość działań, które należy odtworzyć w celu odzyskania do punktu przywracania.
- Przepustowość sieci, jeśli przywracanie jest w innym regionie.
- Liczba współbieżnych żądań przywracania przetwarzanych w regionie docelowym.

W przypadku dużej lub bardzo aktywnej bazy danych przywracanie może potrwać kilka godzin. Jeśli w regionie występuje długotrwała awaria, możliwe jest, że duża liczby żądań przywracania geograficznego zostanie zainicjowana na potrzeby odzyskiwania po awarii. Jeśli istnieje wiele żądań, czas odzyskiwania poszczególnych baz danych może się wydłużyć. Większość przywracania bazy danych kończy się w mniej niż 12 godzin.

W przypadku pojedynczej subskrypcji istnieją ograniczenia liczby współbieżnych żądań przywracania. Te ograniczenia mają zastosowanie do dowolnej kombinacji przywracania do punktu w czasie, przywracania geograficznego i przywracania z kopii zapasowej przechowywanej długoterminowo.

| **Opcja wdrożenia** | **Maksymalna liczba przetwarzanych żądań współbieżnych** | **Maksymalna liczba przesyłanych żądań współbieżnych** |
| :--- | --: | --: |
|**Pojedyncza baza danych (na subskrypcję)**|30|100|
|**Pula elastyczna (na pulę)**|4|2000|


Nie ma wbudowanej metody przywracania całego serwera. Aby uzyskać przykład sposobu wykonania tego zadania, zobacz [Azure SQL Database: Pełne odzyskiwanie serwera.](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)

> [!IMPORTANT]
> Aby odzyskać dane przy użyciu automatycznych kopii zapasowych, musisz być członkiem roli współautora usługi SQL Server lub współautora usługi SQL Managed Instance (w zależności od miejsca docelowego odzyskiwania) w subskrypcji lub być właścicielem subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure RBAC: Built-in roles (Azure RBAC: role wbudowane).](../../role-based-access-control/built-in-roles.md) Odzyskiwanie można uzyskać przy użyciu interfejsu Azure Portal, programu PowerShell lub interfejsu API REST. Nie można używać języka Transact-SQL.

## <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

Bazę danych autonomiczną, bazę danych w puli lub wystąpienie można przywrócić do wcześniejszego punktu w czasie przy użyciu interfejsu Azure Portal, [Programu PowerShell](/powershell/module/az.sql/restore-azsqldatabase)lub interfejsu [API REST.](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.) Żądanie może określić dowolną warstwę usługi lub rozmiar obliczeniowy dla przywróconej bazy danych. Upewnij się, że na serwerze, na którym przywracasz bazę danych, są wystarczające zasoby. 

Po zakończeniu przywracanie tworzy nową bazę danych na tym samym serwerze co oryginalna baza danych. Opłata za przywróconą bazę danych jest naliczana według normalnych stawek na podstawie warstwy usługi i rozmiaru zasobów obliczeniowych. Opłaty nie zostaną naliczone do czasu ukończenia przywracania bazy danych.

Zwykle przywracasz bazę danych do wcześniejszego punktu na potrzeby odzyskiwania. Przywróconą bazę danych można traktować jako zamiennik oryginalnej bazy danych lub użyć jej jako źródła danych do zaktualizowania oryginalnej bazy danych.

- **Wymiana bazy danych**

  Jeśli zamierzasz, aby przywrócona baza danych zastępuje oryginalną bazę danych, określ rozmiar obliczeniowy oryginalnej bazy danych i warstwę usługi. Następnie możesz zmienić nazwę oryginalnej bazy danych i nadać przywróconej bazie danych oryginalną nazwę przy użyciu polecenia [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) w języku T-SQL.

- **Odzyskiwanie danych**

  Jeśli planujesz pobranie danych z przywróconej bazy danych w celu odzyskania danych po błędzie użytkownika lub aplikacji, musisz napisać i wykonać skrypt odzyskiwania danych, który wyodrębnia dane z przywróconej bazy danych i ma zastosowanie do oryginalnej bazy danych. Mimo że operacja przywracania może zająć dużo czasu, przywracanie bazy danych jest widoczne na liście baz danych w całym procesie przywracania. Jeśli usuniesz bazę danych podczas przywracania, operacja przywracania zostanie anulowana i nie zostaną naliczone opłaty za bazę danych, która nie ukończyła przywracania.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Przywracanie do punktu w czasie przy użyciu Azure Portal

Pojedynczą bazę danych lub bazę danych wystąpienia można odzyskać do punktu w czasie z bloku przeglądu bazy danych, którą chcesz przywrócić w Azure Portal.

#### <a name="sql-database"></a>SQL Database

Aby odzyskać bazę danych do punktu w czasie przy użyciu Azure Portal, otwórz  stronę przeglądu bazy danych i wybierz pozycję Przywróć na pasku narzędzi. Wybierz źródło kopii zapasowej, a następnie punkt kopii zapasowej w czasie, z którego zostanie utworzona nowa baza danych.

  ![Zrzut ekranu przedstawiający opcje przywracania bazy danych dla SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby odzyskać bazę danych wystąpienia zarządzanego do punktu w czasie przy użyciu Azure Portal,  otwórz stronę przeglądu bazy danych i wybierz pozycję Przywróć na pasku narzędzi. Wybierz punkt kopii zapasowej w czasie, z którego zostanie utworzona nowa baza danych.

  ![Zrzut ekranu przedstawiający opcje przywracania bazy danych dla wystąpienia zarządzanego SQL.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Aby programowo przywrócić bazę danych z kopii zapasowej, zobacz [Programowe odzyskiwanie przy użyciu automatycznych kopii zapasowych.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Przywracanie usuniętej bazy danych

Usuniętą bazę danych można przywrócić do czasu usunięcia lub wcześniejszego punktu w czasie na tym samym serwerze lub w tym samym wystąpieniu zarządzanym. Można to zrobić za pomocą polecenia Azure Portal, [programu PowerShell](/powershell/module/az.sql/restore-azsqldatabase)lub [interfejsu REST (createMode=Restore).](/rest/api/sql/databases/createorupdate) Usuniętą bazę danych można przywrócić, tworząc nową bazę danych z kopii zapasowej.

> [!IMPORTANT]
> Jeśli usuniesz serwer lub wystąpienie zarządzane, wszystkie jego bazy danych również zostaną usunięte i nie będzie można ich odzyskać. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Usunięto przywracanie bazy danych przy użyciu Azure Portal

Usunięte bazy danych są przywracane z Azure Portal z zasobu serwera lub wystąpienia zarządzanego.

> [!TIP]
> Wyświetlenie ostatnio usuniętych baz danych na  stronie Usunięte bazy danych w programie Azure Portal lub programowe wyświetlanie usuniętych baz danych może potrwać [kilka minut.](#programmatic-recovery-using-automated-backups)

#### <a name="sql-database"></a>SQL Database

Aby odzyskać usuniętą bazę danych do czasu usunięcia przy użyciu Azure Portal, otwórz stronę przeglądu serwera i wybierz pozycję **Usunięte bazy danych.** Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy użyciu danych przywróconych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy danych](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby odzyskać zarządzaną bazę danych przy użyciu Azure Portal, otwórz stronę przeglądu wystąpienia zarządzanego i wybierz pozycję **Usunięte bazy danych.** Wybierz usuniętą bazę danych, którą chcesz przywrócić, i wpisz nazwę nowej bazy danych, która zostanie utworzona przy użyciu danych przywróconych z kopii zapasowej.

  ![Zrzut ekranu przedstawiający przywracanie usuniętej bazy Azure SQL Managed Instance danych](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Przywracanie usuniętej bazy danych przy użyciu programu PowerShell

Użyj następujących przykładowych skryptów, aby przywrócić usuniętą bazę danych dla SQL Database lub SQL Managed Instance przy użyciu programu PowerShell.

#### <a name="sql-database"></a>SQL Database

Aby uzyskać przykładowy skrypt programu PowerShell pokazujący sposób przywracania usuniętej bazy danych w Azure SQL Database, zobacz Restore a database using PowerShell (Przywracanie bazy danych [przy użyciu programu PowerShell).](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby uzyskać przykładowy skrypt programu PowerShell przedstawiający sposób przywracania usuniętej bazy danych wystąpienia, zobacz Restore deleted instance database using PowerShell (Przywracanie bazy danych usuniętego wystąpienia [przy użyciu programu PowerShell)](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Aby programowo przywrócić usuniętą bazę danych, zobacz Programowe wykonywanie [odzyskiwania przy użyciu automatycznych kopii zapasowych.](recovery-using-backups.md)

## <a name="geo-restore"></a>Przywracanie geograficzne

> [!IMPORTANT]
> Przywracanie geograficzne jest dostępne tylko dla baz danych SQL lub wystąpień zarządzanych skonfigurowanych z magazynem geograficznie nadmiarowych [kopii zapasowych.](automated-backups-overview.md#backup-storage-redundancy)

Bazę danych można przywrócić na dowolnym serwerze SQL Database lub bazie danych wystąpienia w dowolnym wystąpieniu zarządzanym w dowolnym regionie świadczenia usługi Azure z najnowszych kopii zapasowych replikowanych geograficznie. Przywracanie geograficzne używa jako źródła kopii zapasowej z replikacją geograficzną. Przywracanie geograficzne można zażądać nawet wtedy, gdy baza danych lub centrum danych są niedostępne z powodu awarii.

Przywracanie geograficzne to domyślna opcja odzyskiwania, gdy baza danych jest niedostępna z powodu zdarzenia w regionie hostingu. Bazę danych można przywrócić na serwerze w dowolnym innym regionie. Istnieje opóźnienie między tworzeniem kopii zapasowej a replikacją geograficzną do obiektu blob platformy Azure w innym regionie. W związku z tym przywrócona baza danych może być maksymalnie o godzinę od oryginalnej bazy danych. Na poniższej ilustracji przedstawiono przywracanie bazy danych z ostatniej dostępnej kopii zapasowej w innym regionie.

![Ilustracja przedstawiająca przywracanie geograficzne](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Przywracanie geograficzne przy użyciu Azure Portal

W Azure Portal utworzysz nową bazę danych wystąpienia zarządzanego lub pojedynczą i wybierzesz dostępną kopię zapasową przywracania geograficznego. Nowo utworzona baza danych zawiera przywrócone geograficznie dane kopii zapasowej.

#### <a name="sql-database"></a>SQL Database

Aby przywrócić geograficznie pojedynczą bazę danych z Azure Portal w regionie i na serwerze, wykonaj następujące kroki:

1. Na **pulpicie nawigacyjnym** wybierz **pozycję Dodaj**  >  **SQL Database**. Na **karcie Podstawowe** wprowadź wymagane informacje.
2. Wybierz **pozycję Dodatkowe ustawienia.**
3. W **przypadku opcji Użyj istniejących danych** wybierz pozycję Kopia **zapasowa**.
4. W **przypadku** opcji Kopia zapasowa wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje SQL Database tworzenia](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych z kopii zapasowej. Gdy tworzysz bazę danych w Azure SQL Database, zawiera ona przywróconą kopię zapasową przywracania geograficznego.

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby geograficznie przywrócić bazę danych wystąpienia zarządzanego z bazy danych Azure Portal do istniejącego wystąpienia zarządzanego w wybranych regionach, wybierz wystąpienie zarządzane, w którym chcesz przywrócić bazę danych. Wykonaj następujące kroki:

1. Wybierz **pozycję Nowa baza danych.**
2. Wpisz nazwę żądanej bazy danych.
3. W **obszarze Użyj istniejących danych** wybierz pozycję Kopia **zapasowa**.
4. Wybierz kopię zapasową z listy dostępnych kopii zapasowych przywracania geograficznego.

    ![Zrzut ekranu przedstawiający opcje nowej bazy danych](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Ukończ proces tworzenia nowej bazy danych. Podczas tworzenia bazy danych wystąpienia zawiera ona przywróconą kopię zapasową z przywracaniem geograficznym.

### <a name="geo-restore-by-using-powershell"></a>Przywracanie geograficzne przy użyciu programu PowerShell

#### <a name="sql-database"></a>SQL Database

Aby uzyskać skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie geograficzne dla pojedynczej bazy danych, zobacz Przywracanie pojedynczej bazy danych do wcześniejszego punktu w czasie przy użyciu [programu PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby uzyskać skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie geograficzne dla bazy danych wystąpienia zarządzanego, zobacz Use [PowerShell to restore a managed instance database to another geo-region](../managed-instance/scripts/restore-geo-backup.md)(Przywracanie bazy danych wystąpienia zarządzanego do innego regionu geograficznego przy użyciu programu PowerShell).

### <a name="geo-restore-considerations"></a>Zagadnienia dotyczące przywracania geograficznego

Nie można wykonać przywracania do punktu w czasie w pomocniczej bazie danych z replikacją geograficzną. Można to zrobić tylko w podstawowej bazie danych. Aby uzyskać szczegółowe informacje na temat używania przywracania geograficznego w celu odzyskania danych po 30. 3. [](../../key-vault/general/disaster-recovery-guidance.md)

> [!IMPORTANT]
> Przywracanie geograficzne to najbardziej podstawowe rozwiązanie do odzyskiwania po awarii dostępne w SQL Database i SQL Managed Instance. Opiera się on na automatycznie utworzonych geograficznie replikowanych kopiach zapasowych z celem punktu odzyskiwania (RPO) do 1 godziny i szacowanym czasem odzyskiwania do 12 godzin. Nie gwarantuje to, że region docelowy będzie miał pojemność do przywracania baz danych po omdliciu regionalnym, ponieważ prawdopodobny jest gwałtowny wzrost zapotrzebowania. Jeśli aplikacja używa stosunkowo małych baz danych i nie ma kluczowego znaczenie dla firmy, przywracanie geograficzne jest odpowiednim rozwiązaniem do odzyskiwania po awarii. 
>
> W przypadku aplikacji o krytycznym znaczeniu dla działania firmy, które wymagają dużych baz danych i muszą zapewnić ciągłość działania, użyj [grup automatycznego trybu failover.](auto-failover-group-overview.md) Oferuje znacznie niższy cel celu czasu odzyskiwania i cel czasu odzyskiwania, a pojemność jest zawsze gwarantowana. 
>
> Aby uzyskać więcej informacji na temat opcji ciągłości działania, zobacz [Overview of business continuity (Omówienie ciągłości działania).](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Odzyskiwanie programowe przy użyciu automatycznych kopii zapasowych

Do odzyskiwania można Azure PowerShell lub interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez usługi SQL Database i SQL Managed Instance, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach Azure Resource Manager są w dużym stopniu identyczne.

#### <a name="sql-database"></a>SQL Database

Aby przywrócić bazę danych autonomiczną lub w puli, [zobacz Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Pobiera usuniętą bazę danych, którą można przywrócić. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Pobiera geograficznie nadmiarową kopię zapasową bazy danych. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Przywraca bazę danych. |

  > [!TIP]
  > Aby uzyskać przykładowy skrypt programu PowerShell, który pokazuje, jak wykonać przywracanie bazy danych do punktu w czasie, zobacz Przywracanie bazy danych [przy użyciu programu PowerShell.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby przywrócić bazę danych wystąpienia zarządzanego, [zobacz Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Polecenie cmdlet | Opis |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Pobiera co najmniej jedno wystąpienie zarządzane. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Pobiera bazę danych wystąpienia. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Przywraca bazę danych wystąpienia. |

### <a name="rest-api"></a>Interfejs API REST

Aby przywrócić bazę danych przy użyciu interfejsu API REST:

| Interfejs API | Opis |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Przywraca bazę danych. |
| [Uzyskiwanie stanu tworzenia lub aktualizowania bazy danych](/rest/api/sql/operations) |Zwraca stan podczas operacji przywracania. |

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

#### <a name="sql-database"></a>SQL Database

Aby przywrócić bazę danych przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az sql db restore](/cli/azure/sql/db#az_sql_db_restore).

#### <a name="sql-managed-instance"></a>Wystąpienie zarządzane SQL

Aby przywrócić bazę danych wystąpienia zarządzanego przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore).

## <a name="summary"></a>Podsumowanie

Automatyczne kopie zapasowe chronią bazy danych przed błędami użytkowników i aplikacji, przypadkowym usunięciem bazy danych i długotrwałymi błędami. Ta wbudowana funkcja jest dostępna dla wszystkich warstw usług i rozmiarów obliczeniowych.

## <a name="next-steps"></a>Następne kroki

- [Omówienie ciągłości działania](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database automatyczne kopie zapasowe](automated-backups-overview.md)
- [Długoterminowe przechowywanie](long-term-retention-overview.md)
- Aby dowiedzieć się więcej o szybszych opcjach odzyskiwania, zobacz [Active geo-replication (Aktywna replikacja geograficzna)](active-geo-replication-overview.md) [lub Auto-failover groups (Grupy automatycznego trybu failover).](auto-failover-group-overview.md)
