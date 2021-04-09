---
title: Konfigurowanie punktu końcowego wewnętrznego modułu równoważenia obciążenia (ILB)
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu konfigurowania Application Gateway przy użyciu prywatnego adresu IP frontonu
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711658"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurowanie bramy aplikacji za pomocą punktu końcowego wewnętrznego modułu równoważenia obciążenia (ILB)

Usługę Azure Application Gateway można skonfigurować za pomocą internetowego adresu IP lub wewnętrznego punktu końcowego, który nie jest narażony na Internet. Wewnętrzny punkt końcowy używa prywatnego adresu IP dla frontonu, który jest również znany jako *punkt końcowy wewnętrznego modułu równoważenia obciążenia (ILB)*.

Konfigurowanie bramy przy użyciu prywatnego adresu IP frontonu jest przydatne w przypadku wewnętrznych aplikacji biznesowych, które nie są dostępne w Internecie. Jest on również przydatny w przypadku usług i warstw w aplikacji wielowarstwowej, które znajdują się w granicach zabezpieczeń, które nie są dostępne w Internecie, ale:

- nadal wymaga dystrybucji obciążenia z działaniem okrężnym
- lepkość sesji
- lub Transport Layer Security (TLS) zakończenie (wcześniej znane jako Secure Sockets Layer (SSL)).

W tym artykule opisano kroki konfigurowania bramy aplikacji z prywatnym adresem IP frontonu przy użyciu Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej. Utwórz nową sieć wirtualną lub Użyj istniejącej. 

W tym przykładzie utworzysz nową sieć wirtualną. Sieć wirtualną można utworzyć podczas tworzenia bramy aplikacji. Wystąpienia Application Gateway są tworzone w różnych podsieciach. W tym przykładzie istnieją dwie podsieci: jeden dla bramy aplikacji i drugi dla serwerów zaplecza.

1. Rozwiń menu Portal i wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie z listy Polecane wybierz pozycję **Application Gateway**.
3. Wprowadź *myAppGateway* dla nazwy bramy aplikacji i *myResourceGroupAG* dla nowej grupy zasobów.
4. W **obszarze region** wybierz pozycję **środkowe stany USA**.
5. W obszarze **warstwa** wybierz pozycję **Standardowy**.
6. W obszarze **Konfigurowanie sieci wirtualnej** wybierz pozycję **Utwórz nową**, a następnie wprowadź następujące wartości dla sieci wirtualnej:
   - *myVNet* — jako nazwę sieci wirtualnej.
   - *10.0.0.0/16* — jako przestrzeń adresową sieci wirtualnej.
   - *myAGSubnet* — jako nazwę podsieci.
   - *10.0.0.0/24* — jako przestrzeń adresową podsieci.
   - *myBackendSubnet* — jako nazwę podsieci zaplecza.
   - *10.0.1.0/24* — dla przestrzeni adresowej podsieci zaplecza.

    ![Tworzenie sieci wirtualnej](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Wybierz **przycisk OK** , aby utworzyć sieć wirtualną i podsieci.
7. Wybierz pozycję **Dalej: frontony**.
8. W obszarze **Typ adresu IP frontonu** wybierz pozycję **prywatny**.

   Domyślnie jest to dynamiczne przypisanie adresu IP. Pierwszy dostępny adres skonfigurowanej podsieci jest przypisywany jako adres IP frontonu.
   > [!NOTE]
   > Po przydzieleniu nie można później zmienić typu adresu IP (statycznego lub dynamicznego).
9. Wybierz pozycję **Dalej: nadkończenie**.
10. Wybierz pozycję **Dodaj pulę zaplecza**.
11. W obszarze **Nazwa** wpisz *appGatewayBackendPool*.
12. W obszarze **Dodaj pulę zaplecza bez elementów docelowych** wybierz pozycję **tak**. Później dodasz cele.
13. Wybierz pozycję **Dodaj**.
14. Wybierz pozycję **Dalej: Konfiguracja**.
15. W obszarze **reguły routingu** wybierz pozycję **Dodaj regułę routingu**.
16. W obszarze **Nazwa reguły** wpisz *RRULE-01*.
17. W przypadku **nazwy odbiornika** wpisz *Listener-01*.
18. W obszarze **adres IP frontonu** wybierz pozycję **prywatny**.
19. Zaakceptuj pozostałe wartości domyślne i wybierz kartę **cele zaplecza** .
20. W obszarze **Typ docelowy** wybierz pozycję **Pula zaplecza**, a następnie wybierz pozycję **appGatewayBackendPool**.
21. W obszarze **Ustawienia protokołu HTTP** wybierz pozycję **Dodaj nowy**.
22. W obszarze **Nazwa ustawienia http** wpisz *http-Setting-01*.
23. W przypadku **protokołu zaplecza** wybierz pozycję **http**.
24. W przypadku **portu zaplecza** wpisz *80*.
25. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dodaj**.
26. Na stronie **Dodawanie reguły routingu** wybierz pozycję **Dodaj**.
27. Wybierz pozycję **Dalej: Tagi**.
28. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.
29. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz** , aby utworzyć zasoby sieciowe i bramę aplikacji. Tworzenie bramy aplikacji może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

## <a name="add-backend-pool"></a>Dodawanie puli zaplecza

Pula zaplecza służy do kierowania żądań do serwerów zaplecza, które obsługują żądanie. Zaplecze może składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zaplecza, takich jak Azure App Service. W tym przykładzie należy używać maszyn wirtualnych jako zaplecza docelowego. Możesz użyć istniejących maszyn wirtualnych lub utworzyć nowe. W tym przykładzie utworzysz dwie maszyny wirtualne, których platforma Azure użyje jako serwerów zaplecza dla bramy aplikacji.

W tym celu:

1. Utwórz dwie nowe maszyny wirtualne, *myVM* i *myVM2*, używane jako serwery zaplecza.
2. Zainstaluj usługi IIS na maszynach wirtualnych, aby sprawdzić, czy Brama aplikacji została utworzona pomyślnie.
3. Dodaj serwery zaplecza do puli zaplecza.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej


1. Wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **obliczenia** , a następnie pozycję **maszyna wirtualna**.
4. Wprowadź poniższe wartości dla maszyny wirtualnej:
   - Wybierz subskrypcję.
   - Wybierz pozycję *myResourceGroupAG* dla **grupy zasobów**.
   - Wpisz *myVM* dla **nazwy maszyny wirtualnej**.
   - Wybierz pozycję **Windows Server 2019 Datacenter** for **Image**.
   - Wpisz prawidłową **nazwę użytkownika**.
   - Wprowadź prawidłowe **hasło**.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: dyski**.
1. Zaakceptuj wartości domyślne i wybierz pozycję **Dalej: sieć**.
1. Upewnij się, że wybrano **myVNet** dla sieci wirtualnej, a podsieć jest **myBackendSubneta**.
1. Zaakceptuj pozostałe wartości domyślne i wybierz pozycję **Dalej: Zarządzanie**.
1. Wybierz pozycję **Wyłącz** , aby wyłączyć diagnostykę rozruchu.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**. Tworzenie maszyny wirtualnej może potrwać kilka minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji.

### <a name="install-iis"></a>Instalowanie usług IIS

1. Otwórz Cloud Shell i upewnij się, że jest ustawiony na program **PowerShell**.
    ![Zrzut ekranu przedstawia otwarte okno konsoli Azure Cloud Shell, w którym jest używane środowisko PowerShell.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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
         -Location CentralUS 

   ```

3. Utwórz drugą maszynę wirtualną i zainstaluj usługi IIS, wykonując kroki, które właśnie ukończono. Użyj myVM2 dla nazwy maszyny wirtualnej i programu na platformie `VMName` programu `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Dodawanie serwerów zaplecza do puli zaplecza

1. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myAppGateway**.
2. Wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **appGatewayBackendPool**.
3. W obszarze **Typ docelowy** wybierz pozycję **maszyna wirtualna**  i w obszarze **cel** wybierz pozycję wirtualnej karty sieciowej skojarzona z myVM.
4. Powtarzaj, aby dodać MyVM2.
   ![Edytowanie okienka puli zaplecza z wyróżnionymi typami docelowymi i obiektami docelowymi.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Wybierz pozycję **Zapisz.**

## <a name="create-a-client-virtual-machine"></a>Tworzenie maszyny wirtualnej klienta

Maszyna wirtualna klienta służy do nawiązywania połączenia z pulą zaplecza bramy aplikacji.

- Utwórz trzecią maszynę wirtualną przy użyciu poprzednich kroków. Użyj myVM3 dla nazwy maszyny wirtualnej.

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

1. Na stronie myAppGateway wybierz pozycję **konfiguracje adresów IP frontonu** , aby zanotować prywatny adres IP frontonu.
    ![Okienko konfiguracje adresów IP frontonu z wyróżnionym typem prywatnym.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Skopiuj prywatny adres IP, a następnie wklej go na pasku adresu przeglądarki w myVM3, aby uzyskać dostęp do puli zaplecza bramy aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby monitorować kondycję puli zaplecza, zapoznaj się z [dziennikami kondycji zaplecza i dzienników diagnostycznych dla Application Gateway](application-gateway-diagnostics.md).
