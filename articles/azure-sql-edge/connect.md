---
title: Łączenie i wykonywanie zapytań w usłudze Azure SQL Edge (wersja zapoznawcza)
description: Informacje na temat łączenia się z usługą Azure SQL Edge (wersja zapoznawcza) i wykonywania w niej zapytań
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17bb5afebe0335bcdcf40298490f94999c04a621
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596918"
---
# <a name="connect-and-query-azure-sql-edge-preview"></a>Łączenie i wykonywanie zapytań w usłudze Azure SQL Edge (wersja zapoznawcza)

Po wdrożeniu kontenera usługi Azure SQL Edge można nawiązać połączenie z aparatem bazy danych SQL z dowolnej z poniższych lokalizacji.

- Wewnątrz kontenera
- Z innego kontenera platformy Docker działającego na tym samym hoście.
- Z komputera-hosta
- Z dowolnego innego komputera klienckiego w sieci.

## <a name="tools-to-connect-to-azure-sql-edge"></a>Narzędzia do nawiązywania połączenia z usługą Azure SQL Edge

Połączenia z wystąpieniem usługi Azure SQL Edge można nawiązać z dowolnego wspólnego narzędzia wymienionego poniżej.

* narzędzia [sqlcmd](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) -sqlcmd Client Tools znajdują się już w obrazie kontenera usługi Azure SQL Edge. Jeśli dołączysz do działającego kontenera z interaktywną powłoką bash, możesz uruchomić narzędzia lokalnie.
* [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode)

Aby nawiązać połączenie z aparatem bazy danych usługi Azure SQL Edge z komputera sieciowego, potrzebne są następujące elementy:

- *Adres IP lub nazwa sieci komputera hosta* — jest to komputer hosta, na którym działa kontener usługi Azure SQL Edge.
- *Mapowanie portu hosta kontenera usługi Azure SQL Edge* — to jest mapowanie portu dla portu kontenera Docker do portu na hoście. W obszarze kontenera SQL Edge zawsze jest mapowany na port 1433. Tę wartość można zmienić w ramach wdrożenia usługi Azure SQL Edge. Aby zmienić numer portu, należy zaktualizować "Opcje tworzenia kontenera" dla modułu programu SQL Edge w Azure IoT Edge. W poniższym przykładzie port 1433 w kontenerze jest mapowany na port 1600 na hoście.

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

- *Hasło administratora wystąpienia programu SQL Edge* — jest to wartość określona dla zmiennej środowiskowej **SA_PASSWORD** podczas wdrażania programu SQL Edge.

## <a name="connecting-to-the-database-engine-from-within-the-container"></a>Łączenie z aparatem bazy danych z poziomu kontenera

[Narzędzia wiersza polecenia SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) są zawarte w obrazie kontenera usługi Azure SQL Edge. Jeśli dołączysz do kontenera za pomocą interakcyjnego wiersza polecenia, możesz uruchomić narzędzia lokalnie.

1. Użyj `docker exec -it` polecenia, aby uruchomić interaktywną powłokę bash wewnątrz działającego kontenera. W poniższym przykładzie `e69e056c702d` jest to identyfikator kontenera.

    ```bash
    docker exec -it <Azure SQL Edge container id or name> /bin/bash
    ```

    > [!TIP]
    > Nie zawsze trzeba określać całego identyfikatora kontenera. Wystarczy określić wystarczającą liczbę znaków, aby jednoznacznie ją zidentyfikować. Dlatego w tym przykładzie może być wystarczające użycie `e6` lub, `e69` a nie pełny identyfikator.

2. Po utworzeniu wewnątrz kontenera Połącz się lokalnie z narzędziem sqlcmd. Narzędzie sqlcmd nie jest domyślnie ścieżką, więc należy określić pełną ścieżkę.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Po zakończeniu przy użyciu narzędzia sqlcmd wpisz polecenie `exit` .

4. Po zakończeniu pracy z interakcyjnym wierszem polecenia wpisz polecenie `exit` . Kontener nadal będzie działać po zamknięciu interaktywnej powłoki bash.

## <a name="connect-to-sql-edge-from-another-container-on-the-same-host"></a>Nawiązywanie połączenia z usługą SQL Edge z innego kontenera na tym samym hoście

Ponieważ dwa kontenery uruchomione na tym samym hoście znajdują się w tej samej sieci platformy Docker, można z łatwością uzyskać do nich dostęp przy użyciu nazwy kontenera i adresu portu usługi. Na przykład, jeśli łączysz się z wystąpieniem programu SQL Edge z innego modułu Python (kontener) na tym samym hoście, możesz użyć parametrów połączenia podobnego do poniższego. W poniższym przykładzie przyjęto, że program SQL Edge jest skonfigurowany do nasłuchiwania na domyślnym porcie.

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-sql-edge-from-another-network-machine"></a>Nawiązywanie połączenia z usługą SQL Edge z innej maszyny sieciowej

Aby nawiązać połączenie z wystąpieniem programu SQL Edge z innego komputera w sieci, należy użyć adresu IP hosta platformy Docker i portu hosta, do którego jest zamapowany kontener usługi SQL Edge. Jeśli na przykład adres IP hosta platformy Docker to * xxx.xxx.xxx.xxx ", a kontener usługi SQL Edge jest mapowany na port hosta *1600*, adres serwera dla wystąpienia programu SQL Edge to **xxx. xxx. xxx. xxx**. Zaktualizowany skrypt języka Python zostałby

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Aby nawiązać połączenie z wystąpieniem programu SQL Edge przy użyciu SQL Server Management Studio uruchomionego na komputerze z systemem Windows, zapoznaj się z [SQL Server Management Studio](https://docs.microsoft.com/sql/linux/sql-server-linux-manage-ssms).

Aby nawiązać połączenie z wystąpieniem programu SQL Edge przy użyciu Visual Studio Code na komputerze z systemem Windows, Mac lub Linux, zapoznaj się z [Visual Studio Code](https://docs.microsoft.com/sql/visual-studio-code/sql-server-develop-use-vscode).

Aby nawiązać połączenie z wystąpieniem programu SQL Edge przy użyciu Azure Data Studio na komputerze z systemem Windows, Mac lub Linux odwołuje się [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-server).

## <a name="see-also"></a>Zobacz także

[Połącz i zapytaj](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Zainstaluj narzędzia SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools)
