---
title: Włączanie usługi SQL Insights
description: Włączanie usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 012aa364fe9e379455b6b63f7c9e541d2d5b97ed
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726902"
---
# <a name="enable-sql-insights-preview"></a>Włączanie usługi SQL Insights (wersja zapoznawcza)
W tym artykule opisano sposób włączania szczegółowych [informacji SQL w](sql-insights-overview.md) celu monitorowania wdrożeń SQL. Monitorowanie jest wykonywane z maszyny wirtualnej platformy Azure, która tworzy połączenie z wdrożeniami SQL i używa dynamicznych widoków zarządzania (DMV) do zbierania danych monitorowania. Za pomocą profilu monitorowania można kontrolować, które zestawy danych są zbierane, oraz częstotliwość zbierania.

> [!NOTE]
> Aby włączyć szczegółowe informacje SQL przez utworzenie profilu monitorowania i maszyny wirtualnej przy użyciu szablonu usługi Resource Manager, zobacz Resource Manager przykłady szablonów dla [usługi SQL Insights.](resource-manager-sql-insights.md)

## <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics
Usługa SQL Insights przechowuje swoje dane w co najmniej jednym [obszarze roboczym usługi Log Analytics.](../logs/data-platform-logs.md#log-analytics-workspaces)  Przed włączeniem usługi SQL Insights należy [utworzyć](../logs/quick-create-workspace.md) obszar roboczy lub wybrać istniejący. Pojedynczy obszar roboczy może być używany z wieloma profilami monitorowania, ale obszar roboczy i profile muszą znajdować się w tym samym regionie świadczenia usługi Azure. Aby włączyć funkcje usługi SQL Insights i uzyskać do nich dostęp, musisz mieć rolę współautora [usługi Log Analytics](../logs/manage-access.md) w obszarze roboczym. 

## <a name="create-monitoring-user"></a>Tworzenie użytkownika monitorowania 
Potrzebujesz użytkownika we wdrożeniach SQL, które chcesz monitorować. Postępuj zgodnie z poniższymi procedurami dla różnych typów wdrożeń SQL.

Poniższe instrukcje obejmują proces dla każdego typu bazy danych SQL, który można monitorować.  Aby to zrobić za pomocą skryptu w kilku usługach SQL jednocześnie, zapoznaj się z następującym plikiem [README](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) i [przykładowym skryptem](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1).


### <a name="azure-sql-database"></a>Baza danych Azure SQL Database
Otwórz Azure SQL Database za [pomocą SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) lub Edytora zapytań [(wersja zapoznawcza)](../../azure-sql/database/connect-query-portal.md) w Azure Portal.

Uruchom następujący skrypt, aby utworzyć użytkownika z wymaganymi uprawnieniami. Zastąp *użytkownika* nazwą użytkownika, a *mystrongpassword* hasłem.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Tworzenie skryptu użytkownika telegrafu." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Sprawdź, czy użytkownik został utworzony.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Sprawdź skrypt użytkownika telegrafu." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Wystąpienie zarządzane Azure SQL
Zaloguj się do Azure SQL Managed Instance i [](../../azure-sql/database/connect-query-ssms.md) użyj SQL Server Management Studio podobnego narzędzia, aby uruchomić następujący skrypt w celu utworzenia użytkownika monitorującego z wymaganymi uprawnieniami. Zastąp *użytkownika* nazwą użytkownika, a *mystrongpassword* hasłem.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Zaloguj się do maszyny wirtualnej platformy Azure z systemem SQL Server i użyj narzędzia [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) lub podobnego narzędzia, aby uruchomić następujący skrypt w celu utworzenia użytkownika monitorującego z wymaganymi uprawnieniami. Zastąp *użytkownika* nazwą użytkownika, a *mystrongpassword* hasłem.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Sprawdź, czy użytkownik został utworzony.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure 
Musisz utworzyć co najmniej jedną maszynę wirtualną platformy Azure, która będzie używana do zbierania danych do monitorowania bazy danych SQL.  

> [!NOTE]
>  Profile [monitorowania określają,](#create-sql-monitoring-profile) jakie dane będą zbierane z różnych typów baz danych SQL, które chcesz monitorować. Z każdą maszyną wirtualną monitorowania może być skojarzony tylko jeden profil monitorowania. Jeśli potrzebujesz wielu profilów monitorowania, musisz utworzyć maszynę wirtualną dla każdego z nich.

### <a name="azure-virtual-machine-requirements"></a>Wymagania maszyny wirtualnej platformy Azure
Maszyny wirtualne platformy Azure mają następujące wymagania.

- System operacyjny: Ubuntu 18.04 
- Zalecane rozmiary maszyn wirtualnych platformy Azure: Standard_B2s (2 procesory CPU, 4 GiB pamięci) 
- Obsługiwane regiony: dowolny [region obsługiwany przez Azure Monitor agenta](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Rozmiar Standard_B2s (2 procesory CPU, 4 GiB pamięci) będzie obsługiwać do 100 parametry połączenia. Nie należy przydzielać więcej niż 100 połączeń do jednej maszyny wirtualnej.

W zależności od ustawień sieciowych zasobów SQL może być konieczne, aby maszyny wirtualne były umieszczane w tej samej sieci wirtualnej co zasoby SQL, aby można było na nich utworzyć połączenia sieciowe w celu zbierania danych monitorowania.  

## <a name="configure-network-settings"></a>Konfigurowanie ustawień sieciowych
Każdy typ języka SQL oferuje metody bezpiecznego dostępu do bazy danych SQL dla maszyny wirtualnej monitorowania.  W poniższych sekcjach o przedstawiono opcje oparte na typie języka SQL.

### <a name="azure-sql-databases"></a>Bazy danych Azure SQL Database  

Usługa SQL Insights obsługuje dostęp do Azure SQL Database za pośrednictwem jego publicznego punktu końcowego, a także z sieci wirtualnej.

Aby uzyskać dostęp za pośrednictwem publicznego punktu końcowego, należy dodać regułę na stronie **Ustawienia** zapory i w sekcji [Ustawienia zapory adresów](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) IP.  Aby określić dostęp z sieci wirtualnej, można ustawić reguły zapory sieci wirtualnej i ustawić tagi usługi wymagane przez [agenta Azure Monitor wirtualnego.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking) [](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules)  [W tym](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) artykule opisano różnice między tymi dwoma typami reguł zapory.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Ustaw zaporę serwera" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Ustawienia zapory." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Wystąpienia usługi Azure SQL Managed Instance 

Jeśli maszyna wirtualna monitorowania będzie w tej samej sieci wirtualnej co zasoby programu SQL MI, zobacz Connect inside the same VNet (Nawiązywanie połączenia [wewnątrz tej samej sieci wirtualnej).](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet) Jeśli maszyna wirtualna monitorowania będzie w innej sieci wirtualnej niż zasoby programu SQL MI, zobacz Connect inside a different VNet (Nawiązywanie połączenia wewnątrz [innej sieci wirtualnej).](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Maszyna wirtualna platformy Azure i Azure SQL wirtualna  
Jeśli maszyna wirtualna monitorowania znajduje się w tej samej sieci wirtualnej co zasoby maszyny wirtualnej SQL, zobacz Connect to SQL Server within a virtual network (Nawiązywanie połączenia z maszyną wirtualną w [sieci wirtualnej).](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network) Jeśli maszyna wirtualna monitorowania będzie w innej sieci wirtualnej niż zasoby maszyny wirtualnej SQL, zobacz Nawiązywanie połączenia [SQL Server przez Internet.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)

## <a name="store-monitoring-password-in-key-vault"></a>Przechowywanie haseł monitorowania w Key Vault
Hasła połączeń użytkowników SQL należy przechowywać w Key Vault zamiast wprowadzać je bezpośrednio w parametry połączenia profilu monitorowania.

Podczas konfiguracji profilu monitorowania bazy danych SQL musisz mieć jedno z następujących uprawnień Key Vault zasobu, który ma być potrzebny:

- Microsoft.Authorization/roleAssignments/write 
- Uprawnienia Microsoft.Authorization/roleAssignments/delete, takie jak administrator dostępu użytkowników lub właściciel 

Nowe zasady dostępu zostaną automatycznie utworzone w ramach tworzenia profilu SQL Monitoring, który używa określonego Key Vault dostępu. Użyj *ustawienia Zezwalaj na dostęp z* Key Vault sieci.


## <a name="create-sql-monitoring-profile"></a>Tworzenie profilu monitorowania SQL
Otwórz usługę SQL Insights, wybierając pozycję **SQL (wersja zapoznawcza)** w sekcji **Szczegółowe** Azure Monitor **menu** w Azure Portal. Kliknij **pozycję Utwórz nowy profil.** 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Utwórz nowy profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profil będzie przechowywać informacje, które mają być zbierane z systemów SQL.  Ma on określone ustawienia dla: 

- Azure SQL Database 
- Wystąpienia usługi Azure SQL Managed Instance 
- SQL Server uruchomione na maszynach wirtualnych  

Można na przykład utworzyć jeden profil o nazwie *SQL Production* i inny o nazwie *Sql Staging* z różnymi ustawieniami częstotliwości zbierania danych, danymi do zebrania i obszarem roboczym, do którego mają być one wysyłane. 

Profil jest przechowywany jako zasób [reguły zbierania](../agents/data-collection-rule-overview.md) danych w wybranej subskrypcji i grupie zasobów. Każdy profil wymaga następujących czynności:

- Nazwa. Nie można edytować po utworzeniu.
- Lokalizacja. Jest to region świadczenia usługi Azure.
- Obszar roboczy usługi Log Analytics do przechowywania danych monitorowania.
- Ustawienia zbierania dotyczące częstotliwości i typu danych monitorowania SQL do zbierania.

> [!NOTE]
> Lokalizacja profilu powinna być w tej samej lokalizacji co obszar roboczy usługi Log Analytics, do którym planujesz wysłać dane monitorowania.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Szczegóły profilu." lightbox="media/sql-insights-enable/profile-details.png":::

Po **wprowadzeniu** szczegółów profilu monitorowania kliknij pozycję Utwórz profil monitorowania. Wdrożenie profilu może potrwać do minuty.  Jeśli nowy profil nie jest widoczny  w polu kombi Profil monitorowania, kliknij przycisk odświeżenia. Powinien on zostać wyświetlony po zakończeniu wdrażania.  Po wybraniu nowego profilu wybierz kartę Zarządzanie profilem, aby dodać maszynę do monitorowania, która zostanie skojarzona z profilem. 

### <a name="add-monitoring-machine"></a>Dodawanie maszyny do monitorowania
Wybierz **pozycję Dodaj maszynę monitorowania,** aby otworzyć panel kontekstowy, aby wybrać maszynę wirtualną do konfiguracji w celu monitorowania wystąpień SQL i podania parametry połączenia.

Wybierz subskrypcję i nazwę maszyny wirtualnej monitorowania. Jeśli używasz usługi Key Vault do przechowywania hasła dla użytkownika monitorującego, wybierz zasoby usługi Key Vault z tymi wpisami tajnymi i wprowadź adres URL oraz nazwę wpisów tajnych, które mają być używane w parametry połączenia. Zobacz następną sekcję, aby uzyskać szczegółowe informacje na temat identyfikowania parametrów połączenia dla różnych wdrożeń SQL.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Dodaj maszynę do monitorowania." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Dodawanie ciągów połączenia 
Ciąg połączenia określa nazwę użytkownika, która powinna być przez usługę SQL Insights podczas logowania się do programu SQL w celu uruchomienia dynamicznych widoków zarządzania. Jeśli używasz konta usługi Key Vault do przechowywania hasła użytkownika monitorującego, podaj adres URL i nazwę tajnego hasła do użycia. 

Ciąg połączeń będzie się różnić dla każdego typu zasobu SQL:

#### <a name="azure-sql-databases"></a>Bazy danych Azure SQL Database 
Wprowadź ciąg połączenia w formularzu:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Pobierz szczegóły z elementu menu **Parametry połączenia** dla bazy danych.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Ciąg połączenia bazy danych SQL" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Aby monitorować pomocniczy element czytelny, dołącz wartość klucza `ApplicationIntent=ReadOnly` do parametrów połączenia. Usługa SQL Insights obsługuje monitorowanie jednego pomocniczego. Zebrane dane zostaną otagowane w celu odzwierciedlenia danych podstawowych lub pomocniczych. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Maszyny wirtualne platformy Azure z systemem SQL Server 
Wprowadź ciąg połączenia w formularzu:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Jeśli maszyna wirtualna monitorowania znajduje się w tej samej sieci wirtualnej, użyj prywatnego adresu IP serwera.  W przeciwnym razie użyj publicznego adresu IP. Jeśli używasz maszyny Azure SQL wirtualnej, możesz zobaczyć, którego portu użyć tutaj na **stronie** Zabezpieczenia zasobu.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Zabezpieczenia maszyny wirtualnej SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Wystąpienia usługi Azure SQL Managed Instance 
Wprowadź w formularzu ciąg połączenia:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Pobierz szczegóły z elementu menu **Parametry połączenia** dla wystąpienia zarządzanego.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance parametrów połączenia" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Aby monitorować pomocniczy element czytelny, dołącz wartość klucz-wartość `ApplicationIntent=ReadOnly` do parametrów połączenia. Usługa SQL Insights obsługuje monitorowanie jednego pomocniczego źródła danych, a zebrane dane zostaną oznaczone w celu odzwierciedlenia danych podstawowych lub pomocniczych. 


## <a name="monitoring-profile-created"></a>Utworzony profil monitorowania 

Wybierz **pozycję Dodaj maszynę wirtualną monitorowania,** aby skonfigurować maszynę wirtualną do zbierania danych z zasobów SQL. Nie wracaj do karty **Przegląd.**  W ciągu kilku minut kolumna Stan powinna zmienić się na "Zbieranie". Powinny zostać wyświetlony dane dotyczące zasobów SQL wybranych do monitorowania.

Jeśli nie widzisz danych, zobacz [Troubleshooting SQL insights](sql-insights-troubleshoot.md) (Rozwiązywanie problemów ze szczegółowych informacji SQL), aby zidentyfikować problem. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Utworzony profil" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Jeśli musisz zaktualizować profil monitorowania lub parametry połączenia na monitorujących maszyn wirtualnych, możesz to zrobić za pośrednictwem karty Zarządzanie **profilem** usługi SQL Insights.  Po zapisaniu aktualizacji zmiany zostaną zastosowane w ciągu około 5 minut.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Troubleshooting SQL insights](sql-insights-troubleshoot.md) if SQL insights isn't working properly after being enabled (Rozwiązywanie problemów ze szczegółowych informacji SQL, jeśli szczegółowe informacje SQL nie działają prawidłowo po włączeniu).
