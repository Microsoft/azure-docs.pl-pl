---
title: 'ExpressRoute: Przenieś obwody z klasycznego do Azure Resource Manager'
description: Dowiedz się więcej na temat tego, co oznacza przeniesienie obwodu usługi Azure ExpressRoute z klasycznego do modelu wdrażania Azure Resource Manager.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: duau
ms.openlocfilehash: 12ace8dff58d3d747bf17260950d2b91e31b398a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395166"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager | Microsoft Azure
W tym artykule wyjaśniono przenoszenie obwodu usługi Azure ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager.

Można użyć jednego obwodu usługi ExpressRoute do łączenia się z sieciami wirtualnymi, które są wdrażane zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager. Obwód usługi ExpressRoute, niezależnie od tego, w jaki sposób jest tworzony, może teraz połączyć się z sieciami wirtualnymi w obu modelach wdrażania.

![Obwód usługi ExpressRoute, który łączy się z sieciami wirtualnymi w obu modelach wdrażania](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Obwody usługi ExpressRoute tworzone w klasycznym modelu wdrażania
Obwody usługi ExpressRoute tworzone w klasycznym modelu wdrażania trzeba najpierw przenieść do modelu wdrażania usługi Resource Manager, aby włączyć łączność zarówno z klasycznym modelem wdrażania, jak i modelem wdrażania usługi Resource Manager. W przypadku przenoszenia połączenia łączność nie zostaje utracona ani zakłócona. Wszystkie linki sieciowe typu obwód-sieć wirtualna w klasycznym modelu wdrażania (w ramach tej samej subskrypcji lub wielu subskrypcji) są zachowywane.

Po pomyślnym ukończeniu przeniesienia obwód usługi ExpressRoute wygląda i działa tak samo jak obwód usługi ExpressRoute utworzony w modelu wdrażania usługi Resource Manager. Możesz teraz tworzyć połączenia z sieciami wirtualnymi w modelu wdrażania usługi Resource Manager.

Po przeniesieniu obwodu usługi ExpressRoute do modelu wdrażania usługi Resource Manager możesz zarządzać cyklem życiowym obwodu tylko używając modelu wdrażania usługi Resource Manager. Oznacza to, że takie operacje jak dodawanie/aktualizowanie/usuwanie komunikacji równorzędnej, aktualizowanie właściwości obwodu (np. przepustowości, jednostki SKU i typu rozliczeń) oraz usuwanie obwodów można wykonać tylko w modelu wdrażania usługi Resource Manager.  W poniższej sekcji dotyczącej obwodów tworzonych w modelu wdrażania usługi Resource Manager znajduje się więcej szczegółów na temat zarządzania dostępem do obu modeli wdrażania.

Przenoszenie nie wymaga udziału dostawcy połączenia.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Obwody usługi ExpressRoute tworzone w modelu wdrażania usługi Resource Manager
Można włączyć ustawienie obwodów usługi ExpressRoute tworzonych w modelu wdrażania usługi Resource Manager, które spowoduje, że będą one dostępne w obu modelach wdrażania. W dowolnym obwodzie usługi ExpressRoute w ramach subskrypcji można włączyć dostęp z obu modeli wdrażania.

* Obwody usługi ExpressRoute utworzone w modelu wdrażania usługi Resource Manager domyślnie nie mają dostępu do klasycznego modelu wdrażania.
* Obwody usługi ExpressRoute przeniesione z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager są domyślnie dostępne z obu modeli wdrażania.
* Obwód usługi ExpressRoute ma zawsze dostęp do modelu wdrażania usługi Resource Manager, niezależnie od tego, czy został utworzony w usłudze Resource Manager czy klasycznym modelu wdrażania. Oznacza to, że można utworzyć połączenia z sieciami wirtualnymi utworzonymi w modelu wdrażania usługi Resource Manager przez wykonanie instrukcji poniżej dotyczących [sposobu łączenia sieci wirtualnych](expressroute-howto-linkvnet-arm.md).
* Dostęp do klasycznego modelu wdrażania jest kontrolowany przy użyciu parametru **allowClassicOperations** w obwodzie usługi ExpressRoute.

> [!IMPORTANT]
> Wszystkie przydziały udokumentowane na stronie [limitów usług](../azure-resource-manager/management/azure-subscription-service-limits.md) obowiązują. Na przykład standardowy obwód może obejmować najwyżej 10 linków/połączeń sieci wirtualnej — zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Kontrolowanie dostępu do klasycznego modelu wdrażania
Można włączyć łączenie pojedynczego obwodu usługi ExpressRoute z sieciami wirtualnymi w obu modelach wdrażania przez ustawienie parametru **allowClassicOperations** obwodu.

Ustawienie parametru **allowClassicOperations** na wartość PRAWDA umożliwia połączenie sieci wirtualnych z obu modeli wdrażania z obwodem usługi ExpressRoute. Można połączyć się z sieciami wirtualnymi w klasycznym modelu wdrażania, wykonując instrukcje dotyczące [sposobu łączenia sieci wirtualnych w klasycznym modelu wdrażania](expressroute-howto-linkvnet-classic.md). Można połączyć się z sieciami wirtualnymi w klasycznym modelu wdrażania usługi Resource Manager, wykonując instrukcje dotyczące [sposobu łączenia sieci wirtualnych w modelu wdrażania usługi Resource Manager](expressroute-howto-linkvnet-arm.md).

Ustawienie parametru **allowClassicOperations** na wartość FAŁSZ blokuje dostęp do obwodu z klasycznego modelu wdrażania. Jednak wszystkie linki sieci wirtualnej w klasycznym modelu wdrażania zostają zachowane. W takim przypadku obwód usługi ExpressRoute nie jest widoczny w klasycznym modelu wdrażania.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operacje obsługiwane w klasycznym modelu wdrażania
Poniższe operacje klasyczne są obsługiwane w obwodzie usługi ExpressRoute, gdy parametr **allowClassicOperations** jest ustawiony na wartość PRAWDA:

* Uzyskanie informacji na temat obwodu usługi ExpressRoute
* Tworzenie/aktualizowanie/pobieranie/usuwanie linków sieci wirtualnej do klasycznych sieci wirtualnych
* Tworzenie/aktualizowanie/pobieranie/usuwanie autoryzacji linków sieci wirtualnej względem łączności obejmującej wiele subskrypcji

Jeśli jednak **allowClassicOperations** ma wartość true, nie można wykonać następujących operacji klasycznych:

* Tworzenie/aktualizowanie/pobieranie/usuwanie komunikacji równorzędnej protokołu BGP dla komunikacji równorzędnej prywatnej i publicznej Azure oraz Microsoft
* Usuwanie obwodów usługi ExpressRoute

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Komunikacja między klasycznym modelem wdrażania i modelem wdrażania usługi Resource Manager
Obwód usługi ExpressRoute zachowuje się jak most między klasycznym modelem wdrażania a modelem wdrażania usługi Resource Manager. Ruch między maszynami wirtualnymi w sieciach wirtualnych w klasycznym modelu wdrażania oraz tymi w sieciach wirtualnych w modelu wdrażania usługi Resource Manager odbywa się za pośrednictwem usługi ExpressRoute, jeśli obie sieci wirtualne są połączone z tym samym obwodem usługi ExpressRoute.

Zagregowana przepływność jest ograniczona przez przepływność bramy sieci wirtualnej. W takich przypadkach ruch nie wchodzi do sieci dostawcy połączenia ani sieci użytkownika. Przepływ ruchu między sieciami wirtualnymi jest całkowicie ograniczony do sieci firmy Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Dostęp do publicznych zasobów Azure i firmy Microsoft komunikacji równorzędnej
Możesz dalej bez żadnych zakłóceń uzyskiwać dostęp do zasobów, które są zwykle dostępne za pośrednictwem publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft.  

## <a name="whats-supported"></a>Jakie operacje są obsługiwane
W tej sekcji opisano, jakie operacje są obsługiwane dla obwodów usługi ExpressRoute:

* Można używać jednego obwodu usługi ExpressRoute do uzyskiwania dostępu do sieci wirtualnych wdrożonych zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
* Można przenieść obwód usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager. Po przeniesieniu obwód usługi ExpressRoute wygląda i działa tak samo jak obwód usługi ExpressRoute utworzony w modelu wdrażania usługi Resource Manager.
* Można przenieść tylko obwód usługi ExpressRoute. Za pomocą tej operacji nie można przenieść linków obwodu, sieci wirtualnych ani bram sieci VPN.
* Po przeniesieniu obwodu usługi ExpressRoute do modelu wdrażania usługi Resource Manager możesz zarządzać cyklem życiowym obwodu tylko używając modelu wdrażania usługi Resource Manager. Oznacza to, że takie operacje jak dodawanie/aktualizowanie/usuwanie komunikacji równorzędnej, aktualizowanie właściwości obwodu (np. przepustowości, jednostki SKU i typu rozliczeń) oraz usuwanie obwodów można wykonać tylko w modelu wdrażania usługi Resource Manager. 
* Obwód usługi ExpressRoute zachowuje się jak most między klasycznym modelem wdrażania a modelem wdrażania usługi Resource Manager. Ruch między maszynami wirtualnymi w sieciach wirtualnych w klasycznym modelu wdrażania oraz tymi w sieciach wirtualnych w modelu wdrażania usługi Resource Manager odbywa się za pośrednictwem usługi ExpressRoute, jeśli obie sieci wirtualne są połączone z tym samym obwodem usługi ExpressRoute.
* Łączność obejmująca wiele subskrypcji jest obsługiwana zarówno w klasycznym modelu wdrażania, jak i w modelu wdrażania usługi Resource Manager.
* Po przeniesieniu obwodu usługi ExpressRoute z modelu klasycznego do modelu usługi Azure Resource Manager możesz [migrować sieci wirtualne połączone z obwodem usługi ExpressRoute](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane
W tej sekcji opisano, jakie operacje nie są obsługiwane dla obwodów usługi ExpressRoute:

* Zarządzanie cyklem życia obwodu usługi ExpressRoute z klasycznego modelu wdrażania.
* Obsługa kontroli dostępu opartej na rolach (RBAC) dla klasycznego modelu wdrażania. W klasycznym modelu wdrażania nie można wykonywać kontrolek RBAC do obwodu. Dowolny administrator/współadministrator subskrypcji może połączyć sieci wirtualne z obwodem lub je od niego odłączyć.

## <a name="configuration"></a>Konfiguracja
Wykonaj instrukcje opisane w artykule [Move an ExpressRoute circuit from the classic to the Resource Manager deployment model](expressroute-howto-move-arm.md) (Przenoszenie obwodu usługi ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Resource Manager).

## <a name="next-steps"></a>Następne kroki
* [Migrowanie sieci wirtualnych połączonych z obwodem usługi ExpressRoute z modelu klasycznego do modelu usługi Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Informacje o przepływach pracy można znaleźć w artykule [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Przepływy pracy inicjowania obsługi obwodu i stany obwodu usługi ExpressRoute).
* Konfigurowanie połączenia usługi ExpressRoute:
  
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-arm.md)

