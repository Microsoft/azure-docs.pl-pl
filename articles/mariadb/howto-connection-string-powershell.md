---
title: Generowanie parametrów połączenia przy użyciu programu PowerShell — Azure Database for MariaDB
description: Ten artykuł zawiera Azure PowerShell przykład generowania parametrów połączenia w celu nawiązania połączenia z Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 79109a7f39debfa14c8a11d9014685382151ab3b
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663727"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Jak wygenerować parametry połączenia Azure Database for MariaDB przy użyciu programu PowerShell

W tym artykule pokazano, jak generować parametry połączenia dla serwera Azure Database for MariaDB. Parametry połączenia służą do nawiązywania połączenia z Azure Database for MariaDB z wielu różnych aplikacji.

## <a name="requirements"></a>Wymagania

W tym artykule są wykorzystywane zasoby utworzone w następującym przewodniku jako punkt początkowy:

* [Szybki Start: Tworzenie serwera Azure Database for MariaDB przy użyciu programu PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

`Get-AzMariaDbConnectionString`Polecenie cmdlet służy do generowania parametrów połączenia w celu łączenia aplikacji z Azure Database for MariaDB. Poniższy przykład zwraca parametry połączenia dla klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Dostosowywanie parametrów serwera Azure Database for MariaDB przy użyciu programu PowerShell](howto-configure-server-parameters-using-powershell.md)
