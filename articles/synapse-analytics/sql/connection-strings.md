---
title: Parametry połączenia dla Synapse SQL (wersja zapoznawcza)
description: Parametry połączenia dla Synapse SQL (wersja zapoznawcza)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: aa940b00e9c23ab08244ea55c0cd22a8d5f1897f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91289382"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Parametry połączenia dla Synapse SQL (wersja zapoznawcza)

Można nawiązać połączenie z usługą Synapse SQL (wersja zapoznawcza) przy użyciu kilku różnych protokołów aplikacji, takich jak [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)i [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Poniżej przedstawiono kilka przykładów połączeń ciągów dla każdego protokołu. 

Możesz również użyć Azure Portal, aby skompilować parametry połączenia.  Aby skompilować parametry połączenia przy użyciu Azure Portal, przejdź do bloku bazy danych, w obszarze *Essentials* wybierz pozycję *Pokaż parametry połączenia bazy danych*.

## <a name="sample-adonet-connection-string"></a>Przykładowe parametry połączenia ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Przykładowe parametry połączenia ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Przykładowe parametry połączenia PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Przykładowe parametry połączenia JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na 300 sekund, aby umożliwić połączenie z krótkim okresem niedostępności.

## <a name="recommendations"></a>Zalecenia

W celu wykonywania zapytań **na żądanie SQL** zalecane są narzędzia [Azure Data Studio](get-started-azure-data-studio.md) i Azure Synapse Studio.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć wykonywanie zapytań dotyczących analizy za pomocą programu Visual Studio i innych aplikacji, zobacz [Query with Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
