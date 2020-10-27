---
title: 'Szybki Start: Tworzenie elastycznego serwera usługi Azure DB dla MySQL — Azure Portal'
description: W tym artykule opisano, jak za pomocą Azure Portal szybko utworzyć Azure Database for MySQL elastyczny serwer w kilka minut.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 0c082c797c75ba912bafead15d24ea3941cfc25e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534196"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Szybki Start: Użyj Azure Portal, aby utworzyć Azure Database for MySQL elastyczny serwer

Azure Database for MySQL elastyczny serwer to zarządzana usługa, która umożliwia uruchamianie i skalowanie serwerów MySQL o wysokiej dostępności w chmurze oraz zarządzanie nimi. W tym przewodniku szybki start pokazano, jak utworzyć elastyczny serwer przy użyciu Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Otwórz przeglądarkę internetową, a następnie przejdź do witryny [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Utwórz Azure Database for MySQL elastyczny serwer

Tworzysz elastyczny serwer ze zdefiniowanym zestawem [zasobów obliczeniowych i magazynu](./concepts-compute-storage.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../../azure-resource-manager/management/overview.md).

Wykonaj następujące kroki, aby utworzyć elastyczny serwer:

1. Wyszukaj ciąg "Azure Database for MySQL" w portalu przy użyciu pola wyszukiwania, aby znaleźć usługę. 
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania" Wybierz opcję **elastyczny serwer** jako opcję wdrożenia.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania":::    

4. Wypełnij formularz **podstawy** przy użyciu następujących informacji: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania"::: 
                                    
    |**Ustawienie**|**Sugerowana wartość**|**Opis**|
    |---|---|---|
    Subskrypcja|Nazwa subskrypcji użytkownika|Subskrypcja platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której chcesz naliczać opłaty za zasób.|
    Grupa zasobów|*myresourcegroup*| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.|
    Nazwa serwera |*mydemoserver*|Unikatowa nazwa identyfikująca elastyczny serwer. Nazwa domeny *MySQL.Database.Azure.com* jest dołączana do podania nazwy serwera. Serwer może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.|
    Nazwa użytkownika administratora |*mydemouser*| Własne konto logowania do użycia podczas łączenia z serwerem. Nazwa logowania administratora nie może być **azure_superuser** , **admin** , **administrator** , **root** , **Guest** ani **Public** .|
    Hasło |Twoje hasło| Nowe hasło do konta administratora serwera. Musi zawierać od 8 do 128 znaków. Hasło musi zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (!, $, #, % itp.).|
    Region|Region najbliżej Twoich użytkowników| Lokalizacja znajdująca się najbliżej użytkowników.|
    Wersja|5.7| Wersja główna programu MySQL.|
    Obliczenia i magazyn | **Burstable** **Standard_B1ms** , **10** **dni** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer** . Wartości domyślne dla **warstwy obliczeniowej** , **rozmiaru obliczeń** , **magazynu** i **okresu przechowywania kopii zapasowej** mogą być przełączone *,* *Standard_B1ms* , *10 GIB* i *7 dni* . Możesz pozostawić te suwaki jako lub dostosować je. Aby zapisać ten wybór obliczeń i magazynu, wybierz pozycję **Zapisz** , aby kontynuować konfigurację. Poniższy zrzut ekranu przedstawia opcje obliczeń i magazynu.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania":::

5. Konfigurowanie opcji sieciowych

    Na karcie Sieć możesz wybrać sposób, w jaki serwer jest osiągalny. Azure Database for MySQL elastyczny serwer oferuje dwie opcje łączenia się z serwerem za pośrednictwem *publicznego dostępu (dozwolone adresy IP)* i *dostępu prywatnego (Integracja z siecią wirtualną)* . Mając *dostęp publiczny (dozwolone adresy IP)* , dostęp do serwera jest ograniczony do dozwolonych adresów IP dodawanych do reguły zapory. Uniemożliwia ona zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i dowolnymi bazami danych na serwerze, chyba że utworzysz regułę otwierającą zaporę dla określonego adresu IP lub zakresu. W przypadku *dostępu prywatnego (Integracja* sieci wirtualnej) dostęp do serwera jest ograniczony do sieci wirtualnej. W tym przewodniku szybki start pokazano, jak umożliwić dostęp publiczny do połączenia z serwerem. Dowiedz się więcej o metodach łączności w [artykule pojęcia](./concepts-networking.md).

    > [!NOTE]
    > Nie można zmienić metody łączności po utworzeniu serwera. Na przykład jeśli wybrano opcję *dostęp publiczny (dozwolone adresy IP)* podczas tworzenia, nie można zmienić *dostępu prywatnego (Integracja z siecią wirtualną)* po utworzeniu. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby bezpiecznie uzyskać dostęp do serwera przy użyciu integracji sieci wirtualnej. Dowiedz się więcej o prywatnym dostępie w [artykule pojęcia](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania":::  

6. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć konfigurację elastycznego serwera.

7. Wybierz pozycję **Utwórz** , aby aprowizować serwer. Inicjowanie obsługi może potrwać kilka minut.

8. Wybierz pozycję **Powiadomienia** (ikonę dzwonka) na pasku narzędzi, aby monitorować proces wdrażania. Po zakończeniu wdrażania możesz wybrać pozycję **Przypnij do pulpitu nawigacyjnego** , co spowoduje utworzenie kafelka dla tego serwera elastycznego na pulpicie nawigacyjnym Azure Portal jako skrót do strony **Przegląd** serwera. Wybranie opcji **Przejdź do zasobu** spowoduje otworzenie strony **Przegląd** serwera.

Domyślnie na serwerze są tworzone następujące bazy danych: **information_schema** , **mysql** , **performance_schema** i **sys** .

> [!NOTE]
> Sprawdź, czy sieć zezwala na ruch wychodzący przez port 3306, który jest używany przez Azure Database for MySQL elastyczny serwer, aby uniknąć problemów z łącznością.  

## <a name="connect-to-using-mysql-command-line-client"></a>Nawiązywanie połączenia przy użyciu klienta wiersza polecenia MySQL

Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)* , należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera.

Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)* , możesz dodać lokalny adres IP do listy reguł zapory na serwerze.

Aby nawiązać połączenie z serwerem ze środowiska lokalnego, możesz wybrać opcję [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . 

W mysql.exe Połącz się przy użyciu poniższego polecenia. Zastąp wartości rzeczywistą nazwą serwera i hasłem. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów
W grupie zasobów został pomyślnie utworzony Azure Database for MySQL elastyczny serwer.  Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów** .
1. Na liście Grupa zasobów wybierz nazwę grupy zasobów.
1. Na stronie Przegląd w grupie zasobów wybierz pozycję **Usuń grupę zasobów** .
1. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń** .

Aby usunąć serwer, możesz kliknąć przycisk **Usuń** na stronie **Przegląd** serwera, jak pokazano poniżej:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Wyszukaj Azure Database for MySQL&quot;:::

2. Wybierz pozycję **Dodaj** . 

3. Na stronie &quot;Wybieranie opcji wdrażania":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web PHP (platformy laravel) za pomocą programu MySQL](tutorial-php-database-app.md)