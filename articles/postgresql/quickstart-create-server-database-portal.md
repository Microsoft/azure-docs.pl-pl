---
title: 'Szybki Start: Tworzenie serwera-Azure Portal-Azure Database for PostgreSQL-pojedynczego serwera'
description: W tym przewodniku szybki start utworzysz serwer Azure Database for PostgreSQL i zarządzasz nim przy użyciu Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 42e15da2fd31d163bc8822a347101704b27e1222
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913420"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Szybki Start: Tworzenie serwera Azure Database for PostgreSQL przy użyciu Azure Portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku szybki start pokazano, jak utworzyć pojedynczy serwer Azure Database for PostgreSQL i nawiązać z nim połączenie.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL
Przejdź do [Azure Portal](https://portal.azure.com/) , aby utworzyć Azure Database for PostgreSQL bazę danych na jednym serwerze. Wyszukaj i wybierz *serwery Azure Database for PostgreSQL*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Znajdź Azure Database for PostgreSQL.":::

1. Wybierz pozycję **Dodaj**.

2. Na stronie Tworzenie Azure Database for PostgreSQL wybierz pozycję  **pojedynczy serwer**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Wybierz pojedynczy serwer":::

3. Teraz wprowadź **podstawowe** informacje w formularzu.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Zrzut ekranu przedstawiający kartę podstawowe służącą do tworzenia jednego serwera.":::

   |Ustawienie|Sugerowana wartość|Opis|
   |:---|:---|:---|
   |Subskrypcja|Nazwa subskrypcji|Wybierz żądaną subskrypcję platformy Azure.|
   |Grupa zasobów|*myresourcegroup*| Nowa lub istniejąca Grupa zasobów z subskrypcji.|
   |Nazwa serwera |*mydemoserver*|Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL. Nazwa domeny *Postgres.Database.Azure.com* jest dołączana do podania nazwy serwera. Serwer może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.|
   |Źródło danych | Brak | Wybierz pozycję **Brak** , aby utworzyć nowy serwer od podstaw. Wybierz pozycję **kopia zapasowa** tylko wtedy, gdy przywrócono z geograficznej kopii zapasowej istniejącego serwera.|
   |Nazwa użytkownika administratora |*myadmin*| Wprowadź nazwę użytkownika administratora serwera. Nie może zaczynać się od **pg_** i te wartości są niedozwolone: **azure_superuser** , **azure_pg_admin** , **admin** , **administrator** , **root** , **Guest** lub **Public**.|
   |Hasło |Twoje hasło| Nowe hasło dla użytkownika administratora serwera. Musi zawierać od 8 do 128 znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (na przykład!, $, #,%).|
   |Lokalizacja|żądana lokalizacja| Wybierz lokalizację z listy rozwijanej.|
   |Wersja|Najnowsza wersja główna| Najnowsza wersja główna usługi PostgreSQL, chyba że masz inne określone wymagania.|
   |Obliczenia i magazyn | *Użyj ustawień domyślnych*| Domyślna warstwa cenowa jest **ogólnego przeznaczenia**  z **4 rdzeni wirtualnych** i **100 GB** pamięci. Przechowywanie kopii zapasowych jest ustawione na **7 dni** przy użyciu opcji tworzenia kopii zapasowej **geograficznie nadmiarowej** .<br/>Zapoznaj się z [cennikiem](https://azure.microsoft.com/pricing/details/postgresql/server/) i zaktualizuj wartości domyślne w razie konieczności.|


   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Należy pamiętać, że serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do Ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci.

5. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Wybierz pozycję **Utwórz** , aby aprowizować serwer. Ta operacja może potrwać kilka minut.
    > [!NOTE]
    > Zostanie utworzona pusta baza danych, **Postgres**. Znajduje się również **azure_maintenance** baza danych służąca do oddzielania procesów usługi zarządzanej od akcji użytkownika. Nie możesz uzyskać dostępu do bazy danych **azure_maintenance** .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="wdrożenie zakończone powodzeniem.":::

[Masz problemy? Daj nam znać.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Konfigurowanie reguły zapory
Domyślnie tworzony serwer nie jest publicznie dostępny. Musisz nadać uprawnienia na adres IP. Przejdź do zasobu serwera w Azure Portal i wybierz pozycję **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera. Jeśli nie masz pewności, jak znaleźć zasób, zobacz [otwarte zasoby](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Zrzut ekranu przedstawiający reguły zapory dotyczące zabezpieczeń połączeń.":::

Wybierz pozycję **Dodaj bieżący adres IP klienta** , a następnie wybierz pozycję **Zapisz**. Możesz dodać więcej adresów IP lub podać zakres adresów IP, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [reguły zapory w Azure Database for PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Aby uniknąć problemów z łącznością, należy sprawdzić, czy sieć zezwala na ruch wychodzący przez port 5432. Azure Database for PostgreSQL używa tego portu.

[Masz problemy? Daj nam znać.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Nawiązywanie połączenia z serwerem za pomocą PSQL

Można używać [PSQL](http://postgresguide.com/utilities/psql.html) lub [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), które są popularnymi klientami PostgreSQL. W ramach tego przewodnika Szybki Start będziemy łączyć się za pomocą PSQL w [Azure Cloud Shell](../cloud-shell/overview.md) w Azure Portal.

1. Zanotuj nazwę serwera, nazwę logowania administratora serwera, hasło i Identyfikator subskrypcji nowo utworzonego serwera z sekcji **Przegląd** serwera.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="Pobierz informacje o połączeniu.":::


2. Otwórz Azure Cloud Shell w portalu, wybierając ikonę w lewym górnym rogu.

   > [!NOTE]
   > W przypadku otwierania Cloud Shell po raz pierwszy zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Zrzut ekranu pokazujący informacje o serwerze i ikonę otwierania Azure Cloud Shell.":::

3. Uruchom następujące polecenie w terminalu Azure Cloud Shell. Zamień wartości na rzeczywistą nazwę serwera i nazwę logowania użytkownika administracyjnego. Użyj pustej bazy danych **Postgres** z administratorem w tym formacie: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Oto jak wygląda środowisko w terminalu Cloud Shell:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. W tym samym terminalu Azure Cloud Shell Utwórz bazę danych o nazwie **gość**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Przełącz połączenia na nowo utworzoną bazę danych **gościa** .

   ```bash
   \c guest
   ```
6. Wpisz `\q` , a następnie wybierz klawisz ENTER, aby zamknąć PSQL.

[Masz problemy? Daj nam znać.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Pomyślnie utworzono serwer Azure Database for PostgreSQL w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub serwera PostgreSQL.

Aby usunąć grupę zasobów:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**.
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, wybierz przycisk **Usuń** na stronie **Przegląd** serwera:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Zrzut ekranu pokazujący przycisk usuwania serwera.":::

[Masz problemy? Daj nam znać.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Projektowanie bazy danych](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/postgres-doc-feedback)
