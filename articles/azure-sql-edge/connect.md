---
title: Łączenie i wykonywanie zapytań w usłudze Azure SQL Edge
description: Dowiedz się, jak nawiązać połączenie z usługą Azure SQL Edge i wysyłać do niej zapytania.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395210"
---
# <a name="connect-and-query-azure-sql-edge"></a>Łączenie i wykonywanie zapytań w usłudze Azure SQL Edge

W usłudze Azure SQL Edge po wdrożeniu kontenera można nawiązać połączenie z aparatem bazy danych z dowolnej z następujących lokalizacji:

- Wewnątrz kontenera
- Z innego kontenera platformy Docker działającego na tym samym hoście
- Z komputera-hosta
- Z dowolnego innego komputera klienckiego w sieci

## <a name="tools-to-connect-to-azure-sql-edge"></a>Narzędzia do nawiązywania połączenia z usługą Azure SQL Edge

Możesz połączyć się z wystąpieniem wystąpienia usługi Azure SQL Edge z dowolnego z następujących popularnych narzędzi:

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): narzędzia klienta SQLCMD są już zawarte w obrazie kontenera usługi Azure SQL Edge. Jeśli dołączysz do działającego kontenera z interaktywną powłoką bash, możesz uruchomić narzędzia lokalnie. Narzędzia klienta SQL nie są dostępne na platformie ARM64, ponieważ nie są one uwzględnione w wersji ARM64 kontenerów programu SQL Edge. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Aby nawiązać połączenie z aparatem bazy danych usługi Azure SQL Edge z komputera sieciowego, potrzebne są następujące elementy:

- **Adres IP lub nazwa sieci komputera hosta**: jest to komputer-host, na którym działa kontener usługi Azure SQL Edge.
- **Mapowanie portu hosta kontenera usługi Azure SQL Edge**: jest to mapowanie portu kontenera Docker na port na hoście. W kontenerze usługa Azure SQL Edge jest zawsze mapowana na port 1433. Możesz to zmienić, jeśli chcesz. Aby zmienić numer portu, zaktualizuj **Opcje tworzenia kontenera** dla modułu usługi Azure SQL Edge w Azure IoT Edge. W poniższym przykładzie port 1433 w kontenerze jest mapowany na port 1600 na hoście.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Hasło administratora wystąpienia usługi Azure SQL Edge**: jest to wartość określona dla `SA_PASSWORD` zmiennej środowiskowej podczas wdrażania usługi Azure SQL Edge.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Nawiązywanie połączenia z aparatem bazy danych z poziomu kontenera

[Narzędzia wiersza polecenia SQL Server](/sql/linux/sql-server-linux-setup-tools) są zawarte w obrazie kontenera usługi Azure SQL Edge. Jeśli dołączysz do kontenera za pomocą interakcyjnego wiersza polecenia, możesz uruchomić narzędzia lokalnie. Narzędzia klienta SQL nie są dostępne na platformie ARM64, ponieważ nie są one uwzględnione w wersji ARM64 kontenerów programu SQL Edge. 

1. Użyj `docker exec -it` polecenia, aby uruchomić interaktywną powłokę bash wewnątrz działającego kontenera. W poniższym przykładzie `e69e056c702d` jest to identyfikator kontenera.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Nie zawsze trzeba określać całego identyfikatora kontenera. Wystarczy określić wystarczającą liczbę znaków, aby jednoznacznie ją zidentyfikować. Dlatego w tym przykładzie może być wystarczające użycie `e6` lub `e69` , a nie pełny identyfikator.

2. Gdy jesteś wewnątrz kontenera, Połącz się lokalnie z narzędziem sqlcmd. Polecenie sqlcmd nie jest domyślnie w ścieżce, więc musisz określić pełną ścieżkę.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Po zakończeniu korzystania z narzędzia sqlcmd wpisz polecenie `exit` .

4. Po zakończeniu pracy z interakcyjnym wierszem polecenia wpisz polecenie `exit` . Kontener nadal będzie działać po zamknięciu interaktywnej powłoki bash.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Nawiązywanie połączenia z usługą Azure SQL Edge z innego kontenera na tym samym hoście

Ponieważ dwa kontenery uruchomione na tym samym hoście znajdują się w tej samej sieci platformy Docker, można z łatwością uzyskać do nich dostęp przy użyciu nazwy kontenera i adresu portu usługi. Na przykład, jeśli łączysz się z wystąpieniem usługi Azure SQL Edge z innego modułu Python (kontener) na tym samym hoście, możesz użyć parametrów połączenia podobnego do poniższego. (W tym przykładzie przyjęto założenie, że usługa Azure SQL Edge jest skonfigurowana do nasłuchiwania na domyślnym porcie).

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Nawiązywanie połączenia z usługą Azure SQL Edge z innej maszyny sieciowej

Możesz chcieć nawiązać połączenie z wystąpieniem usługi Azure SQL Edge z innego komputera w sieci. Aby to zrobić, użyj adresu IP hosta platformy Docker i portu hosta, do którego zamapowany jest kontener usługi Azure SQL Edge. Jeśli na przykład adres IP hosta platformy Docker to *xxx.xxx.xxx.xxx*, a kontener usługi Azure SQL Edge jest mapowany na port hosta *1600*, adres serwera dla wystąpienia usługi Azure SQL Edge to *xxx. xxx. xxx. xxx, 1600*. Zaktualizowany skrypt w języku Python to:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Aby nawiązać połączenie z wystąpieniem usługi Azure SQL Edge przy użyciu SQL Server Management Studio uruchomionej na komputerze z systemem Windows, zobacz [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Aby nawiązać połączenie z wystąpieniem usługi Azure SQL Edge przy użyciu Visual Studio Code na komputerze z systemem Windows, Mac lub Linux, zobacz [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Aby nawiązać połączenie z wystąpieniem usługi Azure SQL Edge przy użyciu Azure Data Studio na komputerze z systemem Windows, Mac lub Linux, zobacz [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Następne kroki

[Nawiązywanie połączeń i wykonywanie zapytań](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Zainstaluj narzędzia SQL Server w systemie Linux](/sql/linux/sql-server-linux-setup-tools)