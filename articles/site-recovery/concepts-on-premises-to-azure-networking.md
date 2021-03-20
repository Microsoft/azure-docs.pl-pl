---
title: Nawiązywanie połączenia z lokalnymi maszynami wirtualnymi platformy Azure za pomocą Azure Site Recovery
description: Zawiera opis sposobu nawiązywania połączenia z maszynami wirtualnymi platformy Azure po przejściu do trybu failover z platformy Azure przy użyciu Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: harshacs
ms.openlocfilehash: 123a68885346062b9e8a53b8d5066204b6b20f5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89568782"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Nawiązywanie połączenia z maszynami wirtualnymi platformy Azure po przejściu w tryb failover z lokalnego 


W tym artykule opisano sposób konfigurowania połączenia w celu pomyślnego nawiązania połączenia z maszynami wirtualnymi platformy Azure po przejściu w tryb failover.

Po skonfigurowaniu odzyskiwania po awarii lokalnych maszyn wirtualnych i serwerów fizycznych na platformie Azure [Azure Site Recovery](site-recovery-overview.md) rozpocznie replikację maszyn na platformę Azure. Po wystąpieniu awarii można przejść do trybu failover na platformie Azure z lokacji lokalnej. W przypadku przełączenia w tryb failover Site Recovery tworzenia maszyn wirtualnych platformy Azure przy użyciu zreplikowanych danych lokalnych. W ramach planowania odzyskiwania po awarii należy dowiedzieć się, jak nawiązać połączenie z aplikacjami uruchomionymi na tych maszynach wirtualnych platformy Azure po przejściu do trybu failover.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Przygotuj maszyny lokalne przed przełączeniem w tryb failover.
> * Przygotowywanie maszyn wirtualnych platformy Azure po przejściu do trybu failover. 
> * Przechowuj adresy IP na maszynach wirtualnych platformy Azure po przejściu do trybu failover.
> * Przypisz nowe adresy IP do maszyn wirtualnych platformy Azure po przejściu do trybu failover.

## <a name="prepare-on-premises-machines"></a>Przygotowywanie maszyn lokalnych

Aby zapewnić łączność z maszynami wirtualnymi platformy Azure, przygotuj maszyny lokalne przed przejściem w tryb failover.

### <a name="prepare-windows-machines"></a>Przygotowywanie maszyn z systemem Windows

Na lokalnych maszynach z systemem Windows wykonaj następujące czynności:

1. Skonfiguruj ustawienia systemu Windows. Dotyczy to również usuwania wszelkich statycznych tras stałych lub serwera proxy WinHTTP oraz ustawiania zasad dotyczących dysków SAN na **OnlineAll**. [Postępuj zgodnie z](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) tymi instrukcjami.

2. Upewnij się, że [te usługi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) działają.

3. Włącz funkcję pulpitu zdalnego (RDP), aby zezwolić na zdalne połączenia z maszyną lokalną. [Dowiedz się, jak](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) włączyć protokół RDP przy użyciu programu PowerShell.

4. Aby uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem Internetu po przejściu w tryb failover, w zaporze systemu Windows na maszynie lokalnej, Zezwól na protokoły TCP i UDP w profilu publicznym i ustaw protokół RDP jako dozwoloną aplikację dla wszystkich profilów.

5. Jeśli chcesz uzyskać dostęp do maszyny wirtualnej platformy Azure za pośrednictwem sieci VPN typu lokacja-lokacja po przejściu w tryb failover, w zaporze systemu Windows na maszynie lokalnej, Zezwól na używanie protokołu RDP dla domeny i profilów prywatnych. [Dowiedz się](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) , jak zezwolić na ruch RDP.
6. Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na lokalnej maszynie wirtualnej podczas wyzwalania trybu failover. W takim przypadku aktualizacje mogą rozpocząć instalację na maszynie wirtualnej platformy Azure po przejściu w tryb failover i nie będzie można zalogować się do maszyny wirtualnej do momentu zakończenia aktualizacji.

### <a name="prepare-linux-machines"></a>Przygotowywanie maszyn z systemem Linux

Na lokalnych maszynach z systemem Linux wykonaj następujące czynności:

1. Sprawdź, czy usługa Secure Shell jest uruchamiana automatycznie przy rozruchu systemu.
2. Sprawdź, czy reguły zapory zezwalają na połączenie SSH.


## <a name="configure-azure-vms-after-failover"></a>Konfigurowanie maszyn wirtualnych platformy Azure po przejściu w tryb failover

Po przejściu w tryb failover wykonaj następujące czynności na maszynach wirtualnych platformy Azure, które zostały utworzone.

1. Aby nawiązać połączenie z maszyną wirtualną za pośrednictwem Internetu, przypisz publiczny adres IP do maszyny wirtualnej. Nie można użyć tego samego publicznego adresu IP dla maszyny wirtualnej platformy Azure, która była używana przez maszynę lokalną. [Dowiedz się więcej](../virtual-network/virtual-network-public-ip-address.md)
2. Sprawdź, czy reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) na maszynie wirtualnej zezwalają na połączenia przychodzące do portu RDP lub SSH.
3. Sprawdź [diagnostykę rozruchu](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) , aby wyświetlić maszynę wirtualną.


> [!NOTE]
> Usługa Azure bastionu oferuje prywatny dostęp RDP i SSH do maszyn wirtualnych platformy Azure. [Dowiedz się więcej](../bastion/bastion-overview.md) o tej usłudze.

## <a name="set-a-public-ip-address"></a>Ustaw publiczny adres IP

Alternatywnie, aby ręcznie przypisywać publiczny adres IP do maszyny wirtualnej platformy Azure, można przypisać ten adres podczas pracy w trybie failover przy użyciu skryptu lub elementu Runbook usługi Azure Automation w [planie odzyskiwania](site-recovery-create-recovery-plans.md)Site Recovery lub skonfigurować Routing na poziomie systemu DNS przy użyciu usługi Azure Traffic Manager. [Dowiedz się więcej](concepts-public-ip-address-with-site-recovery.md) o konfigurowaniu adresu publicznego.


## <a name="assign-an-internal-address"></a>Przypisywanie adresu wewnętrznego

Aby ustawić wewnętrzny adres IP maszyny wirtualnej platformy Azure po przejściu w tryb failover, masz kilka opcji:

- **Zachowaj ten sam adres IP**: możesz użyć tego samego adresu IP na maszynie wirtualnej platformy Azure, który jest przypisany do maszyny lokalnej.
- **Użyj innego adresu IP**: możesz użyć innego adresu IP dla maszyny wirtualnej platformy Azure.


## <a name="retain-ip-addresses"></a>Zachowaj adresy IP

Site Recovery pozwala zachować te same adresy IP po przełączeniu w tryb failover na platformie Azure. Zachowanie tego samego adresu IP pozwala uniknąć potencjalnych problemów z siecią po przejściu w tryb failover, ale wprowadza pewną złożoność.

- Jeśli docelowa maszyna wirtualna platformy Azure używa tego samego adresu IP/podsieci co lokacja lokalna, nie można nawiązać połączenia między nimi przy użyciu połączenia sieci VPN typu lokacja-lokacja lub ExpressRoute z powodu nakładania się adresów. Podsieci muszą być unikatowe.
- Musisz mieć połączenie ze sklepu lokalnego z platformą Azure po przejściu w tryb failover, aby aplikacje były dostępne na maszynach wirtualnych platformy Azure. Platforma Azure nie obsługuje rozciągniętych sieci VLAN, dlatego jeśli chcesz zachować adresy IP, musisz przenieść przestrzeń adresową IP do platformy Azure, przełączając się w tryb failover w całej podsieci, poza maszyną lokalną.
- Podsieć w trybie failover zapewnia, że określona podsieć nie jest dostępna jednocześnie lokalnie i na platformie Azure.

Przechowywanie adresów IP wymaga wykonania następujących czynności:

- We właściwościach & obliczeniowych sieci dla zreplikowanego elementu ustaw opcję Adresowanie sieci i IP dla docelowej maszyny wirtualnej platformy Azure, aby przeprowadzić dublowanie ustawienia lokalnego.
- Podsieci muszą być zarządzane w ramach procesu odzyskiwania po awarii. Musisz mieć sieć wirtualną platformy Azure, aby dopasować ją do sieci lokalnej, a następnie należy zmodyfikować trasy sieciowe trybu failover w celu odzwierciedlenia, że podsieć została przeniesiona na platformę Azure, a nowe lokalizacje adresów IP.  

### <a name="failover-example"></a>Przykład pracy awaryjnej

Spójrzmy na przykład.

- Fikcyjny Bank Woodgrove firmy hostuje swoje aplikacje biznesowe w środowisku lokalnym, które obsługują swoje aplikacje mobilne na platformie Azure.
- Łączą się z lokalnymi z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja. 
- Woodgrove używa Site Recovery do replikowania maszyn lokalnych na platformę Azure.
- Aplikacje lokalne używają zakodowanych adresów IP, dlatego chcą zachować te same adresy IP na platformie Azure.
- Lokalne maszyny, na których działają aplikacje, działają w trzech podsieciach:
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Aplikacje działające na platformie Azure znajdują się w **sieci** wirtualnej platformy Azure w dwóch podsieciach:
    - 172.16.1.0/24
    - 172.16.2.0/24

Aby zachować adresy, wykonaj czynności przedstawione poniżej.

1. Po włączeniu replikacji określają, że maszyny mają być replikowane do **sieci platformy Azure**.
2. Tworzenie **sieci odzyskiwania** na platformie Azure. Ta sieć wirtualna odzwierciedla podsieci 192.168.1.0/24 w sieci lokalnej.
3. Woodgrove konfiguruje połączenie między dwiema sieciami [wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) . 

    > [!NOTE]
    > W zależności od wymagań aplikacji połączenie między sieciami wirtualnymi można skonfigurować przed przełączeniem w tryb failover, w ramach ręcznego kroku/scenariusza skryptu/elementu Runbook usługi Azure Automation w ramach [planu odzyskiwania](site-recovery-create-recovery-plans.md)Site Recovery lub po zakończeniu pracy w trybie failover.

4. Przed przejściem w tryb failover we właściwościach komputera w Site Recovery ustawia docelowy adres IP na adres komputera lokalnego, zgodnie z opisem w następnej procedurze.
5. Po przejściu w tryb failover maszyny wirtualne platformy Azure są tworzone przy użyciu tego samego adresu IP. Woodgrove nawiązuje połączenie z **siecią platformy Azure** z siecią wirtualną **sieci** wirtualnej przy użyciu komunikacji równorzędnej (z włączonym połączeniem tranzytowym).
6. Lokalne, Woodgrove musi wprowadzić zmiany w sieci, w tym modyfikując trasy, aby odzwierciedlić, że 192.168.1.0/24 zostały przeniesione na platformę Azure.  

**Infrastruktura przed przejściem w tryb failover**

![Przed przełączeniem w tryb failover podsieci](./media/site-recovery-network-design/network-design7.png)


**Infrastruktura po pracy w trybie failover**

![Po przełączeniu w tryb failover podsieci](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Ustawianie ustawień sieci docelowej

Przed przejściem w tryb failover Określ ustawienia sieci i adres IP dla docelowej maszyny wirtualnej platformy Azure.

1.  W magazynie Recovery Services — > **zreplikowane elementy** wybierz maszynę lokalną.
2. Na stronie **obliczenia i sieć** dla maszyny kliknij pozycję **Edytuj**, aby skonfigurować ustawienia sieci i karty dla docelowej maszyny wirtualnej platformy Azure.
3. W obszarze **właściwości sieci** wybierz sieć docelową, w której będzie znajdować się maszyna wirtualna platformy Azure, gdy zostanie ona utworzona po przejściu do trybu failover.
4. W obszarze **interfejsy sieciowe** Skonfiguruj karty sieciowe w sieci docelowej. Domyślnie Site Recovery są wyświetlane wszystkie wykryte karty sieciowe na komputerze lokalnym.
    - W obszarze **docelowy typ interfejsu sieciowego** możesz ustawić każdą kartę sieciową jako **podstawową**, **pomocniczą** lub **nieutworzoną** , jeśli nie potrzebujesz tej konkretnej karty sieciowej w sieci docelowej. Jedna karta sieciowa musi być ustawiona jako podstawowa dla trybu failover. Należy zauważyć, że modyfikacja sieci docelowej ma wpływ na wszystkie karty sieciowe maszyny wirtualnej platformy Azure.
    - Kliknij nazwę karty sieciowej, aby określić podsieć, w której zostanie wdrożona maszyna wirtualna platformy Azure.
    - Zastąp wartość **dynamiczną** prywatnym adresem IP, który chcesz przypisać do docelowej maszyny wirtualnej platformy Azure. Jeśli adres IP nie jest określony, Site Recovery przypisze następnego dostępnego adresu IP w podsieci do karty sieciowej w trybie failover.
    - [Dowiedz się więcej](site-recovery-manage-network-interfaces-on-premises-to-azure.md) o zarządzaniu kartami sieciowymi dla lokalnego trybu failover na platformie Azure.


## <a name="get-new-ip-addresses"></a>Pobierz nowe adresy IP

W tym scenariuszu maszyna wirtualna platformy Azure otrzymuje nowy adres IP po przejściu do trybu failover. Aby skonfigurować nowy adres IP dla maszyny wirtualnej utworzonej po przejściu w tryb failover, można odwołać się do następujących kroków:

1. Przejdź do **pozycji zreplikowane elementy**.
2. Wybierz żądaną maszynę wirtualną platformy Azure.
3. Wybierz pozycję **obliczenia i sieć** , a następnie wybierz pozycję **Edytuj**.

     ![Dostosowywanie konfiguracji sieci trybu failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Aby zaktualizować ustawienia sieci trybu failover, wybierz opcję **Edytuj** dla karty sieciowej, którą chcesz skonfigurować. Na następnej stronie, która zostanie otwarta, podaj odpowiedni, wstępnie utworzony adres IP w lokalizacji testowej pracy w trybie failover i w trybie failover.

    ![Edytowanie konfiguracji karty sieciowej](media/azure-to-azure-customize-networking/nic-drilldown.png)

5. Wybierz przycisk **OK**.

Site Recovery będzie teraz przestrzegać tych ustawień i upewnić się, że maszyna wirtualna w trybie failover jest połączona z wybranym zasobem przy użyciu odpowiedniego adresu IP, jeśli jest dostępna w docelowym zakresie adresów IP. W tym scenariuszu nie ma potrzeby przełączania do trybu failover całej podsieci. Aktualizacja systemu DNS będzie wymagana do zaktualizowania rekordów dla maszyny przełączonej w tryb failover, aby wskazywała na nowy adres IP maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat](site-recovery-active-directory.md) replikowania lokalnych Active Directory i DNS na platformę Azure.
