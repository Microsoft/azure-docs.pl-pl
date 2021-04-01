---
title: 'ExpressRoute: Przenieś obwody z klasycznego do Azure Resource Manager'
description: Dowiedz się więcej o tym, co się stanie w przypadku przenoszenia obwodu usługi Azure ExpressRoute z klasycznego modelu wdrażania do Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807945"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager | Microsoft Azure
Ten artykuł zawiera omówienie tego, co się dzieje w przypadku przenoszenia obwodu usługi Azure ExpressRoute z klasycznego modelu wdrażania do Azure Resource Manager.

Można użyć pojedynczego obwodu usługi ExpressRoute do łączenia sieci wirtualnych wdrożonych zarówno w klasycznym, jak i Menedżer zasobów modele wdrażania.

![Obwód usługi ExpressRoute, który łączy się z sieciami wirtualnymi w obu modelach wdrażania](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Obwody usługi ExpressRoute tworzone w klasycznym modelu wdrażania
Obwody usługi ExpressRoute tworzone w klasycznym modelu wdrażania muszą najpierw przeprowadzić migrację do modelu wdrażania Menedżer zasobów. Tylko wtedy można włączyć łączność zarówno z klasycznym, jak i Menedżer zasobów modelami wdrażania. Łączność nie jest tracona lub zakłóca, gdy połączenie jest przenoszone. Wszystkie linki do sieci wirtualnej w klasycznym modelu wdrażania w ramach tej samej subskrypcji i krzyżowe subskrypcje są zachowywane.

Po pomyślnym zakończeniu przenoszenia obwód ExpressRoute będzie działać tak samo jak obwód ExpressRoute, który został utworzony w Menedżer zasobów model wdrażania. Możesz teraz tworzyć połączenia z sieciami wirtualnymi w modelu wdrażania usługi Resource Manager.

Po przeniesieniu obwodu ExpressRoute do modelu wdrażania przy użyciu Menedżera zasobów można zarządzać nim tylko w Menedżer zasobów model wdrażania. Operacje związane z zarządzaniem komunikacjami równorzędnymi, aktualizowaniem właściwości obwodu i usuwaniem obwodów będą dostępne tylko w ramach modelu wdrażania Menedżer zasobów. Więcej informacji o sposobach zarządzania dostępem do obu modeli wdrażania można znaleźć w następującej sekcji.

Nie musisz angażować Twojego dostawcy łączności, aby przenieść obwód do modelu wdrażania Menedżer zasobów.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Obwody usługi ExpressRoute tworzone w modelu wdrażania usługi Resource Manager
Można włączyć ustawienie obwodów usługi ExpressRoute tworzonych w modelu wdrażania usługi Resource Manager, które spowoduje, że będą one dostępne w obu modelach wdrażania. Każdy obwód usługi ExpressRoute w ramach subskrypcji można skonfigurować tak, aby miał dostęp z obu modeli wdrażania.

* Obwody usługi ExpressRoute utworzone w modelu wdrażania Menedżer zasobów nie mają domyślnie dostępu do klasycznego modelu wdrażania.
* Obwody usługi ExpressRoute, które zostały przeniesione z klasycznego modelu wdrażania do modelu wdrażania Menedżer zasobów, są domyślnie dostępne z obu modeli wdrażania.
* Obwód ExpressRoute ma zawsze dostęp do modelu wdrażania Menedżer zasobów, niezależnie od tego, czy został on utworzony w ramach Menedżer zasobów czy klasycznego modelu wdrażania. Połączenia z sieciami wirtualnymi można utworzyć, wykonując instrukcje dotyczące [sposobu łączenia sieci wirtualnych](expressroute-howto-linkvnet-arm.md).
* Dostęp do klasycznego modelu wdrażania jest kontrolowany przy użyciu parametru **allowClassicOperations** w obwodzie usługi ExpressRoute.

> [!IMPORTANT]
> Wszystkie przydziały udokumentowane na stronie [limitów usług](../azure-resource-manager/management/azure-subscription-service-limits.md) obowiązują. Na przykład standardowy obwód może obejmować najwyżej 10 linków/połączeń sieci wirtualnej — zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Kontrolowanie dostępu do klasycznego modelu wdrażania
Obwód usługi ExpressRoute można włączyć w celu łączenia się z sieciami wirtualnymi w obu modelach wdrażania. Aby to zrobić, należy ustawić parametr **allowClassicOperations** w obwodzie ExpressRoute.

Ustawienie parametru **allowClassicOperations** na wartość PRAWDA umożliwia połączenie sieci wirtualnych z obu modeli wdrażania z obwodem usługi ExpressRoute. 
* Aby połączyć sieci wirtualne w klasycznym modelu wdrażania, zobacz [jak połączyć sieci wirtualne dla klasycznego modelu wdrażania](expressroute-howto-linkvnet-classic.md).
* Aby połączyć sieci wirtualne w modelu wdrażania Menedżer zasobów, zobacz [jak połączyć sieci wirtualne w modelu wdrażania Menedżer zasobów](expressroute-howto-linkvnet-arm.md).

Ustawienie parametru **allowClassicOperations** na wartość FAŁSZ blokuje dostęp do obwodu z klasycznego modelu wdrażania. Jednak wszystkie sieci wirtualne połączone z klasycznym modelem wdrażania są nadal zachowywane. Obwód ExpressRoute nie jest widoczny w klasycznym modelu wdrażania.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operacje obsługiwane w klasycznym modelu wdrażania
Poniższe operacje klasyczne są obsługiwane w obwodzie usługi ExpressRoute, gdy parametr **allowClassicOperations** jest ustawiony na wartość PRAWDA:

* Uzyskanie informacji na temat obwodu usługi ExpressRoute
* Tworzenie/aktualizowanie/pobieranie/usuwanie linków sieci wirtualnej do klasycznych sieci wirtualnych
* Tworzenie/aktualizowanie/pobieranie/usuwanie autoryzacji linków sieci wirtualnej względem łączności obejmującej wiele subskrypcji

Jeśli jednak **allowClassicOperations** ma wartość true, nie można wykonać następujących operacji klasycznych:

* Tworzenie/aktualizowanie/pobieranie/usuwanie komunikacji równorzędnej protokołu BGP dla komunikacji równorzędnej prywatnej i publicznej Azure oraz Microsoft
* Usuwanie obwodów usługi ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Komunikacja między klasycznym modelem wdrażania i modelem wdrażania usługi Resource Manager
Obwód usługi ExpressRoute zachowuje się jak most między klasycznym modelem wdrażania a modelem wdrażania usługi Resource Manager. Ruch między sieciami wirtualnymi dla obu modeli wdrażania może przechodzić przez obwód usługi ExpressRoute, jeśli obie sieci wirtualne są połączone z tym samym obwodem.

Zagregowana przepływność jest ograniczona przez przepływność bramy sieci wirtualnej. Ruch sieciowy nie wprowadza w takich przypadkach sieci dostawcy łączności ani sieci. Przepływ ruchu między sieciami wirtualnymi jest całkowicie ograniczony do sieci firmy Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Dostęp do publicznych zasobów Azure i firmy Microsoft komunikacji równorzędnej
Możesz dalej bez żadnych zakłóceń uzyskiwać dostęp do zasobów, które są zwykle dostępne za pośrednictwem publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft.  

## <a name="whats-supported"></a>Jakie operacje są obsługiwane
W tej sekcji opisano, jakie operacje są obsługiwane dla obwodów usługi ExpressRoute:

* Można używać jednego obwodu usługi ExpressRoute do uzyskiwania dostępu do sieci wirtualnych wdrożonych zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
* Można przenieść obwód usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager. Po przeniesieniu obwód ExpressRoute będzie nadal działał podobnie jak każdy inny obwód ExpressRoute, który jest tworzony w Menedżer zasobów model wdrażania.
* Można przenieść tylko obwód usługi ExpressRoute. Za pomocą tej operacji nie można przenieść linków obwodu, sieci wirtualnych ani bram sieci VPN.
* Po przeniesieniu obwodu usługi ExpressRoute do modelu wdrażania usługi Resource Manager możesz zarządzać cyklem życiowym obwodu tylko używając modelu wdrażania usługi Resource Manager. Oznacza to, że można uruchamiać operacje takie jak dodawanie/aktualizowanie/usuwanie komunikacji równorzędnej, aktualizowanie właściwości obwodu (takich jak przepustowość, jednostka SKU i typ rozliczeń), a także usuwanie obwodów tylko w modelu wdrażania Menedżer zasobów.
* Obwód usługi ExpressRoute zachowuje się jak most między klasycznym modelem wdrażania a modelem wdrażania usługi Resource Manager. Ruch między maszynami wirtualnymi w klasycznych sieciach wirtualnych i maszynach wirtualnych w Menedżer zasobów sieci wirtualnych może komunikować się za pomocą ExpressRoute, jeśli obie sieci wirtualne są połączone z tym samym obwodem usługi ExpressRoute.
* Łączność obejmująca wiele subskrypcji jest obsługiwana zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
* Po przeniesieniu obwodu usługi ExpressRoute z modelu klasycznego do modelu usługi Azure Resource Manager możesz [migrować sieci wirtualne połączone z obwodem usługi ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
W tej sekcji opisano, jakie operacje nie są obsługiwane dla obwodów usługi ExpressRoute:

* Zarządzanie cyklem życia obwodu usługi ExpressRoute z klasycznego modelu wdrażania.
* Obsługa kontroli dostępu opartej na rolach (Azure RBAC) dla klasycznego modelu wdrażania. Nie można uruchomić formantów RBAC platformy Azure w ramach obwodu w klasycznym modelu wdrażania. Dowolny administrator/współadministrator subskrypcji może połączyć sieci wirtualne z obwodem lub je od niego odłączyć.

## <a name="configuration"></a>Konfigurowanie
Wykonaj instrukcje opisane w artykule [Move an ExpressRoute circuit from the classic to the Resource Manager deployment model](expressroute-howto-move-arm.md) (Przenoszenie obwodu usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager).

## <a name="next-steps"></a>Następne kroki
* [Migrowanie sieci wirtualnych połączonych z obwodem usługi ExpressRoute z modelu klasycznego do modelu usługi Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Informacje o przepływach pracy można znaleźć w artykule [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Przepływy pracy inicjowania obsługi obwodu i stany obwodu usługi ExpressRoute).
* Konfigurowanie połączenia usługi ExpressRoute:
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)

