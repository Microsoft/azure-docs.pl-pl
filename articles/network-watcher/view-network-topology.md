---
title: Wyświetlanie danych topologii sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlać zasoby w sieci wirtualnej oraz relacje między zasobami.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780303"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Wyświetlanie topologii sieci wirtualnej platformy Azure

Z tego artykułu dowiesz się, jak wyświetlać zasoby w Microsoft Azure wirtualnej oraz relacje między zasobami. Na przykład sieć wirtualna zawiera podsieci. Podsieci zawierają zasoby, takie jak azure Virtual Machines (VM). Maszyny wirtualne mają co najmniej jeden interfejs sieciowy. Każda podsieć może mieć skojarzoną sieciową grupę zabezpieczeń i tabelę tras. Funkcja topologii usługi Azure Network Watcher umożliwia wyświetlanie wszystkich zasobów w sieci wirtualnej, zasobów skojarzonych z zasobami w sieci wirtualnej oraz relacji między zasobami.

Aby wyświetlić [topologię, Azure Portal](#azure-portal)użyć interfejsu wiersza polecenia platformy [Azure](#azure-cli)lub programu [PowerShell.](#powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topologia widoku — Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta z niezbędnymi [uprawnieniami.](required-rbac-permissions.md)
2. W lewym górnym rogu portalu wybierz pozycję **Wszystkie usługi.**
3. W polu **filtru** Wszystkie usługi wprowadź *Network Watcher*. Gdy **Network Watcher** w wynikach, wybierz ją.
4. Wybierz pozycję **Topologia**. Generowanie topologii wymaga usługi Network Watcher w tym samym regionie, w którym istnieje sieć wirtualna, dla której chcesz wygenerować topologię. Jeśli nie masz włączonej usługi Network Watcher w regionie, w którym znajduje się sieć wirtualna, dla której chcesz wygenerować topologię, zostaną one automatycznie utworzone we wszystkich regionach. Usługi Network Watcher są tworzone w grupie zasobów o **nazwie NetworkWatcherRG.**
5. Wybierz subskrypcję, grupę zasobów sieci wirtualnej, dla której chcesz wyświetlić topologię, a następnie wybierz sieć wirtualną. Na poniższej ilustracji przedstawiono topologię sieci wirtualnej o nazwie *MyVnet* w grupie zasobów *o nazwie MyResourceGroup*:

    ![Wyświetlanie topologii](./media/view-network-topology/view-topology.png)

    Jak widać na poprzedniej ilustracji, sieć wirtualna zawiera trzy podsieci. W jednej podsieci jest wdrożona maszyna wirtualna. Do maszyny wirtualnej jest dołączony jeden interfejs sieciowy i skojarzony z nią publiczny adres IP. Pozostałe dwie podsieci mają skojarzoną tabelę tras. Każda tabela tras zawiera dwie trasy. Z jedną podsiecią jest skojarzona sieciowa grupa zabezpieczeń. Informacje o topologii są wyświetlane tylko dla zasobów, które są:
    
    - W tej samej grupie zasobów i regionie co sieć *wirtualna myVnet.* Na przykład sieciowa grupa zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup,* nie jest wyświetlana, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w sieci wirtualnej *MyVnet.*
    - W ramach sieci wirtualnej *myVnet* lub skojarzonej z zasobami w sieci wirtualnej myVnet. Na przykład sieciowa grupa zabezpieczeń, która nie jest skojarzona z podsiecią ani interfejsem sieciowym w sieci wirtualnej *myVnet,* nie jest wyświetlana, nawet jeśli sieciowa grupa zabezpieczeń należy do grupy zasobów *MyResourceGroup.*

   Topologia pokazana na ilustracji jest dla sieci wirtualnej utworzonej po wdrożeniu przykładowego skryptu Route **traffic through a network virtual appliance**(Przekieruj ruch za pośrednictwem skryptu wirtualnego urządzenia sieciowego), który można wdrożyć przy użyciu interfejsu wiersza polecenia platformy [Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)lub programu [PowerShell.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

6. Wybierz **pozycję Pobierz topologię,** aby pobrać obraz jako plik edytowalny w formacie svg.

Zasoby pokazane na diagramie są podzbiorem składników sieciowych w sieci wirtualnej. Na przykład, gdy jest wyświetlana sieciowa grupa zabezpieczeń, reguły zabezpieczeń w tej grupie nie są wyświetlane na diagramie. Chociaż nie rozróżnia się na diagramie, wiersze reprezentują jedną z dwóch relacji: *Zawieranie lub* *skojarzone*. Aby wyświetlić pełną listę zasobów w sieci wirtualnej oraz typ relacji między zasobami, wygeneruj topologię za pomocą programu [PowerShell](#powershell) lub interfejsu wiersza [polecenia platformy Azure.](#azure-cli)

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Wyświetlanie topologii — interfejs wiersza polecenia platformy Azure

Polecenia można uruchomić w poniższych krokach:
- W Azure Cloud Shell, wybierając pozycję **Wypróbuj** w prawym górnym rogu dowolnego polecenia. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, która ma wstępnie zainstalowane i skonfigurowane do użycia typowe narzędzia platformy Azure na koncie.
- Uruchamiając interfejs wiersza polecenia z komputera. Jeśli uruchamiasz interfejs wiersza polecenia z komputera, kroki opisane w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić narzędzie , aby `az login` utworzyć połączenie z platformą Azure.

Konto, z których korzystasz, musi mieć niezbędne [uprawnienia.](required-rbac-permissions.md)

1. Jeśli masz już usługę Network Watcher w tym samym regionie co sieć wirtualna, dla której chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów, która będzie zawierać usługę Network Watcher, za [pomocą az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów w regionie *eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Utwórz usługę Network Watcher za pomocą [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure). Poniższy przykład tworzy usługę Network Watcher w *regionie eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Wyświetl topologię za pomocą [az network watcher show-topology](/cli/azure/network/watcher#az_network_watcher_show_topology). W poniższym przykładzie przedstawiono topologię grupy zasobów o *nazwie MyResourceGroup:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informacje o topologii są zwracane tylko dla zasobów, które znajdują się w tej samej grupie zasobów co grupa zasobów *MyResourceGroup* i w tym samym regionie co usługa Network Watcher. Na przykład sieciowa grupa zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup,* nie jest wyświetlana, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w sieci *wirtualnej MyVnet.*

   Dowiedz się więcej o relacjach [i właściwościach](#properties) w zwracanych danych wyjściowych. Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii, możesz ją utworzyć przy użyciu przykładowego skryptu Przekieruj ruch przez [wirtualne](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) urządzenie sieciowe. Aby wyświetlić diagram topologii i pobrać go w edytowalnym pliku, użyj [portalu](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topologia widoku — PowerShell

Polecenia można uruchomić w poniższych krokach:
- W Azure Cloud Shell, wybierając pozycję **Wypróbuj** w prawym górnym rogu dowolnego polecenia. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, która ma wstępnie zainstalowane i skonfigurowane do użycia typowe narzędzia platformy Azure na koncie.
- Uruchamiając program PowerShell z komputera. Jeśli uruchamiasz program PowerShell z komputera, ten artykuł wymaga Azure PowerShell `Az` modułu. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Konto, z których korzystasz, musi mieć niezbędne [uprawnienia.](required-rbac-permissions.md)

1. Jeśli masz już usługę Network Watcher w tym samym regionie co sieć wirtualna, dla której chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów, która będzie zawierać usługę Network Watcher, za [pomocą programu New-AzResourceGroup.](/powershell/module/az.Resources/New-azResourceGroup) Poniższy przykład tworzy grupę zasobów w regionie *eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Utwórz usługę Network Watcher za [pomocą programu New-AzNetworkWatcher.](/powershell/module/az.network/new-aznetworkwatcher) Poniższy przykład tworzy usługę Network Watcher w regionie eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Pobierz wystąpienie Network Watcher za pomocą [get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Poniższy przykład pobiera usługę Network Watcher w regionie Wschodnie usa:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Pobierz topologię za pomocą [get-AzNetworkWatcherTopology.](/powershell/module/az.network/get-aznetworkwatchertopology) Poniższy przykład pobiera topologię sieci wirtualnej w grupie zasobów o nazwie *MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informacje o topologii są zwracane tylko dla zasobów, które znajdują się w tej samej grupie zasobów co grupa zasobów *MyResourceGroup* i w tym samym regionie co usługa Network Watcher. Na przykład sieciowa grupa zabezpieczeń, która istnieje w grupie zasobów innej niż *MyResourceGroup,* nie jest wyświetlana, nawet jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią w sieci *wirtualnej MyVnet.*

   Dowiedz się więcej o relacjach [i właściwościach](#properties) w zwracanych danych wyjściowych. Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii, możesz ją utworzyć przy użyciu przykładowego skryptu Przekieruj ruch przez [wirtualne](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) urządzenie sieciowe. Aby wyświetlić diagram topologii i pobrać go w edytowalnym pliku, użyj [portalu](#azure-portal).

## <a name="relationships"></a>Relacje

Wszystkie zasoby zwracane w topologii mają jeden z następujących typów relacji z innym zasobem:

| Typ relacji | Przykład                                                                                                |
| ---               | ---                                                                                                    |
| Zawieranie       | Sieć wirtualna zawiera podsieć. Podsieć zawiera interfejs sieciowy.                            |
| Skojarzone        | Interfejs sieciowy jest skojarzony z maszyną wirtualną. Publiczny adres IP jest skojarzony z interfejsem sieciowym. |

## <a name="properties"></a>Właściwości

Wszystkie zasoby zwracane w topologii mają następujące właściwości:

- **Nazwa:** nazwa zasobu
- **Identyfikator:** identyfikator URI zasobu.
- **Lokalizacja:** region świadczenia usługi Azure, w którym znajduje się zasób.
- Skojarzenia: lista skojarzeń z obiektem, do których się odwołujesz. Każde skojarzenie ma następujące właściwości:
    - **AssociationType:** odwołuje się do relacji między obiektem podrzędnym i nadrzędnym. Prawidłowe wartości to *Contains lub* *Associated*.
    - **Nazwa:** nazwa zasobu, do których się odwołujesz.
    - **ResourceId**: — URI zasobu, do których odwołuje się skojarzenie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zdiagnozować problem z filtrowaniem ruchu](diagnose-vm-network-traffic-filtering-problem.md) sieciowego do lub z maszyny wirtualnej przy użyciu Network Watcher weryfikowania przepływu adresów IP
- Dowiedz się, jak [zdiagnozować problem z routingiem](diagnose-vm-network-routing-problem.md) ruchu sieciowego z maszyny wirtualnej przy Network Watcher funkcji następnego przeskoku
