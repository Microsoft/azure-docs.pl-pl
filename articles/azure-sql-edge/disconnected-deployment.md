---
title: Wdrażanie usługi Azure SQL Edge przy użyciu platformy Docker — Azure SQL Edge
description: Dowiedz się więcej o wdrażaniu usługi Azure SQL Edge przy użyciu platformy Docker
keywords: SQL Edge, kontener, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392082"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Wdrażanie usługi Azure SQL Edge przy użyciu platformy Docker

W tym przewodniku szybki start użyjesz platformy Docker do ściągania i uruchamiania obrazu kontenera usługi Azure SQL Edge. Następnie połącz się z narzędziem **sqlcmd** , aby utworzyć pierwszą bazę danych i uruchomić zapytania.

Ten obraz składa się z usługi Azure SQL Edge w oparciu o Ubuntu 18,04. Może być używany z aparatem Docker 1.8 + + w systemie Linux lub w systemie Docker dla komputerów Mac/Windows.

## <a name="prerequisites"></a>Wymagania wstępne

- Aparat platformy Docker 1.8 + na dowolnej obsługiwanej dystrybucji systemu Linux lub Docker dla komputerów Mac/Windows. Aby uzyskać więcej informacji, zobacz [Instalowanie platformy Docker](https://docs.docker.com/engine/installation/). Ponieważ obrazy programu Azure SQL Edge opierają się na Ubuntu 18,04, zaleca się użycie hosta platformy Docker Ubuntu 18,04.
- Sterownik magazynu Docker **overlay2** . Jest to wartość domyślna dla większości użytkowników. Jeśli okaże się, że nie używasz tego dostawcy magazynu i chcesz go zmienić, zobacz instrukcje i ostrzeżenia w [dokumentacji platformy Docker dotyczące konfigurowania overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Co najmniej 10 GB miejsca na dysku.
- Co najmniej 1 GB pamięci RAM.
- [Wymagania sprzętowe dotyczące usługi Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Ściąganie i uruchamianie obrazu kontenera

Przed uruchomieniem poniższych kroków upewnij się, że wybrano preferowaną powłokę (bash, PowerShell lub cmd) w górnej części tego artykułu.

1. Pobierz obraz kontenera usługi Azure SQL Edge z witryny Microsoft Container Registry.

    - Pobierz obraz kontenera usługi Azure SQL Edge
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Polecenia bash w tym artykule `sudo` są używane. W systemie macOS & Windows sudo może nie być wymagane. Jeśli nie chcesz używać sudo do uruchamiania platformy Docker w systemie Linux, możesz skonfigurować grupę platformy Docker i dodać użytkowników do tej grupy. Aby uzyskać więcej informacji, zobacz [czynności po instalacji dla systemu Linux](https://docs.docker.com/engine/install/linux-postinstall/).

Poprzednie polecenie pobiera najnowsze obrazy kontenerów usługi Azure SQL Edge. Aby wyświetlić wszystkie dostępne obrazy, zobacz [stronę Azure-SQL-Egde Docker Hub](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Aby uruchomić obraz kontenera przy użyciu platformy Docker, można użyć następującego polecenia z poziomu powłoki bash (Linux/macOS) lub wiersza polecenia programu PowerShell z podwyższonym poziomem uprawnień.
    
    - Uruchom wystąpienie usługi Azure SQL Edge działające jako wersja Developer
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Uruchom wystąpienie usługi Azure SQL Edge działające jako wersja Premium
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Jeśli używasz programu PowerShell w systemie Windows do uruchamiania tych poleceń, użyj podwójnego cudzysłowu zamiast pojedynczego cudzysłowu.


    > [!NOTE]
    > Hasło powinno być zgodne z domyślnymi zasadami haseł aparatu Microsoft SQL Database Engine, w przeciwnym razie kontener nie może skonfigurować aparatu SQL i przestanie działać. Domyślnie hasło musi składać się z co najmniej 8 znaków i zawierać znaki z trzech z następujących czterech zestawów: wielkie litery, małe litery, cyfry podstawowe 10 i symbole. Dziennik błędów można przeanalizować przez wykonanie polecenia [Docker Logs](https://docs.docker.com/engine/reference/commandline/logs/) .
    
    Poniższa tabela zawiera opis parametrów w poprzednim `docker run` przykładzie:

    | Parametr | Opis |
    |-----|-----|
    | **-e "ACCEPT_EULA = Y"** |  Ustaw zmienną **ACCEPT_EULA** na dowolną wartość w celu potwierdzenia akceptacji [umowy licencyjnej użytkownika końcowego](https://go.microsoft.com/fwlink/?linkid=2139274). Ustawienie wymagane dla obrazu usługi Azure SQL Edge. |
    | **-e "MSSQL_SA_PASSWORD = yourStrong (!) Hasło** | Określ własne silne hasło, które ma co najmniej 8 znaków i spełnia [wymagania dotyczące hasła usługi Azure SQL Edge](/sql/relational-databases/security/password-policy). Ustawienie wymagane dla obrazu usługi Azure SQL Edge. |
    | **-p 1433:1433** | Mapuj port TCP w środowisku hosta (pierwsza wartość) na port TCP w kontenerze (druga wartość). W tym przykładzie usługa Azure SQL Edge nasłuchuje na protokole TCP 1433 w kontenerze i jest on narażony na port 1433 na hoście. |
    | **--azuresqledge nazwy** | Określ niestandardową nazwę kontenera zamiast losowo wygenerowanego. W przypadku uruchomienia więcej niż jednego kontenera nie można ponownie użyć tej samej nazwy. |
    | **-c** | Uruchamianie kontenera w tle (demon) |

    Pełna lista wszystkich zmiennych środowiskowych usługi Azure SQL Edge znajduje się w temacie [Konfigurowanie usługi Azure SQL Edge ze zmiennymi środowiskowymi](configure.md#configure-by-using-environment-variables). Aby skonfigurować kontenery usługi Azure SQL Edge, można również użyć [pliku MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) .

3. Aby wyświetlić kontenery platformy Docker, użyj `docker ps` polecenia.
   
   ```bash
    sudo docker ps -a
   ```

4. Jeśli w kolumnie **stan** zostanie wyświetlony stan **up** , usługa Azure SQL Edge jest uruchomiona w kontenerze i nasłuchuje na porcie określonym w kolumnie **porty** . Jeśli kolumna **stan** dla kontenera usługi Azure SQL Edge została **zakończona** , zapoznaj się z sekcją Rozwiązywanie problemów w dokumentacji usługi Azure SQL Edge.

    `-h`Parametr (nazwa hosta) jest również przydatny, ale nie jest używany w tym samouczku dla uproszczenia. Spowoduje to zmianę wewnętrznej nazwy kontenera na wartość niestandardową. Jest to nazwa, która zostanie zwrócona w następującej kwerendzie języka Transact-SQL:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Ustawienie `-h` i taka `--name` sama wartość to dobry sposób na łatwą identyfikację kontenera docelowego.

5. Ostatnim krokiem jest zmiana hasła administratora systemu, ponieważ jest ono `SA_PASSWORD` widoczne w `ps -eax` danych wyjściowych i przechowywane w zmiennej środowiskowej o tej samej nazwie. Zapoznaj się z poniższymi instrukcjami.

## <a name="change-the-sa-password"></a> Zmienianie hasła administratora systemu

Konto **sa** jest administratorem systemu w wystąpieniu usługi Azure SQL Edge, które jest tworzone podczas instalacji. Po utworzeniu kontenera usługi Azure SQL Edge `MSSQL_SA_PASSWORD` określona zmienna środowiskowa jest wykrywalna przez uruchomienie `echo $SA_PASSWORD` w kontenerze. Ze względów bezpieczeństwa Zmień hasło administratora systemu.

1. Wybierz silne hasło, które ma być używane dla użytkownika administratora systemu.

2. Użyj polecenia `docker exec` , aby uruchomić polecenie **sqlcmd** , aby zmienić hasło przy użyciu języka Transact-SQL. W poniższym przykładzie zastąp stare hasło, `<YourStrong!Passw0rd>` i nowe hasło, `<YourNewStrong!Passw0rd>` z własnymi wartościami hasła.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Łączenie z usługą Azure SQL Edge

Poniższe kroki służą do nawiązywania połączenia z usługą Azure SQL Edge przy użyciu narzędzia wiersza polecenia usługi Azure SQL **Edge w** kontenerze.

> [!NOTE]
> Narzędzie sqlcmd nie jest dostępne w wersji ARM64 kontenerów programu SQL Edge.

1. Użyj `docker exec -it` polecenia, aby uruchomić interaktywną powłokę bash wewnątrz działającego kontenera. W poniższym przykładzie `azuresqledge` Nazwa określona przez `--name` parametr podczas tworzenia kontenera.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Po utworzeniu wewnątrz kontenera Połącz się lokalnie z narzędziem sqlcmd. Narzędzie sqlcmd nie jest domyślnie ścieżką, więc należy określić pełną ścieżkę.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Możesz pominąć hasło w wierszu polecenia, aby uzyskać monit o jego wprowadzenie.

3. Jeśli to się powiedzie, należy przejść do wiersza polecenia **sqlcmd** : `1>` .

## <a name="create-and-query-data"></a>Tworzenie i wykonywanie zapytań dotyczących danych

W poniższych sekcjach opisano, jak za pomocą narzędzia **sqlcmd** i Transact-SQL utworzyć nową bazę danych, dodać dane i uruchomić proste zapytanie.

### <a name="create-a-new-database"></a>Tworzenie nowej bazy danych

Poniższe kroki tworzą nową bazę danych o nazwie `TestDB` .

1. W wierszu polecenia **sqlcmd** Wklej następujące polecenie języka Transact-SQL, aby utworzyć testową bazę danych:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. W następnym wierszu Napisz zapytanie, aby zwrócić nazwę wszystkich baz danych na serwerze:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Wstawianie danych

Następnie utwórz nową tabelę `Inventory` i Wstaw dwa nowe wiersze.

1. W wierszu polecenia **sqlcmd** Przełącz kontekst do nowej `TestDB` bazy danych:

   ```sql
   USE TestDB
   ```

2. Utwórz nową tabelę o nazwie `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Wstaw dane do nowej tabeli:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Wpisz polecenie `GO` , aby wykonać poprzednie polecenia:

   ```sql
   GO
   ```

### <a name="select-data"></a>Wybieranie danych

Teraz uruchom zapytanie, aby zwrócić dane z `Inventory` tabeli.

1. W wierszu polecenia **sqlcmd** wprowadź zapytanie, które zwraca wiersze z `Inventory` tabeli, w której ilość jest większa niż 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Wykonaj polecenie:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Wyjdź z wiersza polecenia sqlcmd

1. Aby zakończyć sesję narzędzia **sqlcmd** , wpisz `QUIT` :

   ```sql
   QUIT
   ```

2. Aby zamknąć interaktywny wiersz polecenia w kontenerze, wpisz `exit` . Kontener nadal będzie działać po zamknięciu interaktywnej powłoki bash.

## <a name="connect-from-outside-the-container"></a>Nawiązywanie połączenia spoza kontenera

Możesz również nawiązać połączenie z wystąpieniem usługi Azure SQL Edge na maszynie platformy Docker z dowolnego zewnętrznego narzędzia Linux, Windows lub macOS, które obsługuje połączenia SQL. Aby uzyskać więcej informacji na temat łączenia się z kontenerem programu SQL Edge z zewnątrz, zapoznaj się z tematem [Connect and Query Edge Azure SQL](connect.md).

## <a name="remove-your-container"></a>Usuwanie kontenera

Jeśli chcesz usunąć kontener usługi Azure SQL Edge używany w tym samouczku, uruchom następujące polecenia:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Zatrzymywanie i usuwanie kontenera trwale usuwa wszystkie dane usługi Azure SQL Edge w kontenerze. Jeśli musisz zachować dane, [Utwórz i skopiuj plik kopii zapasowej z kontenera](backup-restore.md) lub Użyj [techniki trwałości danych kontenera](configure.md#persist-your-data).

## <a name="next-steps"></a>Następne kroki

- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)