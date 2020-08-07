---
title: Generowanie parametrów połączenia przy użyciu programu PowerShell — Azure Database for PostgreSQL
description: Ten artykuł zawiera Azure PowerShell przykład generowania parametrów połączenia w celu nawiązania połączenia z Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909904"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Jak wygenerować parametry połączenia Azure Database for PostgreSQL przy użyciu programu PowerShell

W tym artykule pokazano, jak generować parametry połączenia dla serwera Azure Database for PostgreSQL. Parametry połączenia służą do nawiązywania połączenia z Azure Database for PostgreSQL z wielu różnych aplikacji.

## <a name="requirements"></a>Wymagania

W tym artykule są wykorzystywane zasoby utworzone w następującym przewodniku jako punkt początkowy:

* [Szybki Start: Tworzenie serwera Azure Database for PostgreSQL przy użyciu programu PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

`Get-AzPostgreSqlConnectionString`Polecenie cmdlet służy do generowania parametrów połączenia w celu łączenia aplikacji z Azure Database for PostgreSQL. Poniższy przykład zwraca parametry połączenia dla klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
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
> [Dostosowywanie parametrów serwera Azure Database for PostgreSQL przy użyciu programu PowerShell](howto-configure-server-parameters-using-powershell.md)
