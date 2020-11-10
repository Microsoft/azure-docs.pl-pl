---
title: Ustawienia łączności dla Azure SQL Database i usługi Azure Synapse Analytics
description: W tym artykule wyjaśniono wybór wersji Transport Layer Security (TLS) oraz ustawienia serwera proxy i przekierowania dla Azure SQL Database i usługi Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 9856d71a6398bcea5b979788846afce17e7955f7
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412985"
---
# <a name="azure-sql-connectivity-settings"></a>Ustawienia łączności z usługą Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W tym artykule wprowadzono ustawienia kontrolujące łączność z serwerem dla Azure SQL Database i usługi Azure Synapse Analytics. Te ustawienia mają zastosowanie do wszystkich baz danych programu SQL Database i usługi Azure Synapse Analytics skojarzonych z serwerem.

> [!IMPORTANT]
> Ten artykuł nie dotyczy wystąpienia zarządzanego usługi Azure SQL.

Ustawienia łączności są dostępne z ekranu **zapory i sieci wirtualne** , jak pokazano na poniższym zrzucie ekranu:

 ![Zrzut ekranu przedstawiający okno Ustawienia łączności.][1]

> [!NOTE]
> Te ustawienia zaczną obowiązywać natychmiast po ich zastosowaniu. Klienci mogą napotkać utratę połączenia, jeśli nie spełnią wymagań dla każdego ustawienia.

## <a name="deny-public-network-access"></a>Odmowa dostępu do sieci publicznej

Gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak** , dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych. Jeśli to ustawienie ma wartość **nie** (domyślnie), klienci mogą łączyć się przy użyciu publicznych punktów końcowych (z regułami zapory opartymi na protokole IP lub z regułami zapory opartymi na sieci wirtualnej) lub prywatnych punktów końcowych (przy użyciu prywatnego linku platformy Azure), jak opisano w temacie [Omówienie dostępu do sieci](network-access-controls-overview.md).

 ![Diagram przedstawiający łączność w przypadku odmowy dostępu do sieci publicznej ma ustawioną wartość tak, a gdy opcja Odmów dostępu do sieci publicznej ma wartość nie.][2]

Wszystkie próby ustawienia **Odmów dostępu do sieci publicznej** na **wartość tak** bez żadnych istniejących prywatnych punktów końcowych na serwerze logicznym zakończą się niepowodzeniem z komunikatem o błędzie podobnym do:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Aby zdefiniować reguły zapory sieci wirtualnej na serwerze logicznym, który został już skonfigurowany za pomocą prywatnych punktów końcowych, ustaw opcję **Odmów dostępu do sieci publicznej** na wartość **nie**.

Gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak** , dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych. Wszystkie połączenia za pośrednictwem publicznych punktów końcowych zostaną odrzucone z komunikatem o błędzie podobnym do:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Gdy opcję **Odmów dostępu do sieci publicznej** jest ustawiona na **tak** , wszelkie próby dodania lub aktualizacji reguł zapory zostaną odrzucone z komunikatem o błędzie podobnym do:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Zmień dostęp do sieci publicznej za pomocą programu PowerShell

> [!IMPORTANT]
> Azure SQL Database nadal obsługuje moduł Azure Resource Manager programu PowerShell, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **publicznego dostępu do sieci** na poziomie serwera:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Zmień dostęp do sieci publicznej za pomocą interfejsu wiersza polecenia

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany ustawienia **dostępu do sieci publicznej** w bash Shell:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Minimalna wersja protokołu TLS 

Ustawienie wersji minimalnej [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umożliwia klientom wybór wersji protokołu TLS używanej przez bazę danych SQL.

Obecnie obsługujemy protokoły TLS 1,0, 1,1 i 1,2. Ustawienie minimalnej wersji protokołu TLS zapewnia obsługę nowszych wersji protokołu TLS. Na przykład wybranie wersji TLS większej niż 1,1 oznacza, że są akceptowane tylko połączenia z protokołem TLS 1,1 i 1,2, a połączenia z protokołem TLS 1,0 są odrzucane. Po przetestowaniu w celu potwierdzenia, że aplikacje je obsługują, zalecamy ustawienie minimalnej wersji protokołu TLS na 1,2. Ta wersja zawiera poprawki dotyczące luk w zabezpieczeniach wcześniejszych wersji i stanowi najwyższą wersję protokołu TLS obsługiwaną w Azure SQL Database.

> [!IMPORTANT]
> Wartością domyślną dla minimalnej wersji protokołu TLS jest Zezwalanie na wszystkie wersje. Po wymuszeniu wersji protokołu TLS nie można przywrócić jej wartości domyślnej.

W przypadku klientów mających aplikacje korzystające ze starszych wersji protokołu TLS zalecamy ustawienie minimalnej wersji protokołu TLS zgodnie z wymaganiami aplikacji. W przypadku klientów korzystających z aplikacji do łączenia się przy użyciu nieszyfrowanego połączenia nie zaleca się ustawiania żadnej minimalnej wersji protokołu TLS.

Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące protokołu TLS dotyczące SQL Database łączności](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po ustawieniu minimalnej wersji protokołu TLS próby logowania od klientów, którzy korzystają z wersji TLS niższej niż minimalna wersja protokołu TLS serwera, zakończą się niepowodzeniem z powodu następującego błędu:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem programu PowerShell

> [!IMPORTANT]
> Azure SQL Database nadal obsługuje moduł Azure Resource Manager programu PowerShell, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **minimalnej wersji protokołu TLS** na poziomie serwera logicznego:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany ustawienia **minimalnej wersji protokołu TLS** w bash Shell:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Zmiana zasad połączenia

[Zasady połączeń](connectivity-architecture.md#connection-policy) określają sposób łączenia się klientów z Azure SQL Database.

## <a name="change-the-connection-policy-via-powershell"></a>Zmiana zasad połączenia za pomocą programu PowerShell

> [!IMPORTANT]
> Azure SQL Database nadal obsługuje moduł Azure Resource Manager programu PowerShell, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób zmiany zasad połączenia przy użyciu programu PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Zmiana zasad połączenia za pomocą interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany zasad połączenia w bash Shell:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Interfejs wiersza polecenia platformy Azure z wiersza poleceń systemu Windows

Poniższy skrypt interfejsu wiersza polecenia pokazuje, w jaki sposób zmienić zasady połączenia z wiersza poleceń systemu Windows (z zainstalowanym interfejsem CLI platformy Azure):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Następne kroki

- Omówienie sposobu działania łączności w Azure SQL Database można znaleźć w temacie [Architektura łączności](connectivity-architecture.md).
- Aby uzyskać informacje na temat zmiany zasad połączenia dla serwera, zobacz Connection [-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
