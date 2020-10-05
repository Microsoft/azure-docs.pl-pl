---
title: Generowanie parametrów połączenia przy użyciu programu PowerShell — Azure Database for MySQL
description: Ten artykuł zawiera Azure PowerShell przykład generowania parametrów połączenia w celu nawiązania połączenia z Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: 37a257e2ca7f354659c032833480dd207cc1bed9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87839972"
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
