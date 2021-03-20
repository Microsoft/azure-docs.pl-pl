---
title: 'Szybki Start: Tworzenie Azure Database for MySQL elastyczny serwer — Azure Portal'
description: W tym artykule pokazano, jak za pomocą Azure Portal utworzyć Azure Database for MySQL elastyczny serwer w ciągu kilku minut.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 074b799a4f0e83c47aac0b2b3fca5386bd45429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521972"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Szybki Start: Użyj Azure Portal, aby utworzyć Azure Database for MySQL elastyczny serwer

Azure Database for MySQL elastyczny serwer to zarządzana usługa, która umożliwia uruchamianie i skalowanie serwerów MySQL o wysokiej dostępności w chmurze oraz zarządzanie nimi. W tym przewodniku szybki start pokazano, jak utworzyć elastyczny serwer przy użyciu Azure Portal.

> [!IMPORTANT] 
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Przejdź do witryny [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Utwórz Azure Database for MySQL elastyczny serwer

Tworzysz elastyczny serwer ze zdefiniowanym zestawem [zasobów obliczeniowych i magazynu](./concepts-compute-storage.md). Serwer jest tworzony w ramach [grupy zasobów Azure](../../azure-resource-manager/management/overview.md).

Wykonaj następujące kroki, aby utworzyć elastyczny serwer:

1. Wyszukaj i wybierz **Azure Database for MySQL serwery** w portalu:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Zrzut ekranu przedstawiający wyszukiwanie serwerów Azure Database for MySQL.":::

2. Wybierz pozycję **Dodaj**. 

3. Na stronie **Wybierz opcję wdrożenia Azure Database for MySQL** wybierz opcję **elastyczny serwer** jako opcję wdrażania:
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Zrzut ekranu przedstawiający opcję elastyczny serwer.":::    

4. Na karcie **Podstawowe** wprowadź następujące informacje: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Zrzut ekranu przedstawiający kartę podstawy na stronie elastyczny serwer."::: 
                                    
    |**Ustawienie**|**Sugerowana wartość**|**Opis**|
    |---|---|---|
    Subskrypcja|Nazwa subskrypcji użytkownika|Subskrypcja platformy Azure, która ma być używana dla serwera. Jeśli masz wiele subskrypcji, wybierz subskrypcję, w ramach której chcesz naliczać opłaty za dany zasób.|
    Grupa zasobów|**myresourcegroup**| Nowa nazwa grupy zasobów lub istniejąca nazwa z subskrypcji.|
    Nazwa serwera |**mydemoserver**|Unikatowa nazwa identyfikująca elastyczny serwer. Nazwa domeny `mysql.database.azure.com` jest dołączana do podania nazwy serwera. Nazwa serwera może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi zawierać od 3 do 63 znaków.|
    Nazwa użytkownika administratora |**mydemouser**| Twoje własne konto logowania do użycia podczas łączenia się z serwerem. Nazwa użytkownika administratora nie może być **azure_superuser**, **admin**, **administrator**, **root**, **Guest** ani **Public**.|
    Hasło |Twoje hasło| Nowe hasło do konta administratora serwera. Musi zawierać od 8 do 128 znaków. Musi również zawierać znaki z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (!, $, #,% itd.).|
    Region (Region)|Region najbliżej Twoich użytkowników| Lokalizacja znajdująca się najbliżej użytkowników.|
    Wersja|**5.7**| Wersja główna programu MySQL.|
    Obliczenia i magazyn |  **Standard_B1ms**, **10** **dni** | Konfiguracje obliczania, magazynu i kopii zapasowej dla nowego serwera. Wybierz pozycję **Konfiguruj serwer**. Wartości domyślne dla **warstwy obliczeniowej**, **rozmiaru obliczeń**, **rozmiaru magazynu** i **okresu przechowywania** kopii zapasowej są przełączone **,** **Standard_B1ms**, **10 GIB** i **7 dni** . Te wartości można pozostawić lub dostosować. Aby zapisać wybór obliczeń i magazynu, wybierz pozycję **Zapisz** , aby kontynuować konfigurację. Poniższy zrzut ekranu przedstawia opcje obliczeń i magazynu.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Zrzut ekranu przedstawiający opcje obliczeń i magazynu.":::

5. Skonfiguruj opcje sieci.

    Na karcie **Sieć** możesz wybrać sposób, w jaki serwer jest osiągalny. Azure Database for MySQL elastyczny serwer zapewnia dwa sposoby nawiązywania połączenia z serwerem: 
   - Dostęp publiczny (dozwolone adresy IP)
   - Dostęp prywatny (integracja z siecią wirtualną) 
   
   W przypadku korzystania z dostępu publicznego dostęp do serwera jest ograniczony do dozwolonych adresów IP dodawanych do reguły zapory. Ta metoda uniemożliwia zewnętrznym aplikacjom i narzędziom łączenie się z serwerem i dowolnymi bazami danych na serwerze, chyba że zostanie utworzona reguła otwierająca zaporę dla określonego adresu IP lub zakresu. W przypadku korzystania z prywatnego dostępu (Integracja z siecią wirtualną) dostęp do serwera jest ograniczony do sieci wirtualnej. W tym przewodniku szybki start dowiesz się, jak włączyć dostęp publiczny do łączenia się z serwerem. [Dowiedz się więcej o metodach łączności w artykule pojęcia.](./concepts-networking.md)

    > [!NOTE]
    > Nie można zmienić metody łączności po utworzeniu serwera. Jeśli na przykład wybierzesz opcję **dostęp publiczny (dozwolone adresy IP)** podczas tworzenia serwera, nie możesz zmienić **dostępu prywatnego (Integracja z siecią wirtualną)** po utworzeniu serwera. Zdecydowanie zalecamy utworzenie serwera z dostępem prywatnym, aby pomóc w zabezpieczeniu dostępu do serwera za pośrednictwem integracji sieci wirtualnej. [Dowiedz się więcej o prywatnym dostępie w artykule pojęcia.](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Zrzut ekranu przedstawiający kartę Sieć.":::  

6. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć konfigurację elastycznego serwera.

7. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Inicjowanie obsługi może potrwać kilka minut.

8. Wybierz pozycję **powiadomienia** na pasku narzędzi (przycisk dzwonka), aby monitorować proces wdrażania. Po zakończeniu wdrażania możesz wybrać pozycję **Przypnij do pulpitu nawigacyjnego**, co spowoduje utworzenie kafelka dla elastycznego serwera na pulpicie nawigacyjnym Azure Portal. Ten kafelek jest skrótem do strony **Przegląd** serwera. Po wybraniu opcji **Przejdź do zasobu** zostanie otwarta strona **Przegląd** serwera.

Domyślnie te bazy danych są tworzone na serwerze: information_schema, MySQL, performance_schema i sys.

> [!NOTE]
> Aby uniknąć problemów z łącznością, należy sprawdzić, czy sieć zezwala na ruch wychodzący przez port 3306, który jest używany przez Azure Database for MySQL elastyczny serwer.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Nawiązywanie połączenia z serwerem za pomocą mysql.exe

Jeśli serwer elastyczny został utworzony przy użyciu dostępu prywatnego (Integracja z siecią wirtualną), należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Aby dowiedzieć się więcej, zobacz Konfigurowanie [dokumentacji dostępu prywatnego](how-to-manage-virtual-network-portal.md) .

Jeśli serwer elastyczny został utworzony przy użyciu dostępu publicznego (dozwolone adresy IP), można dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z dokumentacją dotyczącą [tworzenia reguł zapory lub zarządzania nimi](how-to-manage-firewall-portal.md) , aby uzyskać wskazówki krok po kroku.

Aby nawiązać połączenie z serwerem ze środowiska lokalnego, można użyć opcji [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) lub [MySQL Workbench](./connect-workbench.md) . 

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Jeśli udostępniasz elastyczny serwer przy użyciu **dostępu publicznego**, możesz również użyć [Azure Cloud Shell](https://shell.azure.com/bash) , aby nawiązać połączenie z elastycznym serwerem przy użyciu wstępnie zainstalowanego klienta MySQL, jak pokazano poniżej:

Aby można było używać Azure Cloud Shell do nawiązywania połączenia z serwerem elastycznym, należy zezwolić na dostęp sieciowy z Azure Cloud Shell do serwera elastycznego. Aby to osiągnąć, możesz przejść do bloku **Sieć** na Azure Portal dla elastycznego serwera MySQL i zaznaczyć pole wyboru w obszarze **Zapora** , która wskazuje, "Zezwalaj na dostęp publiczny z dowolnej usługi platformy Azure na platformie Azure do tego serwera" i kliknij przycisk Zapisz, aby zachować to ustawienie.

> [!NOTE]
> Sprawdzanie **zezwalania na dostęp publiczny z dowolnej usługi platformy Azure na platformie Azure do tego serwera** powinno być używane tylko do celów deweloperskich i testowych. Konfiguruje zaporę tak, aby zezwalała na połączenia z adresów IP przypisanych do dowolnej usługi lub zasobu platformy Azure, w tym połączeń z subskrypcji innych klientów.

Kliknij pozycję **Wypróbuj** , aby uruchomić Azure Cloud Shell i użyć poniższych poleceń, aby nawiązać połączenie z serwerem elastycznym. Użyj nazwy serwera, nazwy użytkownika i hasła w poleceniu. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Jeśli zostanie wyświetlony następujący komunikat o błędzie podczas nawiązywania połączenia z elastycznym serwerem po poleceniu wcześniej, pominięto ustawienie reguły zapory przy użyciu opcji "Zezwalaj na dostęp publiczny z dowolnych usług platformy Azure na platformie Azure na ten serwer" lub opcja nie została zapisana. Ponów ustawienie zapory i spróbuj ponownie.

BŁĄD 2002 (HY000): nie można nawiązać połączenia z serwerem MySQL na <servername> (115)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W grupie zasobów utworzono teraz Azure Database for MySQL elastyczny serwer. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, możesz je usunąć przez usunięcie grupy zasobów lub usunięcie serwera MySQL. Aby usunąć grupę zasobów, wykonaj następujące kroki:

1. W Azure Portal Wyszukaj i wybierz pozycję **grupy zasobów**.
1. Z listy grup zasobów wybierz nazwę grupy zasobów.
1. Na stronie **Przegląd** dla grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. W oknie dialogowym potwierdzenia wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

Aby usunąć serwer, możesz wybrać pozycję **Usuń** na stronie **Przegląd** dla serwera, jak pokazano poniżej:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Zrzut ekranu pokazujący sposób usunięcia serwera.":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web PHP (platformy laravel) za pomocą programu MySQL](tutorial-php-database-app.md)
