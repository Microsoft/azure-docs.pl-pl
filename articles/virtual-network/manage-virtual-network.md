---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Tworzenie i usuwanie sieci wirtualnej oraz Zmienianie ustawień, takich jak serwery DNS i przestrzenie adresów IP, dla istniejącej sieci wirtualnej.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 61ca4dc8cd7048df69c827e7ca657b9882900819
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218913"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Tworzenie, zmienianie i usuwanie sieci wirtualnej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dowiedz się, jak utworzyć i usunąć sieć wirtualną oraz zmienić ustawienia, takie jak serwery DNS i przestrzenie adresów IP, dla istniejącej sieci wirtualnej. Jeśli dopiero zaczynasz korzystanie z sieci wirtualnych, możesz dowiedzieć się więcej o nich w [sieci wirtualnej](virtual-networks-overview.md) lub za pomocą [samouczka](quick-create-portal.md). Sieć wirtualna zawiera podsieci. Aby dowiedzieć się, jak tworzyć, zmieniać i usuwać podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.
- Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz pozycję **+ Utwórz zasób**  >    >  **Sieć wirtualna sieci**.
2. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:
   - **Nazwa**: nazwa musi być unikatowa w wybranej [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , w której ma zostać utworzona sieć wirtualna. Nie można zmienić nazwy po utworzeniu sieci wirtualnej. W miarę upływu czasu można utworzyć wiele sieci wirtualnych. Aby uzyskać sugestie dotyczące nazewnictwa, zobacz [konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Postępując zgodnie z konwencją nazewnictwa, można ułatwić zarządzanie wieloma sieciami wirtualnymi.
   - **Przestrzeń adresowa**: przestrzeń adresowa dla sieci wirtualnej składa się z co najmniej jednego nienakładających się zakresów adresów, które są określone w notacji CIDR. Zdefiniowany zakres adresów może być publiczny lub prywatny (RFC 1918). Niezależnie od tego, czy zakres adresów został zdefiniowany jako publiczny, czy prywatny, zakres adresów jest dostępny tylko w ramach sieci wirtualnej, z połączonych sieci wirtualnych oraz z dowolnych sieci lokalnych, które zostały podłączone do sieci wirtualnej. Nie można dodać następujących zakresów adresów:
     - 224.0.0.0/4 (multiemisja)
     - 255.255.255.255/32 (emisja)
     - 127.0.0.0/8 (sprzężenie zwrotne)
     - 169.254.0.0/16 (połączenie lokalne)
     - 168.63.129.16/32 (wewnętrzna usługa DNS, DHCP i Azure Load Balancer [sonda kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Chociaż można zdefiniować tylko jeden zakres adresów podczas tworzenia sieci wirtualnej w portalu, można dodać więcej zakresów adresów do przestrzeni adresowej po utworzeniu sieci wirtualnej. Aby dowiedzieć się, jak dodać zakres adresów do istniejącej sieci wirtualnej, zobacz [Dodawanie lub usuwanie zakresu adresów](#add-or-remove-an-address-range).

     > [!WARNING]
     > Jeśli sieć wirtualna ma zakresy adresów, które nakładają się na inną sieć wirtualną lub sieć lokalną, nie można połączyć obu tych sieci. Przed zdefiniowaniem zakresu adresów należy rozważyć, czy w przyszłości warto połączyć sieć wirtualną z innymi sieciami wirtualnymi lub sieciami lokalnymi. Firma Microsoft zaleca skonfigurowanie zakresów adresów sieci wirtualnej z prywatną przestrzenią adresową lub publiczną przestrzenią adresową posiadaną przez organizację.
     >

     - **Nazwa podsieci**: Nazwa podsieci musi być unikatowa w obrębie sieci wirtualnej. Po utworzeniu podsieci nie można zmienić jej nazwy. Portal wymaga zdefiniowania jednej podsieci podczas tworzenia sieci wirtualnej, nawet jeśli sieć wirtualna nie jest wymagana do posiadania żadnych podsieci. W portalu można zdefiniować tylko jedną podsieć podczas tworzenia sieci wirtualnej. Później można dodać więcej podsieci do sieci wirtualnej po utworzeniu sieci wirtualnej. Aby dodać podsieć do sieci wirtualnej, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md). Sieć wirtualną z wieloma podsieciami można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

       >[!TIP]
       >Czasami administratorzy tworzą różne podsieci do filtrowania lub kontrolowania routingu ruchu między podsieciami. Przed zdefiniowaniem podsieci należy rozważyć sposób filtrowania i kierowania ruchu między podsieciami. Aby dowiedzieć się więcej o filtrowaniu ruchu między podsieciami, zobacz [sieciowe grupy zabezpieczeń](./network-security-groups-overview.md). Platforma Azure automatycznie kieruje ruchem między podsieciami, ale można zastąpić trasy domyślne platformy Azure. Aby dowiedzieć się więcej o domyślnym routingu ruchu podsieci platformy Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
       >

     - **Zakres adresów podsieci**: zakres musi mieścić się w przestrzeni adresowej wprowadzonej dla sieci wirtualnej. Najmniejszy zakres, który można określić, to/29, który zapewnia osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci w celu zapewnienia zgodności z protokołem. Trzy dodatkowe adresy są zastrzeżone dla użycia usługi platformy Azure. W związku z tym sieć wirtualna z zakresem adresów podsieci/29 ma tylko trzy użyteczne adresy IP. Jeśli planujesz połączenie sieci wirtualnej z bramą sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej [na temat konkretnych zagadnień dotyczących zakresów adresów dla podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Możesz zmienić zakres adresów po utworzeniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
     - **Subskrypcja**: Wybierz [subskrypcję](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Nie można używać tej samej sieci wirtualnej w więcej niż jednej subskrypcji platformy Azure. Można jednak połączyć sieć wirtualną w jednej subskrypcji z sieciami wirtualnymi w innych subskrypcjach za pomocą [komunikacji równorzędnej sieci wirtualnych](virtual-network-peering-overview.md). Wszystkie zasoby platformy Azure, które nawiązują połączenie z siecią wirtualną, muszą znajdować się w tej samej subskrypcji co sieć wirtualna.
     - **Grupa zasobów**: wybierz istniejącą [grupę zasobów](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) lub Utwórz nową. Zasób platformy Azure, który można połączyć z siecią wirtualną, może znajdować się w tej samej grupie zasobów co sieć wirtualna lub w innej grupie zasobów.
     - **Lokalizacja**: Wybierz [lokalizację](https://azure.microsoft.com/regions/)platformy Azure, nazywaną również regionem. Sieć wirtualna może znajdować się tylko w jednej lokalizacji platformy Azure. Można jednak połączyć sieć wirtualną w jednej lokalizacji z siecią wirtualną w innej lokalizacji przy użyciu bramy sieci VPN. Wszystkie zasoby platformy Azure, które łączą się z siecią wirtualną, muszą znajdować się w tej samej lokalizacji co sieć wirtualna.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Wyświetlanie sieci wirtualnych i ustawień

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz wyświetlić ustawienia.
3. Następujące ustawienia są wyświetlane dla wybranej sieci wirtualnej:
   - **Przegląd**: zawiera informacje o sieci wirtualnej, w tym przestrzeń adresową i serwery DNS. Poniższy zrzut ekranu przedstawia ustawienia dotyczące sieci wirtualnej o nazwie **MyVNet**:

     ![Omówienie interfejsu sieciowego](./media/manage-virtual-network/vnet-overview.png)

     Sieć wirtualną można przenieść do innej subskrypcji lub grupy zasobów, wybierając pozycję **Zmień** obok pozycji **Grupa zasobów** lub **Nazwa subskrypcji**. Aby dowiedzieć się, jak przenieść sieć wirtualną, zobacz [przenoszenie zasobów do innej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artykuł zawiera listę wymagań wstępnych oraz sposób przenoszenia zasobów przy użyciu programu Azure Portal, programu PowerShell i interfejsu wiersza polecenia platformy Azure. Wszystkie zasoby połączone z siecią wirtualną muszą zostać przeniesione z siecią wirtualną.
   - **Przestrzeń adresowa**: wymienione są Przestrzenie adresowe przypisane do sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć zakres adresów do przestrzeni adresowej, wykonaj kroki opisane w sekcji [Dodawanie lub usuwanie zakresu adresów](#add-or-remove-an-address-range).
   - **Podłączone urządzenia**: wyświetlane są wszystkie zasoby połączone z siecią wirtualną. Na poprzednim zrzucie ekranu są połączone z siecią wirtualną przy użyciu trzech interfejsów sieciowych i jednego modułu równoważenia obciążenia. Zostaną wyświetlone wszystkie nowe zasoby utworzone i połączone z siecią wirtualną. W przypadku usunięcia zasobu, który został podłączony do sieci wirtualnej, nie jest już wyświetlany na liście.
   - **Podsieci**: zostanie wyświetlona lista podsieci, które znajdują się w sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć podsieć, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
   - **Serwery DNS**: możesz określić, czy wewnętrzny serwer DNS platformy Azure lub niestandardowy serwer DNS zapewnia rozpoznawanie nazw dla urządzeń, które są połączone z siecią wirtualną. Podczas tworzenia sieci wirtualnej przy użyciu Azure Portal serwery DNS platformy Azure są domyślnie używane do rozpoznawania nazw w sieci wirtualnej. Aby zmodyfikować serwery DNS, wykonaj kroki opisane w temacie [zmiana serwerów DNS](#change-dns-servers) w tym artykule.
   - **Komunikacja równorzędna**: w przypadku istniejących komunikacji równorzędnych w subskrypcji są one wymienione tutaj. Można wyświetlać ustawienia dla istniejących komunikacji równorzędnych lub tworzyć, zmieniać lub usuwać komunikację równorzędną. Aby dowiedzieć się więcej o komunikacji równorzędnej, zobacz [wirtualne sieci równorzędne](virtual-network-peering-overview.md).
   - **Właściwości**: wyświetla ustawienia sieci wirtualnej, w tym identyfikator zasobu sieci wirtualnej i subskrypcję platformy Azure, w której znajduje się.
   - **Diagram**: diagram przedstawia wizualną reprezentację wszystkich urządzeń, które są połączone z siecią wirtualną. Diagram zawiera pewne kluczowe informacje o urządzeniach. Aby zarządzać urządzeniem w tym widoku, na diagramie wybierz urządzenie.
   - **Typowe ustawienia platformy Azure**: Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure, zobacz następujące informacje:
     - [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)
     - [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
     - [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Skrypt usługi Automation](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Dodawanie lub usuwanie zakresu adresów

Możesz dodawać i usuwać zakresy adresów dla sieci wirtualnej. Zakres adresów musi być określony w notacji CIDR i nie może nakładać się na inne zakresy adresów w ramach tej samej sieci wirtualnej. Zdefiniowane zakresy adresów mogą być publiczne lub prywatne (RFC 1918). Niezależnie od tego, czy zakres adresów został zdefiniowany jako publiczny, czy prywatny, zakres adresów jest dostępny tylko w ramach sieci wirtualnej, z połączonych sieci wirtualnych oraz z dowolnych sieci lokalnych, które zostały podłączone do sieci wirtualnej. 

Możesz zmniejszyć zakres adresów dla sieci wirtualnej, o ile nadal obejmuje ona zakresy wszystkich skojarzonych podsieci. Ponadto można zwiększyć zakres adresów, na przykład zmieniając wartości/16 na/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nie można dodać następujących zakresów adresów:

- 224.0.0.0/4 (multiemisja)
- 255.255.255.255/32 (emisja)
- 127.0.0.0/8 (sprzężenie zwrotne)
- 169.254.0.0/16 (połączenie lokalne)
- 168.63.129.16/32 (wewnętrzna usługa DNS, DHCP i Azure Load Balancer [sonda kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Aby dodać lub usunąć zakres adresów:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz dodać lub usunąć zakres adresów.
3. W obszarze **Ustawienia** wybierz pozycję **przestrzeń adresowa**.
4. Wykonaj jedną z następujących czynności:
    - **Dodaj zakres adresów**: Wprowadź nowy zakres adresów. Zakres adresów nie może nakładać się na istniejący zakres adresów, który jest zdefiniowany dla sieci wirtualnej.
    - **Usuń zakres adresów**: po prawej stronie zakresu adresów, który chcesz usunąć, wybierz pozycję **...**, a następnie wybierz pozycję **Usuń**. Jeśli w zakresie adresów istnieje podsieć, nie można usunąć zakresu adresów. Aby usunąć zakres adresów, należy najpierw usunąć wszystkie podsieci (i wszystkie zasoby w podsieciach), które znajdują się w zakresie adresów.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Zmień serwery DNS

Wszystkie maszyny wirtualne, które są połączone z siecią wirtualną, są zarejestrowane na serwerach DNS określonych dla sieci wirtualnej. Używają one także określonego serwera DNS do rozpoznawania nazw. Każdy interfejs sieciowy w maszynie wirtualnej może mieć własne ustawienia serwera DNS. Jeśli karta sieciowa ma własne ustawienia serwera DNS, zastępują ustawienia serwera DNS dla sieci wirtualnej. Aby dowiedzieć się więcej o ustawieniach DNS karty sieciowej, zobacz [zadania i ustawienia interfejsu sieciowego](virtual-network-network-interface.md#change-dns-servers). Aby dowiedzieć się więcej na temat rozpoznawania nazw dla maszyn wirtualnych i wystąpień ról w usłudze Azure Cloud Services, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień ról](virtual-networks-name-resolution-for-vms-and-role-instances.md). Aby dodać, zmienić lub usunąć serwer DNS:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz zmienić serwery DNS.
3. W obszarze **Ustawienia** wybierz pozycję **serwery DNS**.
4. Wybierz jedną z następujących opcji:
   - **Domyślne (udostępniane przez platformę Azure)**: wszystkie nazwy zasobów i prywatne adresy IP są automatycznie rejestrowane na serwerach Azure DNS. Można rozwiązać nazwy między dowolnymi zasobami połączonymi z tą samą siecią wirtualną. Nie można użyć tej opcji do rozpoznawania nazw w sieciach wirtualnych. Aby rozwiązać nazwy w sieciach wirtualnych, należy użyć niestandardowego serwera DNS.
   - **Niestandardowo**: można dodać jeden lub więcej serwerów, do limitu platformy Azure dla sieci wirtualnej. Aby dowiedzieć się więcej na temat limitów serwera DNS, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Do wyboru są następujące opcje:
   - **Dodaj adres**: dodaje serwer do listy serwerów DNS sieci wirtualnej. Ta opcja powoduje także zarejestrowanie serwera DNS na platformie Azure. Jeśli serwer DNS został już zarejestrowany na platformie Azure, możesz wybrać ten serwer DNS z listy.
   - **Usuń adres**: obok serwera, który chcesz usunąć, wybierz pozycję **...**, a następnie **Usuń**. Usunięcie serwera spowoduje usunięcie serwera tylko z tej listy sieci wirtualnych. Serwer DNS pozostaje zarejestrowany na platformie Azure dla innych sieci wirtualnych do użycia.
   - **Zmienianie kolejności adresów serwera DNS**: ważne jest, aby sprawdzić, czy serwery DNS są w odpowiedniej kolejności w danym środowisku. Listy serwerów DNS są używane w kolejności, w jakiej zostały określone. Nie działają one jako Konfiguracja działania okrężnego. Jeśli na liście można osiągnąć pierwszy serwer DNS, klient korzysta z tego serwera DNS, niezależnie od tego, czy serwer DNS działa prawidłowo. Usuń wszystkie wymienione serwery DNS, a następnie dodaj je ponownie w podanej kolejności.
   - **Zmień adres**: Wyróżnij serwer DNS na liście, a następnie wprowadź nowy adres.
5. Wybierz pozycję **Zapisz**.
6. Uruchom ponownie maszyny wirtualne połączone z siecią wirtualną, aby zostały przypisane nowe ustawienia serwera DNS. Maszyny wirtualne nadal używają bieżących ustawień DNS do momentu ponownego uruchomienia.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Sieć wirtualną można usunąć tylko wtedy, gdy nie ma żadnych podłączonych zasobów. Jeśli istnieją zasoby połączone z dowolnymi podsieciami w sieci wirtualnej, należy najpierw usunąć zasoby, które są połączone ze wszystkimi podsieciami w sieci wirtualnej. Kroki wykonywane w celu usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby połączone z podsieciami, zapoznaj się z dokumentacją dla każdego typu zasobu, który chcesz usunąć. Aby usunąć sieć wirtualną:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, którą chcesz usunąć.
3. Upewnij się, że nie ma żadnych urządzeń podłączonych do sieci wirtualnej, wybierając pozycję **podłączone urządzenia**, w obszarze **Ustawienia**. Jeśli są podłączone urządzenia, należy je usunąć przed usunięciem sieci wirtualnej. Jeśli nie ma połączonych urządzeń, wybierz pozycję **Przegląd**.
4. Wybierz pozycję **Usuń**.
5. Aby potwierdzić usunięcie sieci wirtualnej, wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [usuwanie sieci wirtualnej platformy Azure](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania w sieciach wirtualnych, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisane są odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                  |   Nazwa                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft. Network/virtualNetworks/odczyt   |   Odczytaj sieć wirtualną              |
|Microsoft. Network/virtualNetworks/Write  |   Utwórz lub zaktualizuj sieć wirtualną  |
|Microsoft. Network/virtualNetworks/Delete |   Usuwanie sieci wirtualnej            |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i przypisywanie [definicji Azure Policy](./policy-reference.md) dla sieci wirtualnych