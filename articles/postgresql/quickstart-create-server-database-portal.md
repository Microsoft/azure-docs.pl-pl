---
title: 'Szybki Start: Tworzenie serwera-Azure Portal-Azure Database for PostgreSQL-pojedynczego serwera'
description: W tym przewodniku szybki start utworzysz serwer Azure Database for PostgreSQL i zarządzasz nim przy użyciu Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 2c340f9e7d2c44082adc67eeb816276449602035
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484495"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Szybki Start: Tworzenie serwera Azure Database for PostgreSQL przy użyciu Azure Portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku szybki start pokazano, jak utworzyć pojedynczy serwer Azure Database for PostgreSQL w ciągu około pięciu minut przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz przeglądarkę internetową i przejdź do [portalu](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Serwer usługi Azure Database for PostgreSQL jest tworzony ze skonfigurowanym zestawem [zasobów obliczeniowych i przestrzeni dyskowej](./concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Aby utworzyć serwer Azure Database for PostgreSQL:

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::

3. Wybierz opcję wdrożenia **pojedynczego serwera** .

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::

4. Wypełnij formularz **podstaw** , korzystając z poniższych informacji.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::

   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Subskrypcja|Nazwa subskrypcji użytkownika|Subskrypcja platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
   Grupa zasobów|*myresourcegroup*| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
   Nazwa serwera |*mydemoserver*|Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL. Nazwa domeny *Postgres.Database.Azure.com* jest dołączana do podania nazwy serwera. Serwer może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
   Źródło danych | **Brak** | Wybierz pozycję **Brak**, aby utworzyć nowy serwer od podstaw. (W przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera Azure Database for PostgreSQL należy wybrać pozycję **kopia zapasowa** ).
   Nazwa użytkownika administratora |*myadmin*| Własne konto logowania do użycia podczas łączenia z serwerem. Nazwa logowania administratora nie może być **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **Guest**ani **Public**. Nie może zaczynać się od **pg_**.
   Hasło |Twoje hasło| Nowe hasło do konta administratora serwera. Musi zawierać od 8 do 128 znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (na przykład!, $, #,%).
   Lokalizacja|Region najbliżej Twoich użytkowników| Lokalizacja znajdująca się najbliżej użytkowników.
   Wersja|Najnowsza wersja główna| Najnowsza wersja główna usługi PostgreSQL, chyba że masz inne określone wymagania.
   Obliczenia i magazyn | **Ogólnego przeznaczenia**, **Generacja 5**, **2 rdzenie wirtualne**, **5 GB**, **7 dni**, **Geograficznie nadmiarowy** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer**. Następnie wybierz odpowiednią warstwę cenową. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/postgresql/server/). Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** z **opcji nadmiarowości kopii zapasowej**. Wybierz przycisk **OK**.

   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Należy pamiętać, że serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do Ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci. 
   
5. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Ta operacja może potrwać kilka minut.

6. Na pasku narzędzi wybierz ikonę **Powiadomienia** (dzwonek), aby monitorować proces wdrażania. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu** , aby otworzyć stronę **Przegląd** serwera.

Zostanie utworzona pusta baza danych, **Postgres**. Znajduje się również **azure_maintenance** baza danych służąca do oddzielania procesów usługi zarządzanej od akcji użytkownika. Nie możesz uzyskać dostępu do bazy danych **azure_maintenance** .

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera
Domyślnie tworzony serwer nie jest publicznie dostępny. Musisz nadać uprawnienia na adres IP. Przejdź do zasobu serwera w Azure Portal i wybierz pozycję **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera. Jeśli nie masz pewności, jak znaleźć zasób, zobacz [otwarte zasoby](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::
  
Wybierz pozycję **Dodaj bieżący adres IP klienta**, a następnie wybierz pozycję **Zapisz**. Możesz dodać więcej adresów IP lub podać zakres adresów IP, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [reguły zapory w Azure Database for PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Aby uniknąć problemów z łącznością, należy sprawdzić, czy sieć zezwala na ruch wychodzący przez port 5432. Azure Database for PostgreSQL używa tego portu.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Nawiązywanie połączenia z serwerem Azure Database for PostgreSQL przy użyciu PSQL

Można używać [PSQL](http://postgresguide.com/utilities/psql.html) lub [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), które są popularnymi klientami PostgreSQL. W ramach tego przewodnika Szybki Start będziemy łączyć się za pomocą PSQL w [Azure Cloud Shell](../cloud-shell/overview.md) w Azure Portal.

1. Zanotuj nazwę serwera, nazwę logowania administratora serwera, hasło i Identyfikator subskrypcji nowo utworzonego serwera z sekcji **Przegląd** serwera.

2. Otwórz Azure Cloud Shell w portalu, wybierając ikonę w lewym górnym rogu.

   > [!NOTE]
   > W przypadku otwierania Cloud Shell po raz pierwszy zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Pomyślnie utworzono serwer Azure Database for PostgreSQL w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub serwera PostgreSQL. 

Aby usunąć grupę zasobów:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**. 
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie **Przegląd** w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, wybierz przycisk **Usuń** na stronie **Przegląd** serwera:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Zrzut ekranu przedstawiający Azure Database for PostgreSQL w menu.":::

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)