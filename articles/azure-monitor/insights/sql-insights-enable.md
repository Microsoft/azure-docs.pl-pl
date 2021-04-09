---
title: Włącz usługi SQL Insights
description: Włącz usługi SQL Insights w Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: e8dd887d151eb553131048f232940555dbef324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025037"
---
# <a name="enable-sql-insights-preview"></a>Włącz funkcję SQL Insights (wersja zapoznawcza)
W tym artykule opisano sposób włączania usługi [SQL Insights](sql-insights-overview.md) w celu monitorowania wdrożeń SQL. Monitorowanie jest wykonywane z maszyny wirtualnej platformy Azure, która nawiązuje połączenie z wdrożeniami SQL i korzysta z dynamicznych widoków zarządzania (widoków DMV) w celu zbierania danych monitorowania. Można kontrolować, które zestawy danych są zbierane, oraz częstotliwość zbierania danych przy użyciu profilu monitorowania.

## <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics
Usługi SQL Insights przechowują swoje dane w co najmniej jednym [log Analytics obszarach roboczych](../logs/data-platform-logs.md#log-analytics-workspaces).  Aby móc włączyć usługi SQL Insights, musisz [utworzyć obszar roboczy](../logs/quick-create-workspace.md) lub wybrać istniejący. Jednego obszaru roboczego można używać z wieloma profilami monitorowania, ale obszar roboczy i profile muszą znajdować się w tym samym regionie świadczenia usługi Azure. Aby włączyć funkcje usługi SQL Insights i uzyskiwać do nich dostęp, musisz mieć [rolę współautor log Analytics](../logs/manage-access.md) w obszarze roboczym. 

## <a name="create-monitoring-user"></a>Utwórz użytkownika monitorowania 
Potrzebujesz użytkownika na wdrożeniach SQL, które chcesz monitorować. Postępuj zgodnie z poniższymi procedurami dotyczącymi różnych typów wdrożeń SQL.

### <a name="azure-sql-database"></a>Baza danych Azure SQL Database
Otwórz Azure SQL Database z [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) lub [edytorem zapytań (wersja zapoznawcza)](../../azure-sql/database/connect-query-portal.md) w Azure Portal.

Uruchom następujący skrypt, aby utworzyć użytkownika z wymaganymi uprawnieniami. Zamień *użytkownika* na nazwę użytkownika i *mystrongpassword* z hasłem.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Utwórz skrypt użytkownika telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Sprawdź, czy użytkownik został utworzony.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Sprawdź skrypt użytkownika telegraf." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Wystąpienie zarządzane Azure SQL
Zaloguj się do wystąpienia zarządzanego usługi Azure SQL i Użyj [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) lub podobnego narzędzia, aby uruchomić Poniższy skrypt w celu utworzenia użytkownika monitorowania z wymaganymi uprawnieniami. Zamień *użytkownika* na nazwę użytkownika i *mystrongpassword* z hasłem.

 
```
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
Zaloguj się do maszyny wirtualnej platformy Azure z systemem SQL Server i Użyj [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) lub podobnego narzędzia, aby uruchomić Poniższy skrypt w celu utworzenia użytkownika monitorowania z wymaganymi uprawnieniami. Zamień *użytkownika* na nazwę użytkownika i *mystrongpassword* z hasłem.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure 
Należy utworzyć co najmniej jedną maszynę wirtualną platformy Azure, która będzie używana do zbierania danych do monitorowania SQL.  

> [!NOTE]
>  [Profile monitorowania](#create-sql-monitoring-profile) określają, jakie dane będą zbierane z różnych typów SQL, które mają być monitorowane. Z każdą maszyną wirtualną monitorowania może być skojarzony tylko jeden profil monitorowania. Jeśli potrzebujesz wielu profilów monitorowania, musisz utworzyć maszynę wirtualną dla każdej z nich.

### <a name="azure-virtual-machine-requirements"></a>Wymagania maszyny wirtualnej platformy Azure
Na maszynach wirtualnych platformy Azure są spełnione następujące wymagania.

- System operacyjny: Ubuntu 18,04 
- Zalecane rozmiary maszyn wirtualnych platformy Azure: Standard_B2s (2 procesory CPU, 4 pamięci GiB) 
- Obsługiwane regiony: dowolny [region obsługiwany przez agenta Azure monitor](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Rozmiar maszyny wirtualnej Standard_B2s (2 procesory CPU, 4 GiB pamięci) będzie obsługiwał maksymalnie 100 parametrów połączenia. Nie należy przydzielić więcej niż 100 połączeń do pojedynczej maszyny wirtualnej.

W zależności od ustawień sieciowych zasobów SQL, maszyny wirtualne mogą wymagać umieszczenia w tej samej sieci wirtualnej co zasoby SQL, dzięki czemu mogą oni tworzyć połączenia sieciowe w celu zbierania danych monitorowania.  

## <a name="configure-network-settings"></a>Konfigurowanie ustawień sieciowych
Każdy typ SQL oferuje metody monitorowania maszyny wirtualnej w celu bezpiecznego dostępu do bazy danych SQL.  W poniższych sekcjach opisano opcje oparte na typie języka SQL.

### <a name="azure-sql-databases"></a>Bazy danych Azure SQL Database  

Usługi SQL Insights obsługują dostęp do Azure SQL Database za pośrednictwem publicznego punktu końcowego, a także z sieci wirtualnej.

Aby uzyskać dostęp za pośrednictwem publicznego punktu końcowego, należy dodać regułę na stronie **Ustawienia zapory** i w sekcji [Ustawienia zapory adresu IP](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules) .  Aby określić dostęp z sieci wirtualnej, możesz ustawić [reguły zapory sieci wirtualnej](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) i ustawić [Tagi usługi wymagane przez agenta Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  W [tym artykule](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) opisano różnice między tymi dwoma typami reguł zapory.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Ustaw zaporę serwera" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Ustawienia zapory." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Wystąpienia usługi Azure SQL Managed Instance 

Jeśli Twoja maszyna wirtualna monitorowania będzie znajdować się w tej samej sieci wirtualnej co zasoby programu SQL MI, zobacz [łączenie w tej samej sieci wirtualnej](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Jeśli maszyna wirtualna monitorowania będzie znajdować się w innej sieci wirtualnej niż zasoby programu SQL MI, zobacz [łączenie wewnątrz innej sieci wirtualnej](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Azure Virtual Machine i Azure SQL Machine  
Jeśli Twoja maszyna wirtualna monitorowania znajduje się w tej samej sieci wirtualnej co [SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network)zasoby maszyny wirtualnej SQL Jeśli Twoja maszyna wirtualna monitorowania będzie znajdować się w innej sieci wirtualnej niż zasoby maszyny wirtualnej SQL, zobacz  [Connect to SQL Server przez Internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Hasło monitorowania magazynu w Key Vault
Hasła połączeń użytkowników SQL należy przechowywać w Key Vault zamiast wprowadzać je bezpośrednio do parametrów połączenia profilu monitorowania.

Gdy skonfigurujesz profil do monitorowania SQL, musisz mieć jedno z następujących uprawnień do zasobu Key Vault, którego zamierzasz użyć:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft. Authorization/roleAssignments/Usuń uprawnienia, takie jak administrator dostępu użytkowników lub właściciel 

Nowe zasady dostępu zostaną automatycznie utworzone w ramach tworzenia profilu SQL Monitoring, który używa podanej Key Vault. Użyj *Zezwalaj na dostęp ze wszystkich sieci* do Key Vault ustawień sieciowych.


## <a name="create-sql-monitoring-profile"></a>Utwórz profil monitorowania SQL
Otwórz program SQL Insights, wybierając pozycję **SQL (wersja zapoznawcza)** z sekcji **Insights** w menu **Azure monitor** w Azure Portal. Kliknij pozycję **Utwórz nowy profil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Utwórz nowy profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profil będzie przechowywać informacje, które mają być zbierane z systemów SQL.  Ma określone ustawienia dla: 

- Azure SQL Database 
- Wystąpienia usługi Azure SQL Managed Instance 
- SQL Server uruchomione na maszynach wirtualnych  

Można na przykład utworzyć jeden profil o nazwie *produkcja SQL* i inny o nazwie *SQL przemieszczania* z różnymi ustawieniami dotyczącymi częstotliwości zbierania danych, jakie dane mają być zbierane, oraz obszaru roboczego, do którego mają być wysyłane dane. 

Profil jest przechowywany jako zasób [reguły zbierania danych](../agents/data-collection-rule-overview.md) w wybranej subskrypcji i grupie zasobów. Każdy profil wymaga następujących czynności:

- Nazwa. Nie można edytować po utworzeniu.
- Lokalizacja. Jest to region platformy Azure.
- Log Analytics obszar roboczy do przechowywania danych monitorowania.
- Ustawienia kolekcji dla częstotliwości i typu danych monitorowania SQL do zebrania.

> [!NOTE]
> Lokalizacja profilu powinna znajdować się w tej samej lokalizacji co obszar roboczy Log Analytics, do którego planujesz wysłać dane monitorowania.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Szczegóły profilu." lightbox="media/sql-insights-enable/profile-details.png":::

Po wprowadzeniu szczegółów dotyczących profilu monitorowania kliknij przycisk **Utwórz profil monitorowania** . Wdrożenie profilu może potrwać do minuty.  Jeśli w polu kombi **profilu monitorowania** nie widzisz nowego profilu, kliknij przycisk Odśwież, a po zakończeniu wdrażania.  Po wybraniu nowego profilu wybierz kartę **Zarządzanie profilem** , aby dodać maszynę monitorowania, która zostanie skojarzona z profilem.

### <a name="add-monitoring-machine"></a>Dodaj maszynę monitorowania
Wybierz pozycję **Dodaj maszynę monitorowania** , aby otworzyć panel kontekstowy, aby wybrać maszynę wirtualną do skonfigurowania do monitorowania wystąpień SQL i podać parametry połączenia.

Wybierz subskrypcję i nazwę monitorowanej maszyny wirtualnej. Jeśli używasz Key Vault do przechowywania hasła dla użytkownika monitorowania, wybierz zasoby Key Vault z tymi kluczami tajnymi, a następnie wprowadź adres URL i nazwę wpisu tajnego, które mają być używane w parametrach połączenia. Zobacz następną sekcję, aby uzyskać szczegółowe informacje na temat identyfikowania parametrów połączenia dla różnych wdrożeń SQL.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Dodaj maszynę monitorowania." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Dodaj parametry połączenia 
Parametry połączenia określają nazwę użytkownika, który powinien być używany przez usługi SQL Insights podczas logowania do programu SQL, aby uruchomić dynamiczne widoki zarządzania. Jeśli używasz Key Vault do przechowywania hasła dla użytkownika monitorowania, podaj adres URL i nazwę klucza tajnego do użycia. 

Parametry połączeń różnią się w zależności od typu zasobu SQL:

#### <a name="azure-sql-databases"></a>Bazy danych Azure SQL Database 
Wprowadź parametry połączenia w postaci:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Pobierz szczegóły z elementu menu **Parametry połączenia** dla bazy danych.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Parametry połączenia z usługą SQL Database" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Aby monitorować pomocniczą kopię zapasową, należy uwzględnić klucz-wartość `ApplicationIntent=ReadOnly` w parametrach połączenia.


#### <a name="azure-virtual-machines-running-sql-server"></a>Maszyny wirtualne platformy Azure z systemem SQL Server 
Wprowadź parametry połączenia w postaci:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Jeśli maszyna wirtualna monitorowania znajduje się w tej samej sieci wirtualnej, użyj prywatnego adresu IP serwera.  W przeciwnym razie użyj publicznego adresu IP. Jeśli korzystasz z maszyny wirtualnej Azure SQL, możesz sprawdzić, który port ma być używany w tym miejscu na stronie **zabezpieczenia** dla zasobu.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Zabezpieczenia maszyny wirtualnej SQL" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Aby monitorować pomocniczą kopię zapasową, należy uwzględnić klucz-wartość `ApplicationIntent=ReadOnly` w parametrach połączenia.


### <a name="azure-sql-managed-instances"></a>Wystąpienia usługi Azure SQL Managed Instance 
Wprowadź parametry połączenia w postaci:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Pobierz szczegóły z elementu menu **Parametry połączenia** dla wystąpienia zarządzanego.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="Parametry połączenia wystąpienia zarządzanego SQL" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Aby monitorować pomocniczą kopię zapasową, należy uwzględnić klucz-wartość `ApplicationIntent=ReadOnly` w parametrach połączenia.



## <a name="monitoring-profile-created"></a>Utworzono profil monitorowania 

Wybierz pozycję **Dodaj monitorowanie maszyny wirtualnej** , aby skonfigurować maszynę wirtualną do zbierania danych z zasobów SQL. Nie powracaj do karty **Przegląd** .  W ciągu kilku minut kolumna stan powinna ulec zmianie, aby można było odczytać "zbieranie" danych dotyczących zasobów SQL, które zostały wybrane do monitorowania.

Jeśli nie widzisz danych, zobacz temat [Rozwiązywanie problemów z usługą SQL Insights](sql-insights-troubleshoot.md) w celu zidentyfikowania problemu. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Utworzono profil" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Jeśli musisz zaktualizować profil monitorowania lub parametry połączenia na maszynach wirtualnych monitorowania, możesz to zrobić za pomocą karty usługi SQL Insights **Zarządzanie profilem** .  Po zapisaniu aktualizacji zmiany zostaną zastosowane w ciągu około 5 minut.

## <a name="next-steps"></a>Następne kroki

- Zobacz temat [Rozwiązywanie problemów z usługą SQL Insights](sql-insights-troubleshoot.md) , jeśli usługi SQL Insights nie działają prawidłowo po włączeniu.
