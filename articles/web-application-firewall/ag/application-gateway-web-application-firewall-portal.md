---
title: 'Samouczek: tworzenie przy użyciu portalu — Zapora aplikacji sieci Web'
description: W tym samouczku dowiesz się, jak utworzyć bramę aplikacji za pomocą zapory aplikacji sieci Web przy użyciu Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: victorh
ms.openlocfilehash: 35bede052f06c0fcffe46460a376d10690fd4417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559636"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji za pomocą zapory aplikacji sieci Web przy użyciu Azure Portal

W tym samouczku pokazano, jak za pomocą Azure Portal utworzyć Application Gateway za pomocą zapory aplikacji sieci Web (WAF). Zapora aplikacji internetowych chroni Twoje aplikacje, używając reguł [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Reguły te obejmują ochronę przed atakami, takimi jak iniekcja kodu SQL, działanie skryptów między witrynami i porywanie sesji. Po utworzeniu bramy aplikacji przetestuj ją, aby upewnić się, że działa prawidłowo. Dzięki usłudze Azure Application Gateway kierowanie ruchu sieci Web aplikacji do określonych zasobów przez przypisanie odbiorników do portów, tworzenie reguł i Dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym samouczku użyto prostej konfiguracji z publicznym adresem IP frontonu, podstawowego odbiornika do hostowania pojedynczej lokacji w tej bramie aplikacji, dwóch maszyn wirtualnych używanych w ramach puli zaplecza i podstawowej reguły routingu żądań.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji z włączoną zaporą aplikacji internetowych
> * Tworzenie maszyn wirtualnych używanych jako serwery zaplecza
> * Tworzenie konta magazynu i konfigurowanie diagnostyki
> * Testowanie bramy aplikacji

![Przykład zapory aplikacji internetowych](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Application Gateway** na liście **polecanych** .

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **podstawowe** wprowadź następujące wartości następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.
   - **Warstwa**: Wybierz **WAF v2**.

     ![Utwórz nową bramę aplikacji: podstawowe](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej** wybierz pozycję **Utwórz nową** , aby utworzyć nową sieć wirtualną. W otwartym oknie **Tworzenie sieci wirtualnej** wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (Application Gateway podsieć): w siatce **podsieci** zostanie wyświetlona podsieć o nazwie *default*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci** .

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** Wprowadź zakres adresów, który nie nakłada się na zakres adresów *myAGSubnet*. Na przykład jeśli zakres adresów *myAGSubnet* jest równy 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK** , aby zamknąć okno **Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

     ![Utwórz nową bramę aplikacji: Sieć wirtualna](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Na karcie **podstawy** zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Dalej: frontony**.

### <a name="frontends-tab"></a>Karta frontonów

1. Na karcie **frontony** Sprawdź, czy **Typ adresu IP frontonu** jest ustawiony na wartość **Public**. <br>Adres IP frontonu można skonfigurować tak, aby był publiczny lub prywatny jak w przypadku użycia. W tym przykładzie wybrano publiczny adres IP frontonu.
   > [!NOTE]
   > W przypadku jednostki SKU Application Gateway v2 można wybrać tylko **publiczną** konfigurację adresu IP frontonu. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz pozycję **Dodaj nowy** dla **publicznego adresu IP** i wprowadź *MYAGPUBLICIPADDRESS* dla nazwy publicznego adresu IP, a następnie wybierz przycisk **OK**. 

     ![Utwórz nową bramę aplikacji: frontony](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Wybierz pozycję **Dalej: nadkończenie**.

### <a name="backends-tab"></a>Karta zakończyła się

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zapleczy, takich jak Azure App Service. W tym przykładzie utworzysz pustą pulę zaplecza z bramą aplikacji, a następnie dodasz cele zaplecza do puli zaplecza.

1. Na karcie **nadkończenie** wybierz pozycję **Dodaj pulę zaplecza**.

2. W otwartym oknie **Dodawanie puli zaplecza** wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa**: wprowadź *myBackendPool* jako nazwę puli zaplecza.
    - **Dodaj pulę zaplecza bez elementów docelowych**: wybierz opcję **tak** , aby utworzyć pulę zaplecza, która nie ma elementów docelowych. Po utworzeniu bramy aplikacji należy dodać cele zaplecza.

3. W oknie **Dodawanie puli zaplecza** wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza i powrócić **do karty** zaplecze.

     ![Utwórz nową bramę aplikacji: zafrontony](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Na karcie **zakończyło** się wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** zostanie nawiązane połączenie frontonu i puli zaplecza utworzonej przy użyciu reguły routingu.

1. Wybierz pozycję **Dodaj regułę routingu** w kolumnie **reguły routingu** .

2. W otwartym oknie **Dodawanie reguły routingu** wpisz *MyRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika** *: Wprowadź nazwę* odbiornika.
    - **Adres IP frontonu**: wybierz opcję **publiczny** , aby wybrać publiczny adres IP utworzony dla frontonu.
  
      Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.

   ![Utwórz nową bramę aplikacji: odbiornik](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Na karcie **cele zaplecza** wybierz pozycję **myBackendPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http** wybierz pozycję **Dodaj nowe** , aby utworzyć nowe ustawienie http. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **Dodawanie ustawienia protokołu HTTP** , które zostanie otwarte, wprowadź *myHTTPSetting* dla **nazwy ustawienia http**. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** . 

     ![Utwórz nową bramę aplikacji: ustawienie HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj** , aby zapisać regułę routingu, i wróć do karty **Konfiguracja** .

     ![Tworzenie nowej bramy aplikacji: reguła routingu](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Wybierz pozycję **Dalej: Tagi** , a następnie kliknij przycisk **Dalej: przegląd + Utwórz**.

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

Przejrzyj ustawienia na karcie **Przegląd + tworzenie** , a następnie wybierz pozycję **Utwórz** , aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. 

Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodaj cele zaplecza

W tym przykładzie użyjesz maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne używane przez platformę Azure jako serwery zaplecza dla bramy aplikacji.

W tym celu wykonaj następujące czynności:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, które będą używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.<br>Application Gateway może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używane jest centrum danych systemu Windows Server 2016.
3. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: wprowadź *myVM* dla nazwy maszyny wirtualnej.
    - **Nazwa użytkownika: wprowadź** nazwę administratora.
    - **Hasło**: wprowadź hasło administratora.
4. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: dyski**.  
5. Zaakceptuj ustawienia domyślne karty **dyski** , a następnie wybierz pozycję **Dalej: sieć**.
6. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.<br>Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, w której znajduje się, ale należy upewnić się, że połączenie IP jest nawiązywane.
7. Na karcie **Zarządzanie** Skonfiguruj **diagnostykę rozruchu** , aby **wyłączyć**. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
8. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
9. Poczekaj na ukończenie tworzenia maszyny wirtualnej, zanim przejdziesz dalej.

### <a name="install-iis-for-testing"></a>Zainstaluj usługi IIS do testowania

W tym przykładzie należy zainstalować usługi IIS tylko na maszynach wirtualnych, aby sprawdzić, czy platforma Azure utworzyła bramę aplikacji pomyślnie.

1. Otwórz program [Azure PowerShell](../../cloud-shell/quickstart-powershell.md). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Ustaw parametr Location dla środowiska, a następnie uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell-interactive
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

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj *myVM2* dla nazwy maszyny wirtualnej i ustawienia **VMName** polecenia cmdlet **Set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz pozycję **myBackendPool**.

4. W obszarze **Typ docelowy** wybierz z listy rozwijanej pozycję **maszyna wirtualna** .

5. W obszarze **cel** wybierz z listy rozwijanej skojarzony interfejs sieciowy dla **myVM** .
1. Powtórz dla **myVM2**.

   :::image type="content" source="../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png" alt-text="Dodawanie serwerów zaplecza":::


6. Wybierz pozycję **Zapisz**.

7. Przed przejściem do następnego kroku poczekaj na zakończenie wdrożenia.

   
## <a name="create-and-link-a-web-application-firewall-policy"></a>Tworzenie i łączenie zasad zapory aplikacji sieci Web

Wszystkie dostosowania i ustawienia WAF znajdują się w osobnym obiekcie, zwanym zasadami WAF. Zasady muszą być skojarzone z Twoim Application Gateway. 

Utwórz podstawowe zasady WAF z zarządzanym domyślnym zestawem reguł (DRS).

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**. Wyszukaj pozycję **WAF**, wybierz pozycję **Zapora aplikacji sieci Web**, a następnie wybierz pozycję **Utwórz**.
2. Na stronie **Tworzenie zasad WAF** , **podstawowe** karty, wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Zasady dla     |WAF regionalny (Application Gateway)|
   |Subskrypcja     |Wybierz nazwę subskrypcji|
   |Grupa zasobów     |Wybierz **myResourceGroupAG**|
   |Nazwa zasady     |Wpisz unikatową nazwę zasad WAF.|
1. Wybierz kolejno pozycje **Dalej: ustawienia zasad**.
1. Zaakceptuj wartości domyślne, a następnie wybierz kolejno pozycje **Dalej: reguły zarządzane**.
1. Zaakceptuj wartość domyślną, a następnie wybierz kolejno pozycje **Dalej: Reguły niestandardowe**.
1. Wybierz pozycję **Dalej: skojarzenie**.
1. Wybierz pozycję **Dodaj skojarzenie** , a następnie wybierz pozycję **Application Gateway**.
1. Zaznacz pole wyboru **Zastosuj konfigurację zasad zapory aplikacji sieci Web, nawet jeśli różni się od bieżącej konfiguracji**.
1. Wybierz pozycję **Dodaj**.
1. Na karcie **skojarzenie** wybierz pozycję **Dodaj skojarzenie**, a następnie wybierz pozycję **Application Gateway**.

   > [!NOTE]
   > W przypadku przypisywania zasad do Application Gateway (lub odbiornika), które mają już zasady, pierwotne zasady zostaną zastąpione i zastąpione przez nowe zasady.
4. Wybierz pozycję **Przeglądanie i tworzenie**, a następnie wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Dalej: Tagi**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Chociaż usługi IIS nie są wymagane do utworzenia bramy aplikacji, zainstalowano ją w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Na stronie **przeglądowej** Znajdź publiczny adres IP bramy aplikacji. ![ Rejestruj publiczny adres IP bramy aplikacji](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Możesz też wybrać pozycję **wszystkie zasoby**, wprowadzić wartość *myAGPublicIPAddress* w polu wyszukiwania, a następnie wybrać ją w wynikach wyszukiwania. Platforma Azure wyświetla publiczny adres IP na stronie **Omówienie**.
1. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.
1. Sprawdź odpowiedź. Prawidłowa odpowiedź weryfikuje, czy Brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zapleczem.

   ![Testowanie bramy aplikacji](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów:

1. W menu po lewej stronie witryny Azure Portal wybierz pozycję **Grupy zasobów**.
2. Na stronie **Grupy zasobów** wyszukaj pozycję **myResourceGroupAG** na liście i wybierz ją.
3. Na **stronie grupy zasobów** wybierz pozycję **Usuń grupę zasobów**.
4. Wprowadź *myResourceGroupAG* w **polu wpisz nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zaporze aplikacji sieci Web](../overview.md)