---
title: 'Szybki Start: Tworzenie serwera Azure Portal-Azure Database for MySQL'
description: W tym artykule podano instrukcje pozwalające utworzyć przykładowy serwer usługi Azure Database for MySQL za pomocą witryny Azure Portal w czasie około pięciu minut.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: a3438293bcbf656a371b55605c64a005ae4d599a
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541404"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL w Azure Portal

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer usługi Azure Database for MySQL za pomocą witryny Azure Portal w ciągu okołu pięciu minut.  

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz przeglądarkę internetową, a następnie przejdź do witryny [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mysql-server"></a>Tworzenie serwera usługi Azure Database for MySQL
Serwer usługi Azure Database for MySQL jest tworzony za pomocą zdefiniowanego zestawu [zasobów obliczeniowych i przestrzeni dyskowej](./concepts-pricing-tiers.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../azure-resource-manager/management/overview.md).

Wykonaj następujące kroki, aby utworzyć serwer usługi Azure Database for MySQL:

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for MySQL** . Możesz również wprowadzić **MySQL** w polu wyszukiwania, aby znaleźć usługę.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Opcja Azure Database for MySQL":::

3. Wypełnij formularz szczegółów nowego serwera, używając następujących informacji:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Opcja Azure Database for MySQL":::

**Ustawienie** | **Sugerowana wartość** | **Opis pola** 
---|---|---
Subskrypcja | Twoja subskrypcja | Wybierz subskrypcję platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz tę, w ramach której są naliczane opłaty za ten zasób.
Grupa zasobów | *myresourcegroup* | Podaj nazwę nowej lub istniejącej grupy zasobów. Grupy zasobów można używać do organizowania zależności należących do pojedynczego projektu.
Nazwa serwera | Unikatowa nazwa serwera | Wprowadź unikatową nazwę identyfikującą serwer Azure Database for MySQL. Na przykład "mysqldbserver". Nazwa serwera może zawierać tylko małe litery, cyfry i znak łącznika (-). Musi zawierać od 3 do 63 znaków.
Źródło danych |*Brak* | Wybierz pozycję *Brak* , aby utworzyć nowy serwer od podstaw. (W przypadku tworzenia serwera z geograficznej kopii zapasowej istniejącego serwera Azure Database for MySQL należy wybrać pozycję *kopia zapasowa* ).
Identyfikator logowania administratora serwera | myadmin | Wprowadź nazwę użytkownika dla administratora serwera. Nie można używać **azure_superuser** , **admin** , **administrator** , **root** , **Guest** ani **Public** jako nazwy użytkownika administratora.
Hasło | *Wybór* | Podaj nowe hasło dla konta administratora serwera. Hasło musi mieć długość od 8 do 128 znaków z kombinacją wielkich lub małych liter, cyfr i znaków innych niż alfanumeryczne (!, $, #,% itp.).
Potwierdź hasło | *Wybór*| Potwierdź hasło do konta administratora.
Lokalizacja | *Region najbliżej Twoich użytkowników*| Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure.
Wersja | *Najnowsza wersja główna*| Najnowsza wersja główna, chyba że z konkretnych powodów wymagana jest inna wersja.
Obliczenia i magazyn | **Ogólnego przeznaczenia** , **Generacja 5** , **2 rdzenie wirtualne** , **5 GB** , **7 dni** , **Geograficznie nadmiarowy** |Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer** . Następnie wybierz odpowiednią warstwę cenową, aby uzyskać więcej informacji, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/mysql/). Aby włączyć kopie zapasowe serwera w magazynie geograficznie nadmiarowym, wybierz opcję **Geograficznie nadmiarowy** z **opcji nadmiarowości kopii zapasowej** . Wybierz przycisk **OK** .

   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Pamiętaj, że serwerów utworzonych w warstwie cenowej Podstawowa nie można później przeskalować do warstwy Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. 

4. Wybierz pozycję **Przegląd + Utwórz** , aby udostępnić serwer. Aprowizowanie może zająć do 20 minut.
   
5. Wybierz pozycję **Powiadomienia** (ikonę dzwonka) na pasku narzędzi, aby monitorować proces wdrażania.
   
Domyślnie na serwerze są tworzone następujące bazy danych: **information_schema** , **mysql** , **performance_schema** i **sys** .

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera
Domyślnie utworzony serwer jest chroniony za pomocą zapory i nie jest dostępny publicznie. Aby udzielić dostępu do adresu IP, przejdź do zasobu serwera w Azure Portal i wybierz pozycję **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera. Nie wiesz, jak znaleźć zasób, zobacz [Jak otworzyć zasób](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Opcja Azure Database for MySQL":::
   
Teraz wybierz pozycję **Dodaj bieżący adres IP klienta** , a następnie wybierz pozycję **Zapisz** . Możesz dodać dodatkowe adresy IP lub podać zakres adresów, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [jak zarządzać regułami zapory na serwerze Azure Database for MySQL](./concepts-firewall-rules.md)

> [!NOTE]
> Sprawdź, czy sieć zezwala na ruch wychodzący przez port 3306, który jest używany przez Azure Database for MySQL, aby uniknąć problemów z łącznością.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Nawiązywanie połączenia z serwerem Azure Database for MySQL przy użyciu klienta wiersza polecenia MySQL
Aby nawiązać połączenie z serwerem ze środowiska lokalnego, możesz wybrać opcję [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . W tym przewodniku szybki start uruchomimy **mysql.exe** w programie [Azure Cloud Shell](../cloud-shell/overview.md) , aby nawiązać połączenie z serwerem.

1. Uruchom Azure Cloud Shell w portalu, klikając ikonę wyróżnioną w lewym górnym rogu. Zanotuj nazwę serwera, nazwę logowania administratora serwera, hasło i subskrypcję dla nowo utworzonego serwera z sekcji **Przegląd** , jak pokazano na poniższej ilustracji.

    >[!NOTE]
    >W przypadku uruchamiania usługi Cloud Shell po raz pierwszy zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Opcja Azure Database for MySQL":::
2. Uruchom to polecenie w Azure Cloud Shell terminalu. Zamień wartości na rzeczywistą nazwę serwera i nazwę logowania użytkownika administracyjnego. Nazwa użytkownika administratora wymaga znaku "@ \<servername> ", jak pokazano poniżej, aby uzyskać Azure Database for MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Oto jak wygląda jak środowisko w terminalu Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. W tym samym terminalu Azure Cloud Shell Utwórz **gościa** bazy danych 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Zmień na **gościa** bazy danych
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Wpisz ```quit``` , a następnie wybierz klawisz ENTER, aby zamknąć MySQL.   

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Pomyślnie utworzono serwer Azure Database for MySQL w grupie zasobów.  Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:
1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów** . 
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie Przegląd w grupie zasobów wybierz pozycję **Usuń grupę zasobów** .
4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń** .

Aby usunąć serwer, możesz kliknąć przycisk **Usuń** na stronie **Przegląd** serwera, jak pokazano poniżej:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Opcja Azure Database for MySQL":::

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Windows za pomocą programu MySQL](../app-service/tutorial-php-mysql-app.md) 
> [Tworzenie aplikacji PHP w systemie Linux przy użyciu programu MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux) 
> [Tworzenie aplikacji sprężynowej opartej na języku Java za pomocą programu MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)