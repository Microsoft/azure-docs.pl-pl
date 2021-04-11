---
title: 'Samouczek: Konfigurowanie przekazywania portów — Azure Portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku pokazano, jak skonfigurować przekierowanie portów przy użyciu usługi Azure Load Balancer w celu utworzenia połączeń z maszynami wirtualnymi w sieci wirtualnej platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 27851a44dbe3085610fadc8b9bdaf400b4d2876c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056066"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Samouczek: Konfigurowanie przekazywania portów w Azure Load Balancer przy użyciu portalu

Przekierowanie portów umożliwia łączenie się z maszynami wirtualnymi w usłudze Azure Virtual Network przy użyciu publicznego adresu IP i numeru portu usługi Azure Load Balancer. 

Ten samouczek umożliwia skonfigurowanie przekierowania portów w usłudze Azure Load Balancer. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie publicznego modułu równoważenia obciążenia w warstwie Standardowa na potrzeby równoważenia ruchu sieciowego na maszynach wirtualnych. 
> * Tworzenie sieci wirtualnej i maszyn wirtualnych za pomocą reguły sieciowej grupy zabezpieczeń. 
> * Dodawanie maszyn wirtualnych do puli adresów zaplecza modułu równoważenia obciążenia.
> * Tworzenie reguł ruchu i sondy kondycji modułu równoważenia obciążenia.
> * Tworzenie reguł przekierowania portów translatora adresów sieciowych dla ruchu przychodzącego w module równoważenia obciążenia.
> * Instalowanie i konfigurowanie usług IIS na maszynach wirtualnych w celu wyświetlenia równoważenia obciążenia i przekierowania portów w działaniu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

W przypadku wszystkich czynności opisanych w tym samouczku Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.

## <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Najpierw utwórz publiczny moduł równoważenia obciążenia w warstwie Standardowa, który może równoważyć obciążenie ruchu sieciowego maszyn wirtualnych. Moduł równoważenia obciążenia w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia modułu równoważenia obciążenia w warstwie Standardowa tworzysz także nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton modułu równoważenia obciążenia (domyślnie o nazwie **LoadBalancerFrontEnd**). 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób zasobów**  >    >  **Load Balancer**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nową** i wpisz *MyResourceGroupLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region (Region)         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz pozycję **Standardowy**.                          |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |
     
    >[!NOTE]
     >Upewnij się, że moduł równoważenia obciążenia i wszystkie zasoby dla niego są tworzone w lokalizacji, która obsługuje strefy dostępności. Aby uzyskać więcej informacji, zobacz [Regiony obsługujące strefy dostępności](../availability-zones/az-region.md). 

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.  
  
## <a name="create-and-configure-back-end-servers"></a>Tworzenie i konfigurowanie serwerów zaplecza

Utwórz sieć wirtualną z dwiema maszynami wirtualnymi i dodawaj maszyny wirtualne do puli zaplecza modułu równoważenia obciążenia. 

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (wybierz istniejącą grupę zasobów) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Tworzenie maszyn wirtualnych i dodawanie ich do puli zaplecza modułu równoważenia obciążenia

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2016 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja**  >  **Grupa zasobów**: Lista rozwijana i wybierz pozycję **MyResourceGroupLB**.
   - **Nazwa maszyny wirtualnej**: wpisz *MyVM1*.
   - **Region**: wybierz pozycję **Europa Zachodnia**. 
   - **Nazwa użytkownika**: wpisz *azureuser*.
   - **Hasło**: wpisz *Azure1234567*. 
     Wpisz ponownie hasło w polu **Potwierdź hasło**.
   
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   Upewnij się, że zostały wybrane następujące opcje:
   - **Sieć wirtualna**: **MyVNet**
   - **Podsieć**: **MyBackendSubnet**
   
1. W obszarze **Publiczny adres IP** wybierz pozycję **Utwórz nowy**, wybierz opcję **Standardowy** na stronie **Utwórz publiczny adres IP**, a następnie wybierz przycisk **OK**. 
   
1. W obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**, aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli typ zapory. 
   1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
   1. Wpisz *MyNetworkSecurityGroup* i wybierz przycisk **OK**. 
   
   >[!NOTE]
   >Zauważ, że sieciowa grupa zabezpieczeń domyślnie ma już regułę ruchu przychodzącego otwierającą port 3389, tj. port pulpitu zdalnego (RDP).
   
1. Dodaj maszynę wirtualną do tworzonej puli zaplecza modułu równoważenia obciążenia:
   
   1. W obszarze **równoważenie obciążenia**  >  **Umieść tę maszynę wirtualną za istniejącym rozwiązaniem do równoważenia obciążenia?** wybierz pozycję **tak**. 
   1. W obszarze **Opcje równoważenia obciążenia** rozwiń listę i wybierz pozycję **Azure Load Balancer**. 
   1. W obszarze **Wybierz moduł równoważenia obciążenia** rozwiń listę i wybierz pozycję **MyLoadBalancer**. 
   1. W obszarze **Wybierz pulę zaplecza** wybierz opcję **Utwórz nową**, a następnie wpisz *MyBackendPool* i wybierz pozycję **Utwórz**. 
   
   ![Tworzenie sieci wirtualnej](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
   
1. Wybierz pozycję **Przejrzyj i utwórz**.
   
1. Przejrzyj ustawienia. Jeśli weryfikacja zakończy się powodzeniem, wybierz pozycję **Utwórz**. 

1. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *MyVM2* ze wszystkimi innymi ustawieniami takimi samymi, jak w przypadku maszyny MyVM1. 
   
   W obszarze **Sieciowa grupa zabezpieczeń**, po wybraniu opcji **Zaawansowane**, z listy rozwijanej wybierz grupę **MyNetworkSecurityGroup**, która została już utworzona. 
   
   W obszarze **Wybierz pulę zaplecza** upewnij się, że wybrano pozycję **MyBackendPool**. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych

Utwórz regułę sieciowej grupy zabezpieczeń dla maszyn wirtualnych, aby zezwalać na przychodzące połączenia internetowe (HTTP).

>[!NOTE]
>Domyślnie sieciowa grupa zabezpieczeń ma już regułę, która otwiera port 3389 — port pulpitu zdalnego (RDP).

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz pozycję **MyNetworkSecurityGroup** w grupie zasobów **MyResourceGroupLB** .
   
1. W obszarze **Ustawienia** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.
   
1. W oknie dialogowym **Dodawanie reguły zabezpieczeń dla ruchu przychodzącego** wpisz lub wybierz następujące opcje:
   
   - **Źródło**: wybierz pozycję **Tag usługi**.  
   - **Tag usługi źródłowej**: wybierz pozycję **Internet**. 
   - **Zakresy portów docelowych**: wpisz *80*.
   - **Protokół**: wybierz pozycję **TCP**. 
   - **Akcja**: wybierz pozycję **Zezwalaj**.  
   - **Priorytet**: wpisz *100*. 
   - **Nazwa**: wpisz *MyHTTPRule*. 
   - **Opis**: wpisz *Zezwalaj na HTTP*. 
   
1. Wybierz pozycję **Dodaj**. 
   
   ![Tworzenie reguły sieciowej grupy zabezpieczeń](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji sprawdzisz pulę zaplecza modułu równoważenia obciążenia i skonfigurujesz sondę kondycji oraz reguły ruchu modułu równoważenia obciążenia.

### <a name="view-the-back-end-address-pool"></a>Wyświetlanie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne moduł równoważenia obciążenia używa puli adresów zaplecza, która zawiera adresy IP wirtualnych interfejsów sieciowych połączonych z modułem równoważenia obciążenia. 

Pulę zaplecza modułu równoważenia obciążenia utworzono i dodano do niej maszyny wirtualne podczas tworzenia maszyn wirtualnych. Można również tworzyć pule zaplecza i dodawać do nich lub usuwać z nich maszyny wirtualne na stronie **Pule zaplecza** modułu równoważenia obciążenia. 

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**.
   
1. Na stronie **Pule zaplecza** rozwiń węzeł **MyBackendPool** i upewnij się, że na liście znajdują się obydwie maszyny wirtualne: **VM1** i **VM2**.

1. wybierz pozycję **MyBackendPool**. 
   
   Na stronie **MyBackendPool** w obszarach **MASZYNA WIRTUALNA** i **ADRES IP** możesz usunąć lub dodać dostępne maszyny wirtualne do puli.

Nowe pule zaplecza możesz utworzyć, wybierając pozycję **Dodaj** na stronie **Pule zaplecza**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu maszyny wirtualnej. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. 

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie sondy kondycji** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz *MyHealthProbe*.
   - **Protokół**: rozwiń listę i wybierz pozycję **HTTP**. 
   - **Port**: wpisz *80*. 
   - **Ścieżka**: zaakceptuj */* dla domyślnego identyfikatora URI. Tę wartość można zastąpić dowolnym innym identyfikatorem URI. 
   - **Interwał**: wpisz *15*. Interwał to liczba sekund między próbami sondy.
   - **Próg złej kondycji**: wpisz *2*. Ta wartość to liczba kolejnych niepowodzeń sondy, które występują, zanim kondycja maszyny wirtualnej zostanie uznana za złą.
   
1. Wybierz przycisk **OK**.
   
   ![Dodawanie sondy](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do maszyn wirtualnych. Reguła definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego, pulę adresów IP zaplecza do odbierania ruchu oraz wymagany port źródłowy i docelowy. 

Reguła modułu równoważenia obciążenia o nazwie **MyLoadBalancerRule** nasłuchuje na porcie 80 frontonu **LoadBalancerFrontEnd**. Ta reguła wysyła ruch sieciowy do puli adresów zaplecza **MyBackEndPool**, również przy użyciu portu 80. 

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie reguły równoważenia obciążenia** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz *MyLoadBalancerRule*.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: wpisz *80*.
   - **Port zaplecza**: wpisz *80*.
   - **Pula zaplecza**: wybierz pozycję **MyBackendPool**.
   - **Sonda kondycji**: wybierz pozycję **MyHealthProbe**. 
   
1. Wybierz przycisk **OK**.
   
   ![Dodawanie reguły modułu równoważenia obciążenia](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Tworzenie reguły przekierowania portów translatora adresów sieciowych dla ruchu przychodzącego

Utwórz regułę translatora adresów sieciowych modułu równoważenia obciążenia dla ruchu przychodzącego, aby przekierowywać ruch z określonego portu adresu IP frontonu do określonego portu maszyny wirtualnej zaplecza.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** z listy zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Reguły translatora adresów sieciowych dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**. 
   
1. Na stronie **Dodawanie reguły translatora adresów sieciowych dla ruchu przychodzącego** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz *MyNATRuleVM1*.
   - **Port**: type *4221*.
   - **Docelowa maszyna wirtualna**: wybierz pozycję **MyVM1** z listy rozwijanej.
   - **Konfiguracja protokołu IP sieci**: wybierz pozycję **ipconfig1** z listy rozwijanej.
   - **Mapowanie portów**: wybierz pozycję **niestandardowy**.
   - **Port docelowy**: wpisz *3389*.
   
1. Wybierz przycisk **OK**.
   
1. Powtórz kroki, aby dodać regułę NAT dla ruchu przychodzącego o nazwie *MyNATRuleVM2*, używając **portu**: *4222* i **docelowej maszyny wirtualnej**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji zainstalujesz usługi Internet Information Services (IIS) na serwerach zaplecza i dostosujesz domyślną stronę internetową w celu wyświetlenia nazwy maszyny. Następnie użyjesz publicznego adresu IP modułu równoważenia obciążenia, aby przetestować moduł równoważenia obciążenia. 

Każda maszyna wirtualna zaplecza obsługuje inną wersję domyślnej strony internetową usług IIS. Dzięki temu można zobaczyć, jak moduł równoważenia obciążenia dystrybuuje żądania między dwiema maszynami wirtualnymi.

### <a name="connect-to-the-vms-with-rdp"></a>Łączenie z maszynami wirtualnymi za pomocą protokołu RDP

Nawiąż połączenie z każdą maszyną wirtualną przy użyciu pulpitu zdalnego (RDP). 

1. W portalu wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz poszczególne maszyny wirtualne w grupie zasobów **MyResourceGroupLB**.
   
1. Na stronie **Przegląd** wybierz pozycję **Połącz**, a następnie wybierz pozycję **Pobierz plik RDP**. 
   
1. Otwórz pobrany plik RDP i wybierz pozycję **Połącz**.
   
1. Na ekranie zabezpieczeń systemu Windows wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. 
   
   Wprowadź nazwę użytkownika *azureuser* i hasło *Azure1234567* i wybierz przycisk **OK**.
   
1. Na każdy monit dotyczący certyfikatu odpowiedz **Tak**. 
   
   Pulpit maszyny wirtualnej zostanie otwarty w nowym oknie. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalowanie usług IIS i zastępowanie domyślnej strony internetowej usług IIS 

Użyj programu PowerShell, aby zainstalować usługi IIS i zastąpić domyślną stronę internetową usługi IIS stroną, która wyświetla nazwę maszyny wirtualnej.

1. Na maszynach wirtualnych MyVM1 i MyVM2 uruchom program **Windows PowerShell** z poziomu menu **Start**. 

2. Uruchom następujące polecenia, aby zainstalować usługi IIS i zastąpić domyślną stronę internetową usług IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Zamknij połączenia RDP z maszynami wirtualnymi MyVM1 i MyVM2, wybierając pozycję **Rozłącz**. Nie zamykaj maszyn wirtualnych.

### <a name="test-load-balancing"></a>Testowanie równoważenia obciążenia

1. W portalu na stronie **Przegląd** dla modułu **MyLoadBalancer** skopiuj publiczny adres IP wyświetlany w obszarze **Publiczny adres IP**. Umieść kursor na tym adresie i wybierz ikonę **Kopiuj**, aby go skopiować. W tym przykładzie jest to **40.67.218.235**. 
   
1. Wklej lub wpisz publiczny adres IP modułu równoważenia obciążenia (*40.67.218.235*) na pasku adresu przeglądarki internetowej. 
   
   W przeglądarce zostanie wyświetlona dostosowana domyślna strona serwera internetowego usług IIS. Komunikat brzmi **Hello World from MyVM1** lub **Hello World from MyVM2**.
   
   ![Nowa domyślna strona usług IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Odśwież przeglądarkę, aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między maszynami wirtualnymi. Ponieważ moduł równoważenia obciążenia dystrybuuje żądania do poszczególnych maszyn wirtualnych zaplecza, czasami jest wyświetlana strona maszyny wirtualnej **MyVM1**, a czasami strona maszyny wirtualnej **MyVM2**.
   
   >[!NOTE]
   >Może być konieczne wyczyszczenie pamięci podręcznej przeglądarki lub otwarcie nowego okna przeglądarki między kolejnymi próbami.

## <a name="test-port-forwarding"></a>Testowanie przekierowania portów

Za pomocą przekierowania portów można użyć pulpitu zdalnego na maszynie wirtualnej zaplecza, używając adresu IP modułu równoważenia obciążenia i wartości portu frontonu zdefiniowanej w regule translatora adresów sieciowych. 

1. W portalu na stronie **Przegląd** dla modułu **MyLoadBalancer** skopiuj jego publiczny adres IP. Umieść kursor na tym adresie i wybierz ikonę **Kopiuj**, aby go skopiować. W tym przykładzie jest to **40.67.218.235**. 
   
1. Otwórz wiersz polecenia i użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z maszyną MyVM2, używając publicznego adresu IP modułu równoważenia obciążenia i portu frontonu zdefiniowanego w regule translatora adresów sieciowych maszyny wirtualnej. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Otwórz pobrany plik RDP i wybierz opcję **Połącz**.
   
1. Na ekranie zabezpieczeń systemu Windows wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. 
   
   Wprowadź nazwę użytkownika *azureuser* i hasło *Azure1234567* i wybierz przycisk **OK**.
   
1. Na każdy monit dotyczący certyfikatu odpowiedz **Tak**. 
   
   Pulpit maszyny MyVM2 zostanie otwarty w nowym oknie. 

Połączenie RDP powiodło się, ponieważ reguła translatora adresów sieciowych dla ruchu przychodzącego **MyNATRuleVM2** kieruje ruch z portu frontonu 4222 modułu równoważenia obciążenia do portu 3389 (portu RDP) maszyny MyVM2.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć moduł równoważenia obciążenia i wszystkie powiązane z nim zasoby, kiedy nie będą już potrzebne, otwórz grupę zasobów **MyResourceGroupLB** i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono publiczny moduł równoważenia obciążenia w warstwie Standardowa. Utworzono i skonfigurowano zasoby sieciowe, serwery zaplecza, sondę kondycji i reguły dla modułu równoważenia obciążenia. Zainstalowano usługi IIS na maszynach wirtualnych zaplecza i użyto publicznego adresu IP modułu równoważenia obciążenia w celu przetestowania modułu równoważenia obciążenia. Skonfigurowano i przetestowano przekierowanie portów z określonego portu w module równoważenia obciążenia do portu na maszynie wirtualnej zaplecza. 

Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do kolejnych samouczków dotyczących modułu równoważenia obciążenia.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
