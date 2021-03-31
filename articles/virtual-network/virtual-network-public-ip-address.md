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
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934943"
---
# <a name="manage-public-ip-addresses"></a>Zarządzanie publicznymi adresami IP

Dowiedz się więcej o publicznym adresie IP oraz o sposobie tworzenia, zmieniania i usuwania jednego z nich. Publiczny adres IP to zasób z własnymi konfigurowalnymi ustawieniami. Przypisanie publicznego adresu IP do zasobu platformy Azure, który obsługuje publiczne adresy IP, umożliwia:
- Komunikacja przychodząca z Internetu do zasobu, taka jak Azure Virtual Machines (maszyna wirtualna), Azure Application Gateways, Azure Load Gateways, Azure sieci VPN i inne. Można nadal komunikować się z niektórymi zasobami, takimi jak maszyny wirtualne, z Internetu, jeśli maszyna wirtualna nie ma przypisanego publicznego adresu IP, pod warunkiem, że maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, a moduł równoważenia obciążenia ma przypisany publiczny adres IP. Aby określić, czy zasób dla określonej usługi platformy Azure może mieć przypisany publiczny adres IP, czy też może być przekazywany za pomocą publicznego adresu IP innego zasobu platformy Azure, zapoznaj się z dokumentacją usługi.
- Łączność wychodząca z Internetem przy użyciu przewidywalnego adresu IP. Na przykład maszyna wirtualna może komunikować się z Internetem bez przypisanego do niego publicznego adresu IP, ale jego adres jest domyślnie przetłumaczony przez platformę Azure do nieprzewidywalnego adresu publicznego. Przypisanie publicznego adresu IP do zasobu pozwala dowiedzieć się, który adres IP jest używany do połączenia wychodzącego. Chociaż przewidywalna, adres może się zmienić, w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, zobacz [Tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby dowiedzieć się więcej o połączeniach wychodzących z zasobów platformy Azure, zobacz [Omówienie połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

Publiczne adresy IP mają opłata nominalną. Aby wyświetlić Cennik, zapoznaj się ze stroną [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Aby uzyskać instrukcje dotyczące sposobu tworzenia publicznych adresów IP przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia, zobacz następujące strony:

 * [Tworzenie publicznych adresów IP — portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Tworzenie publicznych adresów IP — program PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Tworzenie publicznych adresów IP — interfejs wiersza polecenia platformy Azure](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Mimo że Portal udostępnia opcję tworzenia dwóch zasobów publicznego adresu IP (jeden IPv4 i jeden IPv6), polecenia programu PowerShell i interfejsu CLI tworzą jeden zasób z adresem dla jednej wersji adresu IP. Jeśli potrzebujesz dwóch zasobów publicznego adresu IP, po jednym dla każdej wersji IP, musisz uruchomić polecenie dwa razy, określając różne nazwy i wersje IP dla zasobów publicznego adresu IP.

Dodatkowe szczegóły dotyczące określonych atrybutów publicznego adresu IP podczas tworzenia znajdują się w poniższej tabeli.

   |Ustawienie|Wymagane?|Szczegóły|
   |---|---|---|
   |Wersja protokołu IP|Tak| Wybierz pozycję IPv4 lub IPv6 lub oba te elementy. Wybranie obu z nich spowoduje utworzenie 2 publicznych adresów IP z 1 adresem IPv4 i 1 adres IPv6. Dowiedz się więcej o [protokole IPv6 w usłudze Azure sieci wirtualnych](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Tak|Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostek SKU są publicznymi adresami IP **podstawowej** jednostki SKU. Nie można zmienić jednostki SKU po utworzeniu publicznego adresu IP. Autonomiczna maszyna wirtualna, maszyny wirtualne w zestawie dostępności lub zestawy skalowania maszyn wirtualnych mogą korzystać z podstawowych lub standardowych jednostek SKU. Mieszanie jednostek SKU między maszynami wirtualnymi w ramach zestawów dostępności lub zestawów skalowania lub autonomicznych maszyn wirtualnych jest niedozwolone. **Podstawowa** Jednostka SKU: Jeśli tworzysz publiczny adres IP w regionie, który obsługuje strefy dostępności, ustawienie **strefy dostępności** domyślnie ma wartość *Brak* . Podstawowe publiczne adresy IP nie obsługują stref dostępności. **Standard** Jednostka SKU: publiczny adres IP jednostki SKU można skojarzyć z maszyną wirtualną lub frontonem modułu równoważenia obciążenia. Jeśli tworzysz publiczny adres IP w regionie, który obsługuje strefy dostępności, ustawienie **strefy dostępności** jest domyślnie ustawione na *strefowo nadmiarowe* . Aby uzyskać więcej informacji na temat stref dostępności, zobacz Ustawienie **strefy dostępności** . Standardowa jednostka SKU jest wymagana, jeśli adres jest skojarzony z usługą równoważenia obciążenia w warstwie Standardowa. Aby dowiedzieć się więcej na temat standardowych modułów równoważenia obciążenia, zobacz [standardowa jednostka SKU modułu równoważenia obciążenia platformy Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](./network-security-groups-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.|
   |Warstwa|Tak|Wskazuje, czy adres IP jest skojarzony z regionem (**regionalny**), czy jest "emisją emisji" z wielu regionów (**globalna**). *Należy zauważyć, że adres IP "warstwa globalna" jest funkcją w wersji zapoznawczej dla standardowych adresów IP i obecnie jest używany tylko dla Load Balancer Międzyregionowych*.|
   |Nazwa|Tak|Nazwa musi być unikatowa w ramach wybranej grupy zasobów.|
   |Przypisanie adresu IP|Tak|**Dynamiczne:** Adresy dynamiczne są przypisywane tylko po skojarzeniu publicznego adresu IP z zasobem platformy Azure, a zasób jest uruchamiany po raz pierwszy. Adresy dynamiczne mogą ulec zmianie, jeśli są przypisane do zasobu, takiego jak maszyna wirtualna, a maszyna wirtualna jest zatrzymana (cofnięto przydział), a następnie ponownie uruchomiona. Adres pozostaje taki sam, jeśli maszyna wirtualna jest ponownie uruchamiana lub zatrzymana (ale nie została cofnięta alokacja). Adresy dynamiczne są uwalniane, gdy zasób publicznego adresu IP jest usuwany z zasobu, z którym jest skojarzony. **Statyczny:** Adresy statyczne są przypisywane po utworzeniu publicznego adresu IP. Adresy statyczne nie są wydane, dopóki nie zostanie usunięty zasób publicznego adresu IP. Jeśli adres nie jest skojarzony z zasobem, można zmienić metodę przypisywania po utworzeniu adresu. Jeśli adres jest skojarzony z zasobem, zmiana metody przypisania może nie być możliwa. W przypadku wybrania *protokołu IPv6* dla **wersji IP**, Metoda przypisania musi być *dynamiczna* dla podstawowej jednostki SKU.  Standardowe adresy jednostki SKU są *statyczne* dla protokołów IPv4 i IPv6. |
   |Limit czasu bezczynności (minuty)|Nie|Liczba minut utrzymywania otwartego połączenia TCP lub HTTP bez polegania na klientach do wysyłania komunikatów Keep-Alive. Jeśli wybierzesz opcję IPv6 dla **wersji IP**, nie można zmienić tej wartości. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w lokalizacji platformy Azure, w ramach której tworzysz nazwę w (między wszystkimi subskrypcjami i wszystkimi klientami). Platforma Azure automatycznie rejestruje nazwę i adres IP w systemie DNS, aby można było połączyć się z zasobem o nazwie. System Azure dołącza domyślną podsieć, taką jak *Location.cloudapp.Azure.com* (lokalizacja jest wybieraną lokalizacją) do podania nazwy, aby utworzyć w pełni KWALIFIKOWANĄ nazwę DNS. Jeśli zdecydujesz się utworzyć obie wersje adresów, ta sama nazwa DNS jest przypisywana do adresów IPv4 i IPv6. Domyślna usługa DNS platformy Azure zawiera rekordy nazw i adresów IPv4 a i IPv6 oraz odpowiada przy użyciu obu rekordów podczas wyszukiwania nazwy DNS. Klient wybiera adres (IPv4 lub IPv6) do komunikacji. Zamiast lub oprócz używania etykiety nazwy DNS z domyślnym sufiksem możesz użyć usługi Azure DNS do skonfigurowania nazwy DNS z sufiksem niestandardowym, który jest rozpoznawany jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS z publicznym adresem IP platformy Azure)](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nazwa (widoczne tylko w przypadku wybrania opcji wersja IP **obu**)|Tak, w przypadku wybrania opcji wersja IP **obu**|Nazwa musi być inna niż nazwa wprowadzona dla pierwszej **nazwy** na tej liście. Jeśli zdecydujesz się utworzyć adres IPv4 i IPv6, Portal tworzy dwa oddzielne zasoby publicznych adresów IP, po jednym z przypisanymi do nich wersjami adresów IP.|
   |Przypisanie adresu IP (widoczne tylko w przypadku wybrania wersji IP **obu**)|Tak, w przypadku wybrania opcji wersja IP **obu**|Te same ograniczenia dotyczące przypisywania adresów IP powyżej|
   |Subskrypcja|Tak|Musi znajdować się w tej samej [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) co zasób, do którego zostanie skojarzony publiczny adres IP.|
   |Grupa zasobów|Tak|Może istnieć w tej samej lub innej [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) co zasób, do którego zostanie skojarzony publiczny adres IP.|
   |Lokalizacja|Tak|Musi znajdować się w tej samej [lokalizacji](https://azure.microsoft.com/regions), nazywanej również regionem, jako zasób, do którego zostanie skojarzony publiczny adres IP.|
   |Strefa dostępności| Nie | To ustawienie jest wyświetlane tylko w przypadku wybrania obsługiwanej lokalizacji. Aby zapoznać się z listą obsługiwanych lokalizacji, zobacz temat [strefy dostępności — Omówienie](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wybrania **podstawowej** jednostki SKU *żadna wartość nie* jest automatycznie wybierana. Jeśli wolisz zagwarantować określoną strefę, możesz wybrać określoną strefę. Dowolny z nich nie jest strefowo nadmiarowy. W przypadku wybrania **standardowej** jednostki SKU: Strefa nadmiarowa jest automatycznie wybierana dla Ciebie i sprawia, że ścieżka danych jest odporna na awarie strefy. Jeśli wolisz zagwarantować określoną strefę, która nie jest odporna na awarię strefy, możesz wybrać określoną strefę.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Wyświetlanie, modyfikowanie ustawień lub usuwanie publicznego adresu IP

   - **Wyświetl/** Wyświetl: Aby przejrzeć ustawienia dla publicznego adresu IP, w tym jednostkę SKU, adres, wszelkie odpowiednie skojarzenia (np. karty sieciowe maszyn wirtualnych, Load Balancer frontonu).
   - **Modyfikowanie**: Aby zmodyfikować ustawienia za pomocą informacji w kroku 4 [Tworzenie publicznego adresu IP](#create-a-public-ip-address), takich jak limit czasu bezczynności, etykieta nazwy DNS lub metoda przypisywania.  (Aby uzyskać pełny proces uaktualniania jednostki SKU publicznego adresu IP z warstwy Podstawowa do standardowa, zobacz [uaktualnianie publicznych adresów IP platformy Azure](./virtual-network-public-ip-address-upgrade.md)).
   >[!WARNING]
   >Aby zmienić przydział dla publicznego adresu IP z statycznej na dynamiczny, należy najpierw usunąć skojarzenie adresu z dowolnymi stosownymi konfiguracjami adresów IP (zobacz sekcję **usuwanie** sekcji).  Należy również pamiętać, że w przypadku zmiany metody przypisywania z statycznej na dynamiczny zostanie utracony adres IP przypisany do publicznego adresu IP. Chociaż publiczne serwery DNS platformy Azure zachowują mapowanie między adresami statycznymi lub dynamicznymi oraz dowolną etykietą nazw DNS (jeśli została zdefiniowana), dynamiczny adres IP może ulec zmianie, gdy maszyna wirtualna jest uruchomiona po zatrzymaniu (bez przydziału). Aby zapobiec zmienianiu adresu, przypisz statyczny adres IP.
   
|Operacja|Azure Portal|Azure PowerShell|Interfejs wiersza polecenia platformy Azure|
|---|---|---|---|
|Widok | Sekcja z **omówieniem** publicznego adresu IP |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) , aby pobrać obiekt publicznego adresu IP i wyświetlić jego ustawienia| [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show) to show Settings|
|Lista | W kategorii **publiczne adresy IP** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) , aby pobrać jeden lub więcej obiektów publicznych adresów IP i wyświetlić jego ustawienia|[AZ Network Public-IP list](/cli/azure/network/public-ip#az-network-public-ip-list) , aby wyświetlić listę publicznych adresów IP|
|Modyfikowanie | W przypadku nieskojarzenia adresu IP wybierz pozycję **Konfiguracja** , aby zmodyfikować limit czasu bezczynności, nazwę DNS lub zmienić przypisanie podstawowego adresu IP z statycznej na dynamiczny  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) w celu zaktualizowania ustawień |[AZ Network Public-IP Update](/cli/azure/network/public-ip#az-network-public-ip-update) to Update |

   - **Usuwanie**: usuwanie publicznych adresów IP wymaga, aby publiczny obiekt adresu nie był skojarzony z żadną konfiguracją IP lub kartą sieciową maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz poniższą tabelę.

|Zasób|Azure Portal|Azure PowerShell|Interfejs wiersza polecenia platformy Azure|
|---|---|---|---|
|[Maszyna wirtualna](./remove-public-ip-address-vm.md)|Wybierz opcję Usuń **skojarzenie** , aby usunąć skojarzenie adresu IP z konfiguracji karty sieciowej, a następnie wybierz pozycję **Usuń**.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) , aby usunąć skojarzenie adresu IP z konfiguracji karty sieciowej; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia|[AZ Network Public-IP Update--Usuń](/cli/azure/network/public-ip#az-network-public-ip-update) , aby usunąć skojarzenie adresu IP z konfiguracji karty sieciowej; [AZ Network Public-IP Delete](/cli/azure/network/public-ip#az-network-public-ip-delete) do usuwania |
|Load Balancer frontonu | Przejdź do nieużywanego publicznego adresu IP i wybierz opcję **Skojarz** i wybierz Load Balancer z odpowiednią konfiguracją adresu IP frontonu, aby ją zastąpić (następnie stary adres IP można usunąć przy użyciu tej samej metody jak w przypadku maszyny wirtualnej).  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) , aby skojarzyć nową konfigurację adresu IP frontonu z publiczną Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia; można również użyć [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) , aby usunąć konfigurację adresu IP frontonu, jeśli istnieje więcej niż jedna |[AZ Network lb fronton-IP Update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) w celu skojarzenia nowej konfiguracji adresu IP frontonu z publiczną Load Balancer; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia; można również użyć [AZ Network lb frontonu IP Delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) , aby usunąć konfigurację adresu IP frontonu, jeśli istnieje więcej niż jeden|
|Firewall|Nie dotyczy| [Cofnij przydział ()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) , aby cofnąć przydział zapory i usunąć wszystkie konfiguracje protokołu IP | [AZ Network firewall IP-config Delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) aby usunąć adres IP (ale należy najpierw użyć programu PowerShell do cofnięcia przydziału)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Gdy korzystasz z zestawu skalowania maszyn wirtualnych z publicznymi adresami IP, nie ma żadnych oddzielnych publicznych obiektów dochodzenia skojarzonych z wystąpieniami poszczególnych maszyn wirtualnych. Jednak publiczny obiekt prefiksu adresu IP [może być używany do generowania adresów IP wystąpienia](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Aby wyświetlić listę publicznych adresów IP w zestawie skalowania maszyn wirtualnych, można użyć programu PowerShell ([Get-AzPublicIpAddress-VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) lub interfejsu wiersza polecenia ([AZ VMSS list-instance-Public-IP](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Aby uzyskać więcej informacji, zobacz [Obsługa sieci w kontekście zestawów skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Przypisywanie publicznego adresu IP

Dowiedz się, jak przypisać publiczny adres IP do następujących zasobów:

- Maszyna wirtualna z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (podczas tworzenia) lub do [istniejącej maszyny wirtualnej](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Load Balancer publiczny](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Połączenie typu lokacja-lokacja przy użyciu VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące publicznych adresów IP, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisane są odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                             | Nazwa                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Odczytaj publiczny adres IP                                          |
| Microsoft.Network/publicIPAddresses/write                          | Utwórz lub zaktualizuj publiczny adres IP                           |
| Microsoft. Network/adresów publicipaddress/Delete                         | Usuwanie publicznego adresu IP                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Kojarzenie publicznego adresu IP z zasobem                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie publicznego adresu IP przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Utwórz i przypisz [definicje Azure Policy](./policy-reference.md) dla publicznych adresów IP
