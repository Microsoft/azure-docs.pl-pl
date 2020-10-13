---
title: Pojęcia — API Management
description: Dowiedz się, jak API Management chroni interfejsy API działające na maszynach wirtualnych rozwiązań VMware platformy Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91262981"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management publikowania i ochrony interfejsów API działających na maszynach wirtualnych opartych na rozwiązaniach VMware platformy Azure

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) umożliwia deweloperom i zespołom DevOps bezpieczne publikowanie do klientów wewnętrznych lub zewnętrznych.

Chociaż oferowane w kilku jednostkach SKU, tylko jednostki SKU Developer i Premium umożliwiają integrację z usługą Azure Virtual Network w celu publikowania interfejsów API działających w ramach obciążeń rozwiązań VMware platformy Azure. Te dwie jednostki SKU bezpiecznie umożliwiają połączenie między usługą API Management i zapleczem. Jednostka SKU dla deweloperów jest przeznaczona do programowania i testowania, a jednostka SKU Premium to dla wdrożeń produkcyjnych.

W przypadku usług zaplecza, które są uruchamiane na podstawie maszyn wirtualnych rozwiązań VMware platformy Azure, domyślnie konfiguracja w API Management jest taka sama jak w przypadku lokalnych usług zaplecza. W przypadku wdrożeń wewnętrznych i zewnętrznych usługa API Management konfiguruje wirtualny adres IP (VIP) usługi równoważenia obciążenia jako punkt końcowy zaplecza, gdy serwer wewnętrznej bazy danych jest umieszczony za NSX Load Balancer na stronie rozwiązanie platformy Azure VMware.

## <a name="external-deployment"></a>Wdrożenie zewnętrzne

Wdrożenie zewnętrzne publikuje interfejsy API używane przez użytkowników zewnętrznych przy użyciu publicznego punktu końcowego. Deweloperzy i inżynierowie DevOps mogą zarządzać interfejsami API za pomocą Azure Portal lub programu PowerShell oraz portalu deweloperów API Management.

Zewnętrzny diagram wdrażania pokazuje cały proces i zaangażowane aktory (widoczne u góry). Aktory są następujące:

- **Administratorzy:** Reprezentuje zespół admin lub DevOps, który zarządza rozwiązaniem VMware platformy Azure za pomocą mechanizmów Azure Portal i automatyzacji, takich jak PowerShell lub Azure DevOps.

- **Użytkownicy:**  Reprezentuje odbiorców dostępnych interfejsów API i reprezentuje użytkowników i usługi korzystające z interfejsów API.

Przepływ ruchu przechodzi przez wystąpienie API Management, które jest abstrakcyjne usług zaplecza, podłączone do koncentratora sieci wirtualnej. Brama ExpressRoute kieruje ruch do ExpressRoute Global Reach kanału i osiągnie NSX Load Balancer rozpowszechnić ruch przychodzący do różnych wystąpień usług zaplecza.

API Management ma publicznego interfejsu API platformy Azure i jest zalecane Aktywowanie usługi Azure DDOS Protection. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Wdrożenie zewnętrzne — API Management rozwiązania VMware dla platformy Azure":::


## <a name="internal-deployment"></a>Wdrożenie wewnętrzne

Wdrożenie wewnętrzne publikuje interfejsy API używane przez użytkowników wewnętrznych lub systemy. Deweloperzy zespołu DevOps i interfejsów API używają tych samych narzędzi do zarządzania i portalu dla deweloperów, jak w przypadku wdrożenia zewnętrznego.

Wewnętrzne wdrożenia mogą mieć [Application Gateway platformy Azure](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) , aby utworzyć publiczny i bezpieczny punkt końcowy dla interfejsu API, wykorzystując jego możliwości i tworząc wdrożenie hybrydowe, które umożliwia wykonywanie różnych scenariuszy.  Interfejs API wykorzystuje swoje możliwości i tworzy wdrożenie hybrydowe, które umożliwia wykonywanie różnych scenariuszy.

* Użyj tego samego zasobu API Management do wykorzystania przez użytkowników wewnętrznych i zewnętrznych.

* Istnieje pojedynczy zasób API Management z podzbiorem interfejsów API zdefiniowanych i dostępnych dla użytkowników zewnętrznych.

* Umożliwia łatwe przełączenie dostępu do API Management z publicznej sieci Internet.

Na poniższym diagramie wdrożenia przedstawiono odbiorców, którzy mogą być wewnętrznymi lub zewnętrznymi, przy czym każdy typ uzyskuje dostęp do tych samych lub różnych interfejsów API.

W ramach wewnętrznego wdrożenia interfejsy API są dostępne dla tego samego wystąpienia API Management. Przed API Management, Application Gateway można wdrożyć przy użyciu funkcji Zapora aplikacji sieci Web (WAF) platformy Azure aktywowanej oraz zestawu odbiorników HTTP i zasad służących do filtrowania ruchu, przez ujawnienie tylko podzestawu usług zaplecza działających w ramach rozwiązania Azure VMware.

* Ruch wewnętrzny jest kierowany przez bramę ExpressRoute do zapory platformy Azure, a następnie do API Management w przypadku ustanowienia reguł ruchu lub bezpośrednio do API Management.  

* Ruch zewnętrzny przechodzi do platformy Azure za pośrednictwem Application Gateway, który używa warstwy ochrony zewnętrznej dla API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Wdrożenie zewnętrzne — API Management rozwiązania VMware dla platformy Azure":::