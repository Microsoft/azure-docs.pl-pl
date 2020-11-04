---
title: Generowanie parametrów połączenia przy użyciu programu PowerShell — Azure Database for MySQL
description: Ten artykuł zawiera Azure PowerShell przykład generowania parametrów połączenia w celu nawiązania połączenia z Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: fcba366a0322c3c1b5c6dcdf0fc3571646053fad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337187"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Jak wygenerować parametry połączenia Azure Database for MySQL przy użyciu programu PowerShell

W tym artykule pokazano, jak generować parametry połączenia dla serwera Azure Database for MySQL. Parametry połączenia służą do nawiązywania połączenia z Azure Database for MySQL z wielu różnych aplikacji.

## <a name="requirements"></a>Wymagania

W tym artykule są wykorzystywane zasoby utworzone w następującym przewodniku jako punkt początkowy:

* [Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu programu PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

`Get-AzMySqlConnectionString`Polecenie cmdlet służy do generowania parametrów połączenia w celu łączenia aplikacji z Azure Database for MySQL. Poniższy przykład zwraca parametry połączenia dla klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Prawidłowe wartości `Client` parametru to:

* MODEL ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dostosowywanie parametrów serwera Azure Database for MySQL przy użyciu programu PowerShell](howto-configure-server-parameters-using-powershell.md)
