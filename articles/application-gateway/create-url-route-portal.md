---
title: 'Samouczek: Reguły routingu oparte na ścieżkach URL przy użyciu portalu — Application Gateway platformy Azure'
description: W ramach tego samouczka nauczysz się tworzyć reguły routingu oparte na ścieżkach URL dla bramy aplikacji i zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 407bd5679c6afebf26c2e6b768e0f8513ac39123
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397590"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Samouczek: Tworzenie bramy aplikacji z regułami routingu opartymi na ścieżce przy użyciu Azure Portal

Przy użyciu Azure Portal można skonfigurować [reguły routingu oparte na ścieżkach URL](./url-route-overview.md) podczas tworzenia [bramy aplikacji](./overview.md). W tym samouczku utworzysz pule zaplecza przy użyciu maszyn wirtualnych. Następnie utworzysz reguły routingu, aby zapewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie bramy aplikacji
> * Tworzenie maszyn wirtualnych dla serwerów zaplecza
> * Tworzenie pul zaplecza z serwerami zaplecza
> * Tworzenie odbiornika zaplecza
> * Tworzenie reguły routingu opartej na ścieżce

![Przykład routingu adresów URL](./media/application-gateway-create-url-route-portal/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tym przykładzie utworzysz trzy maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Należy również zainstalować usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji działa zgodnie z oczekiwaniami.

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Windows Server 2016 Datacenter** na popularnej liście.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:

    - W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową** , a następnie wpisz *myResourceGroupAG*.
    - **Nazwa maszyny wirtualnej** : *myVM1*
    - **Region** : *(US) Wschodnie stany USA*
    - **Nazwa użytkownika** : *azureuser*
    - **Hasło** : *Azure123456!*


4. Wybierz pozycję **Dalej: Dyski**.
5. Wybierz **Następny: sieć**
6. W obszarze **Sieć wirtualna** wybierz pozycję **Utwórz nową** , a następnie wpisz następujące wartości dla sieci wirtualnej:

   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myBackendSubnet* dla pierwszej nazwy podsieci
   - *10.0.1.0/24* — dla przestrzeni adresów podsieci.
   - *myAGSubnet* — dla drugiej nazwy podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.
7. Wybierz przycisk **OK**.

8. Upewnij się, że w obszarze **interfejs sieciowy** został wybrany **myBackendSubnet** dla podsieci, a następnie wybierz pozycję **Dalej: Zarządzanie**.
9. Wybierz pozycję **wyłączone** , aby wyłączyć diagnostykę rozruchu.
10. Kliknij przycisk **Przegląd + Utwórz** , przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.
11. Utwórz dwie więcej maszyn wirtualnych, *myVM2* i *myVM3* , a następnie umieść je w sieci wirtualnej *MyVNet* i w podsieci *myBackendSubnet* .

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę interaktywną i upewnij się, że jest ustawiona na program **PowerShell**.

    ![Instalowanie rozszerzenia niestandardowego](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej: 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Utwórz dwie więcej maszyn wirtualnych i zainstaluj usługi IIS przy użyciu właśnie ukończonych kroków. Wprowadź nazwy *myVM2* i *myVM3* dla nazw oraz wartości VMName w polu Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

1. Wybierz pozycję **Utwórz zasób** w menu po lewej stronie w witrynie Azure Portal. Zostanie wyświetlone okno **Nowe**.

2. Wybierz pozycję **Sieć** , a następnie wybierz pozycję **Application Gateway** na liście **polecanych** .

### <a name="basics-tab"></a>Karta Podstawowe

1. Na karcie **podstawowe** wprowadź następujące wartości następujących ustawień bramy aplikacji:

   - **Grupa zasobów** : wybierz pozycję **myResourceGroupAG** dla grupy zasobów.
   - **Nazwa bramy aplikacji** : wprowadź *myAppGateway* jako nazwę bramy aplikacji.
   - **Region** — wybierz pozycję **US (Stany Zjednoczone)**.

        ![Utwórz nową bramę aplikacji: podstawowe](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  W obszarze **Konfigurowanie sieci wirtualnej** wybierz pozycję **myVNet** , aby określić nazwę sieci wirtualnej.
3. Wybierz pozycję **myAGSubnet** dla podsieci.
3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Next (dalej): frontony**.

### <a name="frontends-tab"></a>Karta frontonów

1. Na karcie **frontony** Sprawdź, czy **Typ adresu IP frontonu** jest ustawiony na wartość **Public**.

   > [!NOTE]
   > W przypadku jednostki SKU Application Gateway v2 można wybrać tylko **publiczną** konfigurację adresu IP frontonu. Konfiguracja prywatnego adresu IP frontonu nie jest obecnie włączona dla tej jednostki SKU w wersji 2.

2. Wybierz opcję **Utwórz nowy** dla **publicznego adresu IP** i wprowadź *MYAGPUBLICIPADDRESS* dla nazwy publicznego adresu IP, a następnie wybierz przycisk **OK**. 
3. Wybierz pozycję **Dalej: nadkończenie**.

### <a name="backends-tab"></a>Karta zakończyła się

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Pule zaplecza mogą składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zapleczy, takich jak Azure App Service.

1. Na karcie **nadkończenie** wybierz pozycję **+ Dodaj pulę zaplecza**.

2. W otwartym oknie **Dodawanie puli zaplecza** wprowadź następujące wartości, aby utworzyć pustą pulę zaplecza:

    - **Nazwa** : wprowadź *myBackendPool* jako nazwę puli zaplecza.
3. W obszarze **obiekty docelowe zaplecza** **Wybierz** pozycję **maszyna wirtualna** z listy rozwijanej.

5. W obszarze **cel** wybierz interfejs sieciowy dla **myVM1**.
6. Wybierz pozycję **Dodaj**.
7. Powtórz to polecenie, aby dodać do zestawu *obrazów* pulę zaplecza z *myVM2* jako element docelowy oraz pulę zaplecza *wideo* z *myVM3* jako obiektem docelowym.
8. Wybierz pozycję **Dodaj** , aby zapisać konfigurację puli zaplecza i powrócić **do karty** zaplecze.

4. Na karcie **zakończyło** się wybierz pozycję **Dalej: Konfiguracja**.

### <a name="configuration-tab"></a>Karta Konfiguracja

Na karcie **Konfiguracja** zostanie nawiązane połączenie frontonu i puli zaplecza utworzonej przy użyciu reguły routingu.

1. Wybierz pozycję **Dodaj regułę** w kolumnie **reguły routingu** .

2. W otwartym oknie **Dodawanie reguły routingu** wpisz *MyRoutingRule* dla **nazwy reguły**.

3. Reguła routingu wymaga odbiornika. Na karcie **odbiornik** w oknie **Dodawanie reguły routingu** wprowadź następujące wartości dla odbiornika:

    - **Nazwa odbiornika** *: Wprowadź nazwę* odbiornika.
    - **Adres IP frontonu** : wybierz opcję **publiczny** , aby wybrać publiczny adres IP utworzony dla frontonu.
    - **Port** : type *8080*
  
        Zaakceptuj wartości domyślne pozostałych ustawień na karcie **odbiornik** , a następnie wybierz kartę **cele zaplecza** , aby skonfigurować resztę reguły routingu.

4. Na karcie **cele zaplecza** wybierz pozycję **myBackendPool** dla **elementu docelowego zaplecza**.

5. Dla **Ustawienia http** wybierz pozycję **Utwórz nowy** , aby utworzyć nowe ustawienie http. Ustawienie HTTP określi zachowanie reguły routingu. 

6. W oknie **Dodawanie ustawienia protokołu HTTP** , które zostanie otwarte, wprowadź *myHTTPSetting* dla **nazwy ustawienia http**. Zaakceptuj wartości domyślne pozostałych ustawień w oknie **Dodawanie ustawienia protokołu HTTP** , a następnie wybierz pozycję **Dodaj** , aby powrócić do okna **Dodawanie reguły routingu** .
7. W obszarze **routing oparty na ścieżce** wybierz opcję **Dodaj wiele obiektów docelowych, aby utworzyć regułę opartą na ścieżce**.
8. W obszarze **ścieżka** wpisz */images/* \* .
9. Dla **nazwy reguły ścieżki** wpisz *images*.
10. W przypadku **Ustawienia protokołu HTTP** wybierz pozycję **myHTTPSetting**
11. W obszarze **cel zaplecza** wybierz pozycję **obrazy**.
12. Wybierz pozycję **Dodaj** , aby zapisać regułę ścieżki i wrócić do karty **Dodawanie reguły routingu** .
13. Powtarzaj, aby dodać kolejną regułę dla filmu wideo.
14. Wybierz pozycję **Dodaj** , aby dodać regułę routingu i powrócić do karty **Konfiguracja** .
15. Wybierz pozycję **Dalej: Tagi** , a następnie kliknij przycisk **Dalej: przegląd + Utwórz**.

> [!NOTE]
> Nie trzeba dodawać niestandardowej */* reguły ścieżki * do obsługi domyślnych przypadków. Jest to automatycznie obsługiwane przez domyślną pulę zaplecza.

### <a name="review--create-tab"></a>Przejrzyj i Utwórz kartę

Przejrzyj ustawienia na karcie **Przegląd + tworzenie** , a następnie wybierz pozycję **Utwórz** , aby utworzyć sieć wirtualną, publiczny adres IP i bramę aplikacji. Tworzenie bramy aplikacji na platformie Azure może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.


## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Wybierz pozycję **Wszystkie zasoby** , a następnie wybierz pozycję **myAppGateway**.

    ![Rejestrowanie publicznego adresu IP bramy aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład http: \/ /52.188.72.175:8080.

    ![Testowanie podstawowego adresu URL w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Odbiornik na porcie 8080 kieruje to żądanie do domyślnej puli zaplecza.

3. Zmień adres URL na *http:// &lt; IP-Address &gt; : 8080/images/test.htm* , ZASTĘPUJĄC &lt; adres IP adresem &gt; IP, a powinien wyglądać podobnie do następującego przykładu:

    ![Testowanie adresu URL obrazów w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Odbiornik na porcie 8080 kieruje to żądanie do puli zaplecza *obrazów* .

4. Zmień adres URL na *http:// &lt; IP-Address &gt; : 8080/Video/test.htm* , ZASTĘPUJĄC &lt; adres IP adresem &gt; IP, a powinien wyglądać podobnie do następującego przykładu:

    ![Testowanie adresu URL wideo w bramie aplikacji](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Odbiornik na porcie 8080 kieruje to żądanie do puli zaplecza *wideo* .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włącz kompleksową usługę TLS w usłudze Azure Application Gateway](./ssl-overview.md)