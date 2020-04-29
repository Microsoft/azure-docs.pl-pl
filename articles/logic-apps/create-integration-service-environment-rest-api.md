---
title: Tworzenie środowisk usługi integracji (ISEs) za pomocą interfejsu API REST Logic Apps
description: Utwórz środowisko usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps, dzięki czemu możesz uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478832"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Tworzenie środowiska usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps

W tym artykule przedstawiono sposób tworzenia [ *środowiska usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) za pomocą interfejsu API REST Logic Apps w scenariuszach, w których aplikacje logiki i konta integracji muszą mieć dostęp do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). ISE to środowisko izolowane korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od "globalnej" Logic Apps z wieloma dzierżawcami. Ta separacja również zmniejsza wpływ innych dzierżawców platformy Azure na wydajność aplikacji. ISE udostępnia także własne statyczne adresy IP. Te adresy IP są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, wielodostępnej usłudze.

Możesz również utworzyć ISE za pomocą [przykładowego Azure Resource Manager szablonu szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) lub przy użyciu [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki, które działają w ISE, korzystają z planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Te same wymagania [wstępne](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) i [wymagania dotyczące włączania dostępu do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako podczas tworzenia ISE w Azure Portal

* Narzędzie, za pomocą którego można utworzyć ISE przez wywołanie interfejsu API REST Logic Apps przy użyciu żądania HTTPS PUT. Na przykład można użyć programu [Poster](https://www.getpostman.com/downloads/)lub można utworzyć aplikację logiki, która wykonuje to zadanie.

## <a name="send-the-request"></a>Wysyłanie żądania

Aby utworzyć ISE przez wywołanie interfejsu API REST Logic Apps, wykonaj to żądanie HTTP PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Wersja interfejsu API REST Logic Apps 2019-05-01 wymaga wprowadzenia własnego żądania HTTP PUT dla łączników ISE.

Wdrożenie zazwyczaj trwa w ciągu dwóch godzin. Czasami wdrożenie może trwać do czterech godzin. Aby sprawdzić stan wdrożenia, w [Azure Portal](https://portal.azure.com)na pasku narzędzi platformy Azure wybierz ikonę powiadomienia, która spowoduje otwarcie okienka powiadomienia.

> [!NOTE]
> Jeśli wdrożenie nie powiedzie się lub usuniesz ISE, platforma Azure może upłynąć do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być konieczne odczekanie przed ponownym użyciem tych podsieci w innym ISE.
>
> Po usunięciu sieci wirtualnej platforma Azure zazwyczaj zajmie maksymalnie dwie godziny przed zwolnieniem podsieci, ale ta operacja może trwać dłużej. 
> Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
> Zobacz [usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Nagłówek żądania

W nagłówku żądania uwzględnij następujące właściwości:

* `Content-type`: Ustaw tę wartość właściwości na `application/json`.

* `Authorization`: Ustaw tę wartość właściwości na token okaziciela dla klienta, który ma dostęp do subskrypcji platformy Azure lub grupy zasobów, której chcesz użyć.

### <a name="request-body-syntax"></a>Składnia treści żądania

Poniżej przedstawiono składnię treści żądania opisującą właściwości używane podczas tworzenia ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Przykład treści żądania

W tej przykładowej treści żądania pokazano przykładowe wartości:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

