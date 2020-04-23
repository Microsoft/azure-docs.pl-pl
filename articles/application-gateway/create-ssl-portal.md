---
title: 'Samouczek: Konfigurowanie zakończenia protokołu TLS w portalu — brama aplikacji platformy Azure'
description: W tym samouczku dowiesz się, jak skonfigurować bramę aplikacji i dodać certyfikat dla zakończenia protokołu TLS przy użyciu portalu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: victorh
ms.openlocfilehash: 62f5375a0d468f5b137c4628c89c802d83dee102
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024497"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Samouczek: Konfigurowanie bramy aplikacji z zakończeniem protokołu TLS przy użyciu portalu Azure

Za pomocą portalu Azure można skonfigurować [bramę aplikacji](overview.md) z certyfikatem dla zakończenia protokołu TLS, który używa maszyn wirtualnych dla serwerów zaplecza.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych używanych jako serwery zaplecza
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal at[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

W tej sekcji służy [new-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) do tworzenia certyfikatu z podpisem własnym. Przekaż certyfikat do witryny Azure portal podczas tworzenia odbiornika dla bramy aplikacji.

Na komputerze lokalnym otwórz okno programu Windows PowerShell jako administrator. Uruchom następujące polecenie, aby utworzyć certyfikat:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Powinieneś zobaczyć coś takiego odpowiedzi:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj [export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) z odcisku palca, który został zwrócony do eksportu pliku pfx z certyfikatu. Upewnij się, że hasło ma długość od 4 do 12 znaków:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz **pozycję Sieć,** a następnie wybierz pozycję **Brama aplikacji** na liście **Polecane.**

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **Podstawy** wprowadź następujące wartości dla następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji:** Wprowadź *myAppGateway* dla nazwy bramy aplikacji.

        ![Tworzenie nowej bramy aplikacji: Podstawy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, w tym co brama aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**utwórz nową sieć wirtualną, wybierając **pozycję Utwórz nowy**. W oknie **Utwórz sieć wirtualną,** które zostanie otwarte, wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: Wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (podsieć bramy aplikacji): W siatce **podsieci** zostanie wyświetlonych podsieć o nazwie *Domyślna*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **Podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci.**

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): W drugim wierszu siatki **podsieci** wprowadź zakres adresów, który nie pokrywa się z zakresem adresów *myAGSubnet*. Jeśli na przykład zakres adresów *myAGSubnet* wynosi 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK,** aby zamknąć okno **Utwórz sieć wirtualną** i zapisać ustawienia sieci wirtualnej.

    ![Tworzenie nowej bramy aplikacji: sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie Podstawy zaakceptuj wartości **domyślne** dla innych ustawień, a następnie wybierz pozycję **Dalej: Przednie przyciski**.

### <a name="frontends-tab"></a>Karta Frontendy

1. Na karcie **Frontends** sprawdź, czy **typ adresu IP zaplecza** jest ustawiony na **Publiczny**. <br>Adres IP frontu można skonfigurować jako publiczny lub prywatny, zgodnie z przypadkiem użycia. W tym przykładzie wybierzesz adres IP frontu publicznego.
   > [!NOTE]
   > Dla jednostki SKU bramy aplikacji w wersji 2 można wybrać tylko konfigurację adresów IP **publicznego** frontendu. Konfiguracja ip prywatnego frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz **pozycję Utwórz nowy** dla **publicznego adresu IP** i wprowadź adres *myAGPublicIPAddress* dla publicznej nazwy adresu IP, a następnie wybierz przycisk **OK**. 

   ![Tworzenie nowej bramy aplikacji: frontendy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz **dalej: Zaplecze**.

### <a name="backends-tab"></a>Karta Zaplecze

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service. W tym przykładzie utworzysz pustą pulę wewnętrznej bazy danych z bramą aplikacji, a następnie dodasz obiekty docelowe wewnętrznej bazy danych do puli wewnętrznej bazy danych.

1. Na karcie **Wewnętrznej bazy zaznaczyć** pozycję **+Dodaj pulę zaplecza**.

2. W oknie **Dodaj pulę wewnętrznej bazy** danych, które zostanie otwarte, wprowadź następujące wartości, aby utworzyć pustą pulę wewnętrznej bazy danych:

    - **Nazwa**: Wprowadź *myBackendPool* dla nazwy puli wewnętrznej bazy danych.
    - **Dodaj pulę wewnętrznej bazy danych bez obiektów docelowych:** Wybierz **tak,** aby utworzyć pulę wewnętrznej bazy danych bez obiektów docelowych. Po utworzeniu bramy aplikacji dodasz obiekty docelowe wewnętrznej bazy danych.

3. W oknie **Dodawanie puli wewnętrznej bazy bazy danych** wybierz pozycję **Dodaj,** aby zapisać konfigurację puli wewnętrznej bazy danych i powrócić do karty **Wewnętrznej bazy** danych.

   ![Tworzenie nowej bramy aplikacji: zaplecze](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na karcie **Wewnętrznej bazy** wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** połączysz pulę frontu i wewnętrznej bazy danych utworzonej przy użyciu reguły routingu.

1. Wybierz **pozycję Dodaj regułę** w kolumnie **Reguły routingu.**

2. W oknie **Dodawanie reguły routingu,** które się otworzy, wprowadź *myRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **Odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika**: Wprowadź *myListener* dla nazwy odbiornika.
    - **Adres IP frontendu:** Wybierz **opcję Publiczne,** aby wybrać publiczny adres IP utworzony dla frontendu.
    - **Protokół**: Wybierz **HTTPS**.
    - **Port**: Sprawdź, czy 443 jest wprowadzany dla portu.

   W obszarze **Certyfikat HTTPS:**

   - **Plik certyfikatu PFX** — umożliwia przeglądanie i wybieranie pliku c:\appgwcert.pfx, który został utworzony wcześniej.
   - **Nazwa certyfikatu** — wpisz *mycert1* dla nazwy certyfikatu.
   - **Hasło** — wpisz hasło.
  
        Zaakceptuj wartości domyślne dla innych ustawień na karcie **Odbiornik,** a następnie wybierz kartę **Bazy danych,** aby skonfigurować pozostałą część reguły routingu.

   ![Tworzenie nowej bramy aplikacji: odbiornik](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na karcie **Cele wewnętrznej bazy danych** wybierz **myBackendPool** dla **celu wewnętrznej bazy**danych .

5. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy,** aby utworzyć nowe ustawienie HTTP. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **ustawień Dodaj HTTP,** które się otworzy, wprowadź *pozycjęHTTPSetting* dla **nazwy ustawienia HTTP**. Zaakceptuj wartości domyślne dla innych ustawień w oknie **ustawień Dodaj HTTP,** a następnie wybierz pozycję **Dodaj,** aby powrócić do okna **Dodaj regułę routingu.** 

   ![Tworzenie nowej bramy aplikacji: ustawienie HTTP](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj,** aby zapisać regułę routingu i wróć do karty **Konfiguracja.**

   ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Wybierz **dalej: Tagi,** a następnie **Dalej: Recenzja + utwórz**.

### <a name="review--create-tab"></a>Karta Recenzja + tworzenie

Przejrzyj ustawienia na karcie **Recenzja + utwórz,** a następnie wybierz pozycję **Utwórz,** aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodawanie obiektów docelowych wewnętrznej bazy danych

W tym przykładzie użyjesz maszyn wirtualnych jako docelowego zaplecza. Można użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne, które platforma Azure używa jako serwerów wewnętrznej bazy danych dla bramy aplikacji.

Aby to zrobić, będziesz:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, które mają być używane jako serwery wewnętrznej bazy danych.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodaj serwery wewnętrznej bazy danych do puli wewnętrznej bazy danych.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz **pozycję Centrum danych systemu Windows Server 2016** na liście **Popularne.** Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.

   Brama aplikacji może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli wewnętrznej bazy danych. W tym przykładzie należy użyć centrum danych systemu Windows Server 2016.

1. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: Wybierz **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: Wprowadź *myVM* dla nazwy maszyny wirtualnej.
    - **Nazwa użytkownika**: Wprowadź *użytkownika azureuser* dla nazwy użytkownika administratora.
    - **Hasło**: Wprowadź hasło do konta administratora.
1. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Dalej: Dyski**.  
2. Zaakceptuj domyślne ustawienia karty **Dyski,** a następnie wybierz pozycję **Dalej: Sieć**.
3. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne ustawienia domyślne, a następnie wybierz **pozycję Dalej: Zarządzanie**.

   Brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, w której się znajduje, ale musisz upewnić się, że istnieje łączność IP.
1. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
2. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
3. Poczekaj na zakończenie wdrożenia przed kontynuowaniem.

### <a name="install-iis-for-testing"></a>Instalowanie usługi IIS do testowania

W tym przykładzie można zainstalować usługi IIS na maszynach wirtualnych tylko w celu sprawdzenia, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

1. Otwórz program [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). W tym celu wybierz pozycję **Cloud Shell** na górnym pasku nawigacyjnym w witrynie Azure Portal, a następnie wybierz pozycję **PowerShell** z listy rozwijanej. 

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

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

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Nazwa maszyny wirtualnej i nazwa maszyny **wirtualnej** umożliwia użycie *myVM2* dla nazwy maszyny wirtualnej i dla ustawienia polecenia cmdlet **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli wewnętrznej bazy danych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz **myBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarach **MASZYNA WIRTUALNA** i **INTERFEJSY SIECIOWE** wybierz maszyny wirtualne **myVM** i **myVM2** oraz ich skojarzone interfejsy sieciowe z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

7. Poczekaj na zakończenie wdrożenia przed przejściem do następnego kroku.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Na pasku adresu przeglądarki wpisz *https://\<adresu\>IP bramy aplikacji*.

   Aby zaakceptować ostrzeżenie dotyczące zabezpieczeń, jeśli używano certyfikatu z podpisem własnym, wybierz **opcję Szczegóły** (lub **Zaawansowane** w Chrome), a następnie przejdź do strony sieci Web:

    ![Ostrzeżenie o zabezpieczeniach](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o obsłudze TLS bramy aplikacji](ssl-overview.md)
