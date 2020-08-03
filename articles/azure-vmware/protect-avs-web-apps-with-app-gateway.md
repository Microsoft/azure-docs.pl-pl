---
title: Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure
description: Skonfiguruj Application Gateway platformy Azure, aby bezpiecznie uwidaczniać aplikacje sieci Web działające na platformie Azure VMware (Automatyczna synchronizacja).
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: dfe55ab6b32e9c7b73b8501a16fa6cfaad5bbabe
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514357"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Korzystanie z usługi Azure Application Gateway do ochrony aplikacji sieci Web w rozwiązaniu VMware platformy Azure

[Usługa Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) to moduł równoważenia obciążenia sieci Web warstwy 7, który umożliwia zarządzanie ruchem do aplikacji sieci Web. Oferuje wiele możliwości: koligacja sesji oparta na plikach cookie, routing oparty na adresach URL i Zapora aplikacji sieci Web (WAF). Aby uzyskać pełną listę funkcji, zobacz [Azure Application Gateway Features](../application-gateway/features.md).) Jest ona oferowana w dwóch wersjach, v1 i v2. Oba testy zostały przetestowane przy użyciu aplikacji sieci Web działających na platformie Azure VMware (Automatyczna synchronizacja).

W tym artykule omówiono typowe scenariusze korzystania z Application Gateway przed farmą serwerów sieci Web z zestawem konfiguracji i zaleceniami dotyczącymi ochrony aplikacji sieci Web działającej na platformie Azure VMware (Automatyczna synchronizacja). 

## <a name="topology"></a>Topologia
Jak pokazano na poniższej ilustracji, Application Gateway mogą służyć do ochrony maszyn wirtualnych IaaS platformy Azure, zestawów skalowania maszyn wirtualnych platformy Azure lub serwerów lokalnych. Automatyczna synchronizacja maszyn wirtualnych będzie traktowana jako serwery lokalne przez Application Gateway.

![Application Gateway chroni maszyny wirtualne automatycznej synchronizacji.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> Usługa Azure Application Gateway jest obecnie jedyną obsługiwaną metodą udostępniania aplikacji sieci Web działających na maszynach wirtualnych o automatycznej synchronizacji.

Na poniższym diagramie przedstawiono scenariusz testowania używany do sprawdzania poprawności Application Gateway w przypadku aplikacji sieci Web z funkcją automatycznej synchronizacji.

![Application Gateway integrację z uruchomioną funkcją Web Apps.](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

Wystąpienie Application Gateway jest wdrażane w centrum w dedykowanej podsieci. Ma publiczny adres IP platformy Azure; zalecane jest aktywowanie standardowej ochrony DDoS dla sieci wirtualnej. Serwer sieci Web jest hostowany w chmurze prywatnej o automatycznej synchronizacji za NSX T0 i T1 routery. Automatyczna synchronizacja używa [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) , aby umożliwić komunikację z systemami centralnymi i lokalnymi.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją.
- Wdrożono i uruchomiono chmurę prywatną do automatycznej synchronizacji.

## <a name="deployment-and-configuration"></a>Wdrażanie i konfiguracja

1. W Azure Portal Wyszukaj **Application Gateway** i wybierz pozycję **Utwórz bramę aplikacji**.

2. Podaj podstawowe informacje, jak na poniższym rysunku: następnie wybierz kolejno pozycje **Dalej: frontony>**. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Tworzenie Application Gateway":::

3. Wybierz typ adresu IP frontonu. W obszarze publiczne wybierz istniejący publiczny adres IP lub Utwórz nowy. Wybierz pozycję **Dalej: nadkończenie>**.

    > [!NOTE]
    > Tylko jednostki SKU w warstwie Standardowa i sieci Web (WAF) są obsługiwane w przypadku prywatnych frontonów.

4. Następnie Dodaj pulę zaplecza, która opisuje zestaw wystąpień, które są częścią aplikacji lub usługi (w tym przypadku maszyny wirtualne działające w ramach infrastruktury automatycznej synchronizacji). Podaj szczegóły serwerów sieci Web działających w chmurze prywatnej automatycznej synchronizacji i wybierz pozycję **Dodaj**; następnie wybierz pozycję **Next (dalej): Configuration>**.

1. Na karcie **Konfiguracja** wybierz pozycję **Dodaj regułę routingu**.

6. Na karcie **odbiornik** Podaj szczegóły odbiornika. W przypadku wybrania protokołu HTTPS należy podać certyfikat z pliku PFX lub istniejącego certyfikatu z Azure Key Vault. 

7. Wybierz kartę **cele zaplecza** i wybierz wcześniej utworzoną pulę zaplecza. W polu **Ustawienia protokołu HTTP** wybierz pozycję **Dodaj nowe**.

8. Skonfiguruj parametry ustawień protokołu HTTP. Wybierz pozycję **Dodaj**.

9. Jeśli chcesz skonfigurować reguły oparte na ścieżce, wybierz opcję **Dodaj wiele obiektów docelowych, aby utworzyć regułę opartą na ścieżce**. 

10. Dodaj regułę opartą na ścieżce i wybierz pozycję **Dodaj**. Powtarzaj, aby dodać reguły oparte na ścieżce. 

11. Po zakończeniu dodawania reguł opartych na ścieżce wybierz pozycję **Dodaj** ponownie. następnie wybierz kolejno pozycje **Dalej: tagi>**. 

12. Dodaj wymagane Tagi. Wybierz kolejno pozycje **Dalej: recenzja + utwórz>**.

13. Sprawdzanie poprawności zostanie uruchomione na Application Gateway; Jeśli to się powiedzie, wybierz pozycję **Utwórz** do wdrożenia.

## <a name="configuration-examples"></a>Przykłady konfiguracji

W tej sekcji dowiesz się, jak skonfigurować Application Gateway z maszynami wirtualnymi o automatycznej synchronizacji jako pule zaplecza dla następujących przypadków użycia: 

- [Hostowanie wielu lokacji](#hosting-multiple-sites)
- [Routing według adresu URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hostowanie wielu lokacji

Za pomocą Azure Portal można skonfigurować hostowanie wielu witryn sieci Web podczas tworzenia bramy aplikacji. W tym samouczku zdefiniujesz pule adresów zaplecza przy użyciu maszyn wirtualnych działających w chmurze prywatnej automatycznej synchronizacji na istniejącej bramie aplikacji. Brama aplikacji jest częścią centralnej sieci wirtualnej, zgodnie z opisem w temacie [integrowanie automatycznej synchronizacji w architekturze gwiazdy i szprych](concepts-avs-hub-and-spoke-integration.md). W tym samouczku założono, że posiadasz wiele domen i używamy przykładów www.contoso.com i www.fabrikam.com.

1. Utwórz maszyny wirtualne. W chmurze prywatnej automatycznej synchronizacji Utwórz dwie różne pule maszyn wirtualnych; jeden będzie reprezentować Contoso i drugą firmę fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Pula serwerów sieci Web o automatycznej synchronizacji":::

    Aby zilustrować ten samouczek, użyto systemu Windows Server 2016 z zainstalowaną rolą Internet Information Services (IIS). Po zainstalowaniu maszyn wirtualnych Uruchom następujące polecenia programu PowerShell, aby skonfigurować usługi IIS na każdej z maszyn wirtualnych. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Dodaj pule zaplecza. W istniejącym wystąpieniu bramy aplikacji wybierz pozycję **Pule zaplecza** z menu po lewej stronie, wybierz pozycję **Dodaj**, a następnie wprowadź szczegóły nowych pul. Wybierz pozycję **Dodaj** w okienku po prawej stronie.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Konfiguracja puli zaplecza" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik dla każdej witryny sieci Web. Wprowadź szczegóły dla każdego odbiornika i wybierz pozycję **Dodaj**.

4. Na lewym pasku nawigacyjnym wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Konfiguracja ustawień widzę" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z odpowiednim odbiornikiem. Wybierz pozycję **Dodaj**.

6. Skonfiguruj odpowiednią pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

7. Przetestuj połączenie. Otwórz preferowaną przeglądarkę i przejdź do różnych witryn sieci Web hostowanych w środowisku automatycznej synchronizacji, na przykład http://www.fabrikam.com .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Konfiguracja zaplecza reguły":::

### <a name="routing-by-url"></a>Routing według adresu URL

Za pomocą usługi Azure Application Gateway można skonfigurować reguły routingu oparte na ścieżkach URL. W tym samouczku zdefiniujesz pule adresów zaplecza przy użyciu maszyn wirtualnych działających w chmurze prywatnej automatycznej synchronizacji na istniejącej bramie aplikacji. Brama aplikacji jest częścią centrum sieci wirtualnej, zgodnie z opisem w [dokumentacji dotyczącej automatycznej integracji z usługą Azure Native Integration](concepts-avs-hub-and-spoke-integration.md). Następnie utworzysz reguły routingu, aby zapewnić, że ruch internetowy dociera do odpowiednich serwerów w pulach.

1. Utwórz maszyny wirtualne. W chmurze prywatnej automatycznej synchronizacji Utwórz pulę maszyn wirtualnych do reprezentowania kolektywu serwerów sieci Web. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Pula serwerów sieci Web o automatycznej synchronizacji":::

    Do zilustrowania tego samouczka użyto systemu Windows Server 2016 z zainstalowaną rolą usług IIS. Po zainstalowaniu maszyn wirtualnych Uruchom następujące polecenia programu PowerShell, aby skonfigurować usługi IIS dla samouczka w każdej maszynie wirtualnej. 

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

2. Dodaj pule zaplecza. W istniejącym wystąpieniu bramy aplikacji należy dodać trzy nowe pule zaplecza. Wybierz pozycję **Pule zaplecza** w menu po lewej stronie. Wybierz pozycję **Dodaj** i wprowadź szczegóły pierwszej puli ( **contoso-Web**). Dodaj jedną maszynę wirtualną jako element docelowy. Wybierz pozycję **Dodaj**. Powtórz ten proces dla **contoso-images** i **contoso-Video**, dodając jedną unikatową maszynę wirtualną do każdego miejsca docelowego. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Tworzenie puli zaplecza" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. W sekcji **detektory** Utwórz nowy odbiornik typu Basic przy użyciu portu 8080.

4. Na lewym pasku nawigacyjnym wybierz pozycję **Ustawienia http** i wybierz pozycję **Dodaj** w okienku po lewej stronie. Wypełnij szczegóły, aby utworzyć nowe ustawienie HTTP, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Konfiguracja ustawień widzę":::

5. Utwórz reguły w sekcji **reguły** w menu po lewej stronie. Skojarz każdą regułę z wcześniej utworzonym odbiornikiem. Następnie skonfiguruj główną pulę zaplecza i ustawienia protokołu HTTP. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Konfiguracja zaplecza reguły":::

6. Przetestuj konfigurację. Uzyskaj dostęp do bramy Application Gateway na Azure Portal i w sekcji **Przegląd** Skopiuj publiczny adres IP. Następnie otwórz nowe okno przeglądarki i wprowadź adres URL `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Test konfiguracji":::

    Zmień adres URL na `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Test konfiguracji":::

    Zmień adres URL na `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Test konfiguracji":::

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [dokumentacją Application Gateway platformy Azure](https://docs.microsoft.com/azure/application-gateway/) , aby uzyskać więcej przykładów konfiguracyjnych.
