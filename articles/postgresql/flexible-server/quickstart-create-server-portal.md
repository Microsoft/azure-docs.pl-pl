---
title: 'Szybki Start: Tworzenie serwera Azure Portal-Azure Database for PostgreSQL-elastyczny serwer'
description: Przewodnik Szybki Start dotyczący tworzenia serwera Azure Database for PostgreSQL-elastycznym i zarządzania nim przy użyciu interfejsu użytkownika Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 978fcb8c8cf9d2ffd929da5b76d7fd2ff109f420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92535862"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Szybki Start: Tworzenie Azure Database for PostgreSQL-elastyczny serwer w Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku szybki start pokazano, jak utworzyć serwer elastyczny Azure Database for PostgreSQL w ciągu około pięciu minut przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Otwórz przeglądarkę internetową i przejdź do [portalu](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Serwer usługi Azure Database for PostgreSQL jest tworzony ze skonfigurowanym zestawem [zasobów obliczeniowych i przestrzeni dyskowej](./concepts-compute-storage.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../../azure-resource-manager/management/overview.md).

Aby utworzyć serwer usługi Azure Database for PostgreSQL, wykonaj następujące kroki:

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL w menu":::

3. Wybierz opcję **elastyczne wdrożenie serwera** .

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Wybierz opcję Azure Database for PostgreSQL — elastyczna opcja wdrażania serwera":::

4. Wypełnij formularz **podstawy** przy użyciu następujących informacji:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Tworzenie serwera":::

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    Subskrypcja|Nazwa subskrypcji użytkownika|Subskrypcja platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której chcesz naliczać opłaty za zasób.
    Grupa zasobów|*myresourcegroup*| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
    Nazwa serwera |*mydemoserver*|Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL. Nazwa domeny *postgres.database.azure.com* jest dołączana do podawanej nazwy serwera. Serwer może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
    Nazwa użytkownika administratora |*myadmin*| Własne konto logowania do użycia podczas łączenia z serwerem. Nazwa logowania administratora nie może być **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **Guest** ani **Public**. Nie może zaczynać się od **pg_**.
    Hasło |Twoje hasło| Nowe hasło do konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
    Lokalizacja|Region najbliżej Twoich użytkowników| Lokalizacja znajdująca się najbliżej użytkowników.
    Wersja|Najnowsza wersja główna| Najnowsza wersja główna usługi PostgreSQL, chyba że masz inne określone wymagania.
    Obliczenia i magazyn | **Ogólnego przeznaczenia**, **4 rdzeni wirtualnych**, **512 GB**, **7 dni** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer**. *Ogólnego przeznaczenia*, *4 rdzeni wirtualnych*, *512 GB* i *7 dni* są wartościami domyślnymi dla **warstwy obliczeń**, **rdzeń wirtualny**, **magazynu** i **okresu przechowywania kopii zapasowych**. Możesz pozostawić te suwaki jako lub dostosować je. Aby zapisać tę wybraną warstwę cenową, wybierz przycisk **OK**. Następny zrzut ekranu przedstawia te wybory.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Okienko warstwy cenowej":::
    
5. Konfigurowanie opcji sieciowych

    Na karcie Sieć możesz wybrać sposób, w jaki serwer jest osiągalny. Usługa Azure Database for PostgreSQL tworzy zaporę na poziomie serwera. Uniemożliwia ona zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i wszelkimi bazami danych na tym serwerze, chyba że utworzysz regułę otwierającą zaporę dla konkretnych adresów IP. Zalecamy udostępnienie serwera publicznie:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Okienko sieć":::

    A następnie ograniczenie go do własnego adresu IP klienta:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Wybierz pozycję Dodaj bieżący adres IP klienta":::

6. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Ta operacja może potrwać kilka minut.

7. Na pasku narzędzi wybierz ikonę **Powiadomienia** (dzwonek), aby monitorować proces wdrażania. Po zakończeniu wdrażania możesz wybrać opcję **Przypnij do pulpitu nawigacyjnego**, która tworzy kafelek dla tego serwera na pulpicie nawigacyjnym witryny Azure Portal jako skrót do strony **Przegląd** serwera. Wybranie opcji **Przejdź do zasobu** spowoduje otworzenie strony **Przegląd** serwera.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Okienko powiadomienia":::

   Domyślnie baza danych **postgres** zostanie utworzona na Twoim serwerze. Baza danych [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) to domyślna baza danych, która jest przeznaczona do użycia dla użytkowników oraz na potrzeby narzędzi i aplikacji innych firm. (Inną domyślną bazą danych jest **azure_maintenance**. Służy ona do oddzielania procesów usług zarządzanych od działań użytkownika. Nie możesz uzyskać dostępu do tej bazy danych).

    > [!NOTE]
    > Połączenia z serwerem usługi Azure Database for PostgreSQL używają portu 5432. Kiedy próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 5432 może być blokowany przez zaporę sieciową. W takim przypadku nie można nawiązać połączenia z serwerem, chyba że dział informatyczny otworzy port 5432.
    >

## <a name="get-the-connection-information"></a>Pobieranie informacji o połączeniu

Podczas tworzenia serwera usługi Azure Database for PostgreSQL jest także tworzona domyślna baza danych o nazwie **postgres**. Aby nawiązać połączenie z serwerem bazy danych, potrzebujesz pełnej nazwy serwera i poświadczeń logowania administratora. Być może te wartości zostały zanotowane wcześniej podczas pracy z artykułem Szybki start. W przeciwnym razie możesz łatwo odnaleźć nazwę serwera i informacje dotyczące logowania na stronie **Przegląd** serwera w portalu.

Otwórz stronę **Przegląd** serwera. Zanotuj wartości **Nazwa serwera** i **Nazwa logowania administratora serwera**. Umieszczaj kursor nad poszczególnymi polami, a po prawej stronie tekstu pojawi się symbol kopiowania. Wybierz symbol kopiowania, aby w razie potrzeby skopiować wartości.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="Strona przegląd serwera":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Nawiązywanie połączenia z bazą danych PostgreSQL za pomocą narzędzia psql

Istnieje wiele aplikacji, za pomocą których można nawiązać połączenie z serwerem usługi Azure Database for PostgreSQL. Jeśli na Twoim komputerze klienckim jest zainstalowany program PostgreSQL, możesz użyć lokalnego wystąpienia narzędzia [psql](https://www.postgresql.org/docs/current/static/app-psql.html), aby nawiązać połączenie z serwerem Azure PostgreSQL. Użyj teraz narzędzia wiersza polecenia psql, aby nawiązać połączenie z serwerem Azure PostgreSQL.

1. Uruchom poniższe polecenie psql w celu nawiązania połączenia z serwerem usługi Azure Database for PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Na przykład poniższe polecenie służy do nawiązywania połączenia z domyślną bazą danych o nazwie **postgres** na Twoim serwerze PostgreSQL **mydemoserver.postgres.database.azure.com** za pomocą poświadczeń dostępu. Gdy zostanie wyświetlony monit o podanie hasła, wprowadź wybrane hasło `<server_admin_password>`.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Po nawiązaniu połączenia narzędzie psql wyświetli wiersz polecenia postgres, w którym można wpisywać polecenia sql. W początkowych danych wyjściowych połączenia może być wyświetlane ostrzeżenie, ponieważ używana wersja narzędzia psql może być inna niż wersja na serwerze usługi Azure Database for PostgreSQL.

   Przykład danych wyjściowych narzędzia psql:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Jeśli konfiguracja zapory nie umożliwia dostępu do adresu IP klienta, wystąpi następujący błąd:
   >
   > "PSQL: błąd krytyczny: brak wpisu pg_hba. conf dla hosta `<IP address>` , użytkownik" Administrator ", baza danych" Postgres ", SSL w przypadku krytycznym: wymagane jest połączenie SSL. Określ opcje protokołu SSL i spróbuj ponownie.
   >
   > Upewnij się, że adres IP klienta jest dozwolony w powyższym kroku reguł zapory.

2. Utwórz pustą bazę danych o nazwie „mypgsqldb”, wpisując następujące polecenie w wierszu polecenia:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. W wierszu polecenia wykonaj następujące polecenie, aby przełączyć połączenia na nowo utworzoną bazę danych **mypgsqldb**:

    ```bash
    \c mypgsqldb
    ```

4. Wpisz polecenie `\q`, a następnie naciśnij klawisz Enter, aby zamknąć narzędzie psql.

Nawiązano połączenie z serwerem usługi Azure Database for PostgreSQL za pośrednictwem narzędzia psql i utworzono pustą bazę danych użytkownika.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby, które zostały utworzone w ramach tego przewodnika Szybki start, możesz wyczyścić w jeden z dwóch sposobów. Możesz usunąć grupę zasobów platformy Azure zawierającą wszystkie zasoby w tej grupie. Jeśli chcesz zachować inne zasoby bez zmian, usuń tylko zasób serwera.

> [!TIP]
> Inne przewodniki Szybki start w tej kolekcji bazują na tym przewodniku. Jeśli chcesz kontynuować pracę z przewodnikami Szybki start, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe czynności, aby usunąć zasoby, które zostały utworzone w portalu w ramach tego przewodnika Szybki start.

Aby usunąć całą grupę zasobów łącznie z nowo utworzonym serwerem:

1. Znajdź grupę zasobów w portalu. W menu po lewej stronie wybierz pozycję **Grupy zasobów**. Następnie wybierz nazwę grupy zasobów, na przykład **myresourcegroup**.

2. Na stronie grupy zasobów wybierz pozycję **Usuń**. Wprowadź nazwę grupy zasobów, na przykład, w polu **tekstowym, można** potwierdzić usunięcie. Wybierz pozycję **Usuń**.

Aby usunąć tylko nowo utworzony serwer:

1. Znajdź serwer w portalu, jeśli nie jest otwarty. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Następnie wyszukaj utworzony serwer.

2. Na stronie **Przegląd** wybierz pozycję **Usuń**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="Przycisk Usuń":::

3. Potwierdź nazwę serwera do usunięcia i wyświetl jego bazy danych, których dotyczy ta operacja. W polu tekstowym wprowadź nazwę serwera, na przykład **mydemoserver**. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Wdrażanie aplikacji Django przy użyciu App Service i PostgreSQL](tutorial-django-app-service-postgres.md)