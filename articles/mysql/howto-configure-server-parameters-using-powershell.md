---
title: Konfigurowanie parametrów serwera-Azure PowerShell-Azure Database for MySQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for MySQL przy użyciu programu PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3888e83738d8617d6ec1433a3b760a2c518b874c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627214"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Konfigurowanie parametrów serwera w Azure Database for MySQL przy użyciu programu PowerShell

Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for MySQL przy użyciu programu PowerShell. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować.

>[!Note]
> Parametry serwera można aktualizować globalnie na poziomie serwera, korzystać z [interfejsu wiersza polecenia platformy Azure](./howto-configure-server-parameters-using-cli.md), [programu PowerShell](./howto-configure-server-parameters-using-powershell.md)lub [Azure Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. MySql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. MySql PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for MySQL Server

Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom `Get-AzMySqlConfiguration` polecenie cmdlet.

W poniższym przykładzie przedstawiono listę parametrów konfiguracji serwera dla **mydemoserver** serwera **w grupie zasobów**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję informacje dotyczące programu MySQL w artykule [zmienne systemowe serwera](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera

Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom `Get-AzMySqlConfiguration` polecenie cmdlet i określ parametr **name** .

W tym przykładzie przedstawiono szczegółowe informacje o powolnych parametrach konfiguracji serwera ** \_ \_ dziennika zapytania** dla serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera

Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera MySQL. Aby zaktualizować konfigurację, użyj `Update-AzMySqlConfiguration` polecenia cmdlet.

Aby zaktualizować parametr konfiguracji **powolnego serwera \_ \_ dziennika zapytań** serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Powiększ magazyn na serwerze Azure Database for MySQL przy użyciu programu PowerShell](howto-auto-grow-storage-powershell.md).
