---
title: Tworzenie środowisk usługi integracji (ISEs) za pomocą interfejsu API REST Logic Apps
description: Utwórz środowisko usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps, dzięki czemu możesz uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 427b488fe6673bef505fccdaa7185d69437bceaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231320"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Tworzenie środowiska usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps

W tym artykule przedstawiono sposób tworzenia [ *środowiska usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) za pomocą interfejsu API REST Logic Apps w scenariuszach, w których aplikacje logiki i konta integracji muszą mieć dostęp do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Środowisko usługi integracji (ISE) to dedykowane środowisko, korzystające z dedykowanego magazynu i innych zasobów, które są oddzielone od „globalnej” wielodostępnej usługi Logic Apps. Ta separacja również zmniejsza wpływ innych dzierżawców platformy Azure na wydajność aplikacji. Środowisko ISE zapewnia Ci także własne statyczne adresy IP. Te adresy IP są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, wielodostępnej usłudze.

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

* `Content-type`: Ustaw tę wartość właściwości na `application/json` .

* `Authorization`: Ustaw tę wartość właściwości na token okaziciela dla klienta, który ma dostęp do subskrypcji platformy Azure lub grupy zasobów, której chcesz użyć.

<a name="request-body"></a>

## <a name="request-body"></a>Treść żądania

Poniżej przedstawiono składnię treści żądania opisującą właściwości używane podczas tworzenia ISE. Aby utworzyć ISE, który zezwala na używanie certyfikatu z podpisem własnym, który jest zainstalowany w danej `TrustedRoot` lokalizacji, Uwzględnij `certificates` obiekt wewnątrz sekcji definicji ISE `properties` . W przypadku istniejącej ISE można wysłać żądanie PATCH tylko dla `certificates` obiektu. Aby uzyskać więcej informacji o korzystaniu z certyfikatów z podpisem własnym, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań wychodzących do innych usług i systemów](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

