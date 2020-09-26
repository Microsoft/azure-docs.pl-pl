---
title: Rozwiązywanie problemów z łącznością
description: Rozwiązywanie problemów z łącznością w puli SQL Synapse.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: d32a51e391edbfd32cf57265562d4e0cb8fe0681
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362184"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>Rozwiązywanie problemów z łącznością w puli SQL Synapse

W tym artykule wymieniono typowe techniki rozwiązywania problemów dotyczące łączenia się z bazą danych puli SQL.

## <a name="check-service-availability"></a>Sprawdź dostępność usługi

Sprawdź, czy usługa jest dostępna. W Azure Portal przejdź do puli SQL, którą próbujesz połączyć. W panelu po lewej stronie kliknij pozycję **diagnozowanie i rozwiązywanie problemów**.

![Wybierz kondycję zasobu](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

W tym miejscu zostanie wyświetlony stan puli SQL. Jeśli usługa nie jest wyświetlana jako **dostępna**, należy zapoznać się z kolejnymi krokami.

![Usługa dostępna](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Jeśli Kondycja zasobów pokazuje, że wystąpienie puli SQL jest wstrzymane lub skalowane, postępuj zgodnie ze wskazówkami, aby wznowić wystąpienie.

![Zrzut ekranu przedstawia wystąpienie wstrzymania lub skalowania magazynu danych SQL.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Dodatkowe informacje na temat Resource Health można znaleźć tutaj.

## <a name="check-for-paused-or-scaling-operation"></a>Sprawdzanie istnienia wstrzymania lub operacji skalowania

Sprawdź Portal, aby sprawdzić, czy wystąpienie puli SQL jest wstrzymane lub skalowane.

![Zrzut ekranu pokazuje, jak sprawdzić, czy magazyn danych jest wstrzymany.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Jeśli zobaczysz, że usługa jest wstrzymana lub skalowania, sprawdź, czy nie jest ona wyświetlana w harmonogramie konserwacji. W portalu *dla puli SQL*zostanie wyświetlony wybrany harmonogram konserwacji.

![Harmonogram konserwacji — Omówienie](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

W przeciwnym razie skontaktuj się z administratorem IT, aby sprawdzić, czy ta konserwacja nie jest zaplanowana. Aby wznowić wystąpienie puli SQL, wykonaj następujące [kroki](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Sprawdzanie ustawień zapory

Baza danych puli SQL komunikuje się przez port 1433.Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z [serwerem logicznym](../../azure-sql/database/logical-servers.md) , chyba że dział IT otworzy port 1433. Dodatkowe informacje na temat konfiguracji zapory można znaleźć [tutaj](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Sprawdzanie ustawień sieci wirtualnej/punktu końcowego usługi

Jeśli otrzymujesz błędy 40914 i 40615, zobacz [Opis błędu i rozwiązanie tutaj](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sprawdzanie, czy są zainstalowane najnowsze sterowniki

### <a name="software"></a>Oprogramowanie

Upewnij się, że używasz najnowszych narzędzi do nawiązania połączenia z pulą SQL:

- SSMS
- Azure Data Studio
- Narzędzia danych SQL Server (Visual Studio)

### <a name="drivers"></a>Sterowniki

Upewnij się, że korzystasz z najnowszej wersji sterownika.Używanie starszej wersji sterowników może spowodować nieoczekiwane zachowanie, ponieważ starsze sterowniki mogą nie obsługiwać nowych funkcji.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Sprawdzanie parametrów połączenia

Upewnij się, że parametry połączenia zostały prawidłowo ustawione.  Poniżej przedstawiono przykłady.  Dodatkowe informacje na temat [parametrów połączeń można znaleźć tutaj](sql-data-warehouse-connection-strings.md).

Parametry połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Parametry połączenia ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Parametry połączenia PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Parametry połączenia sterownika JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Sporadyczne problemy z połączeniami

Sprawdź, czy serwer nie jest mocno obciążony, a w kolejce nie ma dużej liczby żądań. Może być konieczne skalowanie w górę puli SQL w celu uzyskania dodatkowych zasobów.

## <a name="common-error-messages"></a>Typowe komunikaty o błędach

Błędy 40914 i 40615, zobacz [Opis i rozdzielczość błędu tutaj](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Nadal występują problemy z łącznością?

Utwórz [bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) , aby zespół inżynieryjny mógł je obsługiwać.
