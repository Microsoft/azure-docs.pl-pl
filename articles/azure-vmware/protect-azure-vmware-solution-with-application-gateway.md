---
title: Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure
description: Skonfiguruj Application Gateway platformy Azure, aby bezpiecznie uwidaczniać aplikacje sieci Web działające w rozwiązaniu VMware platformy Azure.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: fdef37bd76b08a8778db8401a1e8a0406c2ed652
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988627"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) to moduł równoważenia obciążenia sieci Web warstwy 7, który umożliwia zarządzanie ruchem do aplikacji sieci Web. Jest on oferowany w ramach rozwiązania Azure VMware w wersji 1.0 i 2.0. Obie wersje przetestowane z funkcją Web Apps działającą w rozwiązaniu VMware platformy Azure.

Dostępne są następujące możliwości: 
- Koligacja sesji oparta na plikach cookie
- Routing oparty na adresach URL
- Zapora aplikacji internetowej

Aby zapoznać się z pełną listą funkcji, zobacz [Azure Application Gateway Features](../application-gateway/features.md). 

W tym artykule przedstawiono sposób użycia Application Gateway przed farmą serwerów sieci Web w celu ochrony aplikacji sieci Web działającej w rozwiązaniu VMware platformy Azure. 

## <a name="topology"></a>Topologia
Na diagramie przedstawiono sposób, w jaki Application Gateway jest używany do ochrony maszyn wirtualnych IaaS platformy Azure, zestawów skalowania maszyn wirtualnych platformy Azure lub serwerów lokalnych. Application Gateway traktuje maszyny wirtualne rozwiązań VMware platformy Azure jako serwery lokalne. 

![Diagram przedstawiający sposób, w jaki Application Gateway chronią maszyny wirtualne IaaS platformy Azure, zestawy skalowania maszyn wirtualnych platformy Azure lub serwery lokalne.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Usługa Azure Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web działających na maszynach wirtualnych rozwiązań VMware platformy Azure.

Na diagramie przedstawiono scenariusz testowania używany do sprawdzania poprawności Application Gateway przy użyciu aplikacji sieci Web rozwiązań VMware platformy Azure.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Diagram przedstawiający Scenariusz testowania używany do sprawdzania poprawności Application Gateway przy użyciu aplikacji sieci Web rozwiązań VMware platformy Azure." border="false":::

Wystąpienie Application Gateway jest wdrażane w centrum w dedykowanej podsieci. Ma publiczny adres IP platformy Azure. Zalecane jest aktywowanie standardowej ochrony DDoS dla sieci wirtualnej. Serwer sieci Web jest hostowany w chmurze prywatnej rozwiązań VMware platformy Azure za NSX T0 i T1 routery. Rozwiązanie VMware platformy Azure używa [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) , aby umożliwić komunikację z systemami centralnymi i lokalnymi.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją.
- Wdrożono i uruchomiono chmurę prywatną rozwiązania VMware platformy Azure.

## <a name="deployment-and-configuration"></a>Wdrażanie i konfiguracja

1. W Azure Portal Wyszukaj **Application Gateway** i wybierz pozycję **Utwórz bramę aplikacji**.

2. Podaj podstawowe informacje, jak na poniższym rysunku: następnie wybierz kolejno pozycje **Dalej: frontony>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie strony bramy aplikacji w Azure Portal.":::

3. Wybierz typ adresu IP frontonu. W obszarze publiczne wybierz istniejący publiczny adres IP lub Utwórz nowy. Wybierz pozycję **Dalej: nadkończenie>**.

    > [!NOTE]
    > Tylko jednostki SKU w warstwie Standardowa i sieci Web (WAF) są obsługiwane w przypadku prywatnych frontonów.

4. Dodaj pulę zaplecza maszyn wirtualnych działających w infrastrukturze rozwiązań VMware platformy Azure. Podaj szczegóły serwerów sieci Web, które działają w chmurze prywatnej rozwiązania Azure VMware i wybierz pozycję **Dodaj**.  Następnie wybierz pozycję **Next (dalej): Configuration>**.

1. Na karcie **Konfiguracja** wybierz pozycję **Dodaj regułę routingu**.

6. Na karcie **odbiornik** Podaj szczegóły odbiornika. W przypadku wybrania protokołu HTTPS należy podać certyfikat z pliku PFX lub istniejącego certyfikatu Azure Key Vault. 

7. Wybierz kartę **cele zaplecza** i wybierz wcześniej utworzoną pulę zaplecza. W polu **Ustawienia protokołu HTTP** wybierz pozycję **Dodaj nowe**.

8. Skonfiguruj parametry ustawień protokołu HTTP. Wybierz pozycję **Dodaj**.

9. Jeśli chcesz skonfigurować reguły oparte na ścieżce, wybierz opcję **Dodaj wiele obiektów docelowych, aby utworzyć regułę opartą na ścieżce**. 

10. Dodaj regułę opartą na ścieżce i wybierz pozycję **Dodaj**. Powtarzaj, aby dodać reguły oparte na ścieżce. 

11. Po zakończeniu dodawania reguł opartych na ścieżce wybierz pozycję **Dodaj** ponownie. następnie wybierz kolejno pozycje **Dalej: tagi>**. 

12. Dodaj Tagi, a następnie wybierz kolejno pozycje **Dalej: recenzja + utwórz>**.

13. Sprawdzanie poprawności zostanie uruchomione na Application Gateway; Jeśli to się powiedzie, wybierz pozycję **Utwórz** do wdrożenia.

## <a name="configuration-examples"></a>Przykłady konfiguracji

W tej sekcji dowiesz się, jak skonfigurować Application Gateway z maszynami wirtualnymi rozwiązań VMware platformy Azure jako pulami zaplecza dla tych przypadków użycia: 

- [Hostowanie wielu lokacji](#hosting-multiple-sites)
- [Routing według adresu URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hostowanie wielu lokacji

Ta procedura pokazuje, jak definiować pule adresów zaplecza przy użyciu maszyn wirtualnych działających w chmurze prywatnej rozwiązania VMware platformy Azure w istniejącej bramie aplikacji. 

>[!NOTE]
>W tej procedurze przyjęto założenie, że masz wiele domen, dlatego będziemy używać przykładów www.contoso.com i www.fabrikam.com.


1. W chmurze prywatnej Utwórz dwie różne pule maszyn wirtualnych. Jeden reprezentuje Contoso i drugą firmę fabrikam. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="Zrzut ekranu przedstawiający podsumowanie szczegółów serwera sieci Web w kliencie VSphere.":::

    Do zilustrowania tego samouczka użyto systemu Windows Server 2016 z zainstalowaną rolą Internet Information Services (IIS). Po zainstalowaniu maszyn wirtualnych Uruchom następujące polecenia programu PowerShell, aby skonfigurować usługi IIS na każdej z maszyn wirtualnych. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. W istniejącym wystąpieniu bramy aplikacji wybierz pozycję **Pule zaplecza** z menu po lewej stronie, wybierz pozycję  **Dodaj**, a następnie wprowadź szczegóły nowej puli. Wybierz pozycję **Dodaj** w okienku po prawej stronie.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="Zrzut ekranu przedstawiający stronę pule zaplecza służącą do dodawania pul zaplecza." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik dla każdej witryny sieci Web. Wprowadź szczegóły dla każdego odbiornika i wybierz pozycję **Dodaj**.

4. Po lewej stronie wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="Zrzut ekranu strony ustawień protokołu HTTP, aby utworzyć nowe ustawienie protokołu HTTP." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z odpowiednim odbiornikiem. Wybierz pozycję **Dodaj**.

6. Skonfiguruj odpowiednią pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

7. Przetestuj połączenie. Otwórz preferowaną przeglądarkę i przejdź do różnych witryn sieci Web hostowanych w środowisku rozwiązań VMware platformy Azure, na przykład http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądarki przedstawiającą pomyślne testowanie połączenia.":::

### <a name="routing-by-url"></a>Routing według adresu URL

Ta procedura pokazuje, jak definiować pule adresów zaplecza przy użyciu maszyn wirtualnych działających w chmurze prywatnej rozwiązania VMware platformy Azure w istniejącej bramie aplikacji. Następnie utworzysz reguły routingu, aby zapewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach.

1. W chmurze prywatnej Utwórz pulę maszyn wirtualnych do reprezentowania kolektywu serwerów sieci Web. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="Zrzut ekranu strony w programie VMSphere Client pokazujący podsumowanie innej maszyny wirtualnej.":::

    Do zilustrowania tego samouczka użyto systemu Windows Server 2016 z zainstalowaną rolą usług IIS. Po zainstalowaniu maszyn wirtualnych Uruchom następujące polecenia programu PowerShell, aby skonfigurować usługi IIS dla poszczególnych samouczków dotyczących maszyn wirtualnych. 

    Pierwsza maszyna wirtualna contoso-Web-01, która będzie hostować główną witrynę sieci Web.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    Drugą maszyną wirtualną, contoso-Web-02, będzie hostować witrynę images.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    Trzecia maszyna wirtualna contoso-Web-03 będzie hostować witrynę wideo.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Dodaj trzy nowe pule zaplecza w istniejącym wystąpieniu bramy aplikacji. 

   1. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie. 
   1. Wybierz pozycję **Dodaj** i wprowadź szczegóły pierwszej puli ( **contoso-Web**). 
   1. Dodaj jedną maszynę wirtualną jako element docelowy. 
   1. Wybierz pozycję **Dodaj**. 
   1. Powtórz ten proces dla **obrazów contoso** i **wideo firmy Contoso**, dodając jako element docelowy jedną unikatową maszynę wirtualną. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="Zrzut ekranu przedstawiający stronę pule zaplecza z dodaniem trzech nowych pul zaplecza." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik typu Basic przy użyciu portu 8080.

4. Na lewym pasku nawigacyjnym wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie ustawienia protokołu HTTP, która zawiera konfigurację ustawień protokołu HTTP.":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z wcześniej utworzonym odbiornikiem. Następnie skonfiguruj główną pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie reguły routingu w celu skonfigurowania reguł routingu do celu zaplecza.":::

6. Przetestuj konfigurację. Uzyskaj dostęp do bramy Application Gateway na Azure Portal i skopiuj publiczny adres IP w sekcji **Przegląd** . 

   1. Otwórz nowe okno przeglądarki i wprowadź adres URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądarki z pomyślnym testem konfiguracji.":::

   1. Zmień adres URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="Zrzut ekranu przedstawiający inny udany test z nowym adresem URL.":::

   1. Zmień adres URL na `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="Zrzut ekranu przedstawiający pomyślne testowanie przy użyciu końcowego adresu URL.":::

## <a name="next-steps"></a>Następne kroki

Teraz, gdy korzystasz z usługi Application Gateway do ochrony aplikacji sieci Web działającej na platformie Azure VMware, możesz zapoznać się z tematem:

- [Konfigurowanie Application Gateway platformy Azure dla różnych scenariuszy](../application-gateway/configuration-overview.md).
- [Wdrażanie Traffic Manager w celu zrównoważenia obciążeń rozwiązań VMware platformy Azure](deploy-traffic-manager-balance-workloads.md).
- [Integracja Azure NetApp Files z obciążeniami opartymi na rozwiązaniach VMware platformy Azure](netapp-files-with-azure-vmware-solution.md).
