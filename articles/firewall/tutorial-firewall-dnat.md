---
title: 'Samouczek: filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure firewall DNAT przy użyciu portalu'
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować funkcję DNAT usługi Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741909"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Samouczek: filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure firewall DNAT przy użyciu Azure Portal

Możesz skonfigurować funkcję translacji docelowych adresów sieciowych (DNAT) usługi Azure Firewall do wykonywania translacji i filtrowania ruchu internetowego przychodzącego do podsieci. Po skonfigurowaniu DNAT akcja kolekcje reguł translatora adresów sieciowych jest ustawiona na **DNAT**. Każdą regułę w kolekcji reguł translatora adresów sieciowych można następnie użyć do przetłumaczenia publicznego adresu IP i portu zapory na prywatny adres IP i port. Reguły DNAT niejawnie dodają odpowiednią regułę sieci zezwalającą na przetłumaczony ruch. Ze względów bezpieczeństwa zalecanym rozwiązaniem jest dodanie określonego źródła internetowego w celu umożliwienia DNAT dostępu do sieci i unikania używania symboli wieloznacznych. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Testowanie zapory

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na stronie głównej Azure Portal wybierz pozycję **grupy zasobów**, a następnie wybierz pozycję **Dodaj**.
4. W polu **Subskrypcja** wybierz subskrypcję.
1. W polu **Nazwa grupy zasobów** wpisz **RG-DNAT-Test**.
5. W **obszarze region** wybierz region. Wszystkie inne zasoby, które tworzysz, muszą znajdować się w tym samym regionie.
6. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

## <a name="set-up-the-network-environment"></a>Konfigurowanie środowiska sieciowego

W tym samouczku utworzysz dwie sieci wirtualne połączone przy użyciu komunikacji równorzędnej:

- **VN-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VN-Spoke** — w tej sieci wirtualnej znajduje się serwer obciążeń.

Najpierw utwórz sieci wirtualne, a następnie połącz je przy użyciu komunikacji równorzędnej.

### <a name="create-the-hub-vnet"></a>Tworzenie koncentratora sieci wirtualnej

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
7. W obszarze **Grupa zasobów** wybierz pozycję **RG-DNAT-test**.
1. W polu **Nazwa** wpisz wartość **VN-Hub**.
1. W polu **region** wybierz ten sam region, który był wcześniej używany.
1. Wybierz pozycję **Dalej: adresy IP**.
1. Dla **przestrzeni adresów IPv4** zaakceptuj domyślną wartość **10.0.0.0/16**.
1. W obszarze **Nazwa podsieci** wybierz pozycję domyślne.
1. Edytuj **nazwę podsieci** i wpisz **AzureFirewallSubnet**.

     Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
     > [!NOTE]
     > Rozmiar podsieci AzureFirewallSubnet to/26. Aby uzyskać więcej informacji o rozmiarze podsieci, zobacz [często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. W obszarze **zakres adresów podsieci** wpisz **10.0.1.0/26**.
11. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

### <a name="create-a-spoke-vnet"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
1. W obszarze **Grupa zasobów** wybierz pozycję **RG-DNAT-test**.
1. W polu **Nazwa** wpisz wartość **VN-Spoke**.
1. W polu **region** wybierz ten sam region, który był wcześniej używany.
1. Wybierz pozycję **Dalej: adresy IP**.
1. W polu **przestrzeń adresowa IPv4** Edytuj wartość domyślną i wpisz **192.168.0.0/16**.
1. Wybierz pozycję **Dodaj podsieć**.
1. Dla **nazwy podsieci** wpisz **SN-obciążenie**.
10. W obszarze **zakres adresów podsieci** wpisz **192.168.1.0/24**.
11. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

### <a name="peer-the-vnets"></a>Łączenie sieci wirtualnych przy użyciu komunikacji równorzędnej

Teraz połącz sieci wirtualne przy użyciu komunikacji równorzędnej.

1. Wybierz sieć wirtualną dla **centrów VN** .
2. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
3. Wybierz pozycję **Dodaj**.
4. W **tej sieci wirtualnej** dla **nazwy linku komunikacji równorzędnej** wpisz polecenie **peer-HubSpoke**.
5. W obszarze **zdalna Sieć wirtualna** w polu **Nazwa linku komunikacji równorzędnej** wpisz polecenie **peer-SpokeHub**. 
1. Jako sieć wirtualną wybierz **VN-Spoke**.
1. Zaakceptuj wszystkie inne wartości domyślne, a następnie wybierz pozycję **Dodaj**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną obciążenia i umieść ją w podsieci **SN-Workload**.

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **popularne** wybierz pozycję **Windows Server 2016 Datacenter**.

**Podstawy**

1. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **RG-DNAT-test**.
1. W obszarze **Nazwa maszyny wirtualnej** wpisz **SRV-obciążenie**.
1. W polu **region** wybierz tę samą lokalizację, która została wcześniej użyta.
1. Wpisz nazwę użytkownika i hasło.
1. Wybierz pozycję **Dalej: dyski**.

**Dyski**
1. Wybierz pozycję **Dalej: Sieć**.

**Sieć**

1. W obszarze **Sieć wirtualna** wybierz pozycję **VN-szprychy**.
2. W polu **Podsieć** wybierz pozycję **SN-Workload**.
3. W obszarze **publiczny adres IP** wybierz pozycję **Brak**.
4. W przypadku **publicznych portów przychodzących** wybierz opcję **Brak**. 
2. Pozostaw pozostałe ustawienia domyślne i wybierz pozycję **Dalej: Zarządzanie**.

**Zarządzanie**

1. W obszarze **Diagnostyka rozruchu** wybierz pozycję **Wyłącz**.
1. Wybierz pozycję **Przejrzyj i utwórz**.

**Przegląd + tworzenie**

Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz**. Ukończenie tej operacji potrwa kilka minut.

Po zakończeniu wdrożenia zanotuj prywatny adres IP maszyny wirtualnej. Posłuży on później do skonfigurowania zapory. Wybierz nazwę maszyny wirtualnej, a w obszarze **Ustawienia** wybierz pozycję **Sieć** , aby znaleźć prywatny adres IP.

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. Na stronie głównej portalu wybierz pozycję **Utwórz zasób**.
1. Wyszukaj **zaporę**, a następnie wybierz opcję **Zapora**.
1. Wybierz przycisk **Utwórz**. 
1. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Subskrypcja     |\<your subscription\>|
   |Grupa zasobów     |Wybierz **RG-DNAT-test** |
   |Nazwa     |**FW-DNAT-test**|
   |Region (Region)     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Zarządzanie zaporą|**Użyj reguł zapory (klasycznych) do zarządzania tą zaporą**|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: VN-Hub|
   |Publiczny adres IP     |**Dodaj nową** nazwę: **PD-PIP**.|

5. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Przegląd + Utwórz**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz** , aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **RG-DNAT-test** , a następnie wybierz zaporę **PD-DNAT-test** .
8. Zanotuj prywatne i publiczne adresy IP zapory. Będziesz ich używać później podczas tworzenia trasy domyślnej i reguły NAT.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **SN-Workload** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **tabele tras**.
3. Wybierz pozycję **Dodaj**.
5. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz pozycję **RG-DNAT-test**.
1. W polu **region** wybierz ten sam region, który był wcześniej używany.
1. W polu **Nazwa** wpisz **RT-FWroute**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz pozycję **Przejdź do zasobu**.
1. Wybierz pozycję **podsieci**, a następnie wybierz pozycję **Skojarz**.
1. W obszarze **Sieć wirtualna** wybierz pozycję **VN-szprychy**.
1. W polu **Podsieć** wybierz pozycję **SN-Workload**.
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **trasy**, a następnie wybierz pozycję **Dodaj**.
1. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
1. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
1. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Wybierz przycisk **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurowanie reguł translatora adresów sieciowych

1. Otwórz grupę zasobów **RG-DNAT-test** i wybierz zaporę **PD-DNAT-test** . 
2. Na stronie **PD-DNAT-test** w obszarze **Ustawienia** wybierz pozycję **reguły (klasyczne)**. 
3. Wybierz pozycję **Dodaj kolekcję reguł NAT**. 
4. W polu **Nazwa** wpisz **RC-DNAT-01**. 
5. W polu **Priorytet** wpisz wartość **200**. 
6. W obszarze **Reguły** w polu **Nazwa** wpisz **RL-01**.
7. W polu **Protokół** wybierz **TCP**.
1. W obszarze **Typ źródła** wybierz pozycję **adres IP**.
1. Dla elementu **Source** wpisz *. 
1. W polu **adresy docelowe** wpisz publiczny adres IP zapory. 
1. W polu **Porty docelowe** wpisz **3389**. 
1. W polu **Przekształcony adres** wpisz prywatny adres IP maszyny wirtualnej Srv-Workload. 
1. W polu **Przekształcony port** wpisz **3389**. 
1. Wybierz pozycję **Dodaj**. Ukończenie tej operacji potrwa kilka minut.

## <a name="test-the-firewall"></a>Testowanie zapory

1. Połącz pulpit zdalny z publicznym adresem IP zapory. Powinno zostać nawiązane połączenie z maszyną wirtualną **Srv-Workload**.
2. Zamknij pulpit zdalny.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **RG-DNAT-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie testowego środowiska sieciowego
> * Wdrażanie zapory
> * Tworzenie trasy domyślnej
> * Konfigurowanie reguły DNAT
> * Testowanie zapory

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./firewall-diagnostics.md)
