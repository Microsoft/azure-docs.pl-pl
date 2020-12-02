---
title: Nawiązywanie połączenia z pulą SQL na platformie Azure Synapse
description: Połącz z pulą SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse, seo-lt-2019, devx-track-csharp
ms.openlocfilehash: 3483fa03e8c05101b88bde5c964b74b382d32a52
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462855"
---
# <a name="connect-to-a-sql-pool-in-azure-synapse"></a>Nawiązywanie połączenia z pulą SQL na platformie Azure Synapse 

Połącz się z pulą SQL na platformie Azure Synapse.

## <a name="find-your-server-name"></a>Znajdowanie nazwy serwera

W poniższym przykładzie nazwa serwera to sqlpoolservername.database.windows.net. Aby znaleźć w pełni kwalifikowaną nazwę serwera:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Azure Synapse Analytics**.
3. Kliknij pulę SQL, z którą chcesz nawiązać połączenie.
4. Znajdź pełną nazwę serwera.

   ![Pełna nazwa serwera](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Obsługiwane sterowniki i parametry połączenia

Pula SQL obsługuje [ADO.NET](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [php](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)i [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Aby znaleźć najnowszą wersję i dokumentację, kliknij jeden z powyższych sterowników.

Aby automatycznie wygenerować parametry połączenia dla sterownika, którego używasz z Azure Portal, kliknij pozycję **Pokaż parametry połączenia bazy danych** w poprzednim przykładzie. Poniżej przedstawiono również przykłady parametrów połączenia dla każdego sterownika.

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na wartość 300 sekund, aby połączenie nie zostało zakończone mimo krótkich okresów niedostępności.

### <a name="adonet-connection-string-example"></a>Przykład parametrów połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Przykład parametrów połączenia sterownika ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Przykład parametrów połączenia sterownika PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Przykład parametrów połączenia sterownika JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Ustawienia połączenia

Podczas łączenia i tworzenia obiektów w puli SQL jest przeprowadzana standaryzacja. Tych ustawień nie można zastąpić i obejmują one:

| Ustawienie puli SQL | Wartość |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="next-steps"></a>Następne kroki

Aby nawiązać połączenie i rozpocząć tworzenie zapytań przy użyciu programu Visual Studio, zobacz artykuł [Query with Visual Studio](sql-data-warehouse-query-visual-studio.md) (Wykonywanie zapytań przy użyciu programu Visual Studio). Aby dowiedzieć się więcej na temat opcji uwierzytelniania, zobacz [uwierzytelnianie w usłudze Azure Synapse Analytics](sql-data-warehouse-authentication.md).
