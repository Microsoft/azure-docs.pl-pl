---
title: 'Azure ExpressRoute: Migruj klasyczne sieci wirtualnych do Menedżer zasobów'
description: Na tej stronie opisano, jak migrować sieci wirtualne skojarzone z ExpressRoute do Menedżer zasobów po przeniesieniu obwodu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: ac6fe619fc1c65b9ced18227d08cde50997eb7d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395285"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrowanie sieci wirtualnych skojarzonych z ExpressRoute z klasycznej do Menedżer zasobów

W tym artykule wyjaśniono, jak migrować sieci wirtualne skojarzone z ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania Azure Resource Manager po przeniesieniu obwodu usługi ExpressRoute. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Sprawdź, czy masz najnowsze wersje modułów Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/). Aby zainstalować moduł zarządzania usługą programu PowerShell (który jest wymagany dla klasycznego modelu wdrażania), zobacz [Instalowanie modułu Azure PowerShell Service Management](/powershell/azure/servicemanagement/install-azure-ps).
* Przed rozpoczęciem konfiguracji należy sprawdzić wymagania [wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md)i [przepływy pracy](expressroute-workflows.md) .
* Przejrzyj informacje podane w obszarze [przeniesienie obwodu usługi ExpressRoute z klasycznej do Menedżer zasobów](expressroute-move.md). Upewnij się, że w pełni rozumiesz limity i ograniczenia.
* Sprawdź, czy obwód jest w pełni funkcjonalny w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, która została utworzona w modelu wdrażania Menedżer zasobów.
* Zapoznaj się z dokumentacją dotyczącą migracji zasobów:

    * [Obsługiwana przez platformę migracja zasobów IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
    * [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Często zadawane pytania: migracja IaaS zasobów z klasycznej do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Przegląd najczęstszych błędów migracji i środków zaradczych](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenariusze obsługiwane i nieobsługiwane

* Obwód usługi ExpressRoute można przenieść z klasycznego do środowiska Menedżer zasobów bez żadnego przestoju. Możesz przenieść dowolny obwód usługi ExpressRoute z klasycznego do środowiska Menedżer zasobów bez przestojów. Postępuj zgodnie z instrukcjami w temacie [przemieszczanie obwodów usługi ExpressRoute z klasycznego do Menedżer zasobów modelu wdrażania przy użyciu programu PowerShell](expressroute-howto-move-arm.md). Jest to wymaganie wstępne do przenoszenia zasobów podłączonych do sieci wirtualnej.
* Sieci wirtualne, bramy i skojarzone wdrożenia w ramach sieci wirtualnej, które są dołączone do obwodu usługi ExpressRoute w ramach tej samej subskrypcji, można migrować do środowiska Menedżer zasobów bez żadnego przestoju. Aby migrować zasoby, takie jak sieci wirtualne, bramy i maszyny wirtualne wdrożone w ramach sieci wirtualnej, można wykonać kroki opisane w dalszej części tego artykułu. Przed migracją sieci wirtualne należy upewnić się, że zostały skonfigurowane prawidłowo. 
* Sieci wirtualne, bramy i skojarzone wdrożenia w ramach sieci wirtualnej, które nie znajdują się w tej samej subskrypcji, co obwód usługi ExpressRoute, wymagają pewnego przestoju w celu przeprowadzenia migracji. W ostatniej sekcji dokumentu opisano kroki, które należy wykonać, aby przeprowadzić migrację zasobów.
* Nie można migrować sieci wirtualnej z bramą ExpressRoute i VPN Gateway.
* Migracja między subskrypcjami obwodu usługi ExpressRoute nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [Microsoft. Network Move support](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżer zasobów
Przed podjęciem próby migracji zasobów podłączonych do obwodu usługi ExpressRoute należy przenieść obwód usługi ExpressRoute z klasycznego do środowiska Menedżer zasobów. Aby wykonać to zadanie, zobacz następujące artykuły:

* Przejrzyj informacje podane w obszarze [przeniesienie obwodu usługi ExpressRoute z klasycznej do Menedżer zasobów](expressroute-move.md).
* [Przenoszenie obwodu z klasycznego do Menedżer zasobów przy użyciu Azure PowerShell](expressroute-howto-move-arm.md).
* Użyj portalu zarządzania usługami platformy Azure. W celu [utworzenia nowego obwodu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i wybrania opcji Importuj można obserwować przepływ pracy. 

Ta operacja nie obejmuje przestojów. Po zakończeniu migracji można kontynuować transfer danych między środowiskiem lokalnym i firmą Microsoft.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrowanie sieci wirtualnych, bram i skojarzonych wdrożeń

Kroki, które należy wykonać, aby przeprowadzić migrację, zależą od tego, czy zasoby znajdują się w tej samej subskrypcji, czy też w różnych subskrypcjach.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migruj sieci wirtualne, bramy i skojarzone wdrożenia w tej samej subskrypcji co obwód usługi ExpressRoute
W tej sekcji opisano kroki, które należy wykonać w celu przeprowadzenia migracji sieci wirtualnej, bramy i skojarzonych wdrożeń w tej samej subskrypcji co obwód usługi ExpressRoute. Z tą migracją nie jest skojarzony żaden Przestój. Można nadal używać wszystkich zasobów w ramach procesu migracji. Płaszczyzna zarządzania jest zablokowana w trakcie migracji. 

1. Upewnij się, że obwód usługi ExpressRoute został przeniesiony z klasycznego do środowiska Menedżer zasobówowego.
2. Upewnij się, że sieć wirtualna została odpowiednio przygotowana do migracji.
3. Zarejestruj swoją subskrypcję, aby przeprowadzić migrację zasobów. Aby zarejestrować swoją subskrypcję do migracji zasobów, użyj następującego fragmentu kodu programu PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Weryfikowanie, przygotowywanie i migrowanie. Aby przenieść sieć wirtualną, użyj następującego fragmentu kodu programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Możesz również przerwać migrację, uruchamiając następujące polecenie cmdlet programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Następne kroki
* [Obsługiwana przez platformę migracja zasobów IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Często zadawane pytania: migracja IaaS zasobów z klasycznej do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Przegląd najczęstszych błędów migracji i środków zaradczych](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
