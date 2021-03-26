---
title: Konfigurowanie parametrów serwera-Azure PowerShell-Azure Database for PostgreSQL
description: W tym artykule opisano sposób konfigurowania parametrów usługi w Azure Database for PostgreSQL przy użyciu programu PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6e9bf55aa46e2206928a5f93285a4001dc731f64
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604298"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Dostosowywanie parametrów serwera Azure Database for PostgreSQL przy użyciu programu PowerShell

Można wyświetlić, wyświetlić i zaktualizować parametry konfiguracji dla serwera Azure Database for PostgreSQL przy użyciu programu PowerShell. Podzestaw konfiguracji aparatu jest uwidoczniony na poziomie serwera i można go modyfikować.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik, musisz:

- [Moduł AZ PowerShell](/powershell/azure/install-az-ps) zainstalowany lokalnie lub [Azure Cloud Shell](https://shell.azure.com/) w przeglądarce
- [Serwer Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Mimo że moduł AZ. PostgreSql PowerShell jest w wersji zapoznawczej, należy go zainstalować niezależnie od modułu AZ PowerShell przy użyciu następującego polecenia: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Po ogólnym udostępnieniu modułu AZ. PostgreSql PowerShell jest on częścią przyszłych wersji modułu AZ PowerShell i dostępne natywnie z poziomu Azure Cloud Shell.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Wyświetlanie listy parametrów konfiguracji serwera dla Azure Database for PostgreSQL Server

Aby wyświetlić listę wszystkich modyfikowalnych parametrów na serwerze i ich wartości, uruchom `Get-AzPostgreSqlConfiguration` polecenie cmdlet.

W poniższym przykładzie przedstawiono listę parametrów konfiguracji serwera dla **mydemoserver** serwera **w grupie zasobów**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Aby zapoznać się z definicją każdego z wymienionych parametrów, zobacz sekcję Reference PostgreSQL w artykule [zmienne środowiskowe](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Pokaż szczegóły parametru konfiguracji serwera

Aby wyświetlić szczegóły dotyczące określonego parametru konfiguracji dla serwera, uruchom `Get-AzPostgreSqlConfiguration` polecenie cmdlet i określ parametr **name** .

W tym przykładzie przedstawiono szczegółowe informacje o powolnych parametrach konfiguracji serwera **\_ \_ dziennika zapytania** dla serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Modyfikowanie wartości parametru konfiguracji serwera

Można również zmodyfikować wartość określonego parametru konfiguracji serwera, który aktualizuje podstawową wartość konfiguracyjną dla aparatu serwera PostgreSQL. Aby zaktualizować konfigurację, użyj `Update-AzPostgreSqlConfiguration` polecenia cmdlet.

Aby zaktualizować parametr konfiguracji **powolnego serwera \_ \_ dziennika zapytań** serwera **mydemoserver** w **obszarze Grupa zasobów**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Powiększ magazyn na serwerze Azure Database for PostgreSQL przy użyciu programu PowerShell](howto-auto-grow-storage-powershell.md).