---
title: Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure
description: Skonfiguruj Application Gateway platformy Azure, aby bezpiecznie uwidaczniać aplikacje sieci Web działające w rozwiązaniu VMware platformy Azure.
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 7956ea51421f5cfa893942401c1d9a5871039689
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578490"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) to moduł równoważenia obciążenia sieci Web warstwy 7, który umożliwia zarządzanie ruchem do aplikacji sieci Web. Jest on oferowany w ramach rozwiązania Azure VMware w wersji 1.0 i 2.0. Obie wersje przetestowane z funkcją Web Apps działającą w rozwiązaniu VMware platformy Azure.

Dostępne są następujące możliwości: 
- Koligacja sesji oparta na plikach cookie
- Routing oparty na adresach URL
- Zapora aplikacji internetowej

Aby zapoznać się z pełną listą funkcji, zobacz [Azure Application Gateway Features](../application-gateway/features.md). 

W tym artykule pokazano, jak za pomocą usługi applicate Gateway przed farmą serwerów sieci Web chronić aplikację sieci Web działającą w rozwiązaniu VMware platformy Azure. 

## <a name="topology"></a>Topologia
Na diagramie przedstawiono sposób, w jaki Application Gateway jest używany do ochrony maszyn wirtualnych IaaS platformy Azure, zestawów skalowania maszyn wirtualnych platformy Azure lub serwerów lokalnych. Application Gateway traktuje maszyny wirtualne rozwiązań VMware platformy Azure jako serwery lokalne. 

![Application Gateway chroni maszyny wirtualne rozwiązań VMware platformy Azure.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Usługa Azure Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web działających na maszynach wirtualnych rozwiązań VMware platformy Azure.

Na diagramie przedstawiono scenariusz testowania używany do sprawdzania poprawności Application Gateway przy użyciu aplikacji sieci Web rozwiązań VMware platformy Azure.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Application Gateway integrację z rozwiązaniem VMware platformy Azure, które uruchamia aplikacje sieci Web" border="false":::

Wystąpienie Application Gateway jest wdrażane w centrum w dedykowanej podsieci. Ma publiczny adres IP platformy Azure. Zalecane jest aktywowanie standardowej ochrony DDoS dla sieci wirtualnej. Serwer sieci Web jest hostowany w chmurze prywatnej rozwiązań VMware platformy Azure za NSX T0 i T1 routery. Rozwiązanie VMware platformy Azure używa [Global REACH ExpressRoute](../expressroute/expressroute-global-reach.md) , aby umożliwić komunikację z systemami centralnymi i lokalnymi.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją.
- Wdrożono i uruchomiono chmurę prywatną rozwiązania VMware platformy Azure.

## <a name="deployment-and-configuration"></a>Wdrażanie i konfiguracja

1. W Azure Portal Wyszukaj **Application Gateway** i wybierz pozycję **Utwórz bramę aplikacji**.

2. Podaj podstawowe informacje, jak na poniższym rysunku: następnie wybierz kolejno pozycje **Dalej: frontony>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Tworzenie Application Gateway":::

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

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Utwórz maszyny wirtualne.":::

    Do zilustrowania tego samouczka użyto systemu Windows Server 2016 z zainstalowaną rolą Internet Information Services (IIS). Po zainstalowaniu maszyn wirtualnych Uruchom następujące polecenia programu PowerShell, aby skonfigurować usługi IIS na każdej z maszyn wirtualnych. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. W istniejącym wystąpieniu bramy aplikacji wybierz pozycję **Pule zaplecza** z menu po lewej stronie, wybierz pozycję  **Dodaj** , a następnie wprowadź szczegóły nowej puli. Wybierz pozycję **Dodaj** w okienku po prawej stronie.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Dodaj pule zaplecza." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik dla każdej witryny sieci Web. Wprowadź szczegóły dla każdego odbiornika i wybierz pozycję **Dodaj**.

4. Po lewej stronie wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję Zapisz." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z odpowiednim odbiornikiem. Wybierz pozycję **Dodaj**.

6. Skonfiguruj odpowiednią pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

7. Przetestuj połączenie. Otwórz preferowaną przeglądarkę i przejdź do różnych witryn sieci Web hostowanych w środowisku rozwiązań VMware platformy Azure, na przykład http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Przetestuj połączenie.":::

### <a name="routing-by-url"></a>Routing według adresu URL

Ta procedura pokazuje, jak definiować pule adresów zaplecza przy użyciu maszyn wirtualnych działających w chmurze prywatnej rozwiązania VMware platformy Azure w istniejącej bramie aplikacji. Następnie utworzysz reguły routingu, aby zapewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach.

1. W chmurze prywatnej Utwórz pulę maszyn wirtualnych do reprezentowania kolektywu serwerów sieci Web. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Utwórz pulę maszyn wirtualnych w rozwiązaniu VMware platformy Azure.":::

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
   1. Wybierz pozycję **Dodaj** i wprowadź szczegóły pierwszej puli ( **contoso-Web** ). 
   1. Dodaj jedną maszynę wirtualną jako element docelowy. 
   1. Wybierz pozycję **Dodaj**. 
   1. Powtórz ten proces dla **obrazów contoso** i **wideo firmy Contoso** , dodając jako element docelowy jedną unikatową maszynę wirtualną. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Dodaj trzy nowe pule zaplecza." lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik typu Basic przy użyciu portu 8080.

4. Na lewym pasku nawigacyjnym wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Konfiguracja ustawień widzę.":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z wcześniej utworzonym odbiornikiem. Następnie skonfiguruj główną pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Utwórz reguły w sekcji reguły w menu po lewej stronie.":::

6. Przetestuj konfigurację. Uzyskaj dostęp do bramy Application Gateway na Azure Portal i skopiuj publiczny adres IP w sekcji **Przegląd** . 

   1. Otwórz nowe okno przeglądarki i wprowadź adres URL `http://<app-gw-ip-address>:8080` . 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Przetestuj konfigurację z Azure Portal.":::

   1. Zmień adres URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Zmień adres URL.":::

   1. Zmień adres URL na `http://<app-gw-ip-address>:8080/video/test.htm` .

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Ponownie Zmień adres URL.":::

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dokumentacją Application Gateway platformy Azure](../application-gateway/index.yml) , aby uzyskać więcej przykładów konfiguracyjnych.