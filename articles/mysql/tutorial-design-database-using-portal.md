---
title: 'Samouczek: projektowanie serwera Azure Portal-Azure Database for MySQL'
description: W tym samouczku wyjaśniono, jak utworzyć serwer Azure Database for MySQL i bazę danych oraz zarządzać nimi przy użyciu Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: tutorial
ms.date: 3/20/2020
ms.custom: mvc
ms.openlocfilehash: 7559bc2246ca26cf2b14071396e075b28d2af3a7
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532685"
---
# <a name="tutorial-design-an-azure-database-for-mysql-database-using-the-azure-portal"></a>Samouczek: projektowanie bazy danych usługi Azure Database for MySQL za pomocą witryny Azure Portal

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. Za pomocą witryny Azure Portal możesz łatwo zarządzać serwerem i zaprojektować bazę danych.

W tym samouczku nauczysz się wykonywać następujące czynności, używając witryny Azure Portal:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MySQL
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz ulubioną przeglądarkę internetową i przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL

Serwer usługi Azure Database for MySQL jest tworzony za pomocą zdefiniowanego zestawu [zasobów obliczeniowych i przestrzeni dyskowej](./concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

1. Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for MySQL**. Jeśli nie możesz znaleźć serwera MySQL w kategorii **bazy danych** , kliknij pozycję **Zobacz wszystko** , aby wyświetlić wszystkie dostępne usługi bazy danych. Możesz również wpisać frazę **Azure Database for MySQL** w polu wyszukiwania, aby szybko znaleźć tę usługę.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Navigate-to-MySQL.png" alt-text="Przechodzenie do bazy danych MySQL":::

3. Kliknij pozycję **Azure Database for MySQL** kafelek. Wypełnij formularz usługi Azure Database for MySQL.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-create-form.png" alt-text="Tworzenie formularza":::

    **Ustawienie** | **Sugerowana wartość** | **Opis pola**
    ---|---|---
    Nazwa serwera | Unikatowa nazwa serwera | Wybierz unikatową nazwę, która identyfikuje serwer usługi Azure Database for MySQL. Na przykład mydemoserver. Nazwa domeny *.mysql.database.azure.com* jest dołączana do podawanej nazwy serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
    Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
    Grupa zasobów | *myresourcegroup* | Podaj nazwę nowej lub istniejącej grupy zasobów.
    Wybierz źródło | *Puste* | Wybierz opcję *Puste* , aby utworzyć nowy serwer od początku. (Opcję *Kopia zapasowa* należy wybrać w przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera usługi Azure Database for MySQL).
    Identyfikator logowania administratora serwera | myadmin | Konto logowania do użycia podczas łączenia z serwerem. Nazwa logowania administratora nie może być **azure_superuser** , **admin** , **administrator** , **root** , **Guest** ani **Public**.
    Hasło | *Wybór* | Podaj nowe hasło dla konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0–9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
    Potwierdź hasło | *Wybór*| Potwierdź hasło do konta administratora.
    Lokalizacja | *Region najbliżej Twoich użytkowników*| Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
    Wersja | *Najnowsza wersja*| Najnowsza wersja, chyba że z konkretnych powodów wymagana jest inna wersja.
    Warstwa cenowa | **Ogólnego przeznaczenia** , **Generacja 5** , **2 rdzenie wirtualne** , **5 GB** , **7 dni** , **Geograficznie nadmiarowy** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **warstwa cenowa**. Następnie wybierz kartę **ogólnego przeznaczenia** . generacji *5* , *2 rdzeni wirtualnych* , *5 GB* i *7 dni* są wartościami domyślnymi dla okresu przechowywania **obliczeń** , **rdzeń wirtualny** , **magazynu** i **przetrzymywania kopii zapasowych**. Te suwaki możesz zostawić bez zmian. Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** z **opcji nadmiarowości kopii zapasowej**. Aby zapisać tę wybraną warstwę cenową, wybierz przycisk **OK**. Następny zrzut ekranu przedstawia te wybory.

   :::image type="content" source="./media/tutorial-design-database-using-portal/3-pricing-tier.png" alt-text="Warstwa cenowa":::

   > [!TIP]
   > Po włączeniu **automatycznego wzrostu** serwer zwiększa magazyn, gdy zbliża się limit przydziału, bez wywierania wpływu na obciążenie.

4. Kliknij pozycję **Przejrzyj i utwórz**. Możesz kliknąć przycisk **powiadomienia** na pasku narzędzi, aby monitorować proces wdrażania. Wdrożenie może potrwać do 20 minut.

## <a name="configure-firewall"></a>Konfigurowanie zapory

Bazy danych usługi Azure Database for MySQL są chronione przez zaporę. Domyślnie wszystkie połączenia z serwerem i znajdującymi się na nim bazami danych są odrzucane. Przed nawiązaniem pierwszego połączenia z usługą Azure Database for MySQL skonfiguruj zaporę, aby dodać publiczny adres IP (lub zakres adresów IP) sieci maszyny klienta.

1. Kliknij nowo utworzony serwer, a następnie kliknij pozycję **Zabezpieczenia połączeń**.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Connection-security.png" alt-text="Zabezpieczenia połączeń":::
2. W tym miejscu możesz skorzystać z funkcji **Dodaj mój adres IP** lub skonfigurować reguły zapory. Pamiętaj, aby po utworzeniu reguł kliknąć przycisk **Zapisz**.
Teraz możesz nawiązać połączenie z serwerem za pomocą narzędzia wiersza polecenia mysql lub narzędzia z graficznym interfejsem użytkownika MySQL Workbench.

> [!TIP]
> Serwer usługi Azure Database for MySQL komunikuje się przez port 3306. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 3306 może być zablokowany przez zaporę sieciową. Jeśli tak się stanie, nie będzie można nawiązać połączenia z serwerem usługi Azure MySQL, chyba że dział IT otworzy port 3306.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Uzyskaj z witryny Azure Portal w pełni kwalifikowaną **Nazwę serwera** i **Nazwę logowania administratora serwera** dla serwera usługi Azure Database for MySQL. W pełni kwalifikowana nazwa serwera służy do nawiązywania połączenia z serwerem przy użyciu narzędzia wiersza polecenia mysql.

1. W witrynie [Azure Portal](https://portal.azure.com/) kliknij pozycję **Wszystkie zasoby** w menu po lewej stronie, wpisz nazwę, a następnie wyszukaj serwer usługi Azure Database for MySQL. Wybierz nazwę serwera, aby wyświetlić szczegóły.

2. Ze strony **Przegląd** zanotuj **Nazwę serwera** i **Nazwę logowania administratora serwera**. Aby skopiować zawartość do schowka, możesz nacisnąć przycisk kopiowania obok każdego pola.
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-server-properties.png" alt-text="4-2 Właściwości serwera":::

W tym przykładzie nazwa serwera to *mydemoserver.MySQL.Database.Azure.com* , a identyfikator logowania administratora serwera to administrator *\@ mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Nawiązywanie połączenia z serwerem za pomocą narzędzia wiersza polecenia mysql

Nawiąż połączenie z serwerem usługi Azure Database for MySQL za pomocą [narzędzia wiersza polecenia mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html). Narzędzie wiersza polecenia mysql możesz uruchomić z poziomu usługi Azure Cloud Shell w przeglądarce lub z poziomu własnej maszyny, korzystając z zainstalowanych lokalnie narzędzi mysql. Aby uruchomić usługę Azure Cloud Shell, kliknij przycisk `Try It` w bloku kodu w tym artykule lub przejdź do witryny Azure Portal i kliknij ikonę `>_` na pasku narzędzi w prawym górnym rogu.

Aby nawiązać połączenie, wpisz poniższe polecenie:

```azurecli-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Tworzenie pustej bazy danych

Po nawiązaniu połączenia z serwerem Utwórz pustą bazę danych, z którą chcesz współpracować.

```sql
CREATE DATABASE mysampledb;
```

W wierszu polecenia uruchom poniższe polecenie, aby przełączyć połączenie na tę nowo utworzoną bazę danych:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tworzenie tabel w bazie danych

Teraz, gdy wiesz, jak nawiązać połączenie z bazą danych usługi Azure Database for MySQL, możesz wykonać niektóre podstawowe zadania:

Najpierw utwórz tabelę i załaduj do niej dane. Utwórz tabelę z informacjami o spisie.

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Ładowanie danych do tabel

Teraz, gdy masz tabelę, wstaw do niej dane. W otwartym oknie wiersza polecenia uruchom następujące zapytanie, aby wstawić wiersze danych.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

W utworzonej wcześniej tabeli masz teraz dwa wiersze przykładowych danych.

## <a name="query-and-update-the-data-in-the-tables"></a>Wykonywanie zapytania względem danych w tabelach i aktualizowanie tych danych

Wykonaj następujące zapytanie, aby pobrać informacje z tabeli bazy danych.

```sql
SELECT * FROM inventory;
```

Możesz także zaktualizować dane w tabelach.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Wiersz jest aktualizowany podczas pobierania danych.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Przywracanie bazy danych do określonego punktu w czasie

Załóżmy, że przypadkowo usunięto ważną tabelę bazy danych i danych nie można w prosty sposób odzyskać. Usługa Azure Database for MySQL umożliwia przywrócenie serwera do punktu w czasie i utworzenie kopii baz danych na nowym serwerze. Przy użyciu tego nowego serwera można odzyskać usunięte dane. Poniższa procedura opisuje przywrócenie przykładowego serwera do punktu przed dodaniem tabeli.

1. W witrynie Azure Portal znajdź usługę Azure Database for MySQL. Na stronie **Przegląd** kliknij pozycję **Przywróć** na pasku narzędzi. Zostanie otwarta strona Przywracanie.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-restore-a-db.png" alt-text="10-1 Przywracanie bazy danych":::

2. Wypełnij formularz **przywracania** wymaganymi informacjami.

   :::image type="content" source="./media/tutorial-design-database-using-portal/2-restore-form.png" alt-text="10-2 Formularz Przywracanie":::

   - **Punkt przywracania** : wybierz punkt w czasie, do którego chcesz wykonać przywrócenie, w podanym przedziale czasowym. Pamiętaj o przekonwertowaniu lokalnej strefy czasowej na czas UTC.
   - **Przywróć na nowy serwer** : podaj nazwę nowego serwera, do którego chcesz wykonać przywrócenie.
   - **Lokalizacja** : region jest taki sam, jak w przypadku serwera źródłowego, i nie można go zmienić.
   - **Warstwa cenowa** : warstwa cenowa jest taka sama, jak w przypadku serwera źródłowego, i nie można jej zmienić.
   
3. Kliknij przycisk **OK** , aby przywrócić serwer do [punktu w czasie](./howto-restore-server-portal.md) przed usunięciem tabeli. Przywrócenie serwera powoduje utworzenie nowej kopii serwera od określonego punktu w czasie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:
1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**. 
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie Przegląd w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano, jak wykonywać następujące czynności, używając witryny Azure Portal:

> [!div class="checklist"]
> * Tworzenie usługi Azure Database for MySQL
> * Konfigurowanie zapory serwera
> * Tworzenie bazy danych za pomocą narzędzia wiersza polecenia mysql
> * Ładowanie przykładowych danych
> * Zapytania o dane
> * Aktualizowanie danych
> * Przywracanie danych

> [!div class="nextstepaction"]
> [Jak połączyć aplikacje z usługą Azure Database for MySQL](./howto-connection-string.md)