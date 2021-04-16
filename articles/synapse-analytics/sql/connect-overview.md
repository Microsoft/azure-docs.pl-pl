---
title: Nawiązywanie połączenia z Synapse SQL
description: Połącz się z Synapse SQL.
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: f5682302ea0fa83c04a8560ba3f0f98ea075e072
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565546"
---
# <a name="connect-to-synapse-sql"></a>Nawiązywanie połączenia z Synapse SQL
Połącz się z Synapse SQL w Azure Synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool"></a>Obsługiwane narzędzia dla bez serwera puli SQL

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) jest w pełni obsługiwana, począwszy od wersji 1.18.0. Program SSMS jest obsługiwany częściowo, począwszy od wersji 18.5. Można go używać tylko do nawiązywania połączeń i wykonywania zapytań.

> [!NOTE]
> Jeśli w czasie wykonywania zapytania połączenie logowania do usług AAD jest otwarte dłużej niż przez 1 godzinę, wszystkie zapytania, które są związane z usługą AAD, nie powiodą się. Obejmuje to wykonywanie zapytań o magazyn przy użyciu przekazania usługi AAD i instrukcji, które wchodzą w interakcję z usługą AAD (na przykład CREATE EXTERNAL PROVIDER). Ma to wpływ na każde narzędzie, które utrzymuje otwarte połączenia, na przykład w edytorze zapytań w programie SSMS i narzędziu ADS. Nie ma to wpływu na narzędzia, które otwierają nowe połączenia w celu wykonania zapytania, takie Synapse Studio połączenia.

> Aby rozwiązać ten problem, możesz ponownie uruchomić program SSMS lub nawiązać połączenie i rozłączyć się w programie ADS. 

## <a name="find-your-server-name"></a>Znajdowanie nazwy serwera

Nazwa serwera dla dedykowanej puli SQL w poniższym przykładzie to: showdemoweu.sql.azuresynapse.net.
Nazwa serwera dla bez serwera puli SQL w poniższym przykładzie to: showdemoweu-ondemand.sql.azuresynapse.net.

Aby znaleźć w pełni kwalifikowaną nazwę serwera:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję w **obszarach roboczych synapse.**
3. Wybierz obszar roboczy, z którym chcesz nawiązać połączenie.
4. Przejdź do o omówienie.
5. Znajdź pełną nazwę serwera.

## <a name="sql-pool"></a>**Pula SQL**

![Pełna nazwa serwera](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**Bezserwerowa pula SQL**

![Pełna nazwa serwera bez serwera bez serwera puli SQL](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Obsługiwane sterowniki i parametry połączenia
Synapse SQL obsługuje [ADO.NET](/dotnet/framework/data/adonet/), [ODBC,](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows) [PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)i [JDBC.](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) Aby znaleźć najnowszą wersję i dokumentację, wybierz jeden z powyższych sterowników. Aby automatycznie wygenerować parametry połączenia dla sterownika, którego używasz z Azure Portal, wybierz pozycję Pokaż parametry połączenia bazy danych **z** poprzedniego przykładu. Poniżej przedstawiono również przykłady parametrów połączenia dla każdego sterownika.

> [!NOTE]
> Rozważ ustawienie limitu czasu połączenia na wartość 300 sekund, aby połączenie nie zostało zakończone mimo krótkich okresów niedostępności.

### <a name="adonet-connection-string-example"></a>Przykład parametrów połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Przykład parametrów połączenia sterownika ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Przykład parametrów połączenia sterownika PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Przykład parametrów połączenia sterownika JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Ustawienia połączenia
Synapse SQL standaryzuje niektóre ustawienia podczas tworzenia połączenia i obiektu. Tych ustawień nie można przesłonić i obejmują:

| Ustawienia bazy danych | Wartość |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Zalecenia

W przypadku wykonywania bez **serwera zapytań puli SQL** zalecane narzędzia to Azure Data Studio i Azure Synapse Studio. [](get-started-azure-data-studio.md)

## <a name="next-steps"></a>Następne kroki
Aby nawiązać połączenie i rozpocząć tworzenie zapytań przy użyciu programu Visual Studio, zobacz artykuł [Query with Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Wykonywanie zapytań przy użyciu programu Visual Studio). Aby dowiedzieć się więcej na temat opcji uwierzytelniania, zobacz [Authentication to Synapse SQL (Uwierzytelnianie w u Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)).