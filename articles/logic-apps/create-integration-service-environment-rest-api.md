---
title: Tworzenie środowisk usługi integracji (ISEs) za pomocą interfejsu API REST Logic Apps
description: Utwórz środowisko usługi integracji (ISE) za pomocą interfejsu API REST Logic Apps, dzięki czemu możesz uzyskiwać dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550661"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST usługi Logic Apps

W przypadku scenariuszy, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md), można utworzyć [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) za pomocą interfejsu API REST Logic Apps. Aby dowiedzieć się więcej na temat ISEs, zobacz [dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps](connect-virtual-network-vnet-isolated-environment-overview.md).

W tym artykule pokazano, jak utworzyć ISE za Logic Apps pomocą interfejsu API REST. Opcjonalnie można również włączyć [tożsamość zarządzaną przez system lub przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) w ISE, ale tylko za pomocą interfejsu API REST Logic Apps w tym momencie. Ta tożsamość pozwala ISE uwierzytelniać dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy lub usługi, które znajdują się w usłudze lub są połączone z siecią wirtualną platformy Azure. Dzięki temu nie musisz logować się przy użyciu swoich poświadczeń.

Aby uzyskać więcej informacji o innych sposobach tworzenia ISE, zobacz następujące artykuły:

* [Tworzenie ISE przy użyciu Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Tworzenie ISE przy użyciu przykładowego szablonu szybkiego startu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Utwórz element ISE, który obsługuje używanie kluczy zarządzanych przez klienta do szyfrowania danych w spoczynku](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Te same [warunki wstępne](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) i [wymagania dotyczące dostępu](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jak w przypadku tworzenia ISE w Azure Portal

* Wszelkie dodatkowe zasoby, które mają być używane z ISE, aby można było uwzględnić je w definicji ISE, na przykład: 

  * Aby włączyć obsługę certyfikatów z podpisem własnym, należy dołączyć informacje o tym certyfikacie w definicji ISE.

  * Aby włączyć tożsamość zarządzaną przypisaną przez użytkownika, należy utworzyć tę tożsamość z wyprzedzeniem i uwzględnić `objectId` `principalId` `clientId` właściwości i ich wartości w definicji ISE. Aby uzyskać więcej informacji, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Narzędzie, za pomocą którego można utworzyć ISE przez wywołanie interfejsu API REST Logic Apps przy użyciu żądania HTTPS PUT. Na przykład można użyć programu [Poster](https://www.getpostman.com/downloads/)lub można utworzyć aplikację logiki, która wykonuje to zadanie.

## <a name="create-the-ise"></a>Tworzenie ISE

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

W treści żądania Podaj definicję zasobu, która ma być używana do tworzenia ISE, w tym informacje o dodatkowych możliwościach, które mają być włączone w Twoim ISE, na przykład:

* Aby utworzyć ISE, który zezwala na używanie certyfikatu z podpisem własnym i certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa, który został zainstalowany w danej `TrustedRoot` lokalizacji, należy uwzględnić `certificates` obiekt wewnątrz `properties` sekcji definicji ISE, jak opisano w tym artykule.

* Aby utworzyć ISE, który używa tożsamości zarządzanej przypisanej do systemu lub przypisanej przez użytkownika, Dołącz `identity` obiekt z typem tożsamości zarządzanej oraz inne wymagane informacje w definicji ISE, jak opisano w dalszej części tego artykułu.

* Aby utworzyć ISE, który korzysta z kluczy zarządzanych przez klienta i Azure Key Vault do szyfrowania danych przechowywanych w usłudze REST, należy uwzględnić [informacje umożliwiające obsługę klucza zarządzanego przez klienta](customer-managed-keys-integration-service-environment.md). Klucze zarządzane przez klienta można skonfigurować *tylko podczas tworzenia*, a nie później.

### <a name="request-body-syntax"></a>Składnia treści żądania

Poniżej przedstawiono składnię treści żądania opisującą właściwości używane podczas tworzenia ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
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
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
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
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
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
}
```

## <a name="add-custom-root-certificates"></a>Dodawanie niestandardowych certyfikatów głównych

Często używasz ISE do łączenia się z usługami niestandardowymi w sieci wirtualnej lub lokalnie. Te niestandardowe usługi są często chronione przez certyfikat wystawiony przez niestandardowy urząd certyfikacji przedsiębiorstwa, taki jak urząd certyfikacji lub certyfikat z podpisem własnym. Aby uzyskać więcej informacji o korzystaniu z certyfikatów z podpisem własnym, zobacz [bezpieczny dostęp i dostęp do danych dla wywołań wychodzących do innych usług i systemów](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Aby ISE pomyślnie łączył się z tymi usługami za poorednictwem usługi Transport Layer Security (TLS), ISE musi mieć dostęp do tych certyfikatów głównych.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Zagadnienia dotyczące dodawania niestandardowych certyfikatów głównych

Przed aktualizacją ISE za pomocą niestandardowego zaufanego certyfikatu głównego, zapoznaj się z następującymi kwestiami:

* Upewnij się, że przekażesz certyfikat główny *i* wszystkie certyfikaty pośrednie. Maksymalna liczba certyfikatów wynosi 20.

* Przekazywanie certyfikatów głównych jest operacją zastępczą, w której ostatnie przekazanie zastępuje poprzednie operacje przekazywania. Na przykład w przypadku wysłania żądania przekazującego jeden certyfikat, a następnie wysłania kolejnego żądania przekazania innego certyfikatu, ISE używa tylko drugiego certyfikatu. Jeśli musisz użyć obu certyfikatów, Dodaj je razem w tym samym żądaniu.  

* Przekazywanie certyfikatów głównych jest operacją asynchroniczną, która może zająć trochę czasu. Aby sprawdzić stan lub wynik, można wysłać `GET` żądanie przy użyciu tego samego identyfikatora URI. Komunikat odpowiedzi zawiera `provisioningState` pole, które zwraca wartość, `InProgress` gdy operacja przekazywania nadal działa. Gdy `provisioningState` wartość jest równa `Succeeded` , operacja przekazywania zostanie zakończona.

#### <a name="request-syntax"></a>Składnia żądania

Aby zaktualizować ISE za pomocą niestandardowego zaufanego certyfikatu głównego, wyślij następujące żądanie poprawki HTTPS do [adresu URL Azure Resource Manager, który różni się w zależności od środowiska platformy Azure](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane), na przykład:

| Środowisko | Adres URL Azure Resource Manager |
|-------------|----------------------------|
| Azure Global (wiele dzierżawców) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| 21Vianet: Microsoft Azure — Chiny | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Składnia żądania dodania niestandardowych certyfikatów głównych

Poniżej przedstawiono składnię treści żądania opisującą właściwości, które mają być używane podczas dodawania certyfikatów głównych:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
