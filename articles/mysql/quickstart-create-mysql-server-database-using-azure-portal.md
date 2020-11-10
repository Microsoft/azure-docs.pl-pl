---
title: 'Szybki Start: Tworzenie serwera Azure Portal-Azure Database for MySQL'
description: W tym artykule podano instrukcje pozwalające utworzyć przykładowy serwer usługi Azure Database for MySQL za pomocą witryny Azure Portal w czasie około pięciu minut.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: eabc077080e16578857f9ba06e6874441dad07ee
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425860"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL w Azure Portal

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć Azure Database for MySQL pojedynczy serwer i połączyć się z serwerem.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-database-for-mysql-single-server"></a>Tworzenie Azure Database for MySQL pojedynczego serwera
Przejdź do [Azure Portal](https://portal.azure.com/) , aby utworzyć bazę danych pojedynczego serwera MySQL. Wyszukaj i wybierz *Azure Database for MySQL*.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Znajdź Azure Database for MySQL":::

1. Wybierz pozycję **Dodaj**.

2. Na stronie Tworzenie Azure Database for MySQL wybierz pozycję  **pojedynczy serwer**.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Wybierz pojedynczy serwer":::

3. Teraz wprowadź podstawowe ustawienia dla nowego serwera.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Tworzenie formularza serwera":::

   **Ustawienie** | **Sugerowana wartość** | **Opis pola**
   ---|---|---
   Subskrypcja | Twoja subskrypcja | Wybierz żądaną subskrypcję platformy Azure.
   Grupa zasobów | *myresourcegroup* | Nowa lub istniejąca Grupa zasobów z subskrypcji.
   Nazwa serwera | *mydemoserver* | Wprowadź unikatową nazwę. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
   Źródło danych |*Brak* | Wybierz pozycję Brak, aby utworzyć nowy serwer od podstaw. Wybierz pozycję Kopia zapasowa tylko wtedy, gdy przywrócono z geograficznej kopii zapasowej istniejącego serwera.
   Nazwa użytkownika administratora | *mydemoadmin* | Wprowadź nazwę użytkownika administratora serwera. Nie można używać **azure_superuser** , **admin** , **administrator** , **root** , **Guest** ani **Public** jako nazwy użytkownika administratora.
   Hasło | Twoje hasło | Nowe hasło dla użytkownika administratora serwera. Hasło musi mieć długość od 8 do 128 znaków z kombinacją wielkich lub małych liter, cyfr i znaków innych niż alfanumeryczne (!, $, #,% itp.).
   Lokalizacja |żądana lokalizacja | Wybierz lokalizację z listy rozwijanej.
   Wersja | Najnowsza wersja główna| Użyj najnowszej wersji głównej. Zobacz [Wszystkie obsługiwane wersje](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)
   Obliczenia i magazyn | Użyj domyślnych| Domyślna warstwa cenowa jest Ogólnego przeznaczenia z **4 rdzeni wirtualnych** i **100 GB** pamięci. Przechowywanie kopii zapasowych jest ustawione na **7 dni** przy użyciu opcji tworzenia kopii zapasowej geograficznie nadmiarowej.<br/>Zapoznaj się z [cennikiem](https://azure.microsoft.com/pricing/details/mysql/) i zaktualizuj wartości domyślne w razie konieczności.

   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Pamiętaj, że serwerów utworzonych w warstwie cenowej Podstawowa nie można później przeskalować do warstwy Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci.

4. Wybierz pozycję **Przegląd + Utwórz** , aby udostępnić serwer.

5. Poczekaj, aż Strona portalu **zostanie** wyświetlona. Wybierz pozycję **Przejdź do zasobu** , aby przejść do nowo utworzonej strony serwera.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="pomyślne wdrożenie":::

[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Domyślnie utworzony serwer jest chroniony za pomocą zapory. Aby nawiązać połączenie, musisz udzielić dostępu do adresu IP, wykonując następujące czynności:

1. Przejdź do pozycji **zabezpieczenia połączeń** z menu po lewej stronie dla zasobu serwera. Nie wiesz, jak znaleźć zasób, zobacz [Jak otworzyć zasób](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Zabezpieczenia połączeń — reguły zapory":::

2. Wybierz pozycję **Dodaj bieżący adres IP klienta** , a następnie wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Sprawdź, czy sieć zezwala na ruch wychodzący przez port 3306, który jest używany przez Azure Database for MySQL, aby uniknąć problemów z łącznością.

Możesz dodać dodatkowe adresy IP lub podać zakres adresów, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [jak zarządzać regułami zapory na serwerze Azure Database for MySQL](./concepts-firewall-rules.md)


[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-with-mysql-command-line-client"></a>Nawiązywanie połączenia z serwerem za pomocą klienta wiersza polecenia MySQL
Aby nawiązać połączenie z serwerem ze środowiska lokalnego, możesz wybrać opcję [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . W tym przewodniku szybki start uruchomimy **mysql.exe** w programie [Azure Cloud Shell](../cloud-shell/overview.md) , aby nawiązać połączenie z serwerem.


1. Uruchom Azure Cloud Shell w portalu, klikając ikonę wyróżnioną w lewym górnym rogu. Zanotuj nazwę serwera, nazwę logowania administratora serwera, hasło i subskrypcję dla nowo utworzonego serwera z sekcji **Przegląd** , jak pokazano na poniższej ilustracji.

    > [!NOTE]
    > W przypadku uruchamiania usługi Cloud Shell po raz pierwszy zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to krok jednorazowy, który zostanie automatycznie dołączony do wszystkich sesji.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Cloud Shell widoku pełnego portalu":::
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

[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Pomyślnie utworzono serwer Azure Database for MySQL w grupie zasobów.  Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:
1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**.
2. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
3. Na stronie Przegląd w grupie zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, możesz kliknąć przycisk **Usuń** na stronie **Przegląd** serwera, jak pokazano poniżej:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Usuwanie zasobów":::

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Windows za pomocą programu MySQL](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Linux przy użyciu programu MySQL](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/mysql-doc-feedback)
