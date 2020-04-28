---
title: Tworzenie zapytań dotyczących kontenera Docker SQL Server Linux przy użyciu Azure Databricks
description: W tym artykule opisano sposób wdrażania Azure Databricks w sieci wirtualnej, znanej również jako iniekcja wirtualna.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 460079248e6cbd939c36b84f94cac41dce4dda2b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73747664"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Samouczek: wykonywanie zapytania dotyczącego kontenera Docker SQL Server Linux w sieci wirtualnej z notesu Azure Databricks

W tym samouczku przedstawiono sposób integrowania Azure Databricks z kontenerem platformy Docker SQL Server Linux w sieci wirtualnej. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie obszaru roboczego Azure Databricks w sieci wirtualnej
> * Zainstaluj maszynę wirtualną z systemem Linux w sieci publicznej
> * Zainstaluj platformę Docker
> * Zainstaluj Microsoft SQL Server w kontenerze platformy Docker systemu Linux
> * Wykonywanie zapytań dotyczących SQL Server przy użyciu JDBC z notesu datakostks

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [obszar roboczy datacegły w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Zainstaluj program [Ubuntu dla systemu Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

1. W Azure Portal wybierz ikonę **Virtual Machines**. Następnie wybierz pozycję **+ Dodaj**.

    ![Dodaj nową maszynę wirtualną platformy Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Na karcie **podstawowe** wybierz pozycję Ubuntu Server 18,04 LTS i Zmień rozmiar maszyny wirtualnej na B2s. Wybierz nazwę użytkownika i hasło administratora.

    ![Karta podstawy nowej konfiguracji maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Przejdź do karty **Sieć** . Wybierz sieć wirtualną i podsieć publiczną obejmującą klaster Azure Databricks. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby wdrożyć maszynę wirtualną.

    ![Karta Sieć nowej konfiguracji maszyny wirtualnej](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Po zakończeniu wdrażania przejdź do maszyny wirtualnej. Zwróć uwagę na publiczny adres IP i sieć wirtualną/podsieć w **przeglądzie**. Wybierz **publiczny adres IP**

    ![Omówienie maszyny wirtualnej](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Zmień **przypisanie** na **static** i wprowadź **etykietę nazwy DNS**. Wybierz pozycję **Zapisz**, a następnie uruchom ponownie maszynę wirtualną.

    ![Konfiguracja publicznego adresu IP](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Wybierz kartę **Sieć** w obszarze **Ustawienia**. Zwróć uwagę, że sieciowa Grupa zabezpieczeń, która została utworzona podczas wdrażania Azure Databricks, jest skojarzona z maszyną wirtualną. Wybierz pozycję **Dodaj regułę portu wejściowego**.

7. Dodaj regułę, aby otworzyć port 22 dla protokołu SSH. Użyj następujących ustawień:
    
    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Adresy IP|Adresy IP określają, że ruch przychodzący z określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Źródłowe adresy IP|<publicznego adresu IP\>|Wprowadź publiczny adres IP. Publiczny adres IP można znaleźć, odwiedzając [Bing.com](https://www.bing.com/) i wyszukując pozycję **"mój adres IP"**.|
    |Zakresy portów źródłowych|*|Zezwalaj na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określają, że ruch wychodzący dla określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|<publicznego adresu IP maszyny wirtualnej\>|Wprowadź publiczny adres IP maszyny wirtualnej. Ten temat można znaleźć na stronie **Przegląd** swojej maszyny wirtualnej.|
    |Zakresy portów docelowych|22|Otwórz port 22 dla protokołu SSH.|
    |Priorytet|290|Nadaj regule priorytet.|
    |Nazwa|SSH-datakostki — samouczek — VM|Nadaj regule nazwę.|


    ![Dodaj regułę zabezpieczeń dla ruchu przychodzącego dla portu 22](./media/vnet-injection-sql-server/open-port.png)

8. Dodaj regułę, aby otworzyć port 1433 dla SQL z następującymi ustawieniami:

    |Ustawienie|Sugerowana wartość|Opis|
    |-------|---------------|-----------|
    |Element źródłowy|Dowolne|Źródło określa, że ruch przychodzący z określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Zakresy portów źródłowych|*|Zezwalaj na ruch z dowolnego portu.|
    |Element docelowy|Adresy IP|Adresy IP określają, że ruch wychodzący dla określonego źródłowego adresu IP będzie dozwolony lub odrzucany przez tę regułę.|
    |Docelowe adresy IP|<publicznego adresu IP maszyny wirtualnej\>|Wprowadź publiczny adres IP maszyny wirtualnej. Ten temat można znaleźć na stronie **Przegląd** swojej maszyny wirtualnej.|
    |Zakresy portów docelowych|1433|Otwórz port 22 dla SQL Server.|
    |Priorytet|300|Nadaj regule priorytet.|
    |Nazwa|SQL — datakostki — samouczek — VM|Nadaj regule nazwę.|

    ![Dodaj regułę zabezpieczeń dla ruchu przychodzącego dla portu 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Uruchamianie SQL Server w kontenerze platformy Docker

1. Otwórz program [Ubuntu dla systemu Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)lub inne narzędzie, które umożliwi użycie protokołu SSH do maszyny wirtualnej. Przejdź do maszyny wirtualnej w Azure Portal a następnie wybierz pozycję **Połącz** , aby pobrać polecenie SSH.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Wprowadź polecenie w terminalu Ubuntu i wprowadź hasło administratora, które zostało utworzone podczas konfigurowania maszyny wirtualnej.

    ![Logowanie przy użyciu protokołu SSH terminalu Ubuntu](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Użyj poniższego polecenia, aby zainstalować platformę Docker na maszynie wirtualnej.

    ```bash
    sudo apt-get install docker.io
    ```

    Sprawdź instalację platformy Docker przy użyciu następującego polecenia:

    ```bash
    sudo docker --version
    ```

4. Zainstaluj obraz.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź obrazy.

    ```bash
    sudo docker images
    ```

5. Uruchom kontener z obrazu.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Sprawdź, czy kontener jest uruchomiony.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Tworzenie bazy danych SQL

1. Otwórz SQL Server Management Studio i nawiąż połączenie z serwerem przy użyciu nazwy serwera i uwierzytelniania SQL. Nazwa użytkownika logowania to **sa** , a hasło to hasło ustawione w poleceniu Docker. Hasło w przykładowym przykładzie ma `Password1234`wartość.

    ![Nawiązywanie połączenia z SQL Server przy użyciu SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Po pomyślnym nawiązaniu połączenia wybierz pozycję **nowe zapytanie** , a następnie wprowadź Poniższy fragment kodu, aby utworzyć bazę danych, tabelę i wstawić kilka rekordów w tabeli.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Zapytanie w celu utworzenia bazy danych SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>SQL Server zapytań od Azure Databricks

1. Przejdź do obszaru roboczego Azure Databricks i sprawdź, czy klaster został utworzony w ramach wymagań wstępnych. Następnie wybierz pozycję **Utwórz Notes**. Nadaj notesowi nazwę, wybierz opcję *Python* jako język i wybierz utworzony klaster.

    ![Nowe ustawienia notesu dla nowych kostek](./media/vnet-injection-sql-server/create-notebook.png)

2. Użyj poniższego polecenia, aby wysłać polecenie ping do wewnętrznego adresu IP maszyny wirtualnej SQL Server. To polecenie ping powinno zakończyć się pomyślnie. Jeśli nie, sprawdź, czy kontener jest uruchomiony, i sprawdź konfigurację sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Możesz również użyć polecenia nslookup, aby przejrzeć.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Po pomyślnym wysłaniu polecenia ping do SQL Server można wysłać zapytanie do bazy danych i tabel. Uruchom następujący kod w języku Python:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, obszar roboczy Azure Databricks i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz użyć obszaru roboczego Azure Databricks w przyszłości, możesz zatrzymać klaster i ponownie uruchomić go później. Jeśli nie chcesz nadal korzystać z tego obszaru roboczego Azure Databricks, Usuń wszystkie zasoby utworzone w tym samouczku, wykonując następujące czynności:

1. W menu po lewej stronie w Azure Portal kliknij pozycję **grupy zasobów** , a następnie kliknij nazwę utworzonej grupy zasobów.

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie ponownie wybierz pozycję **Usuń** .

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wyodrębnić, przekształcić i załadować dane przy użyciu Azure Databricks.
> [!div class="nextstepaction"]
> [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
