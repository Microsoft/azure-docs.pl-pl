---
title: 'Szybki start: bezpośredni ruch internetowy przy użyciu portalu'
titleSuffix: Azure Application Gateway
description: W tym przewodniku Szybki start dowiesz się, jak za pomocą interfejsu Azure Portal utworzyć maszynę wirtualną, Azure Application Gateway kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 92e966f5a9c4a21a6a55862ad582f0238802720f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538575"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal

W tym przewodniku Szybki start użyjemy Azure Portal do utworzenia bramy aplikacji. Następnie przetestuj go, aby upewnić się, że działa prawidłowo. 

Brama aplikacji kieruje ruch internetowy aplikacji do określonych zasobów w puli zaplecza. Odbiorniki przypisuje się do portów, tworzy się reguły i dodaje zasoby do puli zaplecza. Dla uproszczenia w tym artykule użyto prostej konfiguracji z publicznym adresem IP frontonia, podstawowym odbiornikiem do hostowania pojedynczej lokacji w bramie aplikacji, podstawową regułą routingu żądań i dwiema maszynami wirtualnymi w puli zaplecza.

Możesz również ukończyć ten przewodnik Szybki start przy [użyciu](quick-create-powershell.md) Azure PowerShell wiersza polecenia [platformy Azure.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w [witrynie Azure Portal](https://portal.azure.com) przy użyciu danych konta Azure.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Bramę aplikacji utworzysz przy użyciu kart na stronie **Tworzenie bramy** aplikacji.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję** Sieć, a **następnie Application Gateway** pozycję na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawowe** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów:** wybierz grupę zasobów **myResourceGroupAG.** Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** wprowadź *myAppGateway* jako nazwę bramy aplikacji.

     ![Tworzenie nowej bramy aplikacji: podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Możesz utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną podczas tworzenia bramy aplikacji. Application Gateway są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    > [!NOTE]
    > [Zasady punktu końcowego usługi dla sieci wirtualnej](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nie są obecnie obsługiwane Application Gateway podsieci.

    W **obszarze Konfigurowanie sieci wirtualnej** utwórz nową sieć wirtualną, wybierając pozycję Utwórz **nową.** W **oknie Tworzenie sieci** wirtualnej, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa:** wprowadź *myVNet* jako nazwę sieci wirtualnej.

    - **Nazwa podsieci** (Application Gateway podsieci): w siatce Podsieci zostanie pokazana **podsieć** o nazwie *Domyślna.* Zmień nazwę tej podsieci na *myAGSubnet.*<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera zaplecza): w  drugim wierszu siatki Podsieci wprowadź *wartość myBackendSubnet* w **kolumnie Nazwa podsieci.**

    - **Zakres adresów** (podsieć serwera zaplecza):  w drugim wierszu siatki Podsieci wprowadź zakres adresów, który nie nakłada się na zakres adresów *podsieci myAGSubnet.* Jeśli na przykład zakres adresów podsieci *myAGSubnet* to 10.0.0.0/24, wprowadź *wartość 10.0.1.0/24* jako zakres adresów *podsieci myBackendSubnet.*

    Wybierz **przycisk OK,** aby zamknąć **okno Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

     ![Tworzenie nowej bramy aplikacji: sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie **Podstawowe** zaakceptuj wartości domyślne dla innych ustawień, a następnie wybierz pozycję **Dalej: Frontendy**.

### <a name="frontends-tab"></a>Karta Frontends

1. Na karcie **Frontends (Frontends)** sprawdź, czy dla ustawienia Typ adresu **IP** frontendu ustawiono wartość **Public (Publiczny).** <br>Możesz skonfigurować adres IP frontonia jako publiczny lub prywatny zgodnie ze swoim przypadek użycia. W tym przykładzie wybierzesz publiczny adres IP frontendu.
   > [!NOTE]
   > W przypadku Application Gateway w wersji 2 musi być **konfiguracja** adresu IP publicznego frontonia. Nadal można mieć konfigurację publicznego i prywatnego adresu IP frontonu, ale konfiguracja adresu IP frontonu Tylko prywatna (tylko tryb równoważenia obciążenia) nie jest obecnie włączona dla wersji 2. 

2. Wybierz **pozycję Dodaj nowy** dla pola Publiczny adres **IP** i wprowadź *myAGPublicIPAddress* jako nazwę publicznego adresu IP, a następnie wybierz przycisk **OK.** 

     ![Tworzenie nowej bramy aplikacji: frontendy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz **pozycję Dalej: Zaplecza.**

### <a name="backends-tab"></a>Karta Zaplecza

Pula zaplecza służy do rozsyłania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą składać się z karty sieciowe, zestawy skalowania maszyn wirtualnych, publiczne adresy IP, wewnętrzne adresy IP, w pełni kwalifikowane nazwy domen (FQDN) i wielodostępne zaplecza, takie jak Azure App Service. W tym przykładzie utworzysz pustą pulę zaplecza z bramą aplikacji, a następnie dodasz obiekty docelowe zaplecza do puli zaplecza.

1. Na **karcie Zaplecza wybierz** pozycję **Dodaj pulę zaplecza.**

2. W **oknie Dodawanie puli zaplecza,** które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa:** wprowadź *myBackendPool* jako nazwę puli zaplecza.
    - **Dodaj pulę zaplecza bez obiektów docelowych:** wybierz pozycję **Tak,** aby utworzyć pulę zaplecza bez obiektów docelowych. Cele zaplecza dodasz po utworzeniu bramy aplikacji.

3. W **oknie Dodawanie puli zaplecza** wybierz pozycję **Dodaj,** aby zapisać konfigurację puli zaplecza i wrócić do **karty Zaplecza.**

     ![Tworzenie nowej bramy aplikacji: zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na karcie **Zaplecza wybierz** pozycję **Dalej: Konfiguracja.**

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** połączysz frontend i pulę zaplecza utworzoną przy użyciu reguły rozsyłania.

1. Wybierz **pozycję Dodaj regułę rozsyłania** w **kolumnie Reguły rozsyłania.**

2. W **oknie Dodawanie reguły** rozsyłania, które zostanie otwarte, wprowadź *myRoutingRule* jako **nazwę reguły**.

3. Reguła rozsyłania wymaga odbiornika. Na karcie **Odbiornik** w oknie Dodawanie **reguły rozsyłania** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika:** wprowadź *myListener* jako nazwę odbiornika.
    - **Adres IP frontendu:** **wybierz pozycję Publiczny,** aby wybrać publiczny adres IP utworzony dla frontendu.
  
      Zaakceptuj wartości domyślne dla innych  ustawień na karcie Odbiornik, a następnie wybierz kartę **Cele** zaplecza, aby skonfigurować pozostałą część reguły rozsyłania.

   ![Tworzenie nowej bramy aplikacji: odbiornik](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Na karcie **Cele zaplecza** wybierz **myBackendPool** jako **element docelowy zaplecza**.

5. W przypadku **ustawienia PROTOKOŁU HTTP** wybierz pozycję Dodaj **nowe,** aby dodać nowe ustawienie PROTOKOŁU HTTP. Ustawienie protokołu HTTP określi zachowanie reguły rozsyłania. W **oknie Dodawanie ustawienia PROTOKOŁU HTTP,** które zostanie otwarte, wprowadź *myHTTPSetting* jako nazwę ustawienia **HTTP** i *wartość 80* dla portu **zaplecza**. Zaakceptuj wartości domyślne dla innych ustawień w oknie Dodawanie ustawienia **HTTP, a** następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodawanie reguły rozsyłania.** 

     ![Tworzenie nowej bramy aplikacji: ustawienie HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. W **oknie Dodawanie reguły rozsyłania** wybierz pozycję **Dodaj,** aby zapisać regułę rozsyłania i wrócić do **karty** Konfiguracja.

     ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Wybierz **pozycję Dalej: Tagi,** a następnie **pozycję Dalej: Przeglądanie + tworzenie.**

### <a name="review--create-tab"></a>Karta Przeglądanie i tworzenie

Przejrzyj ustawienia na karcie **Przeglądanie +** tworzenie, a następnie wybierz pozycję Utwórz, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji.  Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych zaplecza

W tym przykładzie użyjesz maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne jako serwery zaplecza dla bramy aplikacji.

W tym celu:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2,* które będą używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Windows Server 2016 Datacenter na** **liście** Popularne. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Application Gateway może przekierowyować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używasz maszyny wirtualnej z systemem Windows Server 2016 Datacenter.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów:** wybierz **myResourceGroupAG** jako nazwę grupy zasobów.
    - **Nazwa maszyny wirtualnej:** wprowadź *myVM* jako nazwę maszyny wirtualnej.
    - **Region:** wybierz ten sam region, w którym utworzono bramę aplikacji.
    - **Nazwa** użytkownika: wpisz nazwę użytkownika administratora.
    - **Hasło:** wpisz hasło.
    - **Publiczne porty przychodzące:** Brak.
4. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
5. Zaakceptuj wartości **domyślne na karcie** Dyski, a następnie wybierz pozycję **Dalej: Sieć.**
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.<br>Application Gateway może komunikować się z wystąpieniami spoza sieci wirtualnej, w których się znajduje, ale musisz upewnić się, że istnieje łączność IP.
7. Na karcie **Zarządzanie** ustaw opcję Diagnostyka **rozruchu** na **wartość Wyłącz.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Instalowanie usług IIS w celu testowania

W tym przykładzie zainstalujesz usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

1. Otwórz program Azure PowerShell.

   Wybierz **Cloud Shell** na górnym pasku nawigacyjnym Azure Portal a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej. W razie *potrzeby* zmień parametr Location: 

    ```azurepowershell
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj *myVM2* jako nazwy maszyny wirtualnej i dla ustawienia **VMName** polecenia cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli zaplecza

1. W menu Azure Portal wybierz pozycję **Wszystkie zasoby** lub wyszukaj i wybierz pozycję Wszystkie *zasoby.* Następnie wybierz **pozycję myAppGateway.**

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **pozycję myBackendPool.**

4. W **obszarze Cele zaplecza** wybierz pozycję Typ **docelowy** **z** listy rozwijanej Maszyna wirtualna.

5. W **obszarze** Cel wybierz maszyny wirtualne **myVM** i **myVM2** oraz skojarzone z nimi interfejsy sieciowe z list rozwijanych.

   > [!div class="mx-imgBorder"]
   > ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

7. Przed przystąpieniem do następnego kroku poczekaj na ukończenie wdrożenia.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Chociaż usługi IIS nie są wymagane do utworzenia bramy aplikacji, zainstalowano je w tym przewodniku Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. 

Użyj usług do przetestowania bramy aplikacji:

1. Znajdź publiczny adres IP bramy aplikacji na stronie **Przegląd.** ![ Nagraj publiczny adres IP bramy aplikacji. Możesz też wybrać pozycję ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) **Wszystkie** zasoby, wprowadzić wartość *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybrać ją w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki, aby przeglądać ten adres IP.
3. Sprawdź odpowiedź. Prawidłowa odpowiedź sprawdza, czy brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zaplecza.

   ![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Odśwież przeglądarkę wiele razy. Powinny zostać wyświetlonych połączenia zarówno z myVM, jak i myVM2.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie bramy aplikacji i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów:

1. W menu Azure Portal grupy zasobów wybierz pozycję **Grupy zasobów** lub wyszukaj i wybierz pozycję *Grupy zasobów.*
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź *myResourceGroupAG w* obszarze **WPISZ NAZWĘ GRUPY ZASOBÓW,** a następnie wybierz pozycję **Usuń**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: konfigurowanie bramy aplikacji z zakończeniem TLS przy użyciu Azure Portal](create-ssl-portal.md)
