---
title: Tworzenie, zmienianie lub usuwanie tabeli tras platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, gdzie znaleźć informacje o routingu ruchu w sieci wirtualnej oraz jak utworzyć, zmienić lub usunąć tabelę tras.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574123"
---
# <a name="create-change-or-delete-a-route-table"></a>Tworzenie, zmienianie i usuwanie tabeli routingu

Platforma Azure automatycznie kieruje ruchem między podsieciami platformy Azure, sieciami wirtualnymi i sieciami lokalnymi. Jeśli chcesz zmienić domyślny Routing systemu Azure, możesz to zrobić, tworząc tabelę tras. Jeśli jesteś nowym sposobem routingu w sieciach wirtualnych, możesz dowiedzieć się więcej na jego temat w obszarze [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md) lub przez zakończenie korzystania z [samouczka](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w każdej sekcji tego artykułu:

- **Użytkownicy portalu**: Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell**: Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub Uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell Browser Znajdź listę rozwijaną **Wybierz środowisko** , a następnie wybierz opcję **PowerShell** , jeśli nie została jeszcze wybrana.

    Jeśli używasz programu PowerShell lokalnie, użyj Azure PowerShell module w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom również `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI)**: uruchamiają polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiają interfejs CLI z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom również `az login` , aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do [roli współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Istnieje ograniczenie dotyczące liczby tabel tras, które można utworzyć na potrzeby lokalizacji i subskrypcji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [limity sieci — Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wprowadź *tabelę tras*. Gdy **tabela tras** zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.

1. Na stronie **tabela tras** wybierz pozycję **Utwórz**.

1. W oknie dialogowym **Tworzenie tabeli tras** :

    1. Wprowadź **nazwę** tabeli tras.
    1. Wybierz **subskrypcję**.
    1. Wybierz istniejącą **grupę zasobów** lub wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów.
    1. Wybierz **lokalizację**.
    1. Jeśli planujesz skojarzenie tabeli tras z podsiecią z siecią wirtualną, która jest połączona z siecią lokalną za pośrednictwem bramy sieci VPN, i nie chcesz propagować tras lokalnych do interfejsów sieciowych w podsieci, ustaw **propagację tras bramy sieci wirtualnej** na **wyłączone**.

1. Wybierz pozycję **Utwórz** , aby utworzyć nową tabelę tras.

### <a name="create-route-table---commands"></a>Tworzenie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Wyświetlanie tabel tras

Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **tabele tras**. Zostaną wyświetlone tabele tras, które istnieją w Twojej subskrypcji.

### <a name="view-route-table---commands"></a>Wyświetlanie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table list](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Wyświetlanie szczegółów tabeli tras

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras, dla której chcesz wyświetlić szczegóły.

1. Na stronie tabela tras w obszarze **Ustawienia** Przejrzyj **trasy** w tabeli tras lub **podsieci** , z którymi jest skojarzona tabela tras.

Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure, zobacz następujące informacje:

- [Dziennik aktywności](../azure-monitor/essentials/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Wyświetl szczegóły trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Zmiana tabeli tras

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras, którą chcesz zmienić.

Najbardziej typowe zmiany polegają na [dodawaniu](#create-a-route) tras, [usunięciu](#delete-a-route) tras, [kojarzeniu](#associate-a-route-table-to-a-subnet) tabel tras z podsieciami lub [skojarzeniu](#dissociate-a-route-table-from-a-subnet) tabel tras z podsieciami.

### <a name="change-a-route-table---commands"></a>Zmienianie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Opcjonalnie możesz skojarzyć tabelę tras z podsiecią. Tabela tras może być skojarzona z zerem lub wieloma podsieciami. Ponieważ tabele tras nie są skojarzone z sieciami wirtualnymi, należy skojarzyć tabelę tras z każdą podsiecią, z którą ma być skojarzona tabela tras. Platforma Azure kieruje cały ruch wychodzący z podsieci w oparciu o trasy utworzone w tabelach tras, [trasy domyślne](virtual-networks-udr-overview.md#default)i trasy propagowane z sieci lokalnej, jeśli sieć wirtualna jest połączona z bramą sieci wirtualnej platformy Azure (ExpressRoute lub sieci VPN). Tabelę tras można skojarzyć tylko z podsieciami w sieciach wirtualnych, które istnieją w tej samej lokalizacji i subskrypcji platformy Azure co tabela tras.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **sieci wirtualne**.

1. Z listy Sieć wirtualna wybierz sieć wirtualną zawierającą podsieć, do której chcesz skojarzyć tabelę tras.

1. Na pasku menu sieci wirtualnej wybierz **podsieć**.

1. Wybierz podsieć, do której chcesz skojarzyć tabelę tras.

1. W **tabeli tras** wybierz tabelę tras, która ma zostać skojarzona z podsiecią.

1. Wybierz pozycję **Zapisz**.

Jeśli sieć wirtualna jest połączona z bramą sieci VPN platformy Azure, nie należy kojarzyć tabeli tras z [podsiecią bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) , która zawiera trasę z miejscem docelowym równym *0.0.0.0/0*. Zaniedbanie tego może spowodować nieprawidłowe działanie bramy. Aby uzyskać więcej informacji o używaniu *0.0.0.0/0* w trasie, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Kojarzenie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Usuwanie skojarzenia tabeli tras z podsiecią

Po skojarzeniu tabeli tras z podsiecią platforma Azure kieruje ruchem na podstawie jego [domyślnych tras](virtual-networks-udr-overview.md#default).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **sieci wirtualne**.

1. Z listy Sieć wirtualna wybierz sieć wirtualną zawierającą podsieć, z której chcesz usunąć skojarzenie tabeli tras.

1. Na pasku menu sieci wirtualnej wybierz **podsieć**.

1. Wybierz podsieć, z której chcesz usunąć skojarzenie tabeli tras.

1. W **tabeli tras** wybierz opcję **Brak**.

1. Wybierz pozycję **Zapisz**.

### <a name="dissociate-a-route-table---commands"></a>Usuwanie skojarzenia tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Usuwanie tabeli tras

Nie można usunąć tabeli tras, która jest skojarzona z dowolnymi podsieciami. [Usuń skojarzenie](#dissociate-a-route-table-from-a-subnet) tabeli tras z wszystkimi podsieciami, zanim spróbujesz ją usunąć.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras, która ma zostać usunięta.

1. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route-table---commands"></a>Usuwanie tabeli tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Delete](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Tworzenie trasy

Istnieje ograniczenie, ile tras na tabelę tras można utworzyć dla lokalizacji i subskrypcji platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [limity sieci — Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras, do której chcesz dodać trasę.

1. Na pasku menu tabeli tras wybierz pozycję **trasy**  >  **Dodaj**.

1. Wprowadź unikatową **nazwę trasy** dla trasy w tabeli tras.

1. Wprowadź **prefiks adresu** w notacji CIDR (Classless Inter-Domain Routing), do której chcesz kierować ruchem. Nie można zduplikować prefiksu w więcej niż jednej trasie w tabeli tras, ale prefiks może znajdować się w innym prefiksie. Na przykład jeśli zdefiniowano *10.0.0.0/16* jako prefiks w jednej trasie, nadal można zdefiniować inną trasę z prefiksem adresu *10.0.0.0/22* . Platforma Azure wybiera trasę dla ruchu na podstawie najdłuższych pasujących prefiksów. Aby dowiedzieć się więcej, zobacz [jak platforma Azure wybiera trasę](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Wybierz **Typ następnego przeskoku**. Aby dowiedzieć się więcej na temat typów następnego przeskoku, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

1. W przypadku wybrania **typu następnego przeskoku** **urządzenia wirtualnego** wprowadź adres IP dla **adresu następnego przeskoku**.

1. Wybierz przycisk **OK**.

### <a name="create-a-route---commands"></a>Tworzenie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Wyświetlanie tras

Tabela tras zawiera zero lub więcej tras. Aby dowiedzieć się więcej na temat informacji wyświetlanych podczas wyświetlania tras, zobacz [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras, dla której chcesz wyświetlić trasy.

1. Na pasku menu tabeli tras wybierz pozycję **trasy** , aby wyświetlić listę tras.

### <a name="view-routes---commands"></a>Wyświetlanie tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Route list](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras zawierającą trasę, dla której chcesz wyświetlić szczegóły.

1. Na pasku menu tabeli tras wybierz pozycję **trasy** , aby wyświetlić listę tras.

1. Wybierz trasę, dla której chcesz wyświetlić szczegóły.

### <a name="view-details-of-a-route---commands"></a>Wyświetlanie szczegółów dotyczących tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Zmienianie trasy

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras zawierającą trasę, którą chcesz zmienić.

1. Na pasku menu tabeli tras wybierz pozycję **trasy** , aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz zmienić.

1. Zmień istniejące ustawienia na nowe ustawienia, a następnie wybierz pozycję **Zapisz**.

### <a name="change-a-route---commands"></a>Zmienianie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Route Update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Usuwanie trasy

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelami tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Na liście tabela tras wybierz tabelę tras zawierającą trasę, którą chcesz usunąć.

1. Na pasku menu tabeli tras wybierz pozycję **trasy** , aby wyświetlić listę tras.

1. Wybierz trasę, którą chcesz usunąć.

1. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** w oknie dialogowym potwierdzenia.

### <a name="delete-a-route---commands"></a>Usuwanie trasy — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network Route-Table Route Delete](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Wyświetl efektywne trasy

Efektywne trasy dla każdego interfejsu sieciowego dołączonego do maszyny wirtualnej są kombinacją utworzonych tabel tras, domyślnych tras platformy Azure oraz wszelkich tras propagowanych z sieci lokalnych za pośrednictwem Border Gateway Protocol (BGP) za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie efektywnych tras dla interfejsu sieciowego jest przydatne podczas rozwiązywania problemów z routingiem. Efektywne trasy można wyświetlić dla dowolnego interfejsu sieciowego dołączonego do uruchomionej maszyny wirtualnej.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać maszynami wirtualnymi. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

1. Z listy maszyn wirtualnych wybierz MASZYNę wirtualną, dla której chcesz wyświetlić obowiązujące trasy.

1. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć**.

1. Wybierz nazwę interfejsu sieciowego.

1. Na pasku menu interfejsu sieciowego wybierz pozycję **efektywne trasy**.

1. Przejrzyj listę efektywnych tras, aby sprawdzić, czy istnieje prawidłowa trasa dla miejsca, do którego chcesz kierować ruchem. Dowiedz się więcej na temat typów następnego przeskoku, które są widoczne na tej liście w obszarze [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Wyświetlanie efektywnych tras — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network nic show-efektywnie-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Weryfikowanie routingu między dwoma punktami końcowymi

Możesz określić typ następnego przeskoku między maszyną wirtualną i adresem IP innego zasobu platformy Azure, zasobem lokalnym lub zasobem w Internecie. Określanie routingu platformy Azure jest pomocne podczas rozwiązywania problemów z routingiem. Aby wykonać to zadanie, musisz mieć istniejący obserwator sieci. Jeśli nie masz istniejącego obserwatora sieciowego, utwórz go, wykonując kroki opisane w temacie [Tworzenie wystąpienia Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać obserwatorami sieciowymi. Wyszukaj i wybierz **Network Watcher**.

1. Na pasku menu obserwatora sieciowego wybierz **Następny przeskok**.

1. W **Network Watcher | Strona następnego przeskoku** :

    1. Wybierz swoją **subskrypcję** i **grupę zasobów** źródłowej maszyny wirtualnej, z której chcesz sprawdzić poprawność routingu.

    1. Wybierz **maszynę wirtualną** i **interfejs sieciowy** , który jest dołączony do maszyny wirtualnej.
    
    1. Wprowadź **źródłowy adres IP** przypisany do interfejsu sieciowego, z którego chcesz sprawdzić poprawność routingu.

    1. Wprowadź **docelowy adres IP** , na który chcesz sprawdzić poprawność routingu.

1. Wybierz **Następny przeskok**.

Po krótkim czasie oczekiwania system Azure poinformuje typ następnego przeskoku i identyfikator trasy, która skierowała ruch. Dowiedz się więcej na temat typów następnego przeskoku, które są zwracane w obszarze [routing ruchu w sieci wirtualnej](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Weryfikowanie routingu między dwoma punktami końcowymi — polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [AZ Network obserwator show-następnym przeskokiem](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące tabel tras i tras, Twoje konto musi być przypisane do [roli współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisanej do odpowiednich czynności wymienionych w poniższej tabeli:

| Akcja                                                          |   Nazwa                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/odczyt                              |   Odczytaj tabelę tras                                    |
| Microsoft. Network/routeTables/Write                             |   Tworzenie lub aktualizowanie tabeli tras                        |
| Microsoft. Network/routeTables/Delete                            |   Usuwanie tabeli tras                                  |
| Microsoft. Network/routeTables/Join/Action                       |   Kojarzenie tabeli tras z podsiecią                   |
| Microsoft. Network/routeTables/Routes/Read                       |   Odczytaj trasę                                          |
| Microsoft. Network/routeTables/Routes/write                      |   Utwórz lub zaktualizuj trasę                              |
| Microsoft. Network/routeTables/Routes/Delete                     |   Usuwanie trasy                                        |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/akcja  |   Pobierz obowiązującą tabelę tras dla interfejsu sieciowego |
| Microsoft. Network/networkWatchers/skoku/akcja                |   Pobiera następny przeskok z maszyny wirtualnej                           |

## <a name="next-steps"></a>Następne kroki

- Tworzenie tabeli tras przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy](cli-samples.md) Azure lub szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i przypisywanie [definicji Azure Policy](./policy-reference.md) dla sieci wirtualnych