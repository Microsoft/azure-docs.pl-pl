---
title: 'Samouczek: Konfigurowanie zakończenia protokołu TLS w portalu — Application Gateway platformy Azure'
description: W tym samouczku dowiesz się, jak skonfigurować bramę aplikacji i dodać certyfikat dla zakończenia protokołu TLS przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: victorh
ms.openlocfilehash: 0d0522dd2f206e02ad8b63b13a9537c049232db2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88245744"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Samouczek: Konfigurowanie bramy aplikacji z zakończeniem protokołu TLS przy użyciu Azure Portal

Za pomocą Azure Portal można skonfigurować [bramę aplikacji](overview.md) z certyfikatem dla zakończenia protokołu TLS, który używa maszyn wirtualnych dla serwerów zaplecza.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Tworzenie bramy aplikacji z certyfikatem
> * Tworzenie maszyn wirtualnych używanych jako serwery zaplecza
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się do Azure Portal w [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

W tej sekcji użyto polecenie [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) , aby utworzyć certyfikat z podpisem własnym. Certyfikat jest przekazywany do Azure Portal podczas tworzenia odbiornika dla bramy aplikacji.

Na komputerze lokalnym Otwórz okno programu Windows PowerShell jako administrator. Uruchom następujące polecenie, aby utworzyć certyfikat:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Powinna zostać wyświetlona podobne odpowiedzi:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj [eksportu-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) z odciskiem palca, który został zwrócony w celu wyeksportowania pliku PFX z certyfikatu. Upewnij się, że hasło jest o długości do 4-12 znaków:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Application Gateway** na liście **polecanych** .

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **podstawowe** wprowadź następujące wartości następujących ustawień bramy aplikacji:

   - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla grupy zasobów. Jeśli ta grupa nie istnieje, wybierz pozycję **Utwórz nową** w celu jej utworzenia.
   - **Nazwa bramy aplikacji**: wprowadź *myAppGateway* jako nazwę bramy aplikacji.

        ![Utwórz nową bramę aplikacji: podstawowe](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną w tym samym czasie, podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

    W obszarze **Konfigurowanie sieci wirtualnej**Utwórz nową sieć wirtualną, wybierając pozycję **Utwórz nową**. W otwartym oknie **Tworzenie sieci wirtualnej** wprowadź następujące wartości, aby utworzyć sieć wirtualną i dwie podsieci:

    - **Nazwa**: wprowadź *myVNet* dla nazwy sieci wirtualnej.

    - **Nazwa podsieci** (Application Gateway podsieć): w siatce **podsieci** zostanie wyświetlona podsieć o nazwie *default*. Zmień nazwę tej podsieci na *myAGSubnet*.<br>Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.

    - **Nazwa podsieci** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** wprowadź *myBackendSubnet* w kolumnie **Nazwa podsieci** .

    - **Zakres adresów** (podsieć serwera wewnętrznej bazy danych): w drugim wierszu siatki **podsieci** Wprowadź zakres adresów, który nie nakłada się na zakres adresów *myAGSubnet*. Na przykład jeśli zakres adresów *myAGSubnet* jest równy 10.0.0.0/24, wprowadź *10.0.1.0/24* dla zakresu adresów *myBackendSubnet*.

    Wybierz **przycisk OK** , aby zamknąć okno **Tworzenie sieci wirtualnej** i zapisać ustawienia sieci wirtualnej.

    ![Utwórz nową bramę aplikacji: Sieć wirtualna](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na karcie **podstawy** zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Dalej: frontony**.

### <a name="frontends-tab"></a>Karta frontonów

1. Na karcie **frontony** Sprawdź, czy **Typ adresu IP frontonu** jest ustawiony na wartość **Public**. <br>Adres IP frontonu można skonfigurować tak, aby był publiczny lub prywatny jak w przypadku użycia. W tym przykładzie wybrano publiczny adres IP frontonu.
   > [!NOTE]
   > W przypadku jednostki SKU Application Gateway v2 można wybrać tylko **publiczną** konfigurację adresu IP frontonu. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz opcję **Utwórz nowy** dla **publicznego adresu IP** i wprowadź *MYAGPUBLICIPADDRESS* dla nazwy publicznego adresu IP, a następnie wybierz przycisk **OK**. 

   ![Utwórz nową bramę aplikacji: frontony](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Wybierz pozycję **Dalej: nadkończenie**.

### <a name="backends-tab"></a>Karta zakończyła się

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zapleczy, takich jak Azure App Service. W tym przykładzie utworzysz pustą pulę zaplecza z bramą aplikacji, a następnie dodasz cele zaplecza do puli zaplecza.

1. Na karcie **nadkończenie** wybierz pozycję **+ Dodaj pulę zaplecza**.

2. W otwartym oknie **Dodawanie puli zaplecza** wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa**: wprowadź *myBackendPool* jako nazwę puli zaplecza.
    - **Dodaj pulę zaplecza bez elementów docelowych**: wybierz opcję **tak** , aby utworzyć pulę zaplecza, która nie ma elementów docelowych. Po utworzeniu bramy aplikacji należy dodać cele zaplecza.

3. W oknie **Dodawanie puli zaplecza** wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza i powrócić **do karty** zaplecze.

   ![Utwórz nową bramę aplikacji: zafrontony](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na karcie **zakończyło** się wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** zostanie nawiązane połączenie frontonu i puli zaplecza utworzonej przy użyciu reguły routingu.

1. Wybierz pozycję **Dodaj regułę** w kolumnie **reguły routingu** .

2. W otwartym oknie **Dodawanie reguły routingu** wpisz *MyRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika** *: Wprowadź nazwę* odbiornika.
    - **Adres IP frontonu**: wybierz opcję **publiczny** , aby wybrać publiczny adres IP utworzony dla frontonu.
    - **Protokół**: wybierz pozycję **https**.
    - **Port**: Sprawdź, czy dla portu wprowadzono 443.

   W obszarze **certyfikat HTTPS**:

   - **Plik certyfikatu PFX** — Przeglądaj w poszukiwaniu i wybierz utworzony wcześniej plik c:\appgwcert.pfx.
   - **Nazwa certyfikatu** — typ *mycert1* dla nazwy certyfikatu.
   - **Hasło** — wpisz hasło.
  
        Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.

   ![Utwórz nową bramę aplikacji: odbiornik](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na karcie **cele zaplecza** wybierz pozycję **myBackendPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http**wybierz pozycję **Utwórz nowy** , aby utworzyć nowe ustawienie http. Ustawienie HTTP określi zachowanie reguły routingu. W oknie **Dodawanie ustawienia protokołu HTTP** , które zostanie otwarte, wprowadź *myHTTPSetting* dla **nazwy ustawienia http**. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** . 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Utwórz nową bramę aplikacji: ustawienie HTTP":::

6. W oknie **Dodawanie reguły routingu** wybierz pozycję **Dodaj** , aby zapisać regułę routingu, i wróć do karty **Konfiguracja** .

   ![Tworzenie nowej bramy aplikacji: reguła routingu](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Wybierz pozycję **Dalej: Tagi** , a następnie kliknij przycisk **Dalej: przegląd + Utwórz**.

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

Przejrzyj ustawienia na karcie **Przegląd + tworzenie** , a następnie wybierz pozycję **Utwórz** , aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-targets"></a>Dodaj cele zaplecza

W tym przykładzie użyjesz maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. Utworzysz dwie maszyny wirtualne używane przez platformę Azure jako serwery zaplecza dla bramy aplikacji.

W tym celu wykonaj następujące czynności:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, które będą używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**. Zostanie wyświetlone okno **Nowe**.
2. Wybierz pozycję **Windows Server 2016 Datacenter** na **popularnej** liście. Zostanie wyświetlona strona **Tworzenie maszyny wirtualnej**.

   Application Gateway może kierować ruch do dowolnego typu maszyny wirtualnej używanej w puli zaplecza. W tym przykładzie używane jest centrum danych systemu Windows Server 2016.

1. Wprowadź następujące wartości na karcie **Podstawy** dla poniższych ustawień maszyny wirtualnej:

    - **Grupa zasobów**: wybierz pozycję **myResourceGroupAG** dla nazwy grupy zasobów.
    - **Nazwa maszyny wirtualnej**: wprowadź *myVM* dla nazwy maszyny wirtualnej.
    - **Nazwa**użytkownika: wprowadź *azureuser* dla nazwy administratora.
    - **Hasło**: wprowadź hasło dla konta administratora.
1. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: dyski**.  
2. Zaakceptuj ustawienia domyślne karty **dyski** , a następnie wybierz pozycję **Dalej: sieć**.
3. Na karcie **Sieć** sprawdź, czy wybrano pozycję **myVNet** w obszarze **Sieć wirtualna** oraz czy pozycja **Podsieć** została ustawiona na wartość **myBackendSubnet**. Zaakceptuj inne wartości domyślne, a następnie wybierz pozycję **Dalej: Zarządzanie**.

   Application Gateway może komunikować się z wystąpieniami poza siecią wirtualną, w której znajduje się, ale należy upewnić się, że połączenie IP jest nawiązywane.
1. Na karcie **Zarządzanie** ustaw pozycję **Diagnostyka rozruchu** na **Wył.** Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przeglądanie + tworzenie**.
2. Na karcie **Przeglądanie + tworzenie** przejrzyj ustawienia, usuń wszystkie błędy walidacji, a następnie wybierz pozycję **Utwórz**.
3. Przed kontynuowaniem zaczekaj na ukończenie wdrażania.

### <a name="install-iis-for-testing"></a>Zainstaluj usługi IIS do testowania

W tym przykładzie należy zainstalować usługi IIS tylko na maszynach wirtualnych, aby sprawdzić, czy platforma Azure utworzyła bramę aplikacji pomyślnie.

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

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki ukończone wcześniej. Użyj *myVM2* dla nazwy maszyny wirtualnej i ustawienia **VMName** polecenia cmdlet **Set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.

2. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie.

3. Wybierz pozycję **myBackendPool**.

4. W obszarze **Cele** wybierz pozycję **Maszyna wirtualna** z listy rozwijanej.

5. W obszarach **MASZYNA WIRTUALNA** i **INTERFEJSY SIECIOWE** wybierz maszyny wirtualne **myVM** i **myVM2** oraz ich skojarzone interfejsy sieciowe z list rozwijanych.

    ![Dodawanie serwerów zaplecza](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Wybierz pozycję **Zapisz**.

7. Przed przejściem do następnego kroku poczekaj na zakończenie wdrożenia.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myAGPublicIPAddress**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Na pasku adresu przeglądarki wpisz *https:// \<your application gateway ip address\> *.

   Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli został użyty certyfikat z podpisem własnym, wybierz pozycję **szczegóły** (lub **Zaawansowane** w programie Chrome), a następnie przejdź do strony sieci Web:

    ![Ostrzeżenie o zabezpieczeniach](./media/create-ssl-portal/application-gateway-secure.png)

    Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o obsłudze protokołu TLS Application Gateway](ssl-overview.md)
