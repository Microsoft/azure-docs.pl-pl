---
title: 'Samouczek: Load Balancer maszyn wirtualnych w strefie — Azure Portal'
titleSuffix: Azure Load Balancer
description: Ten samouczek przedstawia sposób tworzenia wystąpienia usługi Load Balancer w warstwie Standardowa przy użyciu frontonu strefowego w celu zrównoważenia obciążenia maszyn wirtualnych w strefie dostępności przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 940636a5e368a84aaaf0d4490bf874d56d3ddb6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78251905"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Samouczek: równoważenie obciążenia maszyn wirtualnych w strefie dostępności przy użyciu usługi Load Balancer w strefie Standardowa w witrynie Azure Portal

W tym samouczku utworzysz [wystąpienie usługi Load Balancer w warstwie Standardowa na platformie Azure](https://aka.ms/azureloadbalancerstandard) z frontonem strefowym, korzystające z publicznego standardowego adresu IP, w witrynie Azure Portal. W tym scenariuszu wybierzesz określoną strefę dla wystąpień frontonu i zaplecza, aby dostosować zasoby i ścieżkę danych do określonej strefy. Dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie wystąpienia usługi Load Balancer w warstwie Standardowa z frontonem strefowym.
> * Tworzenie sieciowych grup zabezpieczeń w celu zdefiniowania reguł ruchu przychodzącego.
> * Tworzenie strefowych maszyn wirtualnych i dołączanie ich do modułu równoważenia obciążenia.
> * Tworzenie sondy kondycji modułu równoważenia obciążenia.
> * Tworzenie reguł ruchu modułu równoważenia obciążenia.
> * Tworzenie podstawowej witryny usług Internet Information Services (IIS).
> * Wyświetlanie działającego modułu równoważenia obciążenia.

Aby uzyskać więcej informacji o korzystaniu ze stref dostępności usługa Load Balancer w warstwie Standardowa, zobacz [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).

Jeśli wolisz, możesz również wykonać czynności przedstawione w tym samouczku przy użyciu [interfejsu wiersza polecenia platformy Azure](load-balancer-standard-public-zonal-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Tworzenie publicznego wystąpienia usługi Load Balancer w warstwie Standardowa

Usługa Load Balancer w warstwie Standardowa obsługuje tylko standardowy publiczny adres IP. W przypadku tworzenia nowego publicznego adresu IP podczas tworzenia modułu równoważenia obciążenia jest on automatycznie konfigurowany jako standardowa jednostka SKU. Jest on również automatycznie konfigurowany jako strefowo nadmiarowy.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nową** i wpisz *MyResourceGroupZLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz pozycję **Standardowy**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **1**.    |
3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.   

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną. Utworzysz też dwie maszyny wirtualne w tej samej strefie (strefie 1) w regionie, aby dodać je do puli zaplecza modułu równoważenia obciążenia. Następnie zainstalujesz usługi IIS na maszynach wirtualnych, aby ułatwić testowanie strefowo nadmiarowego modułu równoważenia obciążenia. Jeśli jedna z maszyn wirtualnych ulegnie awarii, sonda kondycji maszyny wirtualnej w tej samej strefie również ulegnie awarii. Ruch będzie nadal obsługiwany przez pozostałe maszyny wirtualne w tej samej strefie.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupZLB (wybierz istniejącą grupę zasobów) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.0.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wpisz **Sieciowa grupa zabezpieczeń**. Na stronie sieciowej grupy zabezpieczeń wybierz pozycję **Utwórz**.
2. Na stronie **Tworzenie sieciowej grupy zabezpieczeń** wprowadź następujące wartości:
   - **myNetworkSecurityGroup** — jako nazwę sieciowej grupy zabezpieczeń.
   - **myResourceGroupLBAZ** — jako nazwę istniejącej grupy zasobów.
   
     ![Tworzenie sieciowej grupy zabezpieczeń](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Tworzenie reguł sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące przy użyciu protokołów HTTP i Microsoft Remote Desktop Protocol (RDP) w witrynie Azure Portal.

1. W menu po lewej stronie w witrynie Azure Portal wybierz pozycję **Wszystkie zasoby**. Następnie wyszukaj i wybierz sieciową grupę zabezpieczeń **myNetworkSecurityGroup**. Znajduje się ona w grupie zasobów **myResourceGroupZLB**.
2. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**. Następnie wybierz pozycję **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie **myHTTPRule**, aby umożliwić obsługę przychodzących połączeń protokołu HTTP używających portu 80:
    - **Tag usługi**dla **źródła**.
    - **Internet** w polu **Tag usługi źródłowej**.
    - **80**dla **docelowych zakresów portów**.
    - **TCP** w polu **Protokół**.
    - **Zezwalaj**, dla **akcji**.
    - **100**dla **priorytetu**.
    - **myHTTPRule**, dla **nazwy**.
    - **Zezwalaj na użycie protokołu HTTP**w **opisie**.
4. Wybierz przycisk **OK**.
 
   ![Tworzenie reguł sieciowej grupy zabezpieczeń](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Powtórz kroki od 2 do 4, aby utworzyć kolejną regułę o nazwie **myRDPRule**. Ta reguła umożliwia obsługę przychodzących połączeń protokołu RDP używających portu 3389. Ustaw następujące wartości:
    - **Tag usługi**dla **źródła**.
    - **Internet** w polu **Tag usługi źródłowej**.
    - **3389**dla **docelowych zakresów portów**.
    - **TCP**, dla **protokołu**.
    - **Zezwalaj**, dla **akcji**.
    - **200**dla **priorytetu**.
    - **myRDPRule**, dla **nazwy**.
    - **Zezwalaj na używanie protokołu RDP**na potrzeby **opisu**.

      ![Tworzenie reguły protokołu RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **systemu Windows Server 2016 Datacenter**. Wprowadź poniższe wartości dla maszyny wirtualnej:
    - **myVM1**, jako nazwę maszyny wirtualnej.        
    - **azureuser** — jako nazwę użytkownika administratora.    
    - **myResourceGroupZLB** w polu **Grupa zasobów**. Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroupZLB**.
2. Wybierz przycisk **OK**.
3. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej. Wybierz pozycję **Wybierz**.
4. Wprowadź następujące wartości ustawień maszyny wirtualnej:
    - **Strefa 1** — jako strefę dostępności, w której umieszczono maszynę wirtualną.
    -  **myVNet**. Upewnij się, że jest wybrana ta sieć wirtualna.
    - **myVM1PIP** — jako standardowy publiczny adres IP tworzony przez użytkownika. Wybierz pozycję**Utwórz nowy**. Następnie jako typ nazwy wybierz **myVM1PIP**. W polu **Strefa** wybierz **1**. Jednostka SKU adresu IP ma domyślnie wartość Standardowa.
    - **myBackendSubnet**. Upewnij się, że jest wybrana ta podsieć.
    - **myNetworkSecurityGroup** — jako nazwę sieciowej grupy zabezpieczeń (zapory), która już istnieje.
5. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
6. Wybierz przycisk **OK**. Sprawdź ustawienia na stronie podsumowania. Następnie wybierz przycisk **Utwórz**.
7. Powtórz kroki od 1 do 6, aby utworzyć drugą maszynę wirtualną o nazwie **myVM2** w strefie 1. Wybierz **myVnet** jako sieć wirtualną. Wybierz **myVM2PIP** jako standardowy publiczny adres IP. Wybierz **myBackendSubnet** jako podsieć. Wybierz **myNetworkSecurityGroup** jako sieciową grupę zabezpieczeń.

    ![Tworzenie maszyn wirtualnych](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Następnie na liście zasobów wybierz maszynę wirtualną **myVM1**. Znajduje się ona w grupie zasobów **myResourceGroupZLB**.
2. Na stronie **Przegląd** wybierz pozycję **Połącz**, aby połączyć się z maszyną wirtualną przy użyciu protokołu RDP.
3. Zaloguj się na maszynie wirtualnej, podając nazwę użytkownika i hasło, które zostały określone podczas tworzenia tej maszyny wirtualnej. Aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej, może być konieczne wybranie pozycji **Więcej opcji**. Następnie wybierz pozycję **Użyj innego konta**. Wybierz przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.
4. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows**  >  **programu Windows PowerShell**.
6. W oknie programu **PowerShell** uruchom następujące polecenia, aby zainstalować serwer usług IIS. Te polecenia usuną również domyślny plik iisstart.htm i dodadzą nowy plik iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Zamknij sesję protokołu RDP na maszynie wirtualnej **myVM1**.
8. Powtórz kroki od 1 do 7, aby zainstalować usługi IIS na maszynie wirtualnej **myVM2**.

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji. Określisz również reguły równoważenia obciążenia i translatora adresów sieciowych.


### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów zaplecza **myBackendPool** obejmującą maszyny wirtualne **VM1** i **VM2**.

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**. Następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wykonaj następujące czynności:
    - W polu nazwy wprowadź**myBackEndPool** jako nazwę puli zaplecza.
    - W menu rozwijanym w obszarze **Sieć wirtualna** wybierz pozycję **myVNet**. 
    - W przypadku **maszyn wirtualnych** i **adresów IP**Dodaj **myVM1** i **myVM2** oraz odpowiednie publiczne adresy IP.
4. Wybierz pozycję **Dodaj**.
5. Upewnij się, że w ustawieniach puli zaplecza modułu równoważenia obciążenia są wyświetlane obie maszyny wirtualne — **myVM1** i **myVM2**.
 
    ![Tworzenie puli zaplecza](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**. Następnie wybierz pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
    - **myHealthProbe**, jako nazwę sondy kondycji.
    - Protokołu **http**dla typu protokołu.
    - **80**dla numeru portu.
    - **15** — w polu **Interwał** jako liczby sekund między próbami sondy.
    - **2** — w polu **Próg złej kondycji** jako liczby kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za będącą w złej kondycji.
4. Wybierz przycisk **OK**.

   ![Dodawanie sondy kondycji](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Utwórz regułę modułu równoważenia obciążenia o nazwie **myLoadBalancerRuleWeb** do nasłuchiwania na porcie 80 frontonu **FrontendLoadBalancer**. Ta reguła umożliwia też wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool**, również przy użyciu portu 80. 

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Reguły równoważenia obciążenia**. Następnie wybierz pozycję **Dodaj**.
3. Skonfiguruj regułę równoważenia obciążenia, używając następujących wartości:
    - **myHTTPRule** — jako nazwy reguły równoważenia obciążenia.
    - **TCP**dla typu protokołu.
    - **80**dla numeru portu.
    - **80**dla portu zaplecza.
    - **myBackendPool**, jako nazwę puli zaplecza.
    - **myHealthProbe**, jako nazwę sondy kondycji.
4. Wybierz przycisk **OK**.
    
    ![Dodawanie reguły równoważenia obciążenia](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz pozycję **Wszystkie zasoby**. Następnie wybierz adres **myPublicIP**. 

2. Skopiuj publiczny adres IP. Następnie wklej go na pasku adresu przeglądarki. W przeglądarce zostanie wyświetlona domyślna strona, która zawiera nazwę serwera internetowego.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Aby zobaczyć działanie modułu równoważenia obciążenia, wymuś zatrzymanie wyświetlonej maszyny wirtualnej. Po odświeżeniu okna przeglądarki zobaczysz wyświetloną nazwę drugiego serwera.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów zawierającą moduł równoważenia obciążenia. Następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
