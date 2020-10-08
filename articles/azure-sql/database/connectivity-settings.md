---
title: Ustawienia łączności dla Azure SQL Database i magazynu danych
description: W tym dokumencie wyjaśniono wybór wersji usługi Transport Layer Security (TLS) oraz ustawienia proxy i przekierowania dla Azure SQL Database i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: a3ceb78a85546e5e75c4c484f131b67ff7fc9249
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824141"
---
# <a name="azure-sql-connectivity-settings"></a>Ustawienia łączności usługi Azure SQL
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W tym artykule wprowadzono ustawienia kontrolujące łączność z serwerem dla Azure SQL Database i usługi Azure Synapse Analytics. Te ustawienia mają zastosowanie do **wszystkich** baz danych SQL Database i Azure Synapse skojarzonych z serwerem.

> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **wystąpienia zarządzanego usługi Azure SQL**.

Ustawienia łączności są dostępne z ekranu **zapory i sieci wirtualne** , jak pokazano na poniższym zrzucie ekranu:

 ![Zrzut ekranu ustawień łączności][1]

> [!NOTE]
> Po zastosowaniu tych ustawień **zaczną obowiązywać od razu** i mogą spowodować utratę połączenia dla klientów, jeśli nie spełnią wymagań dla poszczególnych ustawień.

## <a name="deny-public-network-access"></a>Odmowa dostępu do sieci publicznej

Gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak**, dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych. Jeśli to ustawienie jest ustawione na wartość **nie** (domyślnie), klienci mogą łączyć się przy użyciu publicznych punktów końcowych (reguł zapory opartych na protokole IP, reguł zapory opartych na sieci wirtualnej) lub prywatnych punktów końcowych (za pomocą prywatnego linku), jak opisano w temacie [dostęp sieciowy](network-access-controls-overview.md). 

 ![Zrzut ekranu z łącznością z odmową dostępu do sieci publicznej][2]

Wszystkie próby ustawienia **Odmów dostępu do sieci publicznej** na **wartość tak** bez żadnych istniejących prywatnych punktów końcowych na serwerze logicznym zakończą się niepowodzeniem z komunikatem o błędzie podobnym do:  

> [!NOTE]
> Aby zdefiniować reguły zapory sieci wirtualnej na serwerze logicznym, który jest już skonfigurowany za pomocą prywatnych punktów końcowych, ustaw opcję **Odmów dostępu do sieci publicznej** na wartość **nie**.

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak**, dozwolone są tylko połączenia za pośrednictwem prywatnych punktów końcowych, a wszystkie połączenia za pośrednictwem publicznych punktów końcowych są odrzucane z komunikatem o błędzie podobnym do:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Gdy ustawienie **Odmów dostępu do sieci publicznej** ma wartość **tak**, wszelkie próby dodania lub aktualizacji reguł zapory zostaną odrzucone z komunikatem o błędzie podobnym do:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Zmień dostęp do sieci publicznej za pomocą programu PowerShell

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **publicznego dostępu do sieci** na poziomie serwera:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Zmień dostęp do sieci publicznej za pomocą interfejsu wiersza polecenia

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany **dostępu do sieci publicznej** w bash Shell:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```

## <a name="minimal-tls-version"></a>Minimalna wersja protokołu TLS 

Ustawienie wersji minimalnej [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umożliwia klientom kontrolowanie wersji protokołu TLS używanej przez ich Azure SQL Database.

Obecnie obsługujemy protokoły TLS 1,0, 1,1 i 1,2. Ustawienie minimalnej wersji protokołu TLS zapewnia, że są obsługiwane kolejne nowsze wersje protokołu TLS. Na przykład wybranie wersji protokołu TLS większej niż 1,1. oznacza, że akceptowane są tylko połączenia z protokołem TLS 1,1 i 1,2, a protokół TLS 1,0 jest odrzucany. Po przetestowaniu w celu potwierdzenia, że obsługuje ona aplikacje, zalecamy ustawienie minimalnej wersji protokołu TLS na 1,2, ponieważ zawiera ona poprawki dla luk w zabezpieczeniach znalezionych w poprzednich wersjach i stanowi najwyższą wersję protokołu TLS obsługiwaną w Azure SQL Database.

> [!IMPORTANT]
> Wartością domyślną dla minimalnej wersji protokołu TLS jest Zezwalanie na wszystkie wersje. Jednak po wymuszeniu wersji protokołu TLS nie można przywrócić jej do wartości domyślnej.

W przypadku klientów mających aplikacje korzystające ze starszych wersji protokołu TLS zalecamy ustawienie minimalnej wersji protokołu TLS zgodnie z wymaganiami aplikacji. W przypadku klientów korzystających z aplikacji do łączenia się z nieszyfrowanym połączeniem nie zaleca się stosowania żadnej minimalnej wersji protokołu TLS.

Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące protokołu TLS dotyczące SQL Database łączności](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po ustawieniu minimalnej wersji protokołu TLS próby logowania z klientów korzystających ze starszej wersji protokołu TLS niż minimalna wersja protokołu TLS serwera zakończą się niepowodzeniem z powodu następującego błędu:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem programu PowerShell

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **minimalnej wersji protokołu TLS** na poziomie serwera logicznego:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany ustawienia **minimalnej wersji protokołu TLS** w bash Shell:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Zmień zasady połączenia

[Zasady połączeń](connectivity-architecture.md#connection-policy) określają sposób łączenia się klientów z Azure SQL Database.


## <a name="change-connection-policy-via-powershell"></a>Zmiana zasad połączenia za pomocą programu PowerShell

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

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

## <a name="change-connection-policy-via-azure-cli"></a>Zmiana zasad połączenia za pomocą interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

Poniższy skrypt interfejsu wiersza polecenia pokazuje, w jaki sposób zmienić zasady połączenia z wiersza poleceń systemu Windows (z zainstalowanym interfejsem CLI platformy Azure).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem działania łączności w Azure SQL Database, zobacz [Architektura łączności](connectivity-architecture.md)
- Aby uzyskać informacje na temat zmiany zasad połączenia dla serwera, zobacz Connection [-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
