---
title: Reguły zapory adresów IP
description: Skonfiguruj reguły zapory adresów IP na poziomie serwera dla bazy danych w Azure SQL Database lub zapory usługi Azure Synapse Analytics. Zarządzanie dostępem i konfigurowanie reguł zapory adresów IP na poziomie bazy danych dla SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632803"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database i reguły zapory adresów IP dla usługi Azure Synapse
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Podczas tworzenia nowego serwera w programie Azure SQL Database lub Azure Synapse *Analytics o nazwie* MySQLServer.Database.Windows.NET, na przykład Zapora na poziomie serwera blokuje dostęp do publicznego punktu końcowego dla serwera (dostępną w usłudze ). Dla uproszczenia *SQL Database* jest używany do odwoływania się do SQL Database i usługi Azure Synapse Analytics.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy *wystąpienia zarządzanego usługi Azure SQL*. Aby uzyskać informacje o konfiguracji sieci, zobacz [łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL](../managed-instance/connect-application-instance.md).
>
> Usługa Azure Synapse obsługuje tylko reguły zapory adresów IP na poziomie serwera. Nie obsługuje reguł zapory adresów IP na poziomie bazy danych.

## <a name="how-the-firewall-works"></a>Jak działa Zapora

Próby połączenia z Internetu i platformy Azure muszą przejść przez zaporę, zanim dotrą do serwera lub bazy danych, jak pokazano na poniższym diagramie.

   ![Diagram konfiguracji zapory][1]

### <a name="server-level-ip-firewall-rules"></a>Reguły zapory bazujące na adresach IP na poziomie serwera

Te reguły umożliwiają klientom uzyskiwanie dostępu do całego serwera, czyli wszystkich baz danych zarządzanych przez serwer. Reguły są przechowywane w bazie danych *Master* . Dla serwera można określić maksymalnie 128 reguł zapory bazującej na adresach IP na poziomie serwera. Jeśli jest włączone ustawienie **Zezwalaj na dostęp do tego serwera dla usług i zasobów platformy Azure** , jest to jedyna reguła zapory dla serwera.
  
Reguły zapory adresów IP na poziomie serwera można skonfigurować przy użyciu instrukcji Azure Portal, PowerShell lub Transact-SQL.

- Aby korzystać z portalu lub programu PowerShell, musisz być właścicielem subskrypcji lub współautorem subskrypcji.
- Aby można było używać języka Transact-SQL, należy połączyć się z bazą danych *Master* jako główna nazwa logowania na poziomie serwera lub jako administrator Azure Active Directory. (Reguła zapory adresów IP na poziomie serwera musi być najpierw utworzona przez użytkownika, który ma uprawnienia na poziomie platformy Azure).

> [!NOTE]
> Domyślnie podczas tworzenia nowego logicznego serwera SQL z Azure Portal ustawienie **Zezwalaj na dostęp do tego serwera dla usług i zasobów platformy Azure** ma wartość **nie**.

### <a name="database-level-ip-firewall-rules"></a>Reguły zapory adresów IP na poziomie bazy danych

Reguły zapory adresów IP na poziomie bazy danych umożliwiają klientom dostęp do określonych (bezpiecznych) baz danych. Tworzysz reguły dla każdej bazy danych (w tym bazy danych *Master* ) i są one przechowywane w pojedynczej bazie danych.
  
- Można tworzyć i zarządzać regułami zapory adresów IP na poziomie bazy danych dla baz danych Master i User przy użyciu instrukcji języka Transact-SQL i dopiero po skonfigurowaniu pierwszej zapory na poziomie serwera.
- W przypadku określenia zakresu adresów IP w regule zapory adresów IP na poziomie bazy danych, która znajduje się poza zakresem w regule protokołu IP na poziomie serwera, tylko klienci, którzy mają adresy IP w zakresie poziomu bazy danych, mogą uzyskać dostęp do bazy danych.
- Dla bazy danych można uzyskać maksymalnie 128 reguł zapory adresów IP na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory adresów IP na poziomie bazy danych, zobacz przykład w dalszej części tego artykułu i zobacz [sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Zalecenia dotyczące sposobu ustawiania reguł zapory

Zalecamy używanie reguł zapory adresów IP na poziomie bazy danych zawsze wtedy, gdy jest to możliwe. Taka praktyka zwiększa bezpieczeństwo i sprawia, że baza danych jest bardziej przenośna. Użyj reguł zapory adresów IP na poziomie serwera dla administratorów. Należy ich również używać, gdy istnieje wiele baz danych, które mają te same wymagania dotyczące dostępu, i nie chcesz konfigurować każdej bazy danych indywidualnie.

> [!NOTE]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Reguły zapory adresów IP na poziomie serwera i na poziomie bazy danych

*Czy użytkownicy z jednej bazy danych mają być w pełni odizolowani od innej bazy danych?*

Jeśli *tak*, Użyj reguł zapory adresów IP na poziomie bazy danych, aby udzielić dostępu. Ta metoda pozwala uniknąć używania reguł zapory adresów IP na poziomie serwera, które zezwalają na dostęp za pośrednictwem zapory do wszystkich baz danych. Dzięki temu zmniejszy się głębokość obrony.

*Czy użytkownicy korzystający z adresów IP muszą mieć dostęp do wszystkich baz danych?*

Jeśli *tak*, Użyj reguł zapory adresów IP na poziomie serwera, aby zmniejszyć liczbę ponownych prób skonfigurowania reguł zapory adresów IP.

*Czy osoba lub zespół, który konfiguruje reguły zapory IP, ma dostęp tylko za pomocą Azure Portal, programu PowerShell lub interfejsu API REST?*

W takim przypadku należy użyć reguł zapory adresów IP na poziomie serwera. Reguły zapory adresów IP na poziomie bazy danych można skonfigurować tylko przy użyciu języka Transact-SQL.  

*Czy osoba lub zespół, który konfiguruje reguły zapory adresów IP zabroniony z posiadania uprawnień wysokiego poziomu na poziomie bazy danych?*

W takim przypadku należy użyć reguł zapory adresów IP na poziomie serwera. Aby skonfigurować reguły zapory adresów IP na poziomie bazy danych za pomocą języka Transact-SQL, musisz mieć co najmniej *kontrolę uprawnień bazy* danych na poziomie bazy danych.  

*Czy osoba lub zespół, który konfiguruje lub przeprowadza inspekcję zasad zapory IP, centralnie zarządza regułami zapory IP dla wielu (prawdopodobnie setek) baz danych?*

W tym scenariuszu najlepsze rozwiązania są określane przez Twoje potrzeby i środowisko. Reguły zapory adresów IP na poziomie serwera mogą być łatwiejsze do skonfigurowania, ale skrypty mogą konfigurować reguły na poziomie bazy danych. Nawet jeśli są używane reguły zapory adresów IP na poziomie serwera, może być konieczne przeprowadzenie inspekcji reguł zapory adresów IP na poziomie bazy danych, aby zobaczyć, czy użytkownicy z uprawnieniem *Kontrola* w bazie danych tworzą reguły zapory adresów IP na poziomie bazy danych.

*Czy mogę użyć kombinacji reguł zapory adresów IP na poziomie serwera i na poziomie bazy danych?*

Tak. Niektórzy użytkownicy, tacy jak Administratorzy, mogą potrzebować reguł zapory adresów IP na poziomie serwera. Inni użytkownicy, tacy jak użytkownicy aplikacji bazy danych, mogą potrzebować reguł zapory adresów IP na poziomie bazy danych.

### <a name="connections-from-the-internet"></a>Połączenia z Internetu

Gdy komputer próbuje nawiązać połączenie z serwerem za pośrednictwem Internetu, Zapora najpierw sprawdza źródłowy adres IP żądania względem reguł zapory adresów IP na poziomie bazy danych dla bazy danych, której dotyczy żądanie połączenia.

- Jeśli adres mieści się w zakresie określonym w regułach zapory adresów IP na poziomie bazy danych, zostanie nadane połączenie z bazą danych, która zawiera regułę.
- Jeśli adres nie należy do zakresu w regułach zapory adresów IP na poziomie bazy danych, Zapora sprawdza reguły zapory adresów IP na poziomie serwera. Jeśli adres należy do zakresu, który znajduje się w regułach zapory adresów IP na poziomie serwera, nawiązanie połączenia. Reguły zapory adresów IP na poziomie serwera mają zastosowanie do wszystkich baz danych zarządzanych przez serwer.  
- Jeśli adres nie należy do zakresu, który znajduje się w żadnej z reguł zapory adresów IP na poziomie bazy danych lub serwera, żądanie połączenia kończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do Azure SQL Database z komputera lokalnego, upewnij się, że Zapora w sieci i komputer lokalny zezwalają na komunikację wychodzącą na porcie TCP 1433.

### <a name="connections-from-inside-azure"></a>Połączenia z platformy Azure

Aby umożliwić aplikacjom hostowanym na platformie Azure Łączenie się z programem SQL Server, należy włączyć połączenia platformy Azure. Aby włączyć połączenia platformy Azure, musi istnieć Reguła zapory z początkowym i końcowym adresem IP ustawioną na wartość 0.0.0.0.

Gdy aplikacja z platformy Azure próbuje nawiązać połączenie z serwerem, Zapora sprawdzi, czy połączenia platformy Azure są dozwolone, sprawdzając, czy ta reguła zapory istnieje. Tę funkcję można włączyć bezpośrednio w bloku Azure Portal, przełączając ustawienie **Zezwalaj usługom i zasobom platformy Azure na dostęp do tego serwera** **w ustawieniach** **zapory i sieci wirtualne** . Ustawienie na wartość włączone powoduje utworzenie reguły zapory dla ruchu przychodzącego dla adresu IP 0.0.0.0-0.0.0.0 o nazwie **AllowAllWindowsIP**. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć regułę zapory z początkowym i końcowym adresem IP ustawionym na wartość 0.0.0.0, jeśli nie używasz portalu. 

> [!IMPORTANT]
> Ta opcja umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika ograniczają dostęp tylko do autoryzowanych użytkowników.

## <a name="permissions"></a>Uprawnienia

Aby móc tworzyć reguły zapory bazującej na adresach IP dla serwera Azure SQL i zarządzać nimi, musisz spełniać jedno z następujących wymagań:

- w roli [współautor SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- w roli [programu SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- Właściciel zasobu, który zawiera SQL Server platformy Azure

## <a name="create-and-manage-ip-firewall-rules"></a>Tworzenie reguł zapory bazujących na adresach IP i zarządzanie nimi

Pierwsze ustawienie zapory na poziomie serwera można utworzyć przy użyciu [Azure Portal](https://portal.azure.com/) lub programowo przy użyciu [Azure PowerShell](/powershell/module/az.sql), interfejsu [wiersza polecenia platformy Azure](/cli/azure/sql/server/firewall-rule)lub [interfejsu API REST](/rest/api/sql/firewallrules/createorupdate)platformy Azure. Za pomocą tych metod lub Transact-SQL można tworzyć dodatkowe reguły zapory adresów IP na poziomie serwera i zarządzać nimi.

> [!IMPORTANT]
> Reguły zapory adresów IP na poziomie bazy danych mogą być tworzone i zarządzane tylko przy użyciu języka Transact-SQL.

Aby poprawić wydajność, reguły zapory bazującej na adresach IP na poziomie serwera są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> [Inspekcja bazy danych](../../azure-sql/database/auditing-overview.md) umożliwia inspekcję zmian zapory na poziomie serwera i na poziomie bazy danych.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Użyj Azure Portal, aby zarządzać regułami zapory adresów IP na poziomie serwera

Aby ustawić regułę zapory adresów IP na poziomie serwera w Azure Portal, przejdź do strony przegląd dla bazy danych lub serwera.

> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [Tworzenie bazy danych przy użyciu Azure Portal](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Na stronie Przegląd bazy danych

1. Aby ustawić regułę zapory adresów IP na poziomie serwera na stronie Przegląd bazy danych, wybierz opcję **Ustaw zaporę serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji.

    ![Reguła zapory adresu IP serwera](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Zostanie otwarta strona **Ustawienia zapory** dla serwera.

2. Wybierz pozycję **Dodaj IP klienta** na pasku narzędzi, aby dodać adres IP komputera, którego używasz, a następnie wybierz pozycję **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP.

    ![Ustaw regułę zapory adresów IP na poziomie serwera](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Na stronie Przegląd serwera

Zostanie otwarta strona przegląd dla Twojego serwera. Pokazuje w pełni kwalifikowaną nazwę serwera (na przykład *mynewserver20170403.Database.Windows.NET*) i oferuje opcje dalszej konfiguracji.

1. Aby ustawić regułę na poziomie serwera na tej stronie, wybierz opcję **Zapora** z menu **Ustawienia** po lewej stronie.

2. Wybierz pozycję **Dodaj IP klienta** na pasku narzędzi, aby dodać adres IP komputera, którego używasz, a następnie wybierz pozycję **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP przy użyciu języka Transact-SQL

| Widok wykazu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |baza danych |Wyświetla bieżące reguły zapory adresów IP na poziomie bazy danych |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |baza danych |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie bazy danych |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Bazy danych |Usuwa reguły zapory adresów IP na poziomie bazy danych |

Poniższy przykład przegląda istniejące reguły, włącza zakres adresów IP na serwerze *contoso* i usuwa regułę zapory IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie Dodaj regułę zapory adresów IP na poziomie serwera.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory adresów IP na poziomie serwera, wykonaj *sp_delete_firewall_rule* procedury składowanej. Poniższy przykład usuwa regułę *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie prace programistyczne są teraz dostępne dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń w modułach AZ i AzureRm są zasadniczo identyczne.

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

Poniższy przykład używa programu PowerShell do ustawienia reguły zapory adresów IP na poziomie serwera:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Dla $servername Określ nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np. Określ **mysqldbserver** zamiast **mysqldbserver.Database.Windows.NET**
>
> Przykłady dla programu PowerShell w kontekście przewodnika Szybki Start można znaleźć w temacie [Create DB-PowerShell](powershell-script-content-guide.md) i [Create a Single Database i konfigurowanie reguły zapory adresów IP na poziomie serwera przy użyciu programu PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Używanie interfejsu wiersza polecenia do zarządzania regułami zapory adresów IP na poziomie serwera

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serwer|Tworzy regułę zapory adresu IP serwera|
|[AZ SQL Server firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serwer|Wyświetla reguły zapory adresów IP na serwerze|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serwer|Pokazuje szczegóły reguły zapory IP|
|[AZ SQL Server firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serwer|Aktualizuje regułę zapory IP|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serwer|Usuwa regułę zapory adresów IP|

Poniższy przykład używa interfejsu wiersza polecenia, aby ustawić regułę zapory adresów IP na poziomie serwera:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Dla $servername Określ nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np. Określ **mysqldbserver** zamiast **mysqldbserver.Database.Windows.NET**
>
> Przykład interfejsu wiersza polecenia w kontekście przewodnika Szybki Start zawiera temat [Tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md) i [Tworzenie pojedynczej bazy danych oraz konfigurowanie reguły zapory adresów IP na poziomie serwera przy użyciu interfejsu wiersza polecenia platformy Azure](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu interfejsu API REST

| Interfejs API | Poziom | Opis |
| --- | --- | --- |
| [Wyświetlanie listy reguł zapory](/rest/api/sql/firewallrules/listbyserver) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [Utwórz lub zaktualizuj reguły zapory](/rest/api/sql/firewallrules/createorupdate) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [Usuń reguły zapory](/rest/api/sql/firewallrules/delete) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [Pobierz reguły zapory](/rest/api/sql/firewallrules/get) | Serwer | Pobiera reguły zapory adresów IP na poziomie serwera |

## <a name="troubleshoot-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Gdy dostęp do Azure SQL Database nie zadziała w oczekiwany sposób, należy wziąć pod uwagę następujące kwestie.

- **Konfiguracja zapory lokalnej:**

  Aby komputer mógł uzyskać dostęp do Azure SQL Database, może być konieczne utworzenie na komputerze wyjątku zapory dla portu TCP 1433. Aby nawiązać połączenia w ramach granicy chmury platformy Azure, może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zapoznaj się z sekcją "SQL Database: zewnątrz [i wewnątrz" portów powyżej 1433 dla ADO.NET 4,5 i Azure SQL Database](adonet-v12-develop-direct-route-ports.md).

- **Translacja adresów sieciowych:**

  Z powodu translacji adresów sieciowych (NAT) adres IP używany przez komputer do łączenia się z Azure SQL Database może być inny niż adres IP w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure:
    1. Zaloguj się do portalu.
    1. Przejdź na kartę **Konfiguracja** na serwerze, który obsługuje bazę danych.
    1. **Bieżący adres IP klienta** jest wyświetlany w sekcji **dozwolone adresy IP** . Wybierz pozycję **Dodaj** dla **DOZWOLONYch adresów IP** , aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:**

  Aby zmiany konfiguracji zapory Azure SQL Database zaczęły obowiązywać, może wystąpić maksymalnie pięć minut.

- **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:**

  Jeśli logowanie nie ma uprawnień na serwerze lub hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem. Utworzenie ustawienia *zapory umożliwia klientom podjęcie próby* nawiązania połączenia z serwerem. Klient musi nadal podawać wymagane poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania nazw logowania, zobacz [kontrolowanie i udzielanie dostępu do bazy danych](logins-create-manage.md).

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe korzystające z dynamicznego adresowania IP i masz problemy z uzyskaniem przez zaporę, Wypróbuj jedno z następujących rozwiązań:
  
  - Poproszenie usługodawcy internetowego o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera programu. Dodaj ten zakres adresów IP jako regułę zapory adresów IP.
  - Uzyskaj statyczne adresy IP dla komputerów klienckich. Dodaj adresy IP jako reguły zapory adresów IP.

## <a name="next-steps"></a>Następne kroki

- Upewnij się, że środowisko sieci firmowej zezwala na komunikację przychodzącą z zakresów adresów IP obliczeń (w tym zakresów SQL) używanych przez centra danych platformy Azure. Być może trzeba będzie dodać te adresy IP do listy dozwolonych. Zobacz [zakresy adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Zapoznaj się z naszym przewodnikiem Szybki Start dotyczącym [tworzenia pojedynczej bazy danych w Azure SQL Database](single-database-create-quickstart.md).
- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych w Azure SQL Database z aplikacji typu open source lub innych firm, zobacz [przykłady kodu dla klientów z przewodnikiem Szybki Start, aby Azure SQL Database](connect-query-content-reference-guide.md#libraries).
- Aby uzyskać informacje o dodatkowych portach, które mogą być potrzebne, zobacz sekcję "SQL Database: zewnątrz i wewnątrz" [portów powyżej 1433 for ADO.NET 4,5 i SQL Database](adonet-v12-develop-direct-route-ports.md)
- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
