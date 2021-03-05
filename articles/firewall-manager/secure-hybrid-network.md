---
title: 'Samouczek: Zabezpieczanie sieci wirtualnej centrum przy użyciu Menedżera zapory platformy Azure'
description: W tym samouczku dowiesz się, jak zabezpieczyć sieć wirtualną za pomocą Menedżera zapory platformy Azure przy użyciu Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212550"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Samouczek: Zabezpieczanie sieci wirtualnej centrum przy użyciu Menedżera zapory platformy Azure

W przypadku łączenia sieci lokalnej z siecią wirtualną platformy Azure w celu utworzenia sieci hybrydowej ważną częścią ogólnego planu zabezpieczeń jest możliwość kontrolowania dostępu do zasobów sieciowych platformy Azure.

Korzystając z Menedżera zapory platformy Azure, można utworzyć sieć wirtualną centrum w celu zabezpieczenia hybrydowego ruchu sieciowego przeznaczonego dla prywatnych adresów IP, platformy Azure PaaS i Internetu. Za pomocą Menedżera zapory platformy Azure można kontrolować dostęp do sieci w sieci hybrydowej przy użyciu zasad, które definiują dozwolony i zabroniony ruch sieciowy.

Menedżer zapory obsługuje również bezpieczną architekturę koncentratora wirtualnego. Aby zapoznać się z porównaniem bezpiecznych typów architektury sieci wirtualnych i koncentratorów wirtualnych, zobacz [co to są opcje architektury usługi Azure firewall Manager?](vhubs-and-vnets.md)

W tym samouczku zostaną utworzone trzy sieci wirtualne:

- **VNet-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VNet-Spoke** — sieć wirtualna będąca szprychą reprezentuje pakiet roboczy na platformie Azure.
- **VNet-Onprem** — lokalna sieć wirtualna reprezentuje sieć lokalną. W rzeczywistym wdrożeniu może ono być połączone przez połączenie sieci VPN lub ExpressRoute. Dla ułatwienia w tym samouczku zostanie wykorzystane połączenie za pośrednictwem bramy VPN Gateway, a do reprezentowania sieci lokalnej zostanie wykorzystana sieć wirtualna zlokalizowana na platformie Azure.

![Sieć hybrydowa](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie zasad zapory
> * Tworzenie sieci wirtualnych
> * Konfigurowanie i wdrażanie zapory
> * Tworzenie i łączenie bram sieci VPN
> * Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy
> * Tworzenie tras
> * Tworzenie maszyn wirtualnych
> * Testowanie zapory


## <a name="prerequisites"></a>Wymagania wstępne

Sieć hybrydowa używa modelu architektury Hub i szprych do kierowania ruchu między usługą Azure sieci wirtualnych i sieciami lokalnymi. Architektura gwiazdy i szprych ma następujące wymagania:

- Ustaw **AllowGatewayTransit** , gdy Komunikacja równorzędna VNet-Hub się z siecią VNET-szprych. W architekturze sieci typu Hub i szprych, tranzyt bramy umożliwia sieciom wirtualnym współużytkowanie bramy sieci VPN w centrum, a nie wdrażanie bram sieci VPN w każdej sieci wirtualnej szprych. 

   Ponadto trasy do sieci wirtualnych podłączonych do bramy lub sieci lokalnych będą automatycznie propagowane do tabel routingu dla równorzędnych sieci wirtualnych przy użyciu tranzytu bramy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie tranzytu bramy sieci VPN dla komunikacji równorzędnej sieci wirtualnych](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ustaw **useremotegateways o wartości** podczas komunikacji równorzędnej z koncentratorem VNet-Spoke sieci wirtualnej. Jeśli ustawiono opcję **useremotegateways o wartości** i **AllowGatewayTransit** na zdalnej komunikacji równorzędnej, Sieć wirtualna szprych używa bram zdalnej sieci wirtualnej do tranzytu.
- Aby skierować ruch podsieci szprych przez zaporę centrum, potrzebna jest trasa zdefiniowana przez użytkownika (UDR), która wskazuje zaporę z wyłączonym ustawieniem **propagacji trasy bramy sieci wirtualnej** . Ta opcja uniemożliwia dystrybucję tras do podsieci szprych. Zapobiega to wyznaniom tras spowodowanych konfliktami z UDR.
- Skonfiguruj UDR w podsieci bramy centrum, która wskazuje adres IP zapory w następnym przeskoku do sieci szprych. W podsieci usługi Azure Firewall nie jest wymagana trasa zdefiniowana przez użytkownika, ponieważ uzyskuje ona informacje o trasach na podstawie protokołu BGP.

Zapoznaj się z sekcją [Tworzenie tras](#create-the-routes) w tym samouczku, aby zobaczyć, jak te trasy zostały utworzone.

>[!NOTE]
>Usługa Azure Firewall musi mieć bezpośrednie połączenie z Internetem. Jeśli AzureFirewallSubnet nauczy trasy domyślnej do sieci lokalnej za pośrednictwem protokołu BGP, należy przesłonić ten element przy użyciu wartości 0.0.0.0/0 UDR z wartością **NextHopType** ustawioną jako **Internet** w celu utrzymania bezpośredniej łączności z Internetem.
>
>Zaporę platformy Azure można skonfigurować do obsługi wymuszonego tunelowania. Aby uzyskać więcej informacji, zobacz [tunelowanie wymuszone przez zaporę platformy Azure](../firewall/forced-tunneling.md).

>[!NOTE]
>Ruch między wirtualnymi sieciami równorzędnymi połączonymi bezpośrednio jest kierowany bezpośrednio nawet wtedy, gdy trasa zdefiniowana przez użytkownika wskazuje usługę Azure Firewall jako bramę domyślną. Aby w tym scenariuszu wysyłać ruch między podsieciami do zapory, trasa zdefiniowana przez użytkownika musi jawnie zawierać prefiks podsieci docelowej w obu podsieciach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-firewall-policy"></a>Tworzenie zasad zapory

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku wyszukiwania Azure Portal wpisz **Menedżer zapory** i naciśnij klawisz **Enter**.
3. Na stronie Menedżer zapory platformy Azure wybierz pozycję **Wyświetl zasady zapory platformy Azure**.

   ![Zasady zapory](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Wybierz pozycję **Utwórz zasady zapory platformy Azure**.
1. Wybierz swoją subskrypcję, a w obszarze Grupa zasobów wybierz pozycję **Utwórz nową** i Utwórz grupę zasobów o nazwie **PD-hybrydowy-test**.
2. W polu Nazwa zasad wpisz **pol-Net01**.
3. W obszarze region wybierz pozycję **Wschodnie stany USA**.
1. Wybierz pozycję **Dalej: ustawienia DNS**.
1. Wybierz **Następny: Inspekcja TLS (wersja zapoznawcza)**
1. Wybierz pozycję **Dalej: reguły**.
1. Wybierz pozycję **Dodaj kolekcję reguł**.
1. W obszarze **Nazwa** wpisz **RCNet01**.
1. W obszarze **Typ kolekcji reguł** wybierz pozycję **Sieć**.
1. Dla **priorytetu** wpisz **100**.
1. W polu **Akcja** wybierz opcję **Zezwalaj**.
1. W obszarze **reguły**, w polu **Nazwa** wpisz **AllowWeb**.
1. W obszarze **Źródło** wpisz **192.168.1.0/24**.
1. W polu **Protokół** wybierz **TCP**.
1. W przypadku **portów docelowych** wpisz **80**.
1. W obszarze **Typ docelowy** wybierz pozycję **adres IP**.
1. Dla **lokalizacji docelowej** wpisz **10.6.0.0/16**.
1. W następnym wierszu reguły wprowadź następujące informacje:
 
    Name: wpisz **AllowRDP**<br>
    Źródło: wpisz **192.168.1.0/24**.<br>
    Protokół, wybierz **TCP**<br>
    Porty docelowe, typ **3389**<br>
    Typ docelowy, wybierz pozycję **adres IP**<br>
    Dla lokalizacji docelowej wpisz **10.6.0.0/16**

1. Wybierz pozycję **Dodaj**.
2. Wybierz pozycję **Przejrzyj i utwórz**.
3. Przejrzyj szczegóły, a następnie wybierz pozycję **Utwórz**.

## <a name="create-the-firewall-hub-virtual-network"></a>Tworzenie sieci wirtualnej koncentratora zapory

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to/26. Aby uzyskać więcej informacji o rozmiarze podsieci, zobacz [często zadawane pytania dotyczące zapory platformy Azure](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wyszukaj **sieć wirtualną** , a następnie wybierz pozycję **Sieć wirtualna**.
1. Wybierz przycisk **Utwórz**.
1. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz opcję **PD-hybrydowy-test**.
1. W obszarze **Nazwa wpisz nazwę** **koncentratora sieci wirtualnej**.
1. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. Wybierz pozycję **Dalej: adresy IP**.

1. W **polu przestrzeń adresowa IPv4** wpisz **10.5.0.0/16**.
1. W obszarze **Nazwa podsieci** wybierz pozycję **domyślne**.
1.  Zmień **nazwę podsieci** na **AzureFirewallSubnet**. Zapora jest w tej podsieci, a nazwa podsieci **musi** być AzureFirewallSubnet.
1. W obszarze **zakres adresów podsieci** wpisz **10.5.0.0/26**. 
1. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

## <a name="create-the-spoke-virtual-network"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wyszukaj **sieć wirtualną** , a następnie wybierz pozycję **Sieć wirtualna**.
1. Wybierz przycisk **Utwórz**.
1. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz opcję **PD-hybrydowy-test**.
1. W obszarze **Nazwa** wpisz **VNET-szprych**.
1. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. Wybierz pozycję **Dalej: adresy IP**.

1. W **polu przestrzeń adresowa IPv4** wpisz **10.6.0.0/16**.
1. W obszarze **Nazwa podsieci** wybierz pozycję **domyślne**.
1. Zmień **nazwę podsieci** na **SN-obciążenie**.
1. W obszarze **zakres adresów podsieci** wpisz **10.6.0.0/24**. 
1. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.


## <a name="create-the-on-premises-virtual-network"></a>Tworzenie lokalnej sieci wirtualnej

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. Wyszukaj **sieć wirtualną** , a następnie wybierz pozycję **Sieć wirtualna**.
1. Wybierz przycisk **Utwórz**.
1. W polu **Subskrypcja** wybierz subskrypcję.
1. W obszarze **Grupa zasobów** wybierz opcję **PD-hybrydowy-test**.
1. W obszarze **Nazwa** wpisz **VNET-lokalnego**.
1. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. Wybierz pozycję **Dalej: adresy IP**.

1. W **polu przestrzeń adresowa IPv4** wpisz **192.168.0.0/16**.
1. W obszarze **Nazwa podsieci** wybierz pozycję **domyślne**.
1. Zmień **nazwę podsieci** na **SN-Corp**.
1. W obszarze **zakres adresów podsieci** wpisz **192.168.1.0/24**. 
1. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Zapisz**.
2. Wybierz pozycję **Dodaj podsieć**.
3. W obszarze **Nazwa podsieci** wpisz **GatewaySubnet**.
4. Dla typu **zakresu adresów podsieci** **192.168.2.0/24**.
5. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.




## <a name="configure-and-deploy-the-firewall"></a>Konfigurowanie i wdrażanie zapory

Gdy zasady zabezpieczeń są skojarzone z centrum, jest ono określane jako *centralny sieci wirtualnej*.

Przekonwertuj sieć wirtualną **koncentratora** sieci wirtualnej na *centralną sieć wirtualną* i Zabezpiecz ją za pomocą zapory platformy Azure.

1. Na pasku wyszukiwania Azure Portal wpisz **Menedżer zapory** i naciśnij klawisz **Enter**.
3. Na stronie Menedżer zapory platformy Azure w obszarze **Dodawanie zabezpieczeń do sieci wirtualnych** wybierz opcję **Wyświetl sieci wirtualne centrów**.
1. W obszarze **sieci wirtualne** zaznacz pole wyboru dla **koncentratora sieć** wirtualna.
1. Wybierz pozycję **Zarządzaj zabezpieczeniami**, a następnie wybierz pozycję **Wdróż zaporę za pomocą zasad zapory**.
1. Na stronie **Konwertuj sieci wirtualne** w obszarze **zasady zapory** zaznacz pole wyboru **pol-Net01**.
1. Wybierz pozycję **Dalej: Przejrzyj i Potwierdź**
1. Przejrzyj szczegóły, a następnie wybierz pozycję **Potwierdź**.


   Wdrożenie może potrwać kilka minut.
7. Po zakończeniu wdrażania przejdź do grupy zasobów Zapora **PD-hybrydowy-test** , a następnie wybierz zaporę.
9. Zanotuj **prywatny adres IP zapory** na stronie **Przegląd** . Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-and-connect-the-vpn-gateways"></a>Tworzenie i łączenie bram sieci VPN

Sieć wirtualna koncentratora i lokalna sieć wirtualna są połączone za pośrednictwem bram sieci VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej koncentratora

Teraz utwórz bramę sieci VPN dla sieci wirtualnej koncentratora. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym Wyszukaj wpisz **Brama sieci wirtualnej** i naciśnij klawisz **Enter**.
3. Wybierz pozycję **Brama sieci wirtualnej**, a następnie wybierz pozycję **Utwórz**.
4. W obszarze **Nazwa** wpisz **GW-Hub**.
5. W **obszarze region** wybierz pozycję **(US) Wschodnie stany USA**.
6. W obszarze **Typ bramy** wybierz pozycję **Sieć VPN**.
7. W obszarze **Typ sieci VPN** wybierz pozycję **oparta na trasach**.
8. W obszarze **jednostka SKU** wybierz pozycję **podstawowa**.
9. W obszarze **Sieć wirtualna** wybierz opcję **koncentrator** wirtualny.
10. W obszarze **publiczny adres IP** wybierz pozycję **Utwórz nowy**, a następnie w polu Nazwa wpisz **VNET-Hub-GW-PIP** .
11. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Recenzja + Utwórz**.
12. Przejrzyj konfigurację, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Tworzenie bramy sieci VPN dla lokalnej sieci wirtualnej

Teraz utwórz bramę sieci VPN dla lokalnej sieci wirtualnej. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym Wyszukaj wpisz **Brama sieci wirtualnej** i naciśnij klawisz **Enter**.
3. Wybierz pozycję **Brama sieci wirtualnej**, a następnie wybierz pozycję **Utwórz**.
4. W obszarze **Nazwa** wpisz **GW-lokalnego**.
5. W **obszarze region** wybierz pozycję **(US) Wschodnie stany USA**.
6. W obszarze **Typ bramy** wybierz pozycję **Sieć VPN**.
7. W obszarze **Typ sieci VPN** wybierz pozycję **oparta na trasach**.
8. W obszarze **jednostka SKU** wybierz pozycję **podstawowa**.
9. W obszarze **Sieć wirtualna** wybierz pozycję **VNET-lokalnego**.
10. W polu **publiczny adres IP** wybierz pozycję **Utwórz nowy**, a następnie wpisz **VNET-lokalnego-GW-PIP** jako nazwę.
11. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Recenzja + Utwórz**.
12. Przejrzyj konfigurację, a następnie wybierz pozycję **Utwórz**.

### <a name="create-the-vpn-connections"></a>Tworzenie połączeń sieci VPN

Teraz można tworzyć połączenia sieci VPN między centrami centralnymi i lokalnymi.

W tym kroku utworzysz połączenie z sieci wirtualnej koncentratora do lokalnej sieci wirtualnej. W przykładach zastosowano odwołania do klucza współużytkowanego. Możesz wybrać własne wartości dla klucza współużytkowanego. Ważne jest, aby klucz współużytkowany był zgodny z obydwoma połączeniami. Utworzenie połączenia zajmuje trochę czasu.

1. Otwórz grupę zasobów **PD-hybrydowy-test** i wybierz bramę bramy **GW-Hub** .
2. Wybierz pozycję **połączenia** w lewej kolumnie.
3. Wybierz pozycję **Dodaj**.
4. W polu Nazwa połączenia wpisz **Hub-to-lokalnego**.
5. Wybierz pozycję **Sieć wirtualna-sieć wirtualna** dla **typu połączenia**.
6. W przypadku **drugiej bramy sieci wirtualnej** wybierz pozycję **GW-lokalnego**.
7. Dla **klucza współużytkowanego (PSK)** wpisz **AzureA1b2C3**.
8. Wybierz przycisk **OK**.

Utwórz połączenie z lokalnej sieci wirtualnej do sieci wirtualnej koncentratora. Ten krok jest podobny do poprzedniego, jednak w tym przypadku tworzysz połączenie z sieci VNet-Onprem do sieci VNet-hub. Upewnij się, że klucze współużytkowane są zgodne. Po kilku minutach połączenie zostanie ustanowione.

1. Otwórz grupę zasobów **PD-hybrydowy-test** i wybierz bramę **GW-lokalnego** Gateway.
2. Wybierz pozycję **połączenia** w lewej kolumnie.
3. Wybierz pozycję **Dodaj**.
4. Nazwa połączenia wpisz **lokalnego-to-Hub**.
5. Wybierz pozycję **Sieć wirtualna-sieć wirtualna** dla **typu połączenia**.
6. W przypadku **drugiej bramy sieci wirtualnej** wybierz pozycję **GW-Hub**.
7. Dla **klucza współużytkowanego (PSK)** wpisz **AzureA1b2C3**.
8. Wybierz przycisk **OK**.


#### <a name="verify-the-connection"></a>Weryfikowanie połączenia

Po około pięciu minutach lub tak, stan obu połączeń powinien być **połączony**.

![Połączenia bramy](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy

Teraz nawiąż komunikację równorzędną pomiędzy siecią wirtualną koncentratora i siecią wirtualną będącą szprychą.

1. Otwórz grupę zasobów **PD-hybrydowy-test** i wybierz sieć wirtualną **koncentratora** sieci wirtualnej.
2. W lewej kolumnie Wybierz pozycję **Komunikacja równorzędna**.
3. Wybierz pozycję **Dodaj**.
4. W **tej sieci wirtualnej**:
 
   
   |Nazwa ustawienia  |Wartość  |
   |---------|---------|
   |Nazwa łącza komunikacji równorzędnej| HubtoSpoke|
   |Ruch do zdalnej sieci wirtualnej|   Zezwalaj (wartość domyślna)      |
   |Ruch przekierowany z zdalnej sieci wirtualnej    |   Zezwalaj (wartość domyślna)      |
   |Brama sieci wirtualnej lub serwer tras    |  Użyj bramy tej sieci wirtualnej       |
    
5. W obszarze **zdalna Sieć wirtualna**:

   |Nazwa ustawienia  |Wartość  |
   |---------|---------|
   |Nazwa łącza komunikacji równorzędnej | SpoketoHub|
   |Model wdrażania sieci wirtualnej| Resource Manager|
   |Subskrypcja|\<your subscription\>|
   |Sieć wirtualna| VNet-Spoke
   |Ruch do zdalnej sieci wirtualnej     |   Zezwalaj (wartość domyślna)      |
   |Ruch przekierowany z zdalnej sieci wirtualnej    |   Zezwalaj (wartość domyślna)      |
   |Brama sieci wirtualnej     |  Korzystanie z bramy zdalnej sieci wirtualnej       |

5. Wybierz pozycję **Dodaj**.

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Komunikacja równorzędna sieci wirtualnych":::

## <a name="create-the-routes"></a>Tworzenie tras

Następnie należy utworzyć kilka tras:

- Trasa z podsieci bramy koncentratora do podsieci będącej szprychą za pośrednictwem adresu IP zapory
- Trasa domyślna z podsieci będącej szprychą za pośrednictwem adresu IP zapory

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym Wyszukaj wpisz ciąg **Route Table** i naciśnij klawisz **Enter**.
3. Wybierz pozycję **tabela tras**.
4. Wybierz przycisk **Utwórz**.
1. Wybierz z grupy zasobów opcję **PD-hybrydowy-test** .
1. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. W polu Nazwa wpisz **UDR-Hub-szprych**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz przycisk **Utwórz**.
1. Po utworzeniu tabeli tras wybierz ją, aby otworzyć stronę tabela tras.
1. W lewej kolumnie Wybierz pozycję **trasy** .
1. Wybierz pozycję **Dodaj**.
1. Dla nazwy trasy wpisz **ToSpoke**.
1. Dla prefiksu adresu wpisz **10.6.0.0/16**.
1. W polu Typ następnego przeskoku wybierz pozycję **urządzenie wirtualne**.
1. W polu adres następnego przeskoku wpisz zanotowany wcześniej prywatny adres IP zapory.
1. Wybierz przycisk **OK**.

Teraz Skojarz trasę z podsiecią.

1. Na stronie **UDR-Hub-szprych-Routes** wybierz pozycję **podsieci**.
2. Wybierz pozycję **Skojarz**.
4. W obszarze **Sieć wirtualna** wybierz pozycję Virtual **-Hub**.
5. W obszarze **podsieć** wybierz pozycję **GatewaySubnet**.
6. Wybierz przycisk **OK**.

Teraz Utwórz trasę domyślną z podsieci szprych.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym Wyszukaj wpisz ciąg **Route Table** i naciśnij klawisz **Enter**.
3. Wybierz pozycję **tabela tras**.
5. Wybierz przycisk **Utwórz**.
7. Wybierz z grupy zasobów opcję **PD-hybrydowy-test** .
8. W **obszarze region** wybierz pozycję **Wschodnie stany USA**.
1. W polu Nazwa wpisz **UDR-DG**.
4. W przypadku **propagacji tras bramy** wybierz pozycję **nie**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.
1. Po utworzeniu tabeli tras wybierz ją, aby otworzyć stronę tabela tras.
1. W lewej kolumnie Wybierz pozycję **trasy** .
1. Wybierz pozycję **Dodaj**.
1. Dla nazwy trasy wpisz **ToHub**.
1. Dla prefiksu adresu wpisz **0.0.0.0/0**.
1. W polu Typ następnego przeskoku wybierz pozycję **urządzenie wirtualne**.
1. W polu adres następnego przeskoku wpisz zanotowany wcześniej prywatny adres IP zapory.
1. Wybierz przycisk **OK**.

Teraz Skojarz trasę z podsiecią.

1. Na stronie **UDR-DG-Routes** wybierz pozycję **podsieci**.
2. Wybierz pozycję **Skojarz**.
4. W obszarze **Sieć wirtualna** wybierz pozycję **VNET-szprychy**.
5. W obszarze **podsieć** wybierz pozycję **SN-obciążenie**.
6. Wybierz przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne pakietu roboczego szprychy i sieci lokalnej, a następnie umieść je w odpowiednich podsieciach.

### <a name="create-the-workload-virtual-machine"></a>Tworzenie maszyny wirtualnej pakietu roboczego

Tworzenie maszyny wirtualnej w sieci wirtualnej szprych, uruchamianie usług IIS bez publicznego adresu IP.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **popularne** wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
    - **Grupa zasobów** — wybierz opcję **PD-hybrydowa — test**
    - **Nazwa maszyny wirtualnej**: *VM-szprych-01*
    - **Region**  -  *(US) Wschodnie stany USA*
    - **Nazwa użytkownika**: wpisz nazwę użytkownika
    - **Hasło**: wpisz hasło

4. Wybierz pozycję **Dalej: Dyski**.
5. Zaakceptuj wartości domyślne i wybierz pozycję **Dalej: sieć**.
6. Wybierz pozycję Sieć wirtualna **-szprycha** dla sieci wirtualnej, a podsieć jest **obciążeniem SN**.
8. W przypadku **publicznych portów ruchu przychodzącego** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **http (80)** i **protokół RDP (3389).**
1. Wybierz pozycję **Dalej: Zarządzanie**.
1. W obszarze **Diagnostyka rozruchu** wybierz pozycję **Wyłącz**.
1. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. W Azure Portal Otwórz Cloud Shell i upewnij się, że jest ustawiony na program **PowerShell**.
2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej i w razie potrzeby zmienić lokalizację:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Tworzenie maszyny wirtualnej w środowisku lokalnym

Jest to maszyna wirtualna, która jest używana do nawiązywania połączenia przy użyciu Pulpit zdalny z publicznym adresem IP. Z tego miejsca nawiążesz następnie połączenie z serwerem lokalnym za pośrednictwem zapory.

1. Na stronie głównej Azure Portal wybierz pozycję **Utwórz zasób**.
2. W obszarze **popularne** wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
    - **Grupa zasobów** — wybierz pozycję istniejące, a następnie wybierz pozycję **PD-hybrydowy-test**
    - **Nazwa**  -  maszyny wirtualnej *Maszyna wirtualna — lokalnego*
    - **Region**  -  *(US) Wschodnie stany USA*
    - **Nazwa użytkownika**: wpisz nazwę użytkownika
    - **Hasło**: wpisz hasło

4. Wybierz pozycję **Dalej: Dyski**.
5. Zaakceptuj wartości domyślne i wybierz pozycję **Dalej: sieć**.
6. Wybierz pozycję Sieć wirtualna **-lokalnego** dla sieci wirtualnej i sprawdź, czy podsieć jest **SN-Corp**.
7. W przypadku **publicznych portów ruchu przychodzącego** wybierz opcję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **RDP (3389).**
8. Wybierz pozycję **Dalej: Zarządzanie**.
9. W obszarze **Diagnostyka rozruchu** wybierz pozycję **Wyłącz**.
10. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj ustawienia na stronie Podsumowanie, a następnie wybierz pozycję **Utwórz**.

## <a name="test-the-firewall"></a>Testowanie zapory

1. Najpierw Zanotuj prywatny adres IP maszyny wirtualnej VM-szprych-01 na stronie Przegląd VM-szprych-01.

2. W witrynie Azure Portal połącz się z maszyną wirtualną **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otwórz przeglądarkę internetową w obszarze **VM-lokalnego**, a następnie przejdź do http:// \<VM-spoke-01 private IP\> .

   Powinna zostać wyświetlona strona sieci Web **VM-szprych-01** : ![ Strona sieci Web VM-szprych-01](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Z maszyny wirtualnej **VM-lokalnego** Otwórz pulpit zdalny do **maszyny wirtualnej-szprych-01** z prywatnym adresem IP.

   Połączenie powinno być pomyślne i powinno być możliwe zalogowanie się.

Teraz sprawdzono, że reguły zapory działają:

<!---- You can ping the server on the spoke VNet.--->
- Możesz przeglądać serwer internetowy w sieci wirtualnej będącej szprychą.
- Możesz nawiązać połączenie z serwerem w sieci wirtualnej będącej szprychą, korzystając z protokołu RDP.

Następnie zmień ustawienie akcji kolekcji reguł sieci zapory na **Odmów**, aby sprawdzić, czy reguły zapory działają zgodnie z oczekiwaniami.

1. Otwórz grupę zasobów **PD-hybrydowy-test** i wybierz zasady zapory **pol-Net01** .
2. W obszarze **Ustawienia** wybierz pozycję **kolekcje reguł**.
1. Wybierz kolekcję reguł **RCNet01** .
1. Dla **akcji kolekcji reguł** wybierz pozycję **Odmów**.
1. Wybierz pozycję **Zapisz**.

Zamknij wszystkie istniejące Pulpity zdalne i przeglądarki na **maszynie wirtualnej-lokalnego** przed przetestowaniem zmienionych reguł. Po zakończeniu aktualizacji kolekcji reguł ponownie uruchom testy. Ta godzina powinna się nie powieść.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Zasoby zapory można zachować do dalszej analizy, a jeśli nie są już potrzebne, Usuń grupę zasobów **PD-hybrydowy-test** , aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Zabezpieczanie wirtualnej sieci WAN przy użyciu Menedżera zapory platformy Azure](secure-cloud-network.md)