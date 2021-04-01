---
title: 'Szybki Start: Connect-MySQL Workbench-Azure Database for MySQL-elastyczny serwer'
description: Ten przewodnik Szybki Start zawiera instrukcje dotyczące korzystania z programu MySQL Workbench do łączenia i wykonywania zapytań dotyczących danych z serwera Azure Database for MySQL-elastycznym.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90947794"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Szybki Start: korzystanie z programu MySQL Workbench do nawiązywania połączeń i wysyłania zapytań dotyczących danych na serwerze elastycznym Azure Database for MySQL (wersja zapoznawcza)


> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z serwerem elastycznym Azure Database for MySQL za pomocą aplikacji MySQL Workbench.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:

- [Utwórz Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal](./quickstart-create-server-portal.md)
- [Tworzenie Azure Database for MySQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Przygotowywanie stacji roboczej klienta
- Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastyczną siecią wirtualną serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastycznych reguł zapory serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-firewall-cli.md).

- Pobierz i zainstaluj aplikację MySQL Workbench na komputerze przy użyciu [witryny internetowej MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu, które są konieczne do nawiązania połączenia z serwerem elastycznym. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **wszystkie zasoby**, a następnie wyszukaj utworzony serwer (na przykład **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Nawiązywanie połączenia z serwerem za pomocą programu MySQL Workbench

Aby nawiązać połączenie z Azure Database for MySQL elastycznym serwerem przy użyciu programu MySQL Workbench:

1. Uruchom aplikację MySQL Workbench na swoim komputerze.

2. W oknie dialogowym **Setup New Connection** (Konfigurowanie nowego połączenia) wprowadź poniższe informacje na karcie **Parameters** (Parametry):

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="konfigurowanie nowego połączenia":::

    | **Parametry** | **Sugerowana wartość** | **Opis pola** |
    |---|---|---|
    |    Nazwa połączenia | Połączenie demonstracyjne | Podaj etykietę dla tego połączenia. |
    | Connection Method (Metoda połączenia) | Standard (TCP/IP) | Metoda Standardowa (TCP/IP) jest wystarczająca. |
    | Hostname (Nazwa hosta) | *Nazwa serwera* | Określ wartość nazwy serwera, która została użyta wcześniej podczas tworzenia usługi Azure Database for MySQL. Przedstawiony przykładowy serwer to mydemoserver.mysql.database.azure.com. Użyj w pełni kwalifikowanej nazwy domeny (\*.mysql.database.azure.com) tak jak pokazano w przykładzie. Postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia, jeśli nie pamiętasz nazwy serwera.  |
    | Port | 3306 | Zawsze używaj portu 3306 podczas łączenia z usługą Azure Database for MySQL. |
    | Nazwa użytkownika |  *Nazwa logowania administratora serwera* | Wpisz nazwę logowania administratora serwera, którą podano wcześniej podczas tworzenia usługi Azure Database for MySQL. Nasza przykładowa nazwa użytkownika to administrator. Postępuj zgodnie z instrukcjami w poprzedniej sekcji, aby uzyskać informacje dotyczące połączenia, jeśli nie pamiętasz nazwy użytkownika.
    | Hasło | Twoje hasło | Kliknij przycisk **Zapisz w magazynie...** , aby zapisać hasło. |

3. Kliknij przycisk **Testuj połączenie**, aby sprawdzić, czy wszystkie parametry zostały prawidłowo skonfigurowane.

4. Kliknij przycisk **OK** , aby zapisać połączenie.

5. Na liście **MySQL Connections** (Połączenia MySQL) kliknij kafelek odpowiadający serwerowi, a następnie poczekaj na ustanowienie połączenia.

    Zostanie otwarta nowa karta SQL z pustym edytorem, w którym można wpisać swoje zapytania.

> [!NOTE]
> Połączenie szyfrowane korzystające z protokołu TLS 1,2 jest wymagane i wymuszane na Azure Database for MySQL elastycznym serwerze. Chociaż nie jest wymagana dodatkowa konfiguracja z certyfikatami TLS/SSL, aby można było połączyć się z serwerem za pomocą programu MySQL Workbench, zalecamy powiązanie certyfikatu urzędu certyfikacji TLS/SSL z usługą MySQL Workbench. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu protokołu TLS/SSL](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Tworzenie tabeli, wstawianie danych, odczyt danych, aktualizowanie danych, usuwanie danych

1. Skopiuj i wklej przykładowy kod SQL w pustej karcie SQL w celu zilustrowania przykładowych danych.

    Ten kod tworzy pustą bazę danych o nazwie quickstartdb, a następnie tworzy przykładową tabelę o nazwie inventory. Wstawianych jest kilka wierszy, a następnie są one odczytywane. Za pomocą instrukcji UPDATE zmieniane są dane, a następnie wiersze odczytywane są ponownie. Na koniec jeden z wierszy jest usuwany i wiersze odczytywane są ponownie.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Zrzut ekranu przedstawia przykładowy kod SQL w aplikacji SQL Workbench i dane wyjściowe po jego uruchomieniu.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Karta SQL aplikacji MySQL Workbench umożliwia uruchamianie przykładowego kodu SQL":::

2. Aby uruchomić przykładowy kod SQL, kliknij ikonę pioruna na pasku narzędzi karty **SQL File** (Plik SQL).
3. Zwróć uwagę na trzy karty wyników w sekcji **Result Grid** (Siatka wyników) pośrodku strony.
4. Zwróć uwagę na listę **Output** (Dane wyjściowe) w dolnej części strony. Zawiera ona stan każdego polecenia.

Teraz masz połączenie z Azure Database for MySQL elastycznym serwerem przy użyciu programu MySQL Workbench i dane zapytania są wykonywane przy użyciu języka SQL.

## <a name="next-steps"></a>Następne kroki
- [Połączenie szyfrowane przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze](./how-to-connect-tls-ssl.md).
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md).
- [Utwórz i zarządzaj Azure Database for MySQL elastycznymi regułami zapory serwera przy użyciu Azure Portal](./how-to-manage-firewall-portal.md).
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).
