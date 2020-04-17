---
title: Konfigurowanie wewnętrznego punktu końcowego równoważenia obciążenia (ILB)
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące konfigurowania bramy aplikacji przy posługiwać się prywatnym adresem IP wewnętrznej bazy danych
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: df21a2c40dd532ac1ff321638099ceee8a2b3e53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535608"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurowanie bramy aplikacji przy za pomocą wewnętrznego punktu końcowego równoważenia obciążenia (ILB)

Brama aplikacji platformy Azure można skonfigurować za pomocą adresu VIP z dostępem do Internetu lub z wewnętrznym punktem końcowym, który nie jest narażony na Działanie Internetu. Wewnętrzny punkt końcowy używa prywatnego adresu IP dla frontendu, który jest również znany jako *wewnętrzny punkt końcowy równoważenia obciążenia (ILB).*

Konfigurowanie bramy przy użyciu prywatnego adresu IP wewnętrznej bazy danych jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są udostępniane w Internecie. Jest to również przydatne w przypadku usług i warstw w aplikacji wielowarstwowej, które znajdują się w granicach zabezpieczeń, które nie są narażone na dostęp do Internetu, ale nadal wymagają dystrybucji obciążenia okrężnego, lepkości sesji lub zabezpieczeń warstwy transportu (TLS), wcześniej znanego jako Secure Sockets Layer (SSL), zakończenie.

W tym artykule poprowadzą Cię przez kroki konfigurowania bramy aplikacji z prywatnym adresem IP wewnętrznej bazy przy użyciu portalu Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Można utworzyć nową sieć wirtualną lub użyć istniejącej. W tym przykładzie utworzysz nową sieć wirtualną. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia bramy aplikacji są tworzone w oddzielnych podsieciach. W tym przykładzie tworzysz dwie podsieci: jedną dla bramy aplikacji i drugą dla serwerów zaplecza.

1. Rozwiń menu portalu i wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy bramy aplikacji i *myResourceGroupAG* dla nowej grupy zasobów.
4. W obszarze **Region**wybierz **(USA) stany Zjednoczone Centralne**.
5. W przypadku **warstwy**wybierz pozycję **Standardowa**.
6. W obszarze **Konfigurowanie sieci wirtualnej** wybierz **pozycję Utwórz nowe**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.
   - *myBackendSubnet* — dla nazwy podsieci wewnętrznej bazy danych.
   - *10.0.1.0/24* - dla przestrzeni adresowej podsieci wewnętrznej.

    ![Tworzenie sieci wirtualnej](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Wybierz **przycisk OK,** aby utworzyć sieć wirtualną i podsieć.
7. Wybierz **dalej:Frontendy**.
8. W przypadku **typu adresu IP frontu**wybierz pozycję **Prywatne**.

   Domyślnie jest to dynamiczne przypisanie adresu IP. Pierwszy dostępny adres skonfigurowanej podsieci jest przypisywany jako adres IP wewnętrznej bazy.
   > [!NOTE]
   > Po przydzieleniu nie można później zmienić typu adresu IP (statycznego lub dynamicznego).
9. Wybierz **dalej:Zaplecze**.
10. Wybierz **pozycję Dodaj pulę zaplecza**.
11. Dla **name**, wpisz *appGatewayBackendPool*.
12. W obszarze **Dodaj pulę zaplecza bez obiektów docelowych**wybierz pozycję **Tak**. Obiekty docelowe dodasz później.
13. Wybierz pozycję **Dodaj**.
14. Wybierz **dalej:Konfiguracja**.
15. W obszarze **Reguły routingu**wybierz pozycję **Dodaj regułę**.
16. W **przypadku nazwy reguły**wpisz *Rrule-01*.
17. Dla **nazwy odbiornika**wpisz *Odbiornik-01*.
18. W przypadku **adresu IP frontu**wybierz pozycję **Prywatne**.
19. Zaakceptuj pozostałe wartości domyślne i wybierz kartę **Cele wewnętrznej bazy danych.**
20. W obszarze **Typ obiektu docelowego**wybierz pozycję **Pula wewnętrznej bazy**danych , a następnie wybierz pozycję **appGatewayBackendPool**.
21. W przypadku **ustawienia HTTP**wybierz pozycję **Utwórz nowy**.
22. W przypadku **nazwy ustawienia HTTP**wpisz *http-setting-01*.
23. W przypadku **protokołu wewnętrznej bazy**danych wybierz http . **HTTP**
24. W przypadku **portu wewnętrznej**, typ *80*.
25. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dodaj**.
26. Na stronie **Dodawanie reguły routingu** wybierz pozycję **Dodaj**.
27. Wybierz **dalej: Tagi**.
28. Wybierz **dalej: Przejrzyj + utwórz**.
29. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz,** aby utworzyć zasoby sieciowe i bramę aplikacji. Tworzenie bramy aplikacji może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-pool"></a>Dodaj pulę zaplecza

Pula wewnętrznej bazy danych jest używana do kierowania żądań do serwerów wewnętrznej bazy danych, które obsługują żądanie. Zaplecze może składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i zaplecza wielu dzierżawców, takich jak usługa Azure App Service. W tym przykładzie maszyn wirtualnych jako zaplecza docelowego. Można użyć istniejących maszyn wirtualnych lub utworzyć nowe. W tym przykładzie utworzysz dwie maszyny wirtualne, których platforma Azure użyje jako serwerów zaplecza dla bramy aplikacji.

Aby to zrobić, możesz:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.
3. Dodaj serwery wewnętrznej bazy danych do puli wewnętrznej bazy danych.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób**.
2. Wybierz **pozycję Oblicz,** a następnie wybierz pozycję **Maszyna wirtualna**.
4. Wprowadź poniższe wartości dla maszyny wirtualnej:
   - wybierz *myResourceGroupAG* dla **grupy zasobów**.
   - *myVM* - dla **nazwy maszyny wirtualnej**.
   - Wybierz **pozycję Centrum danych systemu Windows Server 2019** dla **obrazu**.
   - prawidłową **nazwę użytkownika**.
   - prawidłowe **hasło**.
5. Zaakceptuj pozostałe wartości domyślne i wybierz **pozycję Dalej : Dyski**.
6. Zaakceptuj ustawienia domyślne i wybierz **pozycję Dalej : Sieć**.
7. Upewnij się, że wybrano sieć wirtualną **myVNet** i podsieć **myBackendSubnet**.
8. Zaakceptuj pozostałe wartości domyślne i wybierz **pozycję Dalej : Zarządzanie**.
9. Wybierz **opcję Wyłącz,** aby wyłączyć diagnostykę rozruchu.
10. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej : Zaawansowane**.
11. Wybierz **dalej : Znaczniki**.
12. **Wybierz dalej : Recenzja + utwórz**.
13. Przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**. Utworzenie maszyny Wirtualnej może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz powłokę Cloud Shell i upewnij się, że jest ustawiona na **program PowerShell**.
    ![prywatny-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki, które właśnie ukończono. Wprowadź myVM2 dla jego nazwy i VMName w Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli wewnętrznej bazy danych

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.
2. Wybierz **pulę wewnętrznej bazy danych**. Wybierz pozycję **appGatewayBackendPool**.
3. W obszarze **Typ docelowy** wybierz **maszynę wirtualną** i w obszarze **Cel**wybierz vNIC skojarzone z myVM.
4. Powtórz tę czynność, aby dodać MyVM2.
   ![prywatny-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. wybierz **pozycję Zapisz.**

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Sprawdź adres IP frontendu, który został przypisany, klikając stronę **Konfiguracje IP frontu** w portalu.
    ![prywatny-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Skopiuj prywatny adres IP, a następnie wklej go do paska adresu przeglądarki na maszynie Wirtualnej w tej samej sieci wirtualnej lub lokalnie, która ma łączność z tą siecią wirtualną, i spróbuj uzyskać dostęp do bramy aplikacji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz monitorować kondycję wewnętrznej bazy danych, zobacz [Kondycja zaplecza i dzienniki diagnostyczne dla bramy aplikacji](application-gateway-diagnostics.md).
