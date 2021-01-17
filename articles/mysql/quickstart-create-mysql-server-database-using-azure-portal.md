---
title: 'Szybki Start: Tworzenie serwera Azure Portal-Azure Database for MySQL'
description: W tym artykule podano instrukcje pozwalające utworzyć przykładowy serwer usługi Azure Database for MySQL za pomocą witryny Azure Portal w czasie około pięciu minut.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: a2e5cd43add6db080441812e4350d6f003ef81a2
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538495"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Szybki Start: Tworzenie serwera Azure Database for MySQL przy użyciu Azure Portal

Azure Database for MySQL to usługa zarządzana, która umożliwia uruchamianie i skalowanie w chmurze baz danych MySQL o wysokiej dostępności, a także zarządzanie nimi. W tym przewodniku szybki start pokazano, jak utworzyć Azure Database for MySQL pojedynczy serwer przy użyciu Azure Portal. Pokazuje również, jak nawiązać połączenie z serwerem.

## <a name="prerequisites"></a>Wymagania wstępne
Wymagana jest subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="create-an-azure-database-for-mysql-single-server"></a>Tworzenie Azure Database for MySQL pojedynczego serwera
1. Przejdź do [Azure Portal](https://portal.azure.com/) , aby utworzyć bazę danych jednego serwera MySQL. Wyszukaj i wybierz **Azure Database for MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Znajdź Azure Database for MySQL":::

1. Wybierz pozycję **Dodaj**.

2. Na stronie **Wybierz opcję wdrożenia Azure Database for MySQL** wybierz pozycję  **pojedynczy serwer**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Zrzut ekranu pokazujący opcję jednego serwera.":::

3. Wprowadź podstawowe ustawienia dla nowego serwera:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie serwera MySQL.":::

   **Ustawienie** | **Sugerowana wartość** | **Opis**
   ---|---|---
   Subskrypcja | Twoja subskrypcja | Wybierz żądaną subskrypcję platformy Azure.
   Grupa zasobów | **myresourcegroup** | Wprowadź nową grupę zasobów lub istniejącą w ramach subskrypcji.
   Nazwa serwera | **mydemoserver** | Wprowadź unikatową nazwę. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.
   Źródło danych |**Brak** | Wybierz pozycję **Brak**, aby utworzyć nowy serwer od podstaw. Wybierz pozycję **kopia zapasowa** tylko wtedy, gdy przywracasz z geograficznej kopii zapasowej istniejącego serwera.
   Lokalizacja |Żądana lokalizacja | Wybierz lokalizację z listy.
   Wersja | Najnowsza wersja główna| Użyj najnowszej wersji głównej. Zobacz [Wszystkie obsługiwane wersje](../mysql/concepts-supported-versions.md).
   Obliczenia i magazyn | Użyj ustawień domyślnych| Domyślna warstwa cenowa jest **ogólnego przeznaczenia** z **4 rdzeni wirtualnych** i **100 GB** pamięci. Przechowywanie kopii zapasowych jest ustawione na **7 dni** przy użyciu opcji tworzenia kopii zapasowej **geograficznie nadmiarowej** .<br/>Zapoznaj się z [cennikiem](https://azure.microsoft.com/pricing/details/mysql/) i zaktualizuj wartości domyślne, jeśli zachodzi taka potrzeba.
   Nazwa użytkownika administratora | **mydemoadmin** | Wprowadź nazwę użytkownika administratora serwera. Nie można użyć **azure_superuser**, **admin**, **administrator**, **root**, **Guest** ani **Public** dla nazwy użytkownika administratora.
   Hasło | Hasło | Nowe hasło dla użytkownika administratora serwera. Hasło musi mieć długość od 8 do 128 znaków i zawierać kombinację wielkich lub małych liter, cyfr i znaków innych niż alfanumeryczne (!, $, #,% itd.).
  

   > [!NOTE]
   > Jeśli niewielkie zasoby obliczeniowe i we/wy są wystarczające dla Twojego obciążenia, warto rozważyć użycie warstwy cenowej Podstawowa. Należy pamiętać, że serwery utworzone w warstwie cenowej podstawowa nie mogą być później skalowane do Ogólnego przeznaczenia lub zoptymalizowane pod kątem pamięci.

4. Wybierz pozycję **Przegląd + Utwórz** , aby udostępnić serwer.

5. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. Wybierz pozycję **Przejdź do zasobu** , aby przejść do nowo utworzonej strony serwera:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Zrzut ekranu pokazujący, że wdrożenie zostało zakończone.":::

[Masz problemy? Daj nam znać.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Domyślnie nowy serwer jest chroniony za pomocą zapory. Aby nawiązać połączenie, musisz zapewnić dostęp do swojego adresu IP, wykonując następujące czynności:

1. Przejdź do pozycji **zabezpieczenia połączeń** z okienka po lewej stronie dla zasobu serwera. Jeśli nie wiesz, jak znaleźć zasób, zobacz [Jak otworzyć zasób](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Zrzut ekranu przedstawiający stronę reguły zapory > zabezpieczenia połączeń.":::

2. Wybierz pozycję **Dodaj bieżący adres IP klienta**, a następnie wybierz pozycję **Zapisz**.

   > [!NOTE]
   > Aby uniknąć problemów z łącznością, należy sprawdzić, czy sieć zezwala na ruch wychodzący przez port 3306, który jest używany przez Azure Database for MySQL. 

Możesz dodać więcej adresów IP lub postanowić zakres adresów, aby połączyć się z serwerem z tych adresów IP. Aby uzyskać więcej informacji, zobacz [jak zarządzać regułami zapory na serwerze Azure Database for MySQL](./concepts-firewall-rules.md).


[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Nawiązywanie połączenia z serwerem za pomocą mysql.exe
Aby nawiązać połączenie z serwerem ze środowiska lokalnego, można użyć opcji [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . W tym przewodniku szybki start będziemy używać mysql.exe w programie [Azure Cloud Shell](../cloud-shell/overview.md) do nawiązywania połączenia z serwerem.


1. Otwórz Azure Cloud Shell w portalu, wybierając pierwszy przycisk na pasku narzędzi, jak pokazano na poniższym zrzucie ekranu. Zanotuj nazwę serwera, nazwę administratora serwera i subskrypcję nowego serwera w sekcji **Przegląd** , jak pokazano na zrzucie ekranu.

    > [!NOTE]
    > Jeśli otwierasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o utworzenie grupy zasobów i konta magazynu. Jest to jednorazowy krok. Zostanie automatycznie dołączona do wszystkich sesji.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Zrzut ekranu, który pokazuje Cloud Shell w Azure Portal.":::
2. Uruchom następujące polecenie w terminalu Azure Cloud Shell. Zastąp wartości podane tutaj rzeczywistą nazwą serwera i nazwą użytkownika administratora. W przypadku Azure Database for MySQL należy dodać `@\<servername>` do nazwy użytkownika administratora, jak pokazano poniżej: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Oto jak wygląda w terminalu Cloud Shell:

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
3. W tym samym terminalu Azure Cloud Shell Utwórz bazę danych o nazwie `guest` :
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Przełącz do `guest` bazy danych:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Wprowadź `quit` polecenie, a następnie wybierz polecenie **Enter** , aby zamknąć MySQL.

[Masz problemy? Daj nam znać.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W grupie zasobów utworzono teraz serwer Azure Database for MySQL.  Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:
1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**.
2. Z listy grup zasobów wybierz nazwę grupy zasobów.
3. Na stronie **Przegląd** dla grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
4. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, możesz wybrać pozycję **Usuń** na stronie **Przegląd** dla serwera, jak pokazano poniżej:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Zrzut ekranu pokazujący przycisk Usuń na stronie Przegląd serwera.":::

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Windows za pomocą programu MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Tworzenie aplikacji PHP w systemie Linux przy użyciu programu MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/mysql-doc-feedback)
