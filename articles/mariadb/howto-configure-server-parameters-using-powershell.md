---
title: Konfigurowanie parametrów serwera-Azure PowerShell-Azure Database for MariaDB
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MariaDB przy użyciu programu PowerShell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 891cb5c6e570269d0414e7b7cece760af143927f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662461"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Konfigurowanie parametrów serwera w Azure Database for MariaDB przy użyciu programu PowerShell

Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for MariaDB przy użyciu programu PowerShell. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować.

>[!Note]
> Parametry serwera można zaktualizować globalnie na poziomie serwera, przy użyciu [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-cli.md), programu [PowerShell](./howto-configure-server-parameters-using-powershell.md) lub witryny [Azure Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MariaDb PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MariaDb PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for MariaDB Server

Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom `Get-AzMariaDbConfiguration` polecenie cmdlet.

W poniższym przykładzie przedstawiono listę parametrów konfiguracji serwera dla **mydemoserver** serwera **w grupie zasobów**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję Referencja MariaDB na stronie [zmienne systemowe serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera

Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom `Get-AzMariaDbConfiguration` polecenie cmdlet i określ parametr **name** .

W tym przykładzie przedstawiono szczegółowe informacje o powolnych parametrach konfiguracji serwera **\_ \_ dziennika zapytania** dla serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera

Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MariaDB. Aby zaktualizować konfigurację, użyj `Update-AzMariaDbConfiguration` polecenia cmdlet.

Aby zaktualizować parametr konfiguracji **powolnego serwera \_ \_ dziennika zapytań** serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Powiększ magazyn na serwerze Azure Database for MariaDB przy użyciu programu PowerShell](howto-auto-grow-storage-powershell.md).