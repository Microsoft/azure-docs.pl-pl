---
title: Rozwiązywanie problemów z łącznością
description: Rozwiązywanie problemów z łącznością w puli sql synapse.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408210"
---
# <a name="troubleshooting-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

W tym artykule wymieniono typowe techniki rozwiązywania problemów związane z łączeniem się z bazą danych usługi SQL Analytics.

## <a name="check-service-availability"></a>Sprawdź dostępność usługi

Sprawdź, czy usługa jest dostępna. W witrynie Azure portal przejdź do puli sql synapse, z którą próbujesz się połączyć. W lewym panelu TOC kliknij **przycisk Diagnozuj i rozwiązuj problemy**.

![Wybierz kondycję zasobów](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Tutaj zostanie wyświetlony stan puli SQL Synapse. Jeśli usługa nie jest wyświetlana jako **Dostępna,** sprawdź dalsze kroki.

![Dostępna usługa](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Jeśli kondycja zasobu pokazuje, że wystąpienie puli Synapse SQL jest wstrzymane lub skalowanie, postępuj zgodnie ze wskazówkami, aby wznowić wystąpienie.

![Usługa wstrzymana](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Dodatkowe informacje o kondycji zasobów można znaleźć tutaj.

## <a name="check-for-paused-or-scaling-operation"></a>Sprawdzanie istnienia wstrzymania lub operacji skalowania

Sprawdź w portalu, czy wystąpienie puli synapse SQL jest wstrzymane lub skalowanie.

![Usługa wstrzymana](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Jeśli widzisz, że usługa jest wstrzymana lub skalowanie, sprawdź, czy nie jest w harmonogramie konserwacji. W portalu dla puli synapse SQL *przegląd puli*, zobaczysz wybrany harmonogram konserwacji.

![Harmonogram konserwacji przeglądowej](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

W przeciwnym razie skontaktuj się z administratorem IT, aby sprawdzić, czy ta konserwacja nie jest zaplanowanym wydarzeniem. Aby wznowić wystąpienie usługi SQL Analytics, wykonaj [następujące kroki](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Sprawdzanie ustawień zapory

Baza danych SQL Analytics komunikuje się za pośrednictwem portu 1433.Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być blokowany przez zaporę sieciową. W takim przypadku nie będzie można nawiązać połączenia z serwerem usługi Azure SQL Database, chyba że dział IT otworzy port 1433. Dodatkowe informacje na temat konfiguracji zapory można znaleźć [tutaj](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Sprawdzanie ustawień sieci wirtualnej/punktu końcowego usługi

Jeśli otrzymujesz błędy 40914 i 40615, zobacz [opis błędu i rozdzielczość tutaj](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Sprawdzanie, czy są zainstalowane najnowsze sterowniki

### <a name="software"></a>Oprogramowanie

Sprawdź, czy używasz najnowszych narzędzi do łączenia się z pulą SQL Synapse:

- SSMS
- Azure Data Studio
- Narzędzia danych programu SQL Server (visual studio)

### <a name="drivers"></a>Sterowniki

Upewnij się, że używasz najnowszych wersji sterowników.Korzystanie ze starszej wersji sterowników może spowodować nieoczekiwane zachowania, ponieważ starsze sterowniki mogą nie obsługiwać nowych funkcji.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Sprawdzanie parametrów połączenia

Upewnij się, że parametry połączenia zostały prawidłowo ustawione.  Poniżej znajduje się kilka przykładów.  Dodatkowe informacje na temat [parametrów połączeń można znaleźć tutaj](sql-data-warehouse-connection-strings.md).

Parametry połączenia sterownika ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Ciąg połączenia ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Php Parametry połączenia

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Parametry połączenia sterownika JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Sporadyczne problemy z połączeniami

Sprawdź, czy serwer nie jest mocno obciążony, a w kolejce nie ma dużej liczby żądań. Może być konieczne skalowanie w górę puli SQL Synapse dla dodatkowych zasobów.

## <a name="common-error-messages"></a>Typowe komunikaty o błędach

Błędy 40914 i 40615, zobacz [opis błędu i rozdzielczość tutaj](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Nadal masz problemy z łącznością?

Utwórz [bilet pomocy technicznej,](sql-data-warehouse-get-started-create-support-ticket.md) aby zespół inżynierów mógł Cię wspierać.
