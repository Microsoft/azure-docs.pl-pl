---
title: 'Samouczek: projektowanie Azure Portal Azure Database for MariaDB'
description: W tym samouczku wyjaśniono, jak utworzyć serwer oraz bazę danych usługi Azure Database for MariaDB i zarządzać nimi przy użyciu witryny Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542443"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Samouczek: projektowanie bazy danych usługi Azure Database for MariaDB za pomocą witryny Azure Portal

Azure Database for MariaDB to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności oraz zarządzanie nimi. Za pomocą witryny Azure Portal możesz łatwo zarządzać serwerem i zaprojektować bazę danych.

W tym samouczku nauczysz się wykonywać następujące czynności, używając witryny Azure Portal:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MariaDB
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mariadb-server"></a>Tworzenie serwera usługi Azure Database for MariaDB

Serwer usługi Azure Database for MariaDB jest tworzony za pomocą zdefiniowanego zestawu [zasobów obliczeniowych i przestrzeni dyskowej](concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for MariaDB**. Możesz również wpisać **MariaDB** w polu wyszukiwania, aby znaleźć usługę.

   ![Przechodzenie do bazy danych MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Wybierz kafelek **Azure Database for MariaDB** . Wprowadź lub wybierz wymagane informacje.

   ![Tworzenie formularza](./media/tutorial-design-database-using-portal/2-create-form.png)

    Ustawienie | Sugerowana wartość | Opis pola
    ---|---|---
    Nazwa serwera | *unikatowa nazwa serwera* | Wybierz unikatową nazwę, która identyfikuje serwer usługi Azure Database for MariaDB. Na przykład **mydemoserver**. Nazwa domeny *.mariadb.database.azure.com* jest dołączana do wprowadzanej nazwy serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
    Subskrypcja | *Twoja subskrypcja* | Wybierz subskrypcję platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | **myresourcegroup** | Wprowadź nazwę nowej grupy zasobów lub wybierz istniejącą grupę zasobów.
    Wybierz źródło | **Puste** | Wybierz pozycję **Puste** , aby utworzyć nowy serwer. W przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera usługi Azure Database for MariaDB należy wybrać pozycję **Kopia zapasowa**.
    Identyfikator logowania administratora serwera | **myadmin** | Konto logowania do użycia podczas nawiązywania połączenia z serwerem. Nazwą logowania administratora nie może być **azure_superuser** , **admin** , **administrator** , **root** , **guest** ani **public**.
    Hasło | *wartość wybrana przez użytkownika* | Wprowadź nowe hasło dla konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0–9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
    Potwierdź hasło | *wartość wybrana przez użytkownika*| Potwierdź hasło do konta administratora.
    Lokalizacja | *Region znajdujący się najbliżej Twoich użytkowników*| Wybierz lokalizację najbliżej użytkowników lub innych aplikacji platformy Azure.
    Wersja | *najnowsza wersja*| Najnowsza wersja, chyba że z konkretnych powodów wymagane jest użycie innej wersji.
    Warstwa cenowa | Patrz opis. | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **warstwa cenowa**  >  **ogólnego przeznaczenia**. Zachowaj wartości domyślne dla następujących ustawień:<br><ul><li>**Generacja obliczeń** (5. generacja)</li><li>**rdzeń wirtualny** (4 rdzeni wirtualnych)</li><li>**Magazyn** (100 GB)</li><li>**Okres przechowywania kopii zapasowej** (7 dni)</li></ul><br>Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, w obszarze **Opcje nadmiarowości kopii zapasowej** wybierz pozycję **Geograficznie nadmiarowy**. <br><br>Aby zapisać tę wybraną warstwę cenową, wybierz przycisk **OK**. Następny zrzut ekranu przedstawia te wybory.
    
   ![Warstwa cenowa](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > Po włączeniu **automatycznego wzrostu** serwer zwiększa magazyn, gdy zbliża się limit przydziału, bez wywierania wpływu na obciążenie.

4. Kliknij pozycję **Przejrzyj i utwórz**. Możesz kliknąć przycisk **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania. Wdrożenie może potrwać do 20 minut.

## <a name="configure-the-firewall"></a>Konfigurowanie zapory

Baza danych usługi Azure Database for MariaDB jest chroniona przez zaporę. Domyślnie wszystkie połączenia z serwerem i znajdującymi się na nim bazami danych są odrzucane. Przed nawiązaniem pierwszego połączenia z usługą Azure Database for MariaDB skonfiguruj zaporę, aby dodać publiczny adres IP (lub zakres adresów IP) sieci komputera klienckiego.

1. Kliknij nowo utworzony serwer, a następnie wybierz pozycję **Zabezpieczenia połączeń**.

   ![Zabezpieczenia połączeń](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. W tym miejscu możesz wybrać pozycję **Dodaj mój adres IP** lub skonfigurować reguły zapory. Pamiętaj o wybraniu pozycji **Zapisz** po utworzeniu zasad.

Teraz możesz nawiązać połączenie z serwerem za pomocą narzędzia wiersza polecenia mysql lub aplikacji MySQL Workbench.

> [!TIP]
> Serwer usługi Azure Database for MariaDB komunikuje się na porcie 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być blokowany przez zaporę sieciową. W takim przypadku w celu nawiązania połączenia z serwerem usługi Azure Database for MariaDB konieczne jest otwarcie portu 3306 przez Twój dział IT.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Uzyskaj z witryny Azure Portal wartości pól **Nazwa serwera** (w pełni kwalifikowana) i **Nazwa logowania administratora serwera** dla serwera usługi Azure Database for MariaDB. W pełni kwalifikowana nazwa serwera służy do nawiązywania połączenia z serwerem przy użyciu narzędzia wiersza polecenia mysql.

1. W menu po lewej stronie w witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Wszystkie zasoby**. Wprowadź nazwę serwera i wyszukaj serwer usługi Azure Database for MariaDB. Wybierz nazwę serwera, aby wyświetlić jego szczegóły.

2. Na stronie **Przegląd** zanotuj wartości pól **Nazwa serwera** i **Nazwa logowania administratora serwera**. Możesz również wybrać przycisk **kopiowania** obok danego pola, aby skopiować wartość do schowka.

   ![Właściwości serwera](./media/tutorial-design-database-using-portal/2-server-properties.png)

W naszym przykładzie nazwa serwera to **mydemoserver.MariaDB.Database.Azure.com** , a nazwa logowania administratora serwera to **\@ mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia mysql

Nawiąż połączenie z serwerem usługi Azure Database for MariaDB za pomocą [narzędzia wiersza polecenia mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html). Narzędzie wiersza polecenia mysql możesz uruchomić z poziomu usługi Azure Cloud Shell w przeglądarce lub z poziomu własnego komputera, korzystając z zainstalowanych lokalnie narzędzi mysql. Aby otworzyć usługę Azure Cloud Shell, kliknij przycisk **Wypróbuj** w bloku kodu w tym artykule lub przejdź do witryny Azure Portal i kliknij ikonę **>_** na pasku narzędzi w prawym górnym rogu.

Aby nawiązać połączenie, wprowadź następujące polecenie:

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych

Po nawiązaniu połączenia z serwerem Utwórz pustą bazę danych do współpracy z programem:

```sql
CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom następujące polecenie, aby przełączyć połączenie na nowo utworzoną bazę danych:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych

Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych usługi Azure Database for MariaDB, możesz wykonać niektóre podstawowe zadania.

Najpierw utwórz tabelę i załaduj do niej dane. Utwórzmy tabelę z informacjami o spisie:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Ładowanie danych do tabel

Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych

Uruchom następujące zapytanie, aby pobrać informacje z tabeli bazy danych:

```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz zostanie zaktualizowany podczas pobierania danych:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie

Załóżmy, że przypadkowo usunięto ważną tabelę bazy danych i nie można jej w prosty sposób odzyskać. W usłudze Azure Database for MariaDB możesz przywrócić serwer do punktu w czasie przez utworzenie kopii baz danych na Twoim nowym serwerze. Przy użyciu tego nowego serwera można odzyskać usunięte dane. Poniższa procedura opisuje przywrócenie przykładowego serwera do punktu w czasie przed dodaniem tabeli:

1. W witrynie Azure Portal znajdź usługę Azure Database for MariaDB. Na stronie **Przegląd** wybierz pozycję **Przywróć**.

   ![Przywracanie bazy danych](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Na stronie **Przywracanie** wprowadź lub wybierz następujące informacje:

   ![Formularz Przywracanie](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Punkt przywracania** : wybierz punkt w czasie w podanym przedziale czasowym, do którego chcesz wykonać przywrócenie. Pamiętaj o przekonwertowaniu lokalnej strefy czasowej na czas UTC.
   - **Przywróć na nowy serwer** : wprowadź nazwę nowego serwera, do którego chcesz wykonać przywrócenie.
   - **Lokalizacja** : region jest taki sam jak w przypadku serwera źródłowego i nie można go zmienić.
   - **Warstwa cenowa** : warstwa cenowa jest taka sama jak w przypadku serwera źródłowego i nie można jej zmienić.
   
3. Wybierz przycisk **OK** , aby [przywrócić serwer do punktu w czasie](./howto-restore-server-portal.md) przed usunięciem tabeli. Przywrócenie serwera powoduje utworzenie nowej kopii serwera od wybranego punktu w czasie. 

## <a name="next-steps"></a>Następne kroki
W tym samouczku opisano, jak wykonywać następujące czynności, używając witryny Azure Portal:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MariaDB
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

> [!div class="nextstepaction"]
> [Jak połączyć aplikacje z usługą Azure Database for MariaDB](howto-connection-string.md)
