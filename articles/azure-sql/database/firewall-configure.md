---
title: Reguły zapory adresów IP
description: Skonfiguruj reguły zapory adresów IP na poziomie serwera dla bazy danych w Azure SQL Database lub Azure Synapse Analytics zapory. Zarządzanie dostępem i konfigurowanie reguł zapory adresów IP na poziomie bazy danych dla SQL Database.
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
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781779"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database i reguły Azure Synapse zapory adresów IP
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Podczas tworzenia nowego serwera w programie Azure SQL Database lub Azure Synapse Analytics o nazwie *mysqlserver* na przykład zapora na poziomie serwera blokuje cały dostęp do publicznego punktu końcowego serwera (który jest dostępny w mysqlserver.database.windows.net *).* Dla uproszczenia *SQL Database* używana do odwoływać się zarówno do SQL Database, jak i Azure Synapse Analytics.

> [!IMPORTANT]
> Ten artykuł nie *dotyczy* *Azure SQL Managed Instance*. Aby uzyskać informacje na temat konfiguracji sieci, zobacz [Connect your application to Azure SQL Managed Instance](../managed-instance/connect-application-instance.md)(Łączenie aplikacji z Azure SQL Managed Instance ).
>
> Azure Synapse obsługuje tylko reguły zapory adresów IP na poziomie serwera. Nie obsługuje reguł zapory adresów IP na poziomie bazy danych.

## <a name="how-the-firewall-works"></a>Jak działa zapora

Próby nawiązaniu połączenia z Internetu i platformy Azure muszą przejść przez zaporę, zanim dotrą do serwera lub bazy danych, jak pokazano na poniższym diagramie.

   ![Diagram konfiguracji zapory][1]

### <a name="server-level-ip-firewall-rules"></a>Reguły zapory bazujące na adresach IP na poziomie serwera

Te reguły umożliwiają klientom uzyskiwanie dostępu do całego serwera, czyli wszystkich baz danych zarządzanych przez serwer. Reguły są przechowywane w bazie *danych master.* Dla serwera można określić maksymalnie 128 reguł zapory bazującej na adresach IP na poziomie serwera. Jeśli masz włączone ustawienie **Zezwalaj** usługom i zasobom platformy Azure na dostęp do tego serwera, będzie to liczone jako pojedyncza reguła zapory dla serwera.
  
Reguły zapory adresów IP na poziomie serwera można skonfigurować przy użyciu instrukcji Azure Portal, PowerShell lub Transact-SQL.

- Aby korzystać z portalu lub programu PowerShell, musisz być właścicielem subskrypcji lub współautorem subskrypcji.
- Aby używać języka Transact-SQL,  musisz nawiązać połączenie z bazą danych master jako główny identyfikator logowania na poziomie serwera lub jako Azure Active Directory administratora. (Reguła zapory adresów IP na poziomie serwera musi najpierw zostać utworzona przez użytkownika, który ma uprawnienia na poziomie platformy Azure).

> [!NOTE]
> Domyślnie podczas tworzenia nowego logicznego serwera SQL na platformie Azure Portal ustawienie Zezwalaj usługom i zasobom platformy **Azure** na dostęp do tego serwera ma wartość **Nie.**

### <a name="database-level-ip-firewall-rules"></a>Reguły zapory adresów IP na poziomie bazy danych

Reguły zapory adresów IP na poziomie bazy danych umożliwiają klientom dostęp do określonych (bezpiecznych) baz danych. Tworzysz reguły dla każdej bazy danych (w tym bazy danych *master)* i są one przechowywane w poszczególnych bazach danych.
  
- Reguły zapory adresów IP na poziomie bazy danych dla baz danych master i baz danych użytkowników można tworzyć i zarządzać nimi tylko przy użyciu instrukcji Języka Transact-SQL i tylko po skonfigurowaniu pierwszej zapory na poziomie serwera.
- W przypadku określenia zakresu adresów IP w regułę zapory adresów IP na poziomie bazy danych, która znajduje się poza zakresem reguły zapory adresów IP na poziomie serwera, tylko ci klienci, którzy mają adresy IP w zakresie na poziomie bazy danych, mogą uzyskać dostęp do bazy danych.
- Dla bazy danych można mieć maksymalnie 128 reguł zapory adresów IP na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory adresów IP na poziomie bazy danych, zobacz przykład w dalszej części tego artykułu i zobacz sp_set_database_firewall_rule [(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Zalecenia dotyczące sposobu ustawienia reguł zapory

Zalecamy używanie reguł zapory adresów IP na poziomie bazy danych zawsze, gdy jest to możliwe. Taka praktyka zwiększa bezpieczeństwo i sprawia, że baza danych jest bardziej przenośna. Użyj reguł zapory adresów IP na poziomie serwera dla administratorów. Należy ich również używać, gdy masz wiele baz danych, które mają takie same wymagania dostępu, i nie chcesz konfigurować poszczególnych baz danych osobno.

> [!NOTE]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Reguły zapory adresów IP na poziomie serwera i na poziomie bazy danych

*Czy użytkownicy jednej bazy danych powinni być całkowicie odizolowani od innej bazy danych?*

Jeśli *tak,* użyj reguł zapory adresów IP na poziomie bazy danych, aby udzielić dostępu. Ta metoda pozwala uniknąć używania reguł zapory adresów IP na poziomie serwera, które zezwalają na dostęp przez zaporę do wszystkich baz danych. Zmniejszy to głębokość obrony.

*Czy użytkownicy adresów IP potrzebują dostępu do wszystkich baz danych?*

Jeśli *tak,* użyj reguł zapory adresów IP na poziomie serwera, aby zmniejszyć liczbę razy, gdy trzeba skonfigurować reguły zapory adresów IP.

*Czy osoba lub zespół, który konfiguruje reguły zapory adresów IP, ma dostęp tylko za pośrednictwem Azure Portal, programu PowerShell lub interfejsu API REST?*

Jeśli tak, należy użyć reguł zapory adresów IP na poziomie serwera. Reguły zapory adresów IP na poziomie bazy danych można skonfigurować tylko za pomocą języka Transact-SQL.  

*Czy osoba lub zespół, który konfiguruje reguły zapory adresów IP, nie ma uprawnień wysokiego poziomu na poziomie bazy danych?*

Jeśli tak, użyj reguł zapory adresów IP na poziomie serwera. Aby skonfigurować reguły zapory adresów IP na poziomie bazy danych za pomocą języka Transact-SQL, musisz mieć co najmniej uprawnienie CONTROL *DATABASE* na poziomie bazy danych.  

*Czy osoba lub zespół, który konfiguruje lub przeprowadza inspekcję reguł zapory adresów IP, centralnie zarządza regułami zapory adresów IP dla wielu (być może setek) baz danych?*

W tym scenariuszu najlepsze rozwiązania są określane przez Twoje potrzeby i środowisko. Reguły zapory adresów IP na poziomie serwera mogą być łatwiejsze do skonfigurowania, ale skrypty mogą konfigurować reguły na poziomie bazy danych. Nawet jeśli używasz reguł zapory adresów IP na poziomie serwera, może być konieczne inspekcja reguł zapory adresów IP na poziomie bazy danych, aby sprawdzić, czy użytkownicy z uprawnieniami *CONTROL* w bazie danych tworzą reguły zapory adresów IP na poziomie bazy danych.

*Czy można używać kombinacji reguł zapory adresów IP na poziomie serwera i na poziomie bazy danych?*

Tak. Niektórzy użytkownicy, tacy jak administratorzy, mogą potrzebować reguł zapory adresów IP na poziomie serwera. Inni użytkownicy, tacy jak użytkownicy aplikacji bazy danych, mogą potrzebować reguł zapory adresów IP na poziomie bazy danych.

### <a name="connections-from-the-internet"></a>Połączenia z Internetu

Gdy komputer próbuje połączyć się z serwerem z Internetu, zapora najpierw sprawdza adres IP żądania względem reguł zapory adresów IP na poziomie bazy danych dla bazy danych, której żąda połączenie.

- Jeśli adres znajduje się w zakresie określonym w regułach zapory adresów IP na poziomie bazy danych, zostanie przyznane połączenie z bazą danych zawierającą regułę.
- Jeśli adres nie znajduje się w zakresie reguł zapory adresów IP na poziomie bazy danych, zapora sprawdza reguły zapory adresów IP na poziomie serwera. Jeśli adres znajduje się w zakresie, który znajduje się w regułach zapory adresów IP na poziomie serwera, połączenie jest udzielane. Reguły zapory adresów IP na poziomie serwera mają zastosowanie do wszystkich baz danych zarządzanych przez serwer.  
- Jeśli adres nie należy do zakresu, który znajduje się w żadnej z reguł zapory adresów IP na poziomie bazy danych lub serwera, żądanie połączenia kończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać Azure SQL Database z komputera lokalnego, upewnij się, że zapora w sieci i komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.

### <a name="connections-from-inside-azure"></a>Połączenia z wewnątrz platformy Azure

Aby umożliwić aplikacjom hostowanych na platformie Azure łączenie się z serwerem SQL, należy włączyć połączenia platformy Azure. Aby włączyć połączenia platformy Azure, musi być reguła zapory z początkowym i kończącym się adresem IP ustawionym na 0.0.0.0.

Gdy aplikacja z platformy Azure próbuje nawiązać połączenie z serwerem, zapora sprawdza, czy połączenia platformy Azure są dozwolone, weryfikując, czy ta reguła zapory istnieje. Tę możliwość można włączyć bezpośrednio z bloku Azure Portal, przełączając pozycję Zezwalaj  usługom i zasobom platformy **Azure** na dostęp do tego serwera na WŁ. w ustawieniach Zapory **i sieci** wirtualne. Ustawienie wartości ON powoduje utworzenie reguły zapory dla ruchu przychodzącego dla adresów IP 0.0.0.0–0.0.0 o nazwie **AllowAllWindowsIP.** Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć regułę zapory z adresami IP na początku i na końcu ustawionymi na 0.0.0.0, jeśli nie używasz portalu. 

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę tak, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. Jeśli wybierzesz tę opcję, upewnij się, że uprawnienia logowania i użytkownika ograniczają dostęp tylko do autoryzowanych użytkowników.

## <a name="permissions"></a>Uprawnienia

Aby móc tworzyć reguły zapory bazującej na adresach IP dla serwera Azure SQL i zarządzać nimi, musisz spełniać jedno z następujących wymagań:

- w roli [SQL Server współautora](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- w roli [Menedżera zabezpieczeń SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- właściciel zasobu, który zawiera Azure SQL Server

## <a name="create-and-manage-ip-firewall-rules"></a>Tworzenie reguł zapory bazujących na adresach IP i zarządzanie nimi

Pierwsze ustawienie zapory na poziomie serwera tworzy się przy użyciu interfejsu [Azure Portal](https://portal.azure.com/) lub programowo przy użyciu interfejsu [Azure PowerShell,](/powershell/module/az.sql)interfejsu wiersza polecenia platformy [Azure](/cli/azure/sql/server/firewall-rule)lub interfejsu [API REST platformy](/rest/api/sql/firewallrules/createorupdate)Azure. Dodatkowe reguły zapory adresów IP na poziomie serwera można tworzyć i zarządzać nimi przy użyciu tych metod lub języka Transact-SQL.

> [!IMPORTANT]
> Reguły zapory adresów IP na poziomie bazy danych można tworzyć i zarządzać nimi tylko przy użyciu języka Transact-SQL.

Aby poprawić wydajność, reguły zapory bazującej na adresach IP na poziomie serwera są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Inspekcja [bazy danych umożliwia inspekcję](../../azure-sql/database/auditing-overview.md) zmian zapory na poziomie serwera i bazy danych.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Korzystanie z Azure Portal do zarządzania regułami zapory adresów IP na poziomie serwera

Aby ustawić regułę zapory adresów IP na poziomie serwera w Azure Portal, przejdź do strony przeglądu bazy danych lub serwera.

> [!TIP]
> Aby uzyskać samouczek, [zobacz Tworzenie bazy danych przy użyciu Azure Portal](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Na stronie przeglądu bazy danych

1. Aby ustawić regułę zapory adresów IP na poziomie  serwera na stronie przeglądu bazy danych, wybierz pozycję Ustaw zaporę serwera na pasku narzędzi, jak pokazano na poniższej ilustracji.

    ![Reguła zapory dla adresów IP serwera](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Zostanie **otwarta strona** Ustawienia zapory dla serwera.

2. Wybierz **pozycję Dodaj adres IP** klienta na pasku narzędzi, aby dodać adres IP komputera, z których korzystasz, a następnie wybierz pozycję **Zapisz.** Dla bieżącego adresu IP tworzona jest reguła zapory adresów IP na poziomie serwera.

    ![Ustawianie reguły zapory adresów IP na poziomie serwera](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Na stronie przeglądu serwera

Zostanie otwarta strona przeglądu serwera. Wyświetla w pełni kwalifikowaną nazwę serwera (na przykład *mynewserver20170403.database.windows.net*) i udostępnia opcje dalszej konfiguracji.

1. Aby ustawić regułę na poziomie serwera na tej stronie, wybierz **pozycję** Zapora z menu **Ustawienia** po lewej stronie.

2. Wybierz **pozycję Dodaj adres IP** klienta na pasku narzędzi, aby dodać adres IP komputera, z których korzystasz, a następnie wybierz pozycję **Zapisz.** Dla bieżącego adresu IP tworzona jest reguła zapory adresów IP na poziomie serwera.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP przy użyciu języka Transact-SQL

| Widok katalogu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |baza danych |Wyświetla bieżące reguły zapory adresów IP na poziomie bazy danych |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |baza danych |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie bazy danych |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Bazy danych |Usuwa reguły zapory adresów IP na poziomie bazy danych |

Poniższy przykład obejmuje przegląd istniejących reguł, włączenie zakresu adresów IP na serwerze *Contoso* i usunięcie reguły zapory adresów IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie dodaj regułę zapory adresów IP na poziomie serwera.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory adresów IP na poziomie serwera, wykonaj *sp_delete_firewall_rule* procedury składowanej. Poniższy przykład usuwa regułę *ContosoFirewallRule:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie prace programowe są teraz dostępne dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w modułach Az i AzureRm są w znacznym stopniu identyczne.

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

W poniższym przykładzie użyto programu PowerShell do ustawienia reguły zapory adresów IP na poziomie serwera:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Na $servername nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np. określ **mysqldbserver** zamiast **mysqldbserver.database.windows.net**
>
> Przykłady programu PowerShell w kontekście przewodnika Szybki start można znaleźć w tematach Create DB - PowerShell (Tworzenie bazy danych — PowerShell) i Create a single database (Tworzenie pojedynczej bazy danych) i Configure [a server-level IP firewall rule using PowerShell](scripts/create-and-configure-database-powershell.md)(Tworzenie bazy danych — [powershell)](powershell-script-content-guide.md) i konfigurowanie reguły zapory adresów IP na poziomie serwera przy użyciu programu PowerShell.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu interfejsu wiersza polecenia

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Serwer|Tworzy regułę zapory adresów IP serwera|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Serwer|Wyświetla listę reguł zapory adresów IP na serwerze|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Serwer|Przedstawia szczegóły reguły zapory adresów IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Serwer|Aktualizuje regułę zapory adresów IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Serwer|Usuwa regułę zapory adresów IP|

W poniższym przykładzie użyto interfejsu wiersza polecenia do ustawienia reguły zapory adresów IP na poziomie serwera:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Na $servername nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np. określ **mysqldbserver** zamiast **mysqldbserver.database.windows.net**
>
> Przykład interfejsu wiersza polecenia w kontekście przewodnika Szybki start można znaleźć w tematach Create DB - Azure CLI (Tworzenie bazy danych — interfejs wiersza polecenia platformy Azure) i Create a single database (Tworzenie pojedynczej bazy danych) i Configure [a server-level IP firewall rule using](scripts/create-and-configure-database-cli.md)the Azure CLI (Tworzenie bazy danych — interfejs wiersza polecenia platformy [Azure)](az-cli-script-samples-content-guide.md) oraz konfigurowanie reguły zapory adresów IP na poziomie serwera przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu interfejsu API REST

| Interfejs API | Poziom | Opis |
| --- | --- | --- |
| [Lista reguł zapory](/rest/api/sql/firewallrules/listbyserver) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [Tworzenie lub aktualizowanie reguł zapory](/rest/api/sql/firewallrules/createorupdate) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [Usuwanie reguł zapory](/rest/api/sql/firewallrules/delete) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [Uzyskiwanie reguł zapory](/rest/api/sql/firewallrules/get) | Serwer | Pobiera reguły zapory adresów IP na poziomie serwera |

## <a name="troubleshoot-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Jeśli dostęp do Azure SQL Database nie działa zgodnie z oczekiwaniami, weź pod uwagę następujące kwestie.

- **Konfiguracja zapory lokalnej:**

  Aby komputer mógł uzyskać dostęp Azure SQL Database, może być konieczne utworzenie na komputerze wyjątku zapory dla portu TCP 1433. Aby nawiązyć połączenia wewnątrz granic chmury platformy Azure, może być trzeba otworzyć dodatkowe porty. Aby uzyskać więcej informacji, zobacz sekcję "SQL Database: na zewnątrz a wewnątrz" w tece [Ports beyond 1433 for ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md)and Azure SQL Database .

- **Translator adresów sieciowych:**

  Ze względu na translatora adresów sieciowych (NAT) adres IP używany przez komputer do nawiązywania połączenia z usługą Azure SQL Database może być inny niż adres IP w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP, za pomocą których komputer łączy się z platformą Azure:
    1. Zaloguj się do portalu.
    1. Przejdź do karty **Konfiguracja** na serwerze, który hostuje bazę danych.
    1. Bieżący **adres IP klienta jest** wyświetlany w sekcji Dozwolone adresy **IP.** Wybierz **pozycję Dodaj** dla opcji Dozwolone adresy **IP,** aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany na liście zezwalań nie zostały jeszcze wprowadzone:**

  Może wystąpić do pięciu minut opóźnienia, aby zmiany konfiguracji zapory Azure SQL Database obowiązywały.

- **Logowanie nie jest autoryzowane lub zostało użyte nieprawidłowe hasło:**

  Jeśli logowanie nie ma uprawnień na serwerze lub hasło jest nieprawidłowe, połączenie z serwerem zostanie odrzucone. Utworzenie ustawienia zapory daje klientom tylko *możliwość* próby nawiązania połączenia z serwerem. Klient musi nadal podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania identyfikatorów logowania, zobacz [Kontrolowanie i udzielanie dostępu do bazy danych.](logins-create-manage.md)

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe, które korzysta z dynamicznego adresowania IP i masz problemy z uzyskaniem dostępu do zapory, wypróbuj jedno z następujących rozwiązań:
  
  - Poproś dostawcę usług internetowych o zakres adresów IP przypisany do komputerów klienckich, które mają dostęp do serwera. Dodaj ten zakres adresów IP jako regułę zapory adresów IP.
  - Zamiast tego pobierz statyczne adresowanie IP dla komputerów klienckich. Dodaj adresy IP jako reguły zapory adresów IP.

## <a name="next-steps"></a>Następne kroki

- Upewnij się, że środowisko sieci firmowej zezwala na komunikację przychodzącyą z zakresów obliczeniowych adresów IP (w tym zakresów SQL), które są używane przez centra danych platformy Azure. Może być konieczne dodanie tych adresów IP do listy zezwalań. Zobacz [Microsoft Azure ip centrum danych](https://www.microsoft.com/download/details.aspx?id=41653).  
- Zobacz nasz przewodnik Szybki start na [temat tworzenia pojedynczej bazy danych w Azure SQL Database](single-database-create-quickstart.md).
- Aby uzyskać pomoc w łączeniu się z bazą danych w Azure SQL Database z aplikacji typu open source lub innych firm, zobacz Client [quickstart code samples to Azure SQL Database](connect-query-content-reference-guide.md#libraries).
- Aby uzyskać informacje o dodatkowych portach, które mogą być konieczne do otwarcia, zobacz sekcję "SQL Database: Na zewnątrz a wewnątrz" w te tematu [Ports beyond 1433 for ADO.NET 4.5 and SQL Database](adonet-v12-develop-direct-route-ports.md) (Porty poza portami 1433 dla ADO.NET 4.5 i SQL Database
- Aby uzyskać omówienie zabezpieczeń Azure SQL Database, zobacz [Securing your database (Zabezpieczanie bazy danych).](security-overview.md)

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
