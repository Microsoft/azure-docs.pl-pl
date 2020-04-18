---
title: Reguły zapory IP
description: Konfigurowanie reguł zapory IP na poziomie serwera dla bazy danych SQL lub zapory usługi SQL Data Warehouse. Zarządzanie dostępem i konfigurowanie reguł zapory IP na poziomie bazy danych dla pojedynczej lub puli bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: 12280e8a5b90c6712703fefc60ec1bfb12ba8573
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606089"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Reguły zapory IP bazy danych SQL azure i usługi Azure SQL Data Warehouse

> [!NOTE]
> Ten artykuł dotyczy serwerów SQL platformy Azure oraz baz danych usługi Azure SQL Database i usługi Azure SQL Data Warehouse na serwerze SQL platformy Azure. Dla uproszczenia *baza danych SQL* jest używana do odwoływania się zarówno do bazy danych SQL, jak i do magazynu danych SQL.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy *wystąpienia zarządzanego usługi Azure SQL Database*. Aby uzyskać informacje dotyczące konfiguracji sieci, zobacz [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database](sql-database-managed-instance-connect-app.md).

Podczas tworzenia nowego serwera SQL platformy Azure o nazwie *mysqlserver*, na przykład zapora bazy danych SQL blokuje cały dostęp do publicznego punktu końcowego dla serwera (który jest dostępny w *mysqlserver.database.windows.net*).

> [!IMPORTANT]
> Usługa SQL Data Warehouse obsługuje tylko reguły zapory IP na poziomie serwera. Nie obsługuje reguł zapory IP na poziomie bazy danych.

## <a name="how-the-firewall-works"></a>Jak działa zapora
Próby połączenia z Internetu i platformy Azure muszą przechodzić przez zaporę, zanim dotrą do serwera SQL lub bazy danych SQL, jak pokazano na poniższym diagramie.

   ![Diagram konfiguracji zapory][1]

### <a name="server-level-ip-firewall-rules"></a>Reguły zapory bazujące na adresach IP na poziomie serwera

  Te reguły umożliwiają klientom dostęp do całego serwera SQL platformy Azure, czyli wszystkich baz danych w ramach tego samego serwera bazy danych SQL. Reguły są przechowywane w *głównej* bazie danych. Dla serwera SQL Server można mieć maksymalnie 128 reguł zapory IP na poziomie serwera. Jeśli masz włączone ustawienie **Zezwalaj na usługi i zasoby platformy Azure, aby uzyskać dostęp do tego serwera,** jest to zliczane jako pojedyncza reguła zapory dla programu Azure SQL Server.
  
  Reguły zapory IP na poziomie serwera można skonfigurować przy użyciu instrukcji Azure portal, PowerShell lub Transact-SQL.
  - Aby korzystać z portalu lub programu PowerShell, musisz być właścicielem subskrypcji lub współautorem subskrypcji.
  - Aby użyć usługi Transact-SQL, należy połączyć się z wystąpieniem bazy danych SQL jako logowanie główne na poziomie serwera lub jako administrator usługi Azure Active Directory. (Reguła zapory IP na poziomie serwera musi najpierw zostać utworzona przez użytkownika, który ma uprawnienia na poziomie platformy Azure).

### <a name="database-level-ip-firewall-rules"></a>Reguły zapory IP na poziomie bazy danych

  Te reguły umożliwiają klientom dostęp do niektórych (bezpiecznych) baz danych w ramach tego samego serwera bazy danych SQL. Tworzenie reguł dla każdej bazy danych (w tym *głównej* bazy danych) i są one przechowywane w poszczególnych baza danych.
  
  Reguły zapory IP na poziomie bazy danych dla baz danych głównych i administratorów danych na poziomie bazy danych można tworzyć i zarządzać nimi tylko przy użyciu instrukcji Transact-SQL i tylko po skonfigurowaniu pierwszej zapory na poziomie serwera.
  
  Jeśli określisz zakres adresów IP w regule zapory IP na poziomie bazy danych, która znajduje się poza zakresem w regule zapory IP na poziomie serwera, tylko ci klienci, którzy mają adresy IP w zakresie na poziomie bazy danych, mogą uzyskać dostęp do bazy danych.
  
  Dla bazy danych może być maksymalnie 128 reguł zapory IP na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory IP na poziomie bazy danych, zobacz przykład w dalszej części tego artykułu i zobacz [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Zalecenia dotyczące ustawiania reguł zapory

Zaleca się używanie reguł zapory IP na poziomie bazy danych, gdy tylko jest to możliwe. Ta praktyka zwiększa bezpieczeństwo i sprawia, że baza danych jest bardziej przenośna. Użyj reguł zapory IP na poziomie serwera dla administratorów. Należy ich również używać, gdy masz wiele baz danych, które mają takie same wymagania dotyczące dostępu i nie chcesz konfigurować każdej bazy danych indywidualnie.

> [!NOTE]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Reguły zapory IP na poziomie serwera i bazy danych

*Czy użytkownicy jednej bazy danych powinni być w pełni odizolowani od innej bazy danych?*

Jeśli *tak,* użyj reguł zapory IP na poziomie bazy danych, aby udzielić dostępu. Ta metoda pozwala uniknąć używania reguł zapory IP na poziomie serwera, które umożliwiają dostęp przez zaporę do wszystkich baz danych. To zmniejszyłoby głębię obrony.

*Czy użytkownicy na adresach IP potrzebują dostępu do wszystkich baz danych?*

Jeśli *tak,* użyj reguł zapory IP na poziomie serwera, aby zmniejszyć liczbę razy, ile trzeba skonfigurować reguł zapory IP.

*Czy osoba lub zespół, który konfiguruje reguły zapory IP, ma dostęp tylko za pośrednictwem witryny Azure portal, programu PowerShell lub interfejsu API REST?*

Jeśli tak, należy użyć reguł zapory IP na poziomie serwera. Reguły zapory IP na poziomie bazy danych można skonfigurować tylko za pośrednictwem usługi Transact-SQL.  

*Czy osoba lub zespół, który konfiguruje reguły zapory IP, nie może mieć uprawnień wysokiego poziomu na poziomie bazy danych?*

Jeśli tak, użyj reguł zapory IP na poziomie serwera. Aby skonfigurować reguły zapory IP na poziomie bazy danych na poziomie bazy danych, należy mieć co najmniej uprawnienie *CONTROL DATABASE* na poziomie bazy danych za pośrednictwem usługi Transact-SQL.  

*Czy osoba lub zespół, który konfiguruje lub kontroluje reguły zapory IP, centralnie zarządza regułami zapory IP dla wielu (być może setek) baz danych?*

W tym scenariuszu najlepsze rozwiązania są określane przez potrzeby i środowisko. Reguły zapory IP na poziomie serwera mogą być łatwiejsze do skonfigurowania, ale skrypty mogą konfigurować reguły na poziomie bazy danych. Nawet jeśli używasz reguł zapory IP na poziomie serwera, może być konieczne przeprowadzenie inspekcji reguł zapory IP na poziomie bazy danych, aby sprawdzić, czy użytkownicy z uprawnieniami *CONTROL* w bazie danych tworzą reguły zapory IP na poziomie bazy danych.

*Czy mogę używać reguł zapory IP na poziomie serwera i bazy danych?*

Tak. Niektórzy użytkownicy, tacy jak administratorzy, mogą potrzebować reguł zapory IP na poziomie serwera. Inni użytkownicy, tacy jak użytkownicy aplikacji bazy danych, mogą potrzebować reguł zapory IP na poziomie bazy danych.

### <a name="connections-from-the-internet"></a>Połączenia z Internetu

Gdy komputer próbuje połączyć się z serwerem bazy danych z Internetu, zapora najpierw sprawdza źródłowy adres IP żądania względem reguł zapory IP na poziomie bazy danych dla bazy danych, których żąda połączenie.

- Jeśli adres znajduje się w zakresie określonym w regułach zapory IP na poziomie bazy danych, połączenie jest przyznawane do bazy danych SQL, która zawiera regułę.
- Jeśli adres nie znajduje się w zakresie reguł zapory IP na poziomie bazy danych, zapora sprawdza reguły zapory IP na poziomie serwera. Jeśli adres znajduje się w zakresie, który znajduje się w regułach zapory IP na poziomie serwera, połączenie jest udzielane. Reguły zapory IP na poziomie serwera mają zastosowanie do wszystkich baz danych SQL na serwerze SQL platformy Azure.  
- Jeśli adres nie znajduje się w zakresie, który znajduje się w dowolnej z reguł zapory IP na poziomie bazy danych lub serwera, żądanie połączenia kończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do bazy danych SQL z komputera lokalnego, upewnij się, że zapora w sieci i na komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.

### <a name="connections-from-inside-azure"></a>Połączenia z wewnętrznej platformy Azure

Aby umożliwić aplikacjom hostowanym na platformie Azure łączenie się z serwerem SQL, należy włączyć połączenia platformy Azure. Gdy aplikacja z platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Można to włączyć bezpośrednio z bloku usługi Azure Portal, ustawiając reguły zapory, a także przełączając zezwalaj na **usługi platformy Azure i zasoby, aby uzyskać dostęp do tego serwera** do **włączonego** w **ustawieniach zapory i sieci wirtualnych.** Jeśli połączenie nie jest dozwolone, żądanie nie dociera do serwera bazy danych SQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę, aby zezwalać na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. Jeśli wybierzesz tę opcję, upewnij się, że twoje uprawnienia logowania i użytkownika ograniczają dostęp tylko do autoryzowanych użytkowników.

## <a name="create-and-manage-ip-firewall-rules"></a>Tworzenie reguł zapory IP i zarządzanie nimi

Pierwsze ustawienie zapory na poziomie serwera można utworzyć przy użyciu [portalu Azure](https://portal.azure.com/) lub programowo przy użyciu narzędzia [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)lub interfejsu API [rest](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)platformy Azure. Tworzenie i zarządzanie dodatkowymi regułami zapory IP na poziomie serwera przy użyciu tych metod lub Transact-SQL.

> [!IMPORTANT]
> Reguły zapory IP na poziomie bazy danych można tworzyć i zarządzać nimi tylko przy użyciu funkcji Transact-SQL.

Aby zwiększyć wydajność, reguły zapory IP na poziomie serwera są tymczasowo buforowane na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> [Inspekcja bazy danych SQL](sql-database-auditing.md) służy do inspekcji zmian na poziomie serwera i zapory na poziomie bazy danych.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory IP na poziomie serwera za pomocą witryny Azure Portal

Aby ustawić regułę zapory IP na poziomie serwera w witrynie Azure portal, przejdź do strony przeglądu bazy danych SQL platformy Azure lub serwera bazy danych SQL.

> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [Tworzenie bazy danych przy użyciu portalu Azure](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Ze strony przeglądu bazy danych

1. Aby ustawić regułę zapory IP na poziomie serwera ze strony przeglądu bazy danych, wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji. 

    ![Reguła zapory IP serwera](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Zostanie otwarta strona **Ustawienia zapory** dla serwera bazy danych SQL.

2. Wybierz **pozycję Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP używanego komputera, a następnie wybierz pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory IP na poziomie serwera.

    ![Ustawianie reguły zapory IP na poziomie serwera](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Ze strony przeglądu serwera

Zostanie otwarta strona przeglądu serwera. Pokazuje w pełni kwalifikowaną nazwę serwera (na przykład *mynewserver20170403.database.windows.net)* i udostępnia opcje dalszej konfiguracji.

1. Aby ustawić regułę na poziomie serwera na tej stronie, wybierz **zaporę** z menu **Ustawienia** po lewej stronie.

2. Wybierz **pozycję Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP używanego komputera, a następnie wybierz pozycję **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory IP na poziomie serwera.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Zarządzanie regułami zapory IP za pomocą funkcji Transact-SQL

| Widok katalogu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serwer |Wyświetla bieżące reguły zapory IP na poziomie serwera |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serwer |Tworzy lub aktualizuje reguły zapory IP na poziomie serwera |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serwer |Usuwa reguły zapory IP na poziomie serwera |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |baza danych |Wyświetla bieżące reguły zapory IP na poziomie bazy danych |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |baza danych |Tworzy lub aktualizuje reguły zapory IP na poziomie bazy danych |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bazy danych |Usuwa reguły zapory IP na poziomie bazy danych |

Poniższy przykład sprawdza istniejące reguły, włącza zakres adresów IP na serwerze *Contoso*i usuwa regułę zapory IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie dodaj regułę zapory IP na poziomie serwera.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory IP *sp_delete_firewall_rule* na poziomie serwera, należy wykonać sp_delete_firewall_rule procedurę składowaną. Poniższy przykład usuwa regułę *ContosoFirewallRule:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory IP na poziomie serwera za pomocą programu PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale cały program deweloperski jest teraz dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w modułach Az i AzureRm są zasadniczo identyczne.

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Zestaw-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Usuń-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

W poniższym przykładzie użyto programu PowerShell do ustawienia reguły zapory IP na poziomie serwera:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Dla $servername określić nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np **mysqldbserver.database.windows.net.** **mysqldbserver**

> [!TIP]
> W przypadku przykładów programu PowerShell w kontekście przewodnika Szybki start zobacz [Tworzenie bazy danych — Program PowerShell](sql-database-powershell-samples.md) i tworzenie [pojedynczej bazy danych oraz konfigurowanie reguły zapory IP na poziomie serwera bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory IP na poziomie serwera za pomocą interfejsu wiersza polecenia

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[az sql server firewall-rule create az sql server firewall-rule create az sql server firewall-rule create az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serwer|Tworzy regułę zapory IP serwera|
|[az sql server firewall-rule lista](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serwer|Wyświetla listę reguł zapory IP na serwerze|
|[az sql server firewall-rule show az sql server firewall-rule show az sql server firewall-rule show az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serwer|Pokazuje szczegóły reguły zapory IP|
|[az sql server firewall-rule update az sql server firewall-rule update az sql server firewall-rule](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serwer|Aktualizuje regułę zapory IP|
|[az sql server firewall-rule delete az sql server firewall-rule delete az sql server firewall-rule delete az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serwer|Usuwa regułę zapory IP|

W poniższym przykładzie użyto interfejsu wiersza polecenia do ustawienia reguły zapory IP na poziomie serwera:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Dla $servername określić nazwę serwera, a nie w pełni kwalifikowaną nazwę DNS, np **mysqldbserver.database.windows.net.** **mysqldbserver**

> [!TIP]
> Przykład interfejsu wiersza polecenia w kontekście przewodnika Szybki start można znaleźć w temacie [Tworzenie interfejsu DB — azure cli](sql-database-cli-samples.md) i tworzenie [pojedynczej bazy danych oraz konfigurowanie reguły zapory IP bazy danych SQL przy użyciu interfejsu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Zarządzanie regułami zapory IP na poziomie serwera za pomocą interfejsu API REST

| interfejs API | Poziom | Opis |
| --- | --- | --- |
| [Lista reguł zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Serwer |Wyświetla bieżące reguły zapory IP na poziomie serwera |
| [Tworzenie lub aktualizowanie reguł zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Serwer |Tworzy lub aktualizuje reguły zapory IP na poziomie serwera |
| [Usuwanie reguł zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Serwer |Usuwa reguły zapory IP na poziomie serwera |
| [Pobierz reguły zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Serwer | Pobiera reguły zapory IP na poziomie serwera |

## <a name="troubleshoot-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Należy wziąć pod uwagę następujące punkty, gdy dostęp do usługi bazy danych SQL nie działa zgodnie z oczekiwaniami.

- **Konfiguracja zapory lokalnej:**

  Zanim komputer będzie mógł uzyskać dostęp do bazy danych SQL, może być konieczne utworzenie wyjątku zapory na komputerze dla portu TCP 1433. Aby nawiązać połączenia wewnątrz granicy chmury platformy Azure, może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zobacz sekcję "Baza danych SQL: poza i wewnątrz" [portów poza 1433 dla ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Translacja adresów sieciowych:**

  Ze względu na translację adresów sieciowych (NAT) adres IP używany przez komputer do łączenia się z bazą danych SQL może być inny niż adres IP w ustawieniach konfiguracji IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure:
    1. Zaloguj się do portalu.
    1. Przejdź do karty **Konfigurowanie** na serwerze, na którym znajduje się baza danych.
    1. **Bieżący adres IP klienta** jest wyświetlany w sekcji **Dozwolone adresy IP.** Wybierz **pozycję Dodaj** dla **dozwolonych adresów IP,** aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany na liście dozwolonych nie zostały jeszcze wprowadzone:**

  Może wystąpić maksymalnie pięć minut opóźnienia, aby zmiany w konfiguracji zapory bazy danych SQL zostały wprowadzone.

- **Login nie jest autoryzowany lub użyto nieprawidłowego hasła:**

  Jeśli login nie ma uprawnień na serwerze bazy danych SQL lub hasło jest niepoprawne, połączenie z serwerem zostanie odrzucone. Utworzenie ustawienia zapory daje tylko klientom *możliwość* nawiązania połączenia z serwerem. Klient musi nadal podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania logowania, zobacz [Kontrolowanie i udzielanie dostępu do bazy danych SQL database i magazynu danych SQL](sql-database-manage-logins.md).

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe korzystające z dynamicznego adresowania IP i masz problemy z przedostaniem się przez zaporę, wypróbuj jedno z następujących rozwiązań:
  
  - Zapytaj dostawcę usług internetowych o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera bazy danych SQL. Dodaj ten zakres adresów IP jako regułę zapory IP.
  - Uzyskaj statyczne adresy IP zamiast dla komputerów klienckich. Dodaj adresy IP jako reguły zapory IP.

## <a name="next-steps"></a>Następne kroki

- Upewnij się, że środowisko sieci firmowej umożliwia komunikację przychodzącą z zakresów adresów IP obliczeniowych (w tym zakresów SQL), które są używane przez centra danych platformy Azure. Może być trzeba dodać te adresy IP do listy dozwolonych. Zobacz [zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Aby uzyskać szybki start dotyczący tworzenia reguły zapory IP na poziomie serwera, zobacz [Tworzenie bazy danych SQL platformy Azure](sql-database-single-database-get-started.md).
- Aby uzyskać pomoc dotyczącą łączenia się z bazą danych SQL platformy Azure z aplikacji typu open source lub innych firm, zobacz [Przykłady kodu szybki start klienta do bazy danych SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Aby uzyskać informacje o dodatkowych portach, które mogą być potrzebne do otwarcia, zobacz sekcję "Baza danych SQL: poza i wewnątrz" [portów poza 1433, aby uzyskać ADO.NET 4.5 i bazę danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Aby zapoznać się z omówieniem zabezpieczeń usługi Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
