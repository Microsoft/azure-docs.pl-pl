---
title: Parametry połączenia dla Synapse SQL
description: Parametry połączenia dla Synapse SQL
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: fb68a15bb64640fd8aa18d2ee01fd4a03d809125
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565274"
---
# <a name="connection-strings-for-synapse-sql"></a>Parametry połączenia dla Synapse SQL

Możesz nawiązać połączenie z Synapse SQL za pomocą kilku różnych protokołów aplikacji, takich jak [ADO.NET,](/dotnet/framework/data/adonet/) [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)i [JDBC.](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) Poniżej przedstawiono kilka przykładów ciągów połączeń dla każdego protokołu. 

Możesz również użyć Azure Portal do tworzenia parametrów połączenia.  Aby utworzyć parametry połączenia przy użyciu Azure Portal, przejdź do  bloku bazy danych, w obszarze Podstawowe wybierz pozycję *Pokaż parametry połączenia bazy danych.*

## <a name="sample-adonet-connection-string"></a>Przykładowe ADO.NET połączenia

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Przykładowe ciągi połączenia ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Przykładowe ciągi połączenia PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Przykładowe ciągi połączenia JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na 300 sekund, aby umożliwić połączeniem przetrwanie krótkich okresów niedostępności.

## <a name="recommendations"></a>Zalecenia

W przypadku wykonywania bez **serwera zapytań puli SQL** zalecane narzędzia to Azure Data Studio i Azure Synapse Studio. [](get-started-azure-data-studio.md)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć wykonywanie zapytań analitycznych za pomocą Visual Studio i innych aplikacji, zobacz [Query with Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)(Wykonywanie zapytań za pomocą Visual Studio ).