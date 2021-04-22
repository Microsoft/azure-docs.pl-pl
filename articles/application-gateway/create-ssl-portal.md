---
title: 'Samouczek: konfigurowanie zakończenia TLS w portalu — Azure Application Gateway'
description: Z tego samouczka dowiesz się, jak skonfigurować bramę aplikacji i dodać certyfikat do zakończenia TLS przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: aa7123a1c4dea5fcede3e94250576f6677671176
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872249"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Samouczek: konfigurowanie bramy aplikacji z zakończeniem TLS przy użyciu Azure Portal

Aby skonfigurować bramę Azure Portal za [](overview.md) pomocą certyfikatu na Azure Portal TLS, który używa maszyn wirtualnych dla serwerów zaplecza, można użyć certyfikatu zakończenia TLS.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych używanych jako serwery zaplecza
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się do Azure Portal pod [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

W tej sekcji użyjemy [new-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) do utworzenia certyfikatu z podpisem własnym. Certyfikat należy przekazać do usługi Azure Portal podczas tworzenia odbiornika dla bramy aplikacji.

Na komputerze lokalnym otwórz okno Windows PowerShell jako administrator. Uruchom następujące polecenie, aby utworzyć certyfikat:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Powinna zostać wyświetlony odpowiedź podobna do tej:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj [funkcji Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) z zwróconym odciskiem palca, aby wyeksportować plik pfx z certyfikatu. Upewnij się, że hasło ma 4–12 znaków:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję** Sieć, a **następnie Application Gateway** pozycję na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawowe** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów:** wybierz grupę zasobów **myResourceGroupAG.** Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** wprowadź *myAppGateway* jako nazwę bramy aplikacji.

        ![Tworzenie nowej bramy aplikacji: podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Możesz utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną podczas tworzenia bramy aplikacji. Application Gateway są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W **obszarze Konfigurowanie sieci wirtualnej** utwórz nową sieć wirtualną, wybierając pozycję Utwórz **nową.** W **oknie Tworzenie sieci** wirtualnej, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa:** wprowadź *myVNet* jako nazwę sieci wirtualnej.

    - **Nazwa podsieci** (Application Gateway podsieci): w siatce Podsieci zostanie pokazana **podsieć** o nazwie *Domyślna.* Zmień nazwę tej podsieci na *myAGSubnet.*<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera zaplecza): w  drugim wierszu siatki Podsieci wprowadź *wartość myBackendSubnet* w **kolumnie Nazwa podsieci.**

    - **Zakres adresów** (podsieć serwera zaplecza):  w drugim wierszu siatki Podsieci wprowadź zakres adresów, który nie nakłada się na zakres adresów *podsieci myAGSubnet.* Jeśli na przykład zakres adresów podsieci *myAGSubnet* to 10.0.0.0/24, wprowadź *wartość 10.0.1.0/24* jako zakres adresów *podsieci myBackendSubnet.*

    Wybierz **przycisk OK,** aby zamknąć **okno Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

    ![Tworzenie nowej bramy aplikacji: sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie **Podstawowe** zaakceptuj wartości domyślne dla innych ustawień, a następnie wybierz pozycję **Dalej: Frontendy**.

### <a name="frontends-tab"></a>Karta Frontends

1. Na karcie **Frontends (Frontends)** sprawdź, czy dla ustawienia Typ adresu **IP** frontendu ustawiono wartość **Public (Publiczny).** <br>Możesz skonfigurować adres IP frontendu jako publiczny lub prywatny zgodnie ze swoim przypadekem użycia. W tym przykładzie wybierzesz publiczny adres IP frontendu.
   > [!NOTE]
   > W przypadku Application Gateway w wersji 2 można wybrać tylko **konfigurację adresu** IP frontendu publicznego. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej wersji 2.

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

3. Reguła rozsyłania wymaga odbiornika. Na karcie **Odbiornik** w **oknie Dodawanie reguły rozsyłania** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika:** wprowadź *myListener* jako nazwę odbiornika.
    - **Adres IP frontendu:** **wybierz pozycję Publiczny,** aby wybrać publiczny adres IP utworzony dla frontendu.
    - **Protokół:** wybierz **pozycję HTTPS.**
    - **Port:** Sprawdź, czy dla portu wprowadzono numer 443.

   W **obszarze Ustawienia protokołu HTTPS:**

   - **Wybierz certyfikat —** wybierz **pozycję Przekaż certyfikat.**
   - **Plik certyfikatu PFX** — przejdź do wcześniej utworzyć pliku c:\appgwcert.pfx i wybierz go.
   - **Nazwa certyfikatu** — wpisz *mycert1* jako nazwę certyfikatu.
   - **Hasło** — wpisz hasło użyte do utworzenia certyfikatu.
  
        Zaakceptuj wartości domyślne dla innych  ustawień na karcie Odbiornik, a następnie wybierz kartę **Cele** zaplecza, aby skonfigurować pozostałą część reguły routingu.

   ![Tworzenie nowej bramy aplikacji: odbiornik](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na karcie **Cele zaplecza** wybierz **myBackendPool** jako **element docelowy zaplecza.**

5. W przypadku **ustawienia PROTOKOŁU HTTP** wybierz pozycję Dodaj **nowy,** aby utworzyć nowe ustawienie protokołu HTTP. Ustawienie PROTOKOŁU HTTP określi zachowanie reguły routingu. W **oknie Dodawanie ustawienia PROTOKOŁU HTTP,** które zostanie otwarte, wprowadź *myHTTPSetting* jako **nazwę ustawienia HTTP**. Zaakceptuj wartości domyślne dla innych ustawień w oknie Dodawanie ustawienia **HTTP, a** następnie wybierz pozycję **Dodaj,** aby powrócić do okna Dodawanie **reguły routingu.** 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Tworzenie nowej bramy aplikacji: ustawienie HTTP":::

6. W **oknie Dodawanie reguły routingu** wybierz pozycję **Dodaj,** aby zapisać regułę rozsyłania i wrócić do **karty** Konfiguracja.

   ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Wybierz **pozycję Dalej: Tagi,** a następnie **Dalej: Przeglądanie + tworzenie.**

### <a name="review--create-tab"></a>Karta Przeglądanie i tworzenie

Przejrzyj ustawienia na karcie **Przeglądanie +** tworzenie, a następnie wybierz pozycję Utwórz, aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji.  Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych zaplecza

W tym przykładzie użyjesz maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne, których platforma Azure używa jako serwerów zaplecza dla bramy aplikacji.

W tym celu:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2,* które będą używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Windows Server 2016 Datacenter na** **liście** Popularne. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.

   Application Gateway może przekierowyować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używasz systemu Windows Server 2016 Datacenter.

1. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów:** wybierz **myResourceGroupAG** jako nazwę grupy zasobów.
    - **Nazwa maszyny wirtualnej:** wprowadź *myVM* jako nazwę maszyny wirtualnej.
    - **Nazwa** użytkownika: wprowadź nazwę użytkownika administratora.
    - **Hasło:** wprowadź hasło dla konta administratora.
1. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
2. Zaakceptuj wartości **domyślne na karcie** Dyski, a następnie wybierz pozycję **Dalej: Sieć.**
3. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.

   Application Gateway może komunikować się z wystąpieniami spoza sieci wirtualnej, w których się znajduje, ale należy upewnić się, że istnieje łączność IP.
1. Na karcie **Zarządzanie** ustaw opcję Diagnostyka **rozruchu** na **wartość Wyłącz.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
2. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
3. Przed kontynuowaniem zaczekaj na ukończenie wdrażania.

### <a name="install-iis-for-testing"></a>Instalowanie usług IIS do testowania

W tym przykładzie zainstalujesz usługi IIS na maszynach wirtualnych tylko w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

1. Otwórz program [Azure PowerShell](../cloud-shell/quickstart-powershell.md). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Zmień ustawienie lokalizacji dla środowiska, a następnie uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj *myVM2* jako nazwy maszyny wirtualnej i dla ustawienia **VMName** polecenia cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **pozycję myBackendPool.**

4. W **obszarze Typ docelowy** wybierz pozycję Maszyna **wirtualna** z listy rozwijanej.

5. W **obszarze** Cel wybierz interfejs sieciowy w obszarze **myVM** z listy rozwijanej.

6. Powtórz tę czynność, aby dodać interfejs sieciowy dla **myVM2.**

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

7. Przed przystąpieniem do następnego kroku poczekaj na ukończenie wdrażania.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz **pozycję Wszystkie zasoby,** a następnie **wybierz pozycję myAGPublicIPAddress.**

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Na pasku adresu przeglądarki wpisz *https:// \<your application gateway ip address\>*.

   Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu  z podpisem własnym, wybierz pozycję **Szczegóły** (lub Zaawansowane w przeglądarce Chrome), a następnie przejdź do strony internetowej:

    ![Ostrzeżenie o zabezpieczeniach](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu wybierz grupę zasobów i wybierz pozycję **Usuń grupę zasobów.**

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Application Gateway TLS](ssl-overview.md)
