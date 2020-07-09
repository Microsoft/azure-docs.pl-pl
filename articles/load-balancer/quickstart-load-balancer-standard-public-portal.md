---
title: 'Szybki Start: Tworzenie Load Balancer publicznego — Azure Portal'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia Load Balancer przy użyciu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 99a2de4cd8a19d3f05b9dc37f3bcd08cd84b2e68
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052755"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Szybki Start: Tworzenie Load Balancer równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Za pomocą witryny Azure Portal można utworzyć moduł równoważenia obciążenia na potrzeby równoważenia obciążenia maszyn wirtualnych. W tym przewodniku szybki start przedstawiono sposób równoważenia obciążenia maszyn wirtualnych przy użyciu publicznej Load Balancer.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz Load Balancer, która pomaga zrównoważyć obciążenie maszyn wirtualnych. Można utworzyć publiczną Load Balancer lub Load Balancer wewnętrzny. Podczas tworzenia publicznej Load Balancer należy również utworzyć nowy publiczny adres IP, który jest konfigurowany jako fronton (domyślnie przyznana jako *LoadBalancerFrontend* Load Balancer).

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nowy** i wpisz *myResourceGroupSLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Publiczny**.                                        |
    | SKU           | Wybierz opcję **standardowa** lub **podstawowa**. Firma Microsoft zaleca standardy dotyczące obciążeń produkcyjnych. |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz pozycję **Użyj istniejącej** |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   Użyj ```-SKU Basic``` , aby utworzyć podstawowy publiczny adres IP. Podstawowe publiczne adresy IP nie są zgodne z usługą równoważenia obciążenia w **warstwie Standardowa** . Firma Microsoft zaleca używanie **standardu** dla obciążeń produkcyjnych.|
    | Strefa dostępności | Wpisz *strefowo nadmiarowe* , aby utworzyć odporną Load Balancer. Aby utworzyć strefę Load Balancer, wybierz określoną strefę z 1, 2 lub 3 |

> [!IMPORTANT]
> W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.


3. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

    ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów Load Balancer

W tej sekcji skonfigurujesz Load Balancer ustawienia dla puli adresów zaplecza, sondy kondycji i określ regułę modułu równoważenia obciążenia.

### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Aby dystrybuować ruch do maszyn wirtualnych, Pula adresów zaplecza zawiera adresy IP wirtualnych (kart sieciowych) podłączonych do Load Balancer. Utwórz pulę adresów zaplecza *myBackendPool* , aby uwzględnić maszyny wirtualne na potrzeby ruchu internetowego związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę *myBackendPool*, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby umożliwić Load Balancer monitorowania stanu aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne z obrotu Load Balancer na podstawie ich odpowiedzi na testy kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHealthProbe*. |
    | Protokół | Wybierz pozycję **http**. |
    | Port | Wprowadź *80*.|
    | Interval | Wprowadź *15* dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz **2** dla liczby **progów złej kondycji** lub kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.|
    | | |
4. Wybierz przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę Load Balancer *myLoadBalancerRuleWeb* do nasłuchiwania na porcie 80 w frontonie *myfrontendpool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80. 

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHTTPRule*. |
    | Protokół | wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Wybierz pozycję *myBackendPool*.|
    | Sonda kondycji | Wybierz pozycję *myHealthProbe*. |
4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.


## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, utworzono trzy maszyny wirtualne dla puli zaplecza Load Balancer, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie Load Balancer.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa Zachodnia      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych
Jednostki SKU publicznego adresu IP i jednostki SKU Load Balancer muszą być zgodne. Aby uzyskać usługa Load Balancer w warstwie Standardowa, użyj maszyn wirtualnych ze standardowymi adresami IP w puli zaplecza. W tej sekcji utworzysz trzy maszyny wirtualne (*myVM1*, *myVM2* i *myVM3*) ze standardowym publicznym adresem IP w trzech różnych strefach (*Strefa 1*, *strefa 2*i *Strefa 3*), które później zostaną dodane do puli zaplecza Load Balancer utworzonego wcześniej. W przypadku wybrania warstwy Podstawowa Użyj maszyn wirtualnych z podstawowymi adresami IP.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2019 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupSLB**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myVM1*.
   - **Szczegóły wystąpienia**  >  **Region** > wybierz pozycję **Europa Zachodnia**.
   - **Szczegóły wystąpienia**  >  **Opcje dostępności** > wybierz **strefy dostępności**. 
   - **Szczegóły wystąpienia**  >  **Strefa dostępności** > wybierz **1**.
   - **Konto administratora**> wprowadź **nazwę użytkownika**, **hasło** i **Potwierdź** informacje o haśle.
   - Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
1. Na karcie **Sieć** upewnij się, że wybrano następujące elementy:
   - **Sieć wirtualna**: *myVnet*
   - **Podsieć**: *myBackendSubnet*
   - > **publicznego adresu IP** wybierz pozycję **Utwórz nowy**, a następnie w oknie **Tworzenie publicznego adresu IP** w obszarze **jednostka SKU**wybierz pozycję **standardowa**, a dla **strefy dostępność**wybierz pozycję **strefa nadmiarowa**, a następnie wybierz przycisk **OK**. Jeśli utworzono podstawową Load Balancer, wybierz pozycję podstawowa. Firma Microsoft zaleca używanie standardowej jednostki SKU dla obciążeń produkcyjnych.
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Wpisz *myNetworkSecurityGroup*, a następnie wybierz **przycisk OK**.
   - Aby utworzyć maszynę wirtualną jako część puli zaplecza Load Balancer, wykonaj następujące czynności:
        - W obszarze **równoważenie obciążenia**, aby **umieścić tę maszynę wirtualną za istniejącym rozwiązaniem do równoważenia obciążenia?**, wybierz pozycję **tak**.
        - W obszarze **Ustawienia równoważenia obciążenia**dla **opcji równoważenia obciążenia**wybierz pozycję **Usługa równoważenia obciążenia Azure**.
        - W obszarze **Wybierz moduł równoważenia obciążenia** *myLoadBalancer*.
        - Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**.
2. Na karcie **Zarządzanie** w obszarze **monitorowanie**ustaw opcję **Diagnostyka rozruchu** na **wyłączona**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Wykonaj kroki od 2 do 6, aby utworzyć dwie dodatkowe maszyny wirtualne z następującymi wartościami, a wszystkie inne ustawienia takie same jak *myVM1*:

    | Ustawienie | MW 2| MASZYNA WIRTUALNA 3|
    | ------- | ----- |---|
    | Nazwa |  *myVM2* |*myVM3*|
    | Strefa dostępności | 2 |3|
    |Publiczny adres IP| **Standard** Magazyn|**Standard** Magazyn|
    | Strefa publicznej dostępności adresu IP| **Strefa nadmiarowa** |**Strefa nadmiarowa**|
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący *myNetworkSecurityGroup*| Wybierz istniejący *myNetworkSecurityGroup*|

 ### <a name="create-nsg-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz regułę sieciowej grupy zabezpieczeń, która zezwala na połączenia przychodzące przy użyciu protokołu HTTP.

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myNetworkSecurityGroup** , która znajduje się w grupie zasobów **myResourceGroupSLB** .
2. W obszarze **Ustawienia** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - **Źródło**: *tag usługi*
    -  **Tag usługi źródłowej**: *Internet*
    - **Docelowe zakresy portów**: *80*
    - **Protokół**: *TCP*
    - **Akcja**: *Zezwalaj*
    - **Priorytet**: *100*
    - **Nazwa**: *myHTTPRule* 
    - **Opis**: "*Zezwalaj na http* 
4. Wybierz pozycję **Dodaj**.
5. Powtórz kroki dla reguły ruchu przychodzącego RDP, w razie potrzeby, z następującymi różnymi wartościami:
   - **Zakresy portów docelowych**: wpisz *3389*.
   - **Priorytet**: wpisz *200*. 
   - **Nazwa**: wpisz *MyRDPRule*. 
   - **Opis**: wpisz *Zezwalaj na RDP*. 
 
### <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM1** , która znajduje się w grupie zasobów *myResourceGroupSLB* .
2. Na stronie **Przegląd** wybierz pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń podanych podczas jej tworzenia. Spowoduje to uruchomienie sesji pulpitu zdalnego z maszyną wirtualną — *myVM1*.
6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows** > **programu Windows PowerShell**.
7. W oknie programu PowerShell uruchom poniższe polecenia, aby zainstalować serwer usług IIS, usunąć domyślny plik iisstart.htm i dodać nowy plik iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zamknij sesję protokołu RDP na maszynie wirtualnej *myVM1*.
7. Powtórz kroki od 1 do 6, aby zainstalować usługi IIS i zaktualizowany plik iisstart.htm na maszynach *myVM2* i *myVM3*.

## <a name="test-the-load-balancer"></a>Przetestuj Load Balancer
1. Na ekranie **przeglądu** Znajdź publiczny adres IP dla Load Balancer. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby wyświetlić Load Balancer Dystrybuuj ruch między wszystkimi trzema maszynami wirtualnymi, można dostosować domyślną stronę każdego z serwerów sieci Web usług IIS na maszynie wirtualnej, a następnie wymusić odświeżenie przeglądarki sieci Web na komputerze klienckim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, Load Balancer i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu wybierz grupę zasobów (*myResourceGroupSLB*), która zawiera Load Balancer, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono usługa Load Balancer w warstwie Standardowa, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu Load Balancer, sondę kondycji, a następnie przetestowano Load Balancer. Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do [Azure Load Balancer samouczków](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Dowiedz się więcej na temat [stref Load Balancer i dostępności](load-balancer-standard-availability-zones.md).
