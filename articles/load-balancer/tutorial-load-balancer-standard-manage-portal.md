---
title: 'Samouczek: Równoważenie obciążenia ruchu internetowego do maszyn wirtualnych — Azure Portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku pokazano, jak utworzyć wystąpienie usługi Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240362"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Samouczek: równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych przy użyciu witryny Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki usługi Load Balancer w warstwie Standardowa platformy Azure, które dystrybuują ruch internetowy do maszyn wirtualnych i zapewniają wysoką dostępność. Omawiane kwestie:


> [!div class="checklist"]
> * Tworzenie Azure Load Balancer
> * Tworzenie zasobów Load Balancer
> * Tworzenie maszyn wirtualnych i instalowanie serwera usług IIS
> * Wyświetl Load Balancer w akcji
> * Dodawanie i usuwanie maszyn wirtualnych z Load Balancer

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

W tej sekcji utworzysz usługa Load Balancer w warstwie Standardowa, która pomaga zrównoważyć obciążenie maszyn wirtualnych. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nowy** i wpisz *myResourceGroupSLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz pozycję **Standardowy**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.

   ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów Load Balancer

W tej sekcji skonfigurujesz Load Balancer ustawienia dla puli adresów zaplecza, sondy kondycji i określ regułę modułu równoważenia obciążenia.

### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Aby dystrybuować ruch do maszyn wirtualnych, Pula adresów zaplecza zawiera adresy IP wirtualnych (kart sieciowych) podłączonych do Load Balancer. Utwórz pulę adresów zaplecza *myBackendPool* , aby uwzględnić maszyny wirtualne na potrzeby ruchu internetowego związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer** .
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę *myBackendPool*, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby umożliwić Load Balancer monitorowania stanu aplikacji, należy użyć sondy kondycji. Sonda kondycji dynamicznie dodaje lub usuwa maszyny wirtualne z obrotu Load Balancer na podstawie ich odpowiedzi na testy kondycji. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer** .
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
     
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHealthProbe*. |
    | Protokół | Wybierz pozycję **http**. |
    | Port | Wprowadź *80*.|
    | Interwał | Wprowadź *15* dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz *2* dla liczby **progów złej kondycji** lub kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.|
    
4. Wybierz przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę Load Balancer *myLoadBalancerRuleWeb* do nasłuchiwania na porcie 80 w frontonie *myfrontendpool* i wysyłania ruchu sieciowego o zrównoważonym obciążeniu do puli adresów zaplecza *myBackEndPool* również przy użyciu portu 80.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer** .
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *myHTTPRule*. |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź *80*.|
    | Port zaplecza | Wprowadź *80*. |
    | Pula zaplecza | Wybierz pozycję *myBackendPool*.|
    | Sonda kondycji | Wybierz pozycję *myHealthProbe*. |
    
4. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, utworzono trzy maszyny wirtualne dla puli zaplecza Load Balancer, a następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie Load Balancer.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB (wybierz istniejącą grupę zasobów) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Usługa Load Balancer w warstwie Standardowa obsługuje tylko maszyny wirtualne ze standardowymi adresami IP w puli zaplecza. W tej sekcji utworzysz trzy maszyny wirtualne (*myVM1*, *myVM2*i *myVM3*) ze standardowym publicznym adresem IP w trzech różnych strefach (*Strefa 1*, *strefa 2*i *strefa 3*), które zostaną dodane do puli zaplecza usługa Load Balancer w warstwie Standardowa utworzonego wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2016 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subskrypcja**  >  **Grupa zasobów**: wybierz pozycję **myResourceGroupSLB**.
   - **Szczegóły wystąpienia**  >  **Nazwa maszyny wirtualnej**: wpisz *myVM1*.
   - **Szczegóły wystąpienia**  >  **Region** > wybierz pozycję **Europa Zachodnia**.
   - **Szczegóły wystąpienia**  >  **Opcje dostępności** > wybierz **strefy dostępności**. 
   - **Szczegóły wystąpienia**  >  **Strefa dostępności** > wybierz **1**.
  
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   - Upewnij się, że zostały wybrane następujące opcje:
       - **Sieć wirtualna**: **myVnet**
       - **Podsieć**: **myBackendSubnet**
       - **> publicznego adresu IP** wybierz pozycję **Utwórz nowy**, a następnie w oknie **Tworzenie publicznego adresu IP** w obszarze **jednostka SKU**wybierz pozycję **standardowa**, a w **obszarze strefa dostępności**wybierz pozycję **strefa nadmiarowa**
      
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Wpisz *myNetworkSecurityGroup*, a następnie wybierz **przycisk OK**.

   - Aby utworzyć maszynę wirtualną jako część puli zaplecza Load Balancer, wykonaj następujące czynności:
        - W obszarze **równoważenie obciążenia**, aby **umieścić tę maszynę wirtualną za istniejącym rozwiązaniem do równoważenia obciążenia?**, wybierz pozycję **tak**.
        - W obszarze **Ustawienia równoważenia obciążenia**dla **opcji równoważenia obciążenia**wybierz pozycję **Usługa równoważenia obciążenia Azure**.
        - W obszarze **Wybierz moduł równoważenia obciążenia** *myLoadBalancer*. 
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**. 
1. Wybierz pozycję **Przeglądanie + tworzenie**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Postępuj **zgodnie z** instrukcjami, aby utworzyć dwie dodatkowe maszyny wirtualne — *myVM2* i *MYVM3*, z publicznym adresem IP jednostki SKU odpowiednio **2** i **3** , a wszystkie inne ustawienia takie same jak *myVM1*.  

### <a name="create-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

W tej sekcji utworzysz regułę sieciowej grupy zabezpieczeń, która zezwala na połączenia przychodzące przy użyciu protokołu HTTP.

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myNetworkSecurityGroup** , która znajduje się w grupie zasobów **myResourceGroupSLB** .
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* dla nazwy
    - *Zezwalaj na HTTP* —jako opis
4. Wybierz pozycję **Dodaj**.

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myVM1** , która znajduje się w grupie zasobów *myResourceGroupSLB* .
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. W wyskakującym okienku **Podłączanie do maszyny wirtualnej** wybierz pozycję **Pobierz plik RDP**, a następnie otwórz pobrany plik RDP.
4. W oknie **Podłączanie pulpitu zdalnego** kliknij pozycję **Połącz**.
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
1. Na ekranie **przeglądu** Znajdź publiczny adres IP dla Load Balancer. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie kliknij pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć, Load Balancer Dystrybuuj ruch między trzema maszynami wirtualnymi, na których uruchomiono aplikację, możesz wymusić odświeżenie przeglądarki sieci Web.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Usuwanie lub dodawanie maszyn wirtualnych w puli zaplecza
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. W tej sekcji pokazano, jak usunąć lub dodać maszynę wirtualną (*myVM1*) z Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Usuwanie maszyny wirtualnej z puli zaplecza
Aby usunąć *myVM1* z puli zaplecza, wykonaj następujące czynności:

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów kliknij pozycję **myLoadBalancer** .
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie na liście pul zaplecza kliknij pozycję **myBackendPool**.
3. Na stronie **myBackendPool** , aby usunąć *VM1* wybierz ikonę Usuń na końcu wiersza zawierającego *myVM1*, a następnie kliknij przycisk **Zapisz**.

Po usunięciu maszyny *myVM1* z puli adresów zaplecza możesz wykonywać wszelkie zadania konserwacyjne na maszynie *myVM1*, takie jak instalowanie aktualizacji oprogramowania. W przypadku braku *VM1*obciążenie jest teraz zrównoważone dla *myVM2* i *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Dodawanie maszyny wirtualnej do puli zaplecza
Aby dodać *myVM1* z powrotem do puli zaplecza, wykonaj następujące czynności:

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myVM1** z listy zasoby.
2. Na stronie **VM1** w obszarze **Ustawienia**wybierz pozycję **Sieć**.
3. Na stronie **Sieć** wybierz kartę **równoważenie obciążenia** , a następnie wybierz pozycję **Dodaj Równoważenie obciążenia**.
4. Na stronie **Dodawanie równoważenia obciążenia** wykonaj następujące czynności:
   1. W obszarze **Opcje równoważenia obciążenia**wybierz pozycję **moduł równoważenia obciążenia platformy Azure**.
   2. W obszarze **Wybierz moduł równoważenia obciążenia**wybierz pozycję *myLoadBalancer*.
   3. W obszarze **wybierz pulę zaplecza**wybierz pozycję *myBackendPool*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów, Load Balancer i wszystkie powiązane zasoby. Aby to zrobić, wybierz grupę zasobów *myResouceGroupSLB* zawierającą Load Balancer a następnie wybierz pozycję  **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono usługa Load Balancer w warstwie Standardowa dołączone do niego maszyny wirtualne, skonfigurowano regułę ruchu Load Balancer, sondę kondycji, a następnie przetestowano Load Balancer. Ponadto usunięto maszynę wirtualną z zestawu o zrównoważonym obciążeniu i dodano ją z powrotem do puli adresów zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
