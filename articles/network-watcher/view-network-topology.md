---
title: Wyświetlanie topologii sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak wyświetlać zasoby w sieci wirtualnej i relacje między zasobami.
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
ms.openlocfilehash: c04da65af27ebd5ac654bc059ae004c157a20f33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737533"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Wyświetlanie topologii sieci wirtualnej platformy Azure

W tym artykule przedstawiono sposób wyświetlania zasobów w Microsoft Azure sieci wirtualnej oraz relacji między zasobami. Na przykład Sieć wirtualna zawiera podsieci. Podsieci zawierają zasoby, takie jak Azure Virtual Machines (VM). Maszyny wirtualne mają co najmniej jeden interfejs sieciowy. Każda podsieć może mieć skojarzoną z nią grupę zabezpieczeń sieci i tabelę tras. Funkcja topologii Network Watcher platformy Azure umożliwia wyświetlanie wszystkich zasobów w sieci wirtualnej, zasobów skojarzonych z zasobami w sieci wirtualnej oraz relacji między zasobami.

Aby wyświetlić topologię, można użyć [Azure Portal](#azure-portal), [interfejsu wiersza polecenia platformy Azure](#azure-cli)lub [programu PowerShell](#powershell) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Wyświetl topologię — Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które ma wymagane [uprawnienia](required-rbac-permissions.md).
2. W lewym górnym rogu portalu wybierz pozycję **wszystkie usługi**.
3. W polu Filtr **wszystkie usługi** wprowadź *Network Watcher*. Gdy **Network Watcher** pojawi się w wynikach, wybierz ją.
4. Wybierz pozycję **Topologia**. Generowanie topologii wymaga obserwatora sieci w tym samym regionie, w którym znajduje się sieć wirtualna, w której ma zostać wygenerowana topologia. Jeśli nie masz włączonego obserwatora sieciowego w regionie, w którym nie ma sieci wirtualnej, dla której ma zostać wygenerowana topologia, w programie obserwatorzy sieci są automatycznie tworzeniu dla Ciebie we wszystkich regionach. Obserwatorzy sieciowi są tworzeniu w grupie zasobów o nazwie **NetworkWatcherRG**.
5. Wybierz subskrypcję, grupę zasobów sieci wirtualnej, dla której ma zostać wyświetlona topologia, a następnie wybierz sieć wirtualną. Na poniższej ilustracji przedstawiono topologię sieci wirtualnej o nazwie *MyVnet* w grupie zasobów o nazwie Moja *zasobów*:

    ![Wyświetl topologię](./media/view-network-topology/view-topology.png)

    Jak widać na poprzedniej ilustracji, Sieć wirtualna zawiera trzy podsieci. Jedna podsieć ma wdrożoną maszynę wirtualną. Do maszyny wirtualnej jest dołączony jeden interfejs sieciowy i skojarzony z nim publiczny adres IP. Pozostałe dwie podsieci mają skojarzoną tabelę tras. Każda tabela tras zawiera dwie trasy. Z jedną podsiecią jest skojarzona sieciowa Grupa zabezpieczeń. Informacje o topologii są wyświetlane tylko dla zasobów, które są następujące:
    
    - W tej samej grupie zasobów i regionie co sieć wirtualna *myVnet* . Na przykład sieciowa Grupa zabezpieczeń, która istnieje w grupie zasobów innej niż grupa *zasobów*, nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet* .
    - W ramach lub skojarzonych z zasobami w sieci wirtualnej *myVnet* . Na przykład sieciowa Grupa zabezpieczeń, która nie jest skojarzona z podsiecią lub interfejsem sieciowym w sieci wirtualnej *myVnet* , nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci *znajduje się w grupie zasobów.*

   Topologia wyświetlana na obrazie jest dla sieci wirtualnej utworzonej po wdrożeniu **ruchu trasy za pośrednictwem sieciowego przykładowego skryptu urządzenia wirtualnego**, który można wdrożyć za pomocą [interfejsu wiersza polecenia platformy Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)lub [programu PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Wybierz opcję **topologia pobierania** , aby pobrać obraz jako plik edytowalny w formacie SVG.

Zasoby wyświetlane na diagramie są podzbiorem składników sieciowych w sieci wirtualnej. Na przykład podczas wyświetlania sieciowej grupy zabezpieczeń nie są one wyświetlane na diagramie. Chociaż nie różnią się w diagramie, wiersze reprezentują jedną z dwóch relacji: *zawieranie* lub *skojarzone*. Aby wyświetlić pełną listę zasobów w sieci wirtualnej oraz typ relacji między zasobami, należy wygenerować topologię przy użyciu [programu PowerShell](#powershell) lub [interfejsu wiersza polecenia platformy Azure](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Wyświetl topologię — interfejs wiersza polecenia platformy Azure

Polecenia można uruchomić w następujących krokach:
- W Azure Cloud Shell, wybierając pozycję **Wypróbuj** w prawym górnym rogu dowolnego polecenia. Azure Cloud Shell to bezpłatna interaktywna powłoka, która ma wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem.
- Uruchomienie interfejsu wiersza polecenia na komputerze. Jeśli interfejs wiersza polecenia zostanie uruchomiony z komputera, kroki opisane w tym artykule wymagają interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić polecenie `az login` , aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć wymagane [uprawnienia](required-rbac-permissions.md).

1. Jeśli w tym samym regionie znajduje się już obserwator sieciowy, dla którego chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów, aby zawierała obserwatora sieciowego za pomocą [AZ Group Create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów w regionie *wschodnim* :

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Utwórz obserwatora sieciowego za pomocą [AZ Network obserwator Configure](/cli/azure/network/watcher#az-network-watcher-configure). Poniższy przykład tworzy obserwatora sieci w regionie *wschodnim* :

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Wyświetl topologię za pomocą [AZ Network obserwator show-Topology](/cli/azure/network/watcher#az-network-watcher-show-topology). Poniższy przykład przedstawia topologię grupy zasobów o nazwie Moja *zasobów*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informacje o topologii są zwracane tylko w przypadku zasobów znajdujących się w tej samej grupie zasobów co grupa zasobów i ten sam region, w *którym znajduje się* obserwator sieci. Na przykład sieciowa Grupa zabezpieczeń, która istnieje w grupie zasobów innej niż grupa *zasobów*, nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet* .

   Dowiedz się więcej na temat relacji i [Właściwości](#properties) w zwracanych danych wyjściowych. Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii programu, możesz ją utworzyć za pomocą przykładowego skryptu dla [sieciowego urządzenia wirtualnego](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Aby wyświetlić diagram topologii i pobrać go w edytowalnym pliku, użyj [portalu](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Wyświetlanie topologii — PowerShell

Polecenia można uruchomić w następujących krokach:
- W Azure Cloud Shell, wybierając pozycję **Wypróbuj** w prawym górnym rogu dowolnego polecenia. Azure Cloud Shell to bezpłatna interaktywna powłoka, która ma wspólne narzędzia platformy Azure preinstalowane i skonfigurowane do użycia z Twoim kontem.
- Przez uruchomienie programu PowerShell z komputera. Jeśli uruchamiasz program PowerShell z komputera, ten artykuł wymaga modułu Azure PowerShell `Az` . Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Konto, którego używasz, musi mieć wymagane [uprawnienia](required-rbac-permissions.md).

1. Jeśli w tym samym regionie znajduje się już obserwator sieciowy, dla którego chcesz utworzyć topologię, przejdź do kroku 3. Utwórz grupę zasobów, aby zawierała obserwatora sieci z [nowym AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Poniższy przykład tworzy grupę zasobów w regionie *wschodnim* :

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Utwórz obserwatora sieciowego przy użyciu elementu [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). Poniższy przykład tworzy obserwatora sieci w regionie wschodnim:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Pobierz wystąpienie Network Watcher za pomocą elementu [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Poniższy przykład pobiera obserwatora sieci w regionie Wschodnie stany USA:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Pobieranie topologii za pomocą [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). Poniższy przykład pobiera topologię sieci wirtualnej w grupie zasobów o nazwie Moja *zasobów*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informacje o topologii są zwracane tylko w przypadku zasobów znajdujących się w tej samej grupie zasobów co grupa zasobów i ten sam region, w *którym znajduje się* obserwator sieci. Na przykład sieciowa Grupa zabezpieczeń, która istnieje w grupie zasobów innej niż grupa *zasobów*, nie jest wyświetlana, nawet jeśli grupa zabezpieczeń sieci jest skojarzona z podsiecią w sieci wirtualnej *MyVnet* .

   Dowiedz się więcej na temat relacji i [Właściwości](#properties) w zwracanych danych wyjściowych. Jeśli nie masz istniejącej sieci wirtualnej do wyświetlania topologii programu, możesz ją utworzyć za pomocą przykładowego skryptu dla [sieciowego urządzenia wirtualnego](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Aby wyświetlić diagram topologii i pobrać go w edytowalnym pliku, użyj [portalu](#azure-portal).

## <a name="relationships"></a>Relacje

Wszystkie zasoby zwrócone w topologii mają jeden z następujących typów relacji z innym zasobem:

| Typ relacji | Przykład                                                                                                |
| ---               | ---                                                                                                    |
| Zawieranie       | Sieć wirtualna zawiera podsieć. Podsieć zawiera interfejs sieciowy.                            |
| Skojarzone        | Interfejs sieciowy jest skojarzony z maszyną wirtualną. Publiczny adres IP jest skojarzony z interfejsem sieciowym. |

## <a name="properties"></a>Właściwości

Wszystkie zasoby zwrócone w topologii mają następujące właściwości:

- **Nazwa**: nazwa zasobu
- **Identyfikator**: identyfikator URI zasobu.
- **Lokalizacja**: region świadczenia usługi Azure, w którym znajduje się zasób.
- **Skojarzenia**: Lista skojarzeń z przywoływanym obiektem. Każde skojarzenie ma następujące właściwości:
    - **AssociationType**: odwołuje się do relacji między obiektem podrzędnym a elementem nadrzędnym. Prawidłowe wartości są *zawarte* lub *skojarzone*.
    - **Name**: Nazwa przywoływanego zasobu.
    - **ResourceID**: — identyfikator URI zasobu, do którego odwołuje się skojarzenie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zdiagnozować problem z filtrem ruchu sieciowego do lub z maszyny wirtualnej](diagnose-vm-network-traffic-filtering-problem.md) za pomocą funkcji weryfikacji przepływu adresu IP Network Watcher
- Dowiedz się, jak [zdiagnozować problem z routingiem ruchu sieciowego z maszyny wirtualnej](diagnose-vm-network-routing-problem.md) przy użyciu funkcji następnego przeskoku Network Watcher
