---
title: 'Szybki Start: Tworzenie serwera-Azure Portal-Azure Database for PostgreSQL-pojedynczego serwera'
description: Przewodnik Szybki Start dotyczący tworzenia serwera Azure Database for PostgreSQL i zarządzania nim przy użyciu interfejsu użytkownika Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9741e00cdcc4907c547ebfcc6f8e7df7a3b32891
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529686"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Szybki start: tworzenie serwera usługi Azure Database for PostgreSQL w witrynie Azure Portal

Azure Database for PostgreSQL to usługa zarządzana, która służy do uruchamiania i skalowania w chmurze baz danych PostgreSQL o wysokiej dostępności, a także zarządzania nimi. W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer usługi Azure Database for PostgreSQL w ciągu okołu pięciu minut za pomocą witryny Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz przeglądarkę internetową i przejdź do [portalu](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-postgresql-server"></a>Tworzenie serwera usługi Azure Database for PostgreSQL

Serwer usługi Azure Database for PostgreSQL jest tworzony ze skonfigurowanym zestawem [zasobów obliczeniowych i przestrzeni dyskowej](./concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Aby utworzyć serwer usługi Azure Database for PostgreSQL, wykonaj następujące kroki:
1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > !["Azure Database for PostgreSQL" w menu](./media/quickstart-create-database-portal/1-create-database.png)

3. Wybierz opcję wdrożenia **pojedynczego serwera** .

   > [!div class="mx-imgBorder"]
   > ![Wybierz opcję wdrażania Azure Database for PostgreSQL-pojedynczego serwera](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Wypełnij formularz **podstawy** przy użyciu następujących informacji:

   > [!div class="mx-imgBorder"]
   > ![Tworzenie serwera](./media/quickstart-create-database-portal/create-basics.png)

   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Subskrypcja|Nazwa subskrypcji użytkownika|Subskrypcja platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
   Grupa zasobów|*myresourcegroup*| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.
   Nazwa serwera |*mydemoserver*|Unikatowa nazwę, która identyfikuje serwer usługi Azure Database for PostgreSQL. Nazwa domeny *postgres.database.azure.com* jest dołączana do podawanej nazwy serwera. Serwer może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
   Źródło danych | *Brak* | Wybierz opcję *Brak* , aby utworzyć nowy serwer od podstaw. (Opcję *Kopia zapasowa* należy wybrać w przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera usługi Azure Database for PostgreSQL).
   Nazwa użytkownika administratora |*myadmin*| Własne konto logowania do użycia podczas łączenia z serwerem. Nazwa logowania administratora nie może być **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **Guest**ani **Public**. Nie może zaczynać się od **pg_**.
   Hasło |Twoje hasło| Nowe hasło do konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).
   Lokalizacja|Region najbliżej Twoich użytkowników| Lokalizacja znajdująca się najbliżej użytkowników.
   Wersja|Najnowsza wersja główna| Najnowsza wersja główna usługi PostgreSQL, chyba że masz inne określone wymagania.
   Obliczenia i magazyn | **Ogólnego przeznaczenia**, **Generacja 5**, **2 rdzenie wirtualne**, **5 GB**, **7 dni**, **Geograficznie nadmiarowy** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer**. Następnie wybierz odpowiednią warstwę cenową, aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/postgresql/server/). Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** z **opcji nadmiarowości kopii zapasowej**. Wybierz przycisk **OK**.

   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Pamiętaj, że serwerów utworzonych w warstwie cenowej Podstawowa nie można później przeskalować do warstwy Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. 
   
5. Wybierz pozycję **Recenzja + Utwórz** , aby przejrzeć wybrane opcje. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Ta operacja może potrwać kilka minut.

6. Na pasku narzędzi wybierz ikonę **Powiadomienia** (dzwonek), aby monitorować proces wdrażania. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu** powoduje otwarcie strony **Przegląd** serwera.

Zostanie utworzona pusta baza danych, **Postgres** . Zostanie również wyszukana **azure_maintenance** baza danych służąca do oddzielania procesów usługi zarządzanej od akcji użytkownika. Nie można uzyskać dostępu do **azure_maintenance** bazy danych.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera
Domyślnie utworzony serwer nie jest publicznie dostępny i musisz udzielić uprawnień do swojego adresu IP. Aby udzielić dostępu do adresu IP, przejdź do zasobu serwera w Azure Portal i wybierz pozycję **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera. Jeśli nie wiesz, jak znaleźć zasób, zobacz [Jak otworzyć zasób](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> ![Zabezpieczenia połączeń — reguły zapory](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Teraz wybierz pozycję **Dodaj bieżący adres IP klienta** , a następnie wybierz pozycję **Zapisz**. Możesz dodać dodatkowe adresy IP lub podać zakres adresów, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [jak zarządzać regułami zapory](./concepts-firewall-rules.md)
   
> [!NOTE]
> Sprawdź, czy sieć zezwala na ruch wychodzący przez port 5432, który jest używany przez Azure Database for PostgreSQL, aby uniknąć problemów z łącznością.  

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Nawiązywanie połączenia z serwerem Azure Database for PostgreSQL przy użyciu usługi PSQL

Można używać [PSQL](http://postgresguide.com/utilities/psql.html) lub [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) , które są popularnymi klientami PostgreSQL. W ramach tego przewodnika Szybki Start będziemy łączyć się za pomocą PSQL w [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) w Azure Portal.

1. Zanotuj nazwę serwera, nazwę logowania administratora serwera, hasło i Identyfikator subskrypcji dla nowo utworzonego serwera z sekcji **Przegląd** serwera, jak pokazano na poniższej ilustracji.

2. Uruchom Azure Cloud Shell w portalu, wybierając ikonę w lewym górnym rogu jako wyróżnioną na poniższej ilustracji.

   > [!NOTE]
   > W przypadku uruchamiania Cloud Shell po raz pierwszy zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji. 

   > [!div class="mx-imgBorder"]
   > ![Otwórz Azure Cloud Shell](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Uruchom to polecenie w Azure Cloud Shell terminalu. Zamień wartości na rzeczywistą nazwę serwera i nazwę logowania użytkownika administracyjnego. Użyj pustej bazy danych **Postgres** z administratorem w tym formacie "<admin-username> @ <servername> , jak pokazano poniżej dla Azure Database for PostgreSQL.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Oto jak wygląda jak środowisko w terminalu Cloud Shell
   
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
4. W tym samym terminalu Azure Cloud Shell Utwórz **gościa** bazy danych
   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Teraz, aby przełączyć połączenia do nowo utworzonego **gościa** bazy danych

   ```bash
   \c guest
   ```
6. Wpisz `\q` , a następnie wybierz klawisz ENTER, aby zakończyć PSQL. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Pomyślnie utworzono serwer Azure Database for PostgreSQL w grupie zasobów.  Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub po prostu Usuń serwer PostgreSQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**. 
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie Przegląd w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, możesz kliknąć przycisk **Usuń** na stronie **Przegląd** serwera, jak pokazano poniżej:
> [!div class="mx-imgBorder"]
> ![Usuwanie zasobów](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
