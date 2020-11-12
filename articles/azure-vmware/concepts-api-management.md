---
title: Pojęcia — API Management
description: Dowiedz się, jak API Management chroni interfejsy API działające na maszynach wirtualnych rozwiązań VMware platformy Azure.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541967"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management publikowania i ochrony interfejsów API działających na maszynach wirtualnych opartych na rozwiązaniach VMware platformy Azure

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) umożliwia bezpieczne publikowanie do użytkowników wewnętrznych lub zewnętrznych.  Tylko wersje SKU Developer i Premium umożliwiają integrację z usługą Azure Virtual Network w celu publikowania interfejsów API działających w ramach obciążeń rozwiązań VMware platformy Azure.  Obie jednostki SKU bezpiecznie zapewniają łączność między usługą API Management i zapleczem. 

>[!NOTE]
>Jednostka SKU dla deweloperów jest przeznaczona do programowania i testowania, a jednostka SKU Premium to dla wdrożeń produkcyjnych.

Konfiguracja API Management jest taka sama dla usług zaplecza, które są uruchamiane na maszynach wirtualnych rozwiązań VMware platformy Azure i lokalnie. W przypadku obu wdrożeń program API Management konfiguruje wirtualny adres IP (VIP) w usłudze równoważenia obciążenia jako punkt końcowy zaplecza, gdy serwer wewnętrznej bazy danych jest umieszczony za NSX Load Balancer w rozwiązaniu VMware platformy Azure. 


## <a name="external-deployment"></a>Wdrożenie zewnętrzne

Wdrożenie zewnętrzne publikuje interfejsy API używane przez użytkowników zewnętrznych przy użyciu publicznego punktu końcowego. Deweloperzy i inżynierowie DevOps mogą zarządzać interfejsami API za pomocą Azure Portal lub programu PowerShell oraz portalu deweloperów API Management.

Zewnętrzny diagram wdrażania pokazuje cały proces i zaangażowane aktory (widoczne u góry). Aktory są następujące:

- **Administratorzy:** Reprezentuje zespół admin lub DevOps, który zarządza rozwiązaniem VMware platformy Azure za pomocą mechanizmów Azure Portal i automatyzacji, takich jak PowerShell lub Azure DevOps.

- **Użytkownicy:**  Reprezentuje odbiorców dostępnych interfejsów API i reprezentuje użytkowników i usługi korzystające z interfejsów API.

Przepływ ruchu przechodzi przez wystąpienie API Management, które jest abstrakcyjne usług zaplecza, podłączone do sieci wirtualnej centrum. Brama ExpressRoute kieruje ruch do kanału ExpressRoute Global Reach i osiągnie NSX Load Balancer rozpowszechnić ruch przychodzący do różnych wystąpień usług zaplecza.

API Management ma publicznego interfejsu API platformy Azure i zalecane jest Aktywowanie usługi Azure DDOS Protection. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Wdrożenie zewnętrzne — API Management rozwiązania VMware dla platformy Azure":::


## <a name="internal-deployment"></a>Wdrożenie wewnętrzne

Wdrożenie wewnętrzne publikuje interfejsy API używane przez użytkowników wewnętrznych lub systemy. Deweloperzy zespołu DevOps i interfejsów API używają tych samych narzędzi do zarządzania i portalu dla deweloperów, jak w przypadku wdrożenia zewnętrznego.

Wdrożenie wewnętrzne można przeprowadzić [za pomocą usługi Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) , aby utworzyć publiczny i bezpieczny punkt końcowy dla interfejsu API.  Możliwości bramy służą do tworzenia wdrożenia hybrydowego, które umożliwia wykonywanie różnych scenariuszy.  

* Użyj tego samego zasobu API Management do wykorzystania przez użytkowników wewnętrznych i zewnętrznych.

* Istnieje pojedynczy zasób API Management z podzbiorem interfejsów API zdefiniowanych i dostępnych dla użytkowników zewnętrznych.

* Umożliwia łatwe przełączenie dostępu do API Management z publicznej sieci Internet.

Na poniższym diagramie wdrożenia przedstawiono odbiorców, którzy mogą być wewnętrznymi lub zewnętrznymi, przy czym każdy typ uzyskuje dostęp do tych samych lub różnych interfejsów API.

W ramach wewnętrznego wdrożenia interfejsy API są dostępne dla tego samego wystąpienia API Management. Przed API Management, Application Gateway można wdrożyć przy użyciu funkcji Zapora aplikacji sieci Web (WAF) platformy Azure. Wdrożono również zestaw odbiorników HTTP i reguł służących do filtrowania ruchu, uwidaczniając tylko podzestaw usług zaplecza działających w rozwiązaniu VMware platformy Azure.


* Wewnętrzne trasy ruchu za pomocą bramy ExpressRoute do zapory platformy Azure, a następnie do API Management, bezpośrednio lub za pomocą reguł ruchu.   

* Ruch zewnętrzny przechodzi do platformy Azure za pośrednictwem Application Gateway, który używa warstwy ochrony zewnętrznej dla API Management.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Wdrożenie wewnętrzne — API Management rozwiązania VMware dla platformy Azure" lightbox="media/api-management/internal-deployment.png":::