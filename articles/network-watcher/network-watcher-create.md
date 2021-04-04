---
title: Tworzenie wystąpienia usługi Azure Network Watcher | Microsoft Docs
description: Dowiedz się, jak utworzyć Network Watcher platformy Azure w regionie świadczenia usługi Azure przy użyciu Azure Portal lub innych technologii oraz jak usunąć Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962012"
---
# <a name="create-an-azure-network-watcher-instance"></a>Tworzenie wystąpienia usługi Azure Network Watcher

Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Monitorowanie poziomu scenariusza umożliwia diagnozowanie problemów w widoku na poziomie sieci. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure. Network Watcher jest włączana za pomocą tworzenia zasobu Network Watcher. Ten zasób umożliwia korzystanie z możliwości Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher jest automatycznie włączona
Podczas tworzenia lub aktualizowania sieci wirtualnej w ramach subskrypcji usługa Network Watcher zostanie automatycznie włączona w regionie sieci wirtualnej. Automatyczne włączanie usługi Network Watcher nie wpływa na zasoby i nie wiąże się z opłatami.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Rezygnacja z automatycznego włączania Network Watcher
Jeśli chcesz zrezygnować z Network Watcher automatycznego włączania, możesz to zrobić, uruchamiając następujące polecenia:

> [!WARNING]
> Rezygnacja z Network Watcher automatycznego włączania jest trwałą zmianą. Gdy zrezygnujesz [z pomocy technicznej](https://azure.microsoft.com/support/options/) , nie możesz się zadecydować

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Tworzenie Network Watcher w portalu

Przejdź do **wszystkich usług**  >    >  **Network Watcher** sieci. Możesz wybrać wszystkie subskrypcje, dla których chcesz włączyć Network Watcher. Ta akcja tworzy Network Watcher w każdym dostępnym regionie.

![Tworzenie obserwatora sieciowego](./media/network-watcher-create/figure1.png)

Po włączeniu Network Watcher przy użyciu portalu nazwa wystąpienia Network Watcher zostanie automatycznie ustawiona na *NetworkWatcher_region_name* gdzie *region_name* odnosi się do regionu platformy Azure, w którym to wystąpienie jest włączone. Na przykład Network Watcher włączony w regionie Zachodnio-środkowe stany USA ma nazwę *NetworkWatcher_westcentralus*.

Wystąpienie Network Watcher jest tworzone automatycznie w grupie zasobów o nazwie *NetworkWatcherRG*. Grupa zasobów jest tworzona, jeśli jeszcze nie istnieje.

Jeśli chcesz dostosować nazwę wystąpienia Network Watcher i grupę zasobów, w której jest ona umieszczana, możesz użyć programu PowerShell, interfejsu wiersza polecenia platformy Azure lub metod ARMClient opisanych w poniższych sekcjach. W każdej opcji Grupa zasobów musi istnieć przed utworzeniem w niej Network Watcher.  

## <a name="create-a-network-watcher-with-powershell"></a>Tworzenie Network Watcher przy użyciu programu PowerShell

Aby utworzyć wystąpienie Network Watcher, uruchom następujący przykład:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Tworzenie Network Watcher przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć wystąpienie Network Watcher, uruchom następujący przykład:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Tworzenie Network Watcher przy użyciu interfejsu API REST

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na czekolady w [ARMClient na czekoladie](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Logowanie za pomocą ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Tworzenie obserwatora sieciowego

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Tworzenie Network Watcher przy użyciu szablonu szybkiego startu platformy Azure

Aby utworzyć wystąpienie Network Watcher odwołaj się do tego [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Usuwanie Network Watcher w portalu

Przejdź do **wszystkich usług**  >    >  **Network Watcher** sieci.

Wybierz kartę Przegląd, jeśli jeszcze tego nie zrobiono. Użyj listy rozwijanej, aby wybrać subskrypcję, w której chcesz wyłączyć obserwatora sieciowego.
Rozwiń listę regionów dla wybranej subskrypcji, klikając strzałkę. Dla każdego z nich należy użyć 3 kropek po prawej stronie, aby uzyskać dostęp do menu kontekstowego.
Kliknij pozycję "Wyłącz obserwatora sieci", aby rozpocząć wyłączanie. Zostanie wyświetlony monit o potwierdzenie tego kroku. Kliknij przycisk Tak, aby kontynuować.
W portalu należy wykonać te czynności osobno dla każdego regionu w każdej subskrypcji.


## <a name="delete-a-network-watcher-with-powershell"></a>Usuwanie Network Watcher przy użyciu programu PowerShell

Aby usunąć wystąpienie Network Watcher, uruchom następujący przykład:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już wystąpienie Network Watcher, zapoznaj się z dostępnymi funkcjami:

* [Topologia](./view-network-topology.md)
* [Przechwytywanie pakietów](network-watcher-packet-capture-overview.md)
* [Weryfikacja przepływu adresów IP](network-watcher-ip-flow-verify-overview.md)
* [Narzędzie Następny przeskok](network-watcher-next-hop-overview.md)
* [Widok grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
* [Rejestrowanie przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-overview.md)
* [Rozwiązywanie problemów z bramą Virtual Network](network-watcher-troubleshoot-overview.md)