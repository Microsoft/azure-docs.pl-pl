---
title: Zarządzanie publicznymi adresami IP | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Zarządzanie publicznymi adresami IP.  Dowiedz się również, jak publiczny adres IP jest zasobem z własnymi konfigurowalnymi ustawieniami.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 3277d5836d85f1071b7aa169cc83896934a2f63d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872447"
---
# <a name="manage-public-ip-addresses"></a>Zarządzanie publicznymi adresami IP

Dowiedz się więcej na temat publicznego adresu IP oraz sposobu jego tworzenia, zmieniania i usuwania. Publiczny adres IP to zasób z własnymi konfigurowalnymi ustawieniami. Przypisanie publicznego adresu IP do zasobu platformy Azure, który obsługuje publiczne adresy IP, umożliwia:
- Komunikacja przychodzących z Internetu do zasobu, takiego jak usługi Azure Virtual Machines (VM), Azure Application Gateway, Azure Load Balancer, Azure VPN Gateway i inne. Nadal możesz komunikować się z niektórymi zasobami, takimi jak maszyny wirtualne, z Internetu, jeśli maszyna wirtualna nie ma przypisanego publicznego adresu IP, o ile maszyna wirtualna jest częścią puli usługi równoważenia obciążenia, a do usługi równoważenia obciążenia jest przypisany publiczny adres IP. Aby określić, czy zasób dla określonej usługi platformy Azure może mieć przypisany publiczny adres IP, czy też może być komunikowany za pośrednictwem publicznego adresu IP innego zasobu platformy Azure, zapoznaj się z dokumentacją usługi.
- Łączność wychodząca do Internetu przy użyciu przewidywalnego adresu IP. Na przykład maszyna wirtualna może komunikować się wychodząco z Internetem bez przypisanego do niego publicznego adresu IP, ale jej adres jest adresem sieciowym przetłumaczonym przez platformę Azure na nieprzewidywalny adres publiczny, domyślnie. Przypisanie publicznego adresu IP do zasobu pozwala dowiedzieć się, który adres IP jest używany dla połączenia wychodzącego. Mimo że adres jest przewidywalny, może ulec zmianie w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, [zobacz Create a public IP address (Tworzenie publicznego adresu IP).](#create-a-public-ip-address) Aby dowiedzieć się więcej na temat połączeń wychodzących z zasobów platformy Azure, zobacz [Understand outbound connections (Informacje o połączeniach wychodzących).](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w dowolnej sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać bezpłatne [konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z portalu, otwórz plik https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń programu PowerShell, uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchamiając program PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule używasz poleceń interfejsu wiersza polecenia platformy Azure, uruchom polecenia w witrynie [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiając interfejs wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić program , aby `az login` utworzyć połączenie z platformą Azure.

Konto, na które się logujesz lub z platformą [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) Azure łączysz [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) się z platformą Azure, musi być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w [tesłudze.](#permissions)

Za publiczne adresy IP są naliczane nominalne opłaty. Aby wyświetlić cennik, przeczytaj stronę [cennika adresów IP.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać instrukcje dotyczące tworzenia publicznych adresów IP przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia , zapoznaj się z następującymi stronami:

 * [Tworzenie publicznych adresów IP — portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Tworzenie publicznych adresów IP — program PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Tworzenie publicznych adresów IP — interfejs wiersza polecenia platformy Azure](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Mimo że portal umożliwia utworzenie dwóch zasobów publicznych adresów IP (jednego IPv4 i jednego protokołu IPv6), polecenia programu PowerShell i interfejsu wiersza polecenia tworzą jeden zasób z adresem dla jednej lub drugiej wersji adresu IP. Jeśli chcesz mieć dwa zasoby publicznego adresu IP, po jednym dla każdej wersji adresu IP, musisz uruchomić polecenie dwa razy, określając różne nazwy i wersje adresów IP dla zasobów publicznego adresu IP.

Aby uzyskać dodatkowe informacje na temat określonych atrybutów publicznego adresu IP podczas tworzenia, zobacz tabelę poniżej.

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Wersja adresu IP|Tak| Wybierz pozycję IPv4, IPv6 lub Oba. Wybranie opcji Oba spowoduje utworzenie 2 publicznych adresów IP — 1 adres IPv4 i 1 adres IPv6. Dowiedz się więcej o [protokole IPv6 w sieciach wirtualnych platformy Azure.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |SKU|Tak|Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostki SKU są **publicznymi** adresami IP podstawowej jednostki SKU. Nie można zmienić sku po utworzeniu publicznego adresu IP. Autonomiczna maszyna wirtualna, maszyny wirtualne w zestawie dostępności lub zestawy skalowania maszyn wirtualnych mogą używać podstawowych lub standardowych jednostki SKU. Mieszanie jednostki SKU między maszynami wirtualnymi w zestawach dostępności, zestawami skalowania lub autonomicznymi maszynami wirtualnymi jest niedozwolone. **Podstawowa** SKU: jeśli tworzysz publiczny adres IP w regionie,  który obsługuje strefy dostępności, ustawienie Strefa dostępności jest domyślnie ustawione na *wartość* Brak. Podstawowe publiczne ip nie obsługują stref dostępności. **Standardowa** SKU: publiczny adres IP standardowej wersji SKU można skojarzyć z maszyną wirtualną lub frontonem usługi równoważenia obciążenia. Jeśli tworzysz publiczny adres IP w regionie, który  obsługuje strefy dostępności, ustawienie Strefa dostępności ma domyślnie wartość *Strefowo* nadmiarowe. Aby uzyskać więcej informacji na temat stref dostępności, zobacz **Ustawienie strefy** dostępności. Standardowa sku jest wymagana w przypadku skojarzenia adresu z standardowym równoważeniem obciążenia. Aby dowiedzieć się więcej na temat standardowych usług równoważenia obciążenia, zobacz [Azure load balancer standard SKU (Standardowa sku usługi Azure Load Balancer).](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](./network-security-groups-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.|
   |Warstwa|Tak|Wskazuje, czy adres IP jest skojarzony z regionem **(region)** czy jest "emisji dowolnej" z wielu regionów **(globalny).** *Należy pamiętać, że adres IP w warstwie* globalnej to funkcja w wersji zapoznawczej dla adresów IP w warstwie Standardowa, która jest obecnie wykorzystywana tylko w przypadku adresów IP w Load Balancer.|
   |Nazwa|Tak|Nazwa musi być unikatowa w obrębie wybranej grupy zasobów.|
   |Przypisywanie adresu IP|Tak|**Dynamiczny:** Adresy dynamiczne są przypisywane dopiero wtedy, gdy publiczny adres IP jest skojarzony z zasobem platformy Azure i zasób jest uruchomiony po raz pierwszy. Adresy dynamiczne mogą ulec zmianie, jeśli zostaną przypisane do zasobu, takiego jak maszyna wirtualna, i maszyna wirtualna zostanie zatrzymana (cofnie przydział), a następnie ponownie uruchomiona. Adres pozostaje taki sam, jeśli maszyna wirtualna zostanie ponownie uruchomiona lub zatrzymana (ale nie cofniesz jej alokacji). Adresy dynamiczne są zwalniane, gdy zasób publicznego adresu IP jest nieskojarzony z zasobem, z którym jest skojarzony. **Statyczne:** Adresy statyczne są przypisywane podczas tworzenia publicznego adresu IP. Adresy statyczne są zwalniane dopiero po usunięciu zasobu publicznego adresu IP. Jeśli adres nie jest skojarzony z zasobem, możesz zmienić metodę przypisania po utworzeniu adresu. Jeśli adres jest skojarzony z zasobem, zmiana metody przypisania może nie być możliwe. W przypadku wybrania *protokołu IPv6* dla wersji **adresu IP** metoda przypisania musi być *dynamiczna* dla podstawowej wersji SKU.  Standardowe adresy SKU są *statyczne zarówno* dla protokołu IPv4, jak i IPv6. |
   |Limit czasu bezczynności (minuty)|Nie|Ile minut ma być otwarte połączenie TCP lub HTTP bez konieczności wysyłania komunikatów keep-alive przez klientów. W przypadku wybrania protokołu IPv6 jako **wersji adresu IP** nie można zmienić tej wartości. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w obrębie lokalizacji platformy Azure, w ramach których zostanie tworzymy nazwę (dla wszystkich subskrypcji i wszystkich klientów). Platforma Azure automatycznie rejestruje nazwę i adres IP w systemie DNS, aby można było nawiązać połączenie z zasobem o nazwie . Platforma Azure dołącza domyślną podsieć, taką *jak location.cloudapp.azure.com* (gdzie lokalizacja to wybierana lokalizacja) do podanej nazwy, aby utworzyć w pełni kwalifikowaną nazwę DNS. Jeśli zdecydujesz się utworzyć obie wersje adresów, ta sama nazwa DNS jest przypisywana zarówno do adresów IPv4, jak i IPv6. Domyślny serwer DNS platformy Azure zawiera rekordy nazw A i IPv6 AAAA IPv4 i IPv6 oraz odpowiada przy użyciu obu rekordów podczas ich rozpoznawania. Klient wybiera adres (IPv4 lub IPv6), z którym ma się komunikować. Zamiast lub oprócz używania etykiety nazwy DNS z domyślnym sufiksem możesz użyć usługi Azure DNS do skonfigurowania nazwy DNS z sufiksem niestandardowym, który jest rozpoznawany jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS z publicznym adresem IP platformy Azure)](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nazwa (widoczna tylko w przypadku wybrania wersji obu **adresów** IP)|Tak, w przypadku wybrania opcji Wersja adresu IP **obu**|Nazwa musi być inna niż nazwa w wprowadzana dla pierwszej **nazwy** na tej liście. Jeśli zdecydujesz się utworzyć zarówno adres IPv4, jak i IPv6, portal utworzy dwa oddzielne zasoby publicznego adresu IP, po jednym z przypisanymi do niego wersjami adresów IP.|
   |Przypisanie adresu IP (widoczne tylko w przypadku wybrania opcji Wersja adresu IP **dla obu adresów**)|Tak, w przypadku wybrania opcji Wersja adresu IP **obu**|Ograniczenia takie same jak w powyższym przypisaniu adresu IP|
   |Subskrypcja|Tak|Musi istnieć w tej [samej subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, z którym skojarzysz publiczny adres IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) zasobów co zasób, z którym skojarzysz publiczny adres IP.|
   |Lokalizacja|Tak|Musi istnieć w [tej](https://azure.microsoft.com/regions)samej lokalizacji , określanej także jako region, jako zasób, z którym skojarzysz publiczny adres IP.|
   |Strefa dostępności| Nie | To ustawienie jest wyświetlane tylko w przypadku wybrania obsługiwanej lokalizacji. Aby uzyskać listę obsługiwanych lokalizacji, zobacz [Omówienie stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wyboru podstawowej **wersji** SKU *automatycznie* wybrana jest opcja Brak. Jeśli wolisz zagwarantować konkretną strefę, możesz wybrać określoną strefę. Wybór nie jest strefowo nadmiarowy. W przypadku wyboru **standardowej jednostki** SKU: opcja Strefowo nadmiarowa jest automatycznie wybierana i sprawia, że ścieżka danych jest odporna na awarie strefy. Jeśli wolisz zagwarantować konkretną strefę, która nie jest odporna na awarie strefy, możesz wybrać określoną strefę.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Wyświetlanie, modyfikowanie ustawień publicznego adresu IP lub usuwanie go

   - **Widok/lista:** aby przejrzeć ustawienia dla publicznego adresu IP, w tym sku, adres, wszelkie odpowiednie skojarzenia (np. karta sieciowa maszyny wirtualnej, Load Balancer frontonie).
   - **Modyfikowanie:** Aby zmodyfikować ustawienia, korzystając z informacji w kroku 4 tworzenia publicznego adresu [IP,](#create-a-public-ip-address)takich jak limit czasu bezczynności, etykieta nazwy DNS lub metoda przypisania.  (Aby uzyskać pełny proces uaktualniania publicznej wersji SKU adresu IP z podstawowej do standardowej, zobacz [Upgrade Azure public IP addresses (Uaktualnianie publicznych adresów IP platformy Azure).](./virtual-network-public-ip-address-upgrade.md)
   >[!WARNING]
   >Aby zmienić przypisanie publicznego adresu IP ze statycznego na dynamiczny, należy najpierw usunąć skojarzenie adresu z dowolnymi konfiguracjami adresów IP (zobacz **sekcję Usuwanie).**  Ponadto zmiana metody przypisania ze statycznej na dynamiczną oznacza utratę adresu IP przypisanego do publicznego adresu IP. Podczas gdy publiczne serwery DNS platformy Azure utrzymują mapowanie między adresami statycznymi lub dynamicznymi i dowolną etykietą nazwy DNS (jeśli ją zdefiniowano), dynamiczny adres IP może ulec zmianie, gdy maszyna wirtualna zostanie uruchomiona po zatrzymaniu (cofniu alokacji). Aby zapobiec zmianie adresu, przypisz statyczny adres IP.
   
|Operacja|Azure Portal|Azure PowerShell|Interfejs wiersza polecenia platformy Azure|
|---|---|---|---|
|Widok | W sekcji **Przegląd** publicznego adresu IP |[Get-AzPublicIpAddress,](/powershell/module/az.network/get-azpublicipaddress) aby pobrać obiekt publicznego adresu IP i wyświetlić jego ustawienia| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) to show settings|
|Lista | W kategorii **Publiczne adresy IP** |[Get-AzPublicIpAddress,](/powershell/module/az.network/get-azpublicipaddress) aby pobrać co najmniej jeden obiekt publicznego adresu IP i wyświetlić jego ustawienia|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) to list public IP addresses|
|Modyfikowanie | W przypadku nieskojarzonych adresów  IP wybierz pozycję Konfiguracja, aby zmodyfikować limit czasu bezczynności, etykietę nazwy DNS lub zmienić przypisanie podstawowego adresu IP ze statycznego na dynamiczny  |[Set-AzPublicIpAddress w](/powershell/module/az.network/set-azpublicipaddress) celu zaktualizowania ustawień |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) to update |

   - **Usuń:** usunięcie publicznych adresów IP wymaga, aby obiekt publicznego adresu IP nie był skojarzony z konfiguracją adresu IP ani kartą sieciową maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz tabelę poniżej.

|Zasób|Azure Portal|Azure PowerShell|Interfejs wiersza polecenia platformy Azure|
|---|---|---|---|
|[Maszyna wirtualna](./remove-public-ip-address-vm.md)|Wybierz **pozycję Usuń skojarzenie,** aby usunąć skojarzenie adresu IP z konfiguracji karty sieciowej, a następnie wybierz pozycję **Usuń**.|[Set-AzPublicIpAddress,](/powershell/module/az.network/set-azpublicipaddress) aby skojarzyć adres IP z konfiguracji karty sieciowej; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia|[az network public-ip update --remove,](/cli/azure/network/public-ip#az_network_public_ip_update) aby usunąć skojarzenie adresu IP z konfiguracją karty sieciowej; [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) to delete |
|Load Balancer frontonie | Przejdź do nieużywanego publicznego adresu  IP i wybierz pozycję Skojarz i wybierz adres Load Balancer z odpowiednią konfiguracją adresu IP frontona, aby go zastąpić (stary adres IP można usunąć przy użyciu tej samej metody co w przypadku maszyny wirtualnej)  | [Set-AzLoadBalancerFrontendIpConfig,](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) aby skojarzyć nową konfigurację adresu IP frontendu z publicznym Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia; Może również użyć [polecenie Remove-AzLoadBalancerFrontendIpConfig,](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) aby usunąć konfigurację adresu IP frontendu, jeśli istnieje więcej niż jedna |[az network lb frontend-ip update,](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) aby skojarzyć nową konfigurację adresu IP frontendu z publicznym Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia; Może również użyć [az network lb frontend-ip delete,](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) aby usunąć konfigurację adresu IP frontendu, jeśli istnieje więcej niż jedna|
|Firewall|Nie dotyczy| [Cofają alokację zapory i](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) usuwają wszystkie konfiguracje adresów IP | [az network firewall ip-config delete, aby](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) usunąć adres IP (ale najpierw należy cofnij alokację przy użyciu programu PowerShell)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

W przypadku używania zestawu skalowania maszyn wirtualnych z publicznymi adresami IP nie ma oddzielnych obiektów publicznych adresów IP skojarzonych z poszczególnymi wystąpieniami maszyn wirtualnych. Jednak obiektu Prefiks publicznego adresu IP [można użyć do wygenerowania adresów IP wystąpienia](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Aby wyświetlić listę publicznych adresów IP w zestawie skalowania maszyn wirtualnych, możesz użyć programu PowerShell[(Get-AzPublicIpAddress -VirtualMachineScaleSetName)](/powershell/module/az.network/get-azpublicipaddress)lub interfejsu wiersza polecenia[(az vmss list-instance-public-ips).](/cli/azure/vmss#az_vmss_list_instance_public_ips)

Aby uzyskać więcej informacji, zobacz [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Przypisywanie publicznego adresu IP

Dowiedz się, jak przypisać publiczny adres IP do następujących zasobów:

- Maszyna [wirtualna](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z systemem [Windows lub Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (podczas tworzenia) lub do [istniejącej maszyny wirtualnej](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Publiczny Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Połączenie lokacja-lokacja przy użyciu VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania na publicznych adresach IP, [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) Twoje konto musi [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) być przypisane do roli współautora sieci lub roli niestandardowej, która ma przypisane odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                             | Nazwa                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Odczytywanie publicznego adresu IP                                          |
| Microsoft.Network/publicIPAddresses/write                          | Tworzenie lub aktualizowanie publicznego adresu IP                           |
| Microsoft.Network/publicIPAddresses/delete                         | Usuwanie publicznego adresu IP                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Kojarzenie publicznego adresu IP z zasobem                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie publicznego adresu IP przy użyciu [przykładowych skryptów programu PowerShell](powershell-samples.md) lub interfejsu wiersza polecenia platformy [Azure](cli-samples.md) albo szablonów Resource Manager [Azure](template-samples.md)
- Tworzenie i [przypisywanie Azure Policy dla](./policy-reference.md) publicznych adresów IP
