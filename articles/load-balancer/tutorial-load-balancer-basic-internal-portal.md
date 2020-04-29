---
title: 'Samouczek: Tworzenie wewnętrznego modułu równoważenia obciążenia — Azure Portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku przedstawiono sposób tworzenia wewnętrznego podstawowego modułu równoważenia obciążenia w witrynie Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 6f62771d707d1aebccbfaf809dee7d0dedf5fefa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79096117"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Samouczek: równoważenie obciążenia ruchu wewnętrznego przy użyciu podstawowego modułu równoważenia obciążenia w witrynie Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między maszyny wirtualne. Za pomocą witryny Azure Portal można utworzyć podstawowy moduł równoważenia obciążenia i równoważyć obciążenie ruchu wewnętrznego wśród maszyn wirtualnych. W tym samouczku pokazano, jak utworzyć i skonfigurować wewnętrzny moduł równoważenia obciążenia, serwery zaplecza i zasoby sieciowe w warstwie cenowej Podstawowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

Jeśli wolisz, możesz wykonać te kroki przy użyciu [interfejsu wiersza polecenia platformy Azure](load-balancer-get-started-ilb-arm-cli.md) lub [programu Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md), zamiast korzystać z portalu.

Aby wykonać kroki opisane w tym samouczku, zaloguj się do Azure Portal pod [https://portal.azure.com](https://portal.azure.com)adresem.

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Tworzenie sieci wirtualnej, serwerów zaplecza i testowej maszyny wirtualnej

Najpierw utwórz sieć wirtualną. W sieci wirtualnej utwórz dwie maszyny wirtualne do użycia dla puli zaplecza podstawowego modułu równoważenia obciążenia oraz trzecią maszynę wirtualną na potrzeby testowania modułu równoważenia obciążenia. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Networking** > Sieć**sieci wirtualnej**.
   
1. W okienku **Tworzenie sieci wirtualnej** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz *MyVNet*.
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź nazwę *MyResourceGroupLB* i wybierz przycisk **OK**. 
   - **Subnet** > **Nazwa**podsieci: wpisz *MyBackendSubnet*.
   
1. Wybierz przycisk **Utwórz**.

   ![Tworzenie sieci wirtualnej](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **systemu Windows Server 2016 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subscription** > **Grupa zasobów**subskrypcji: Lista rozwijana i wybierz pozycję **MyResourceGroupLB**.
   - **Szczegóły** > wystąpienia**Nazwa maszyny wirtualnej**: wpisz *MyVM1*.
   - **Instance Details** > **Opcje dostępności**szczegółów wystąpienia: 
     1. rozwiń listę i wybierz pozycję **Zestaw dostępności**. 
     2. Wybierz pozycję **Utwórz nowy**, wpisz *MyAvailabilitySet* i wybierz przycisk **OK**.
   
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   Upewnij się, że zostały wybrane następujące opcje:
   - **Sieć wirtualna**: **MyVNet**
   - **Podsieć**: **MyBackendSubnet**
   
   W obszarze **Sieciowa grupa zabezpieczeń**:
   1. Wybierz pozycję **Zaawansowane**. 
   1. Rozwiń listę **Skonfiguruj sieciową grupę zabezpieczeń** i wybierz pozycję **Brak**. 
   
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
   
1. Wybierz pozycję **Przegląd + utwórz**.
   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 

1. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę wirtualną o nazwie *MyVM2* ze wszystkimi innymi ustawieniami takimi samymi, jak w przypadku maszyny MyVM1. 

1. Ponownie postępuj zgodnie z instrukcjami, aby utworzyć trzecią maszynę wirtualną o nazwie *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

Utwórz podstawowy wewnętrzny moduł równoważenia obciążenia przy użyciu portalu. Utworzona przez Ciebie nazwa i adres IP są automatycznie konfigurowane jako fronton modułu równoważenia obciążenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób zasobów** > **Networking** > **Load Balancer**.
   
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nową** i wpisz *MyResourceGroupLB* w polu tekstowym.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | Typ          | wybierz pozycję **Wewnętrzny**.                                        |
    | SKU           | Wybierz pozycję **podstawowa**.                          |
    | Sieć wirtualna           | Wybierz pozycję *MyVNet*.                          |    
    | Przypisanie adresu IP              | Wybierz wartość **Statyczny**.   |
    | Prywatny adres IP|wpisz adres, który znajduje się w przestrzeni adresowej sieci wirtualnej i podsieci, na przykład *10.3.0.7*.  |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**. 
   

## <a name="create-basic-load-balancer-resources"></a>Tworzenie zasobów podstawowego modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz reguły modułu równoważenia obciążenia.

### <a name="create-a-back-end-address-pool"></a>Tworzenie puli adresów zaplecza

Aby dystrybuować ruch do maszyn wirtualnych, moduł równoważenia obciążenia używa puli adresów zaplecza. Ta pula adresów zaplecza zawiera adresy IP wirtualnych interfejsów sieciowych (NIC, virtual network interface) połączonych z modułem równoważenia obciążenia. 

**Aby utworzyć pulę adresów zaplecza, która obejmuje maszyny VM1 i VM2:**

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie puli zaplecza** wpisz lub wybierz następujące wartości:
   
   - **Nazwa**: wpisz *MyBackendPool*.
   - **Skojarzone z**: Lista rozwijana i wybierz **maszynę wirtualną**.
   
   
1. Wybierz pozycję **maszyna wirtualna**. 
   1. Dodaj maszyny wirtualne **MyVM1** i **MyVM2** do puli zaplecza.
   2. Po dodaniu każdej maszyny otwórz listę rozwijaną i wybierz dla niej pozycję **Konfiguracja adresu IP sieci**. 
   
   >[!NOTE]
   >Nie dodawaj maszyny wirtualnej **MyTestVM** do puli. 
   
1. Wybierz przycisk **OK**.
   
   ![Dodawanie puli adresów zaplecza](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na stronie **Pule zaplecza** rozwiń węzeł **MyBackendPool** i upewnij się, że na liście znajdują się obydwie maszyny wirtualne: **VM1** i **VM2**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu maszyny wirtualnej. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. 

**Aby utworzyć sondę kondycji w celu monitorowania kondycji maszyn wirtualnych:**

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
   
   ![Dodawanie sondy](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do maszyn wirtualnych. Reguła definiuje konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego, pulę adresów IP zaplecza do odbierania ruchu oraz wymagany port źródłowy i docelowy. 

Reguła modułu równoważenia obciążenia o nazwie **MyLoadBalancerRule** nasłuchuje na porcie 80 frontonu **LoadBalancerFrontEnd**. Ta reguła wysyła ruch sieciowy do puli adresów zaplecza **MyBackEndPool**, również przy użyciu portu 80. 

**Aby utworzyć regułę modułu równoważenia obciążenia:**

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **MyLoadBalancer** na liście zasobów.
   
1. W obszarze **Ustawienia** wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
   
1. Na stronie **Dodawanie reguły równoważenia obciążenia** wpisz lub wybierz następujące wartości, jeśli jeszcze nie istnieją:
   
   - **Nazwa**: wpisz *MyLoadBalancerRule*.
   - **Adres IP frontonu:** wpisz *LoadBalancerFrontend*, jeśli ta wartość nie istnieje.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: wpisz *80*.
   - **Port zaplecza**: wpisz *80*.
   - **Pula zaplecza**: wybierz pozycję **MyBackendPool**.
   - **Sonda kondycji**: wybierz pozycję **MyHealthProbe**. 
   
1. Wybierz przycisk **OK**.
   
   ![Dodawanie reguły modułu równoważenia obciążenia](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

Zainstaluj usługi Internet Information Services (IIS) na serwerach zaplecza, a następnie użyj maszyny wirtualnej MyTestVM, aby przetestować moduł równoważenia obciążenia przy użyciu jego prywatnego adresu IP. Każda maszyna wirtualna zaplecza obsługuje inną wersję domyślnej strony internetową usług IIS. Dzięki temu można zobaczyć, jak moduł równoważenia obciążenia dystrybuuje żądania między dwiema maszynami wirtualnymi.

W portalu na stronie **Przegląd** dla modułu **MyLoadBalancer** znajdź adres IP w obszarze **Prywatny adres IP**. Umieść kursor na tym adresie i wybierz ikonę **Kopiuj**, aby go skopiować. W tym przykładzie jest to **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Łączenie z maszynami wirtualnymi za pomocą protokołu RDP

Najpierw należy połączyć się ze wszystkimi trzema maszynami wirtualnymi przy użyciu pulpitu zdalnego (RDP). 

>[!NOTE]
>Domyślnie maszyny wirtualne mają już otwarty port **RDP** (pulpit zdalny), aby zezwolić na dostęp do pulpitu zdalnego. 

**Aby połączyć pulpit zdalny (RDP) z maszynami wirtualnymi:**

1. W portalu wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz poszczególne maszyny wirtualne w grupie zasobów **MyResourceGroupLB**.
   
1. Na stronie **Przegląd** wybierz pozycję **Połącz**, a następnie wybierz pozycję **Pobierz plik RDP**. 
   
1. Otwórz pobrany plik RDP i wybierz pozycję **Połącz**.
   
1. Na ekranie zabezpieczeń systemu Windows wybierz pozycję **Więcej opcji**, a następnie pozycję **Użyj innego konta**. 
   
   Wprowadź nazwę użytkownika i hasło, a następnie wybierz przycisk **OK**.
   
1. Na każdy monit dotyczący certyfikatu odpowiedz **Tak**. 
   
   Pulpit maszyny wirtualnej zostanie otwarty w nowym oknie. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalowanie usług IIS i zastępowanie domyślnej strony usług IIS na maszynach wirtualnych zaplecza

Na każdym serwerze zaplecza należy użyć programu PowerShell do zainstalowania usług IIS i domyślnej strony internetowej usługi IIS dostosowaną stroną.

>[!NOTE]
>Można również użyć **Kreatora dodawania ról i funkcji** w **Menedżerze serwera** do zainstalowania usługi IIS. 

**Aby zainstalować usługi IIS i zaktualizować domyślną stronę internetową przy użyciu programu PowerShell:**

1. Na maszynach wirtualnych MyVM1 i MyVM2 uruchom program **Windows PowerShell** z poziomu menu **Start**. 

2. Uruchom następujące polecenia, aby zainstalować usługi IIS i zastąpić domyślną stronę internetową usług IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Zamknij połączenia RDP z maszynami wirtualnymi MyVM1 i MyVM2, wybierając pozycję **Rozłącz**. Nie zamykaj maszyn wirtualnych.

### <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Na maszynie wirtualnej MyTestVM otwórz program **Internet Explorer** i odpowiedz **OK** na wszystkie monity dotyczące konfiguracji. 
   
1. Wklej lub wpisz prywatny adres IP modułu równoważenia obciążenia (*10.3.0.7*) na pasku adresu przeglądarki. 
   
   W przeglądarce zostanie wyświetlona dostosowana domyślna strona serwera internetowego usług IIS. Komunikat brzmi **Hello World from MyVM1** lub **Hello World from MyVM2**.
   
1. Odśwież przeglądarkę, aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch między maszynami wirtualnymi. Może być również konieczne wyczyszczenie pamięci podręcznej przeglądarki między próbami.

   Ponieważ moduł równoważenia obciążenia dystrybuuje żądania do poszczególnych maszyn wirtualnych zaplecza, czasami jest wyświetlana strona maszyny wirtualnej **MyVM1**, a czasami strona maszyny wirtualnej **MyVM2**. 

   ![Nowa domyślna strona usług IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć moduł równoważenia obciążenia i wszystkie powiązane z nim zasoby, kiedy nie będą już potrzebne, otwórz grupę zasobów **MyResourceGroupLB** i wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono wewnętrzny moduł równoważenia obciążenia w warstwie Podstawowa. Utworzono i skonfigurowano zasoby sieciowe, serwery zaplecza, sondę kondycji i reguły dla modułu równoważenia obciążenia. Usługi IIS zostały zainstalowane na maszynach wirtualnych zaplecza i użyto testowej maszyny wirtualnej do testowania modułu równoważenia obciążenia w przeglądarce. 

Następnie dowiedz się, jak równoważyć obciążenia maszyn wirtualnych w różnych strefach dostępności.

> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
