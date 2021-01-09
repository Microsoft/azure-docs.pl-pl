---
title: 'Samouczek: filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure firewall DNAT przy użyciu portalu'
description: W ramach tego samouczka dowiesz się, jak wdrożyć i skonfigurować funkcję DNAT usługi Azure Firewall przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 281d0587ca4c041c7149e49aad6227f6dc0b7fbf
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050871"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Samouczek: filtrowanie przychodzącego ruchu internetowego za pomocą usługi Azure firewall DNAT przy użyciu Azure Portal

Możesz skonfigurować funkcję translacji docelowych adresów sieciowych (DNAT) usługi Azure Firewall do wykonywania translacji i filtrowania ruchu internetowego przychodzącego do podsieci. Po skonfigurowaniu DNAT akcja kolekcje reguł translatora adresów sieciowych jest ustawiona na **DNAT**. Każda reguła w kolekcji reguł NAT umożliwia wykonanie translacji publicznego adresu IP i portu zapory na prywatny adres IP i port. Reguły DNAT niejawnie dodają odpowiednią regułę sieci zezwalającą na przetłumaczony ruch. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Aby dowiedzieć się więcej na temat logiki przetwarzania reguł usługi Azure Firewall, zobacz [Azure Firewall rule processing logic (Logika przetwarzania reguł usługi Azure Firewall)](rule-processing.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

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
3. W polu **Nazwa grupy zasobów** wpisz **RG-DNAT-Test**.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W polu **Lokalizacja grupy zasobów** wybierz lokalizację. Wszystkie kolejne zasoby, które utworzysz, muszą znajdować się w tej samej lokalizacji.
6. Wybierz pozycję **Utwórz**.

## <a name="set-up-the-network-environment"></a>Konfigurowanie środowiska sieciowego

W tym samouczku utworzysz dwie sieci wirtualne połączone przy użyciu komunikacji równorzędnej:

- **VN-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VN-Spoke** — w tej sieci wirtualnej znajduje się serwer obciążeń.

Najpierw utwórz sieci wirtualne, a następnie połącz je przy użyciu komunikacji równorzędnej.

### <a name="create-the-hub-vnet"></a>Tworzenie koncentratora sieci wirtualnej

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **VN-Hub**.
5. W polu **Przestrzeń adresowa** wpisz wartość **10.0.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**.

     Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
     > [!NOTE]
     > Rozmiar podsieci AzureFirewallSubnet to/26. Aby uzyskać więcej informacji o rozmiarze podsieci, zobacz [często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. W obszarze **zakres adresów** wpisz **10.0.1.0/26**.
11. Użyj innych ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-spoke-vnet"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **sieci wirtualne**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz wartość **VN-Spoke**.
5. W polu **Przestrzeń adresowa** wpisz wartość **192.168.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieć** w polu **Nazwa** wpisz **SN-Workload**.

    Serwer będzie znajdował się w tej podsieci.
10. W polu **Zakres adresów** wpisz wartość **192.168.1.0/24**.
11. Użyj innych ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="peer-the-vnets"></a>Łączenie sieci wirtualnych przy użyciu komunikacji równorzędnej

Teraz połącz sieci wirtualne przy użyciu komunikacji równorzędnej.

1. Wybierz sieć wirtualną dla **centrów VN** .
2. W obszarze **Ustawienia** wybierz pozycję **Komunikacja równorzędna**.
3. Wybierz pozycję **Dodaj**.
4. Wpisz **peer-HubSpoke** dla **nazwy komunikacji równorzędnej z VN-Hub do VN-szprych**.
5. Jako sieć wirtualną wybierz **VN-Spoke**.
6. Wpisz **peer-SpokeHub** dla **nazwy komunikacji równorzędnej z VN-Spoke do centrum VN**.
7. Dla opcji **Zezwalaj na ruch przesłany z VN-Spoke do centrum z ODczynem VN** wybierz opcję **włączone**.
8. Wybierz pozycję **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną obciążenia i umieść ją w podsieci **SN-Workload**.

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **popularne** wybierz pozycję **Windows Server 2016 Datacenter**.

**Podstawy**

1. W polu **Subskrypcja** wybierz subskrypcję.
1. W polu **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
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

1. W obszarze **Diagnostyka rozruchu** wybierz pozycję **wyłączone**.
1. Wybierz pozycję **Przejrzyj i utwórz**.

**Przegląd + tworzenie**

Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz**. Ukończenie tej operacji potrwa kilka minut.

Po zakończeniu wdrożenia zanotuj prywatny adres IP maszyny wirtualnej. Posłuży on później do skonfigurowania zapory. Wybierz nazwę maszyny wirtualnej, a w obszarze **Ustawienia** wybierz pozycję **Sieć** , aby znaleźć prywatny adres IP.

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. Na stronie głównej portalu wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a **następnie wybierz** pozycję **Zobacz wszystko**.
3. Wybierz opcję **Zapora**, a następnie wybierz pozycję **Utwórz**. 
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Nazwa     |FW-DNAT-test|
   |Subskrypcja     |\<your subscription\>|
   |Grupa zasobów     |**Użyj istniejącej**: RG-DNAT-Test |
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Użyj istniejącej**: VN-Hub|
   |Publiczny adres IP     |**Utwórz nowy**. Publiczny adres IP musi mieć typ Standardowa jednostka SKU.|

5. Wybierz pozycję **Przejrzyj i utwórz**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz** , aby utworzyć zaporę.

   Wdrożenie potrwa klika minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **RG-DNAT-test** , a następnie wybierz zaporę **PD-DNAT-test** .
8. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-a-default-route"></a>Tworzenie trasy domyślnej

Na potrzeby podsieci **SN-Workload** skonfiguruj trasę domyślną ruchu wychodzącego, aby przechodziła przez zaporę.

1. Na stronie głównej Azure Portal wybierz pozycję **wszystkie usługi**.
2. W obszarze **Sieć** wybierz pozycję **tabele tras**.
3. Wybierz pozycję **Dodaj**.
4. W polu **Nazwa** wpisz **RT-FWroute**.
5. W polu **Subskrypcja** wybierz subskrypcję.
6. W obszarze **Grupa zasobów** wybierz pozycję **Użyj istniejącej**, a następnie wybierz pozycję **RG-DNAT-Test**.
7. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
8. Wybierz pozycję **Utwórz**.
9. Wybierz pozycję **Odśwież**, a następnie wybierz tabelę tras **RT-FWroute** .
10. Wybierz pozycję **podsieci**, a następnie wybierz pozycję **Skojarz**.
11. Wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **VN-szprycha**.
12. W polu **Podsieć** wybierz pozycję **SN-Workload**.
13. Wybierz pozycję **OK**.
14. Wybierz pozycję **trasy**, a następnie wybierz pozycję **Dodaj**.
15. W polu **Nazwa trasy** wpisz wartość **FW-DG**.
16. W polu **Prefiks adresu** wpisz wartość **0.0.0.0/0**.
17. W obszarze **Typ następnego skoku** wybierz pozycję **Urządzenie wirtualne**.

    Usługa Azure Firewall to w rzeczywistości usługa zarządzana, ale urządzenie wirtualne działa w tej sytuacji.
18. W polu **Adres następnego skoku** wpisz wcześniej zanotowany prywatny adres IP zapory.
19. Wybierz pozycję **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurowanie reguł translatora adresów sieciowych

1. Otwórz **RG-DNAT-test**, a następnie wybierz zaporę **PD-DNAT-test** . 
2. Na stronie **PD-DNAT-test** w obszarze **Ustawienia** wybierz pozycję **reguły**. 
3. Wybierz pozycję **Dodaj kolekcję reguł NAT**. 
4. W polu **Nazwa** wpisz **RC-DNAT-01**. 
5. W polu **Priorytet** wpisz wartość **200**. 
6. W obszarze **Reguły** w polu **Nazwa** wpisz **RL-01**.
7. W polu **Protokół** wybierz **TCP**.
8. W polu **Adresy źródłowe** wpisz znak *. 
9. W polu **Adresy docelowe** wpisz publiczny adres IP zapory. 
10. W polu **Porty docelowe** wpisz **3389**. 
11. W polu **Przekształcony adres** wpisz prywatny adres IP maszyny wirtualnej Srv-Workload. 
12. W polu **Przekształcony port** wpisz **3389**. 
13. Wybierz pozycję **Dodaj**. 

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