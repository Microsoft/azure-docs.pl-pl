---
title: Co to jest łącze prywatne platformy Azure?
description: Omówienie funkcji łączy prywatnych platformy Azure, architektury i implementacji. Dowiedz się, jak działają prywatne punkty końcowe platformy Azure oraz usługa Azure Private link i jak z nich korzystać.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 6a85bfe7b3390b32fc220000b0c710b5a4e35067
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496494"
---
# <a name="what-is-azure-private-link"></a>Co to jest łącze prywatne platformy Azure? 
Link prywatny platformy Azure umożliwia dostęp do usług Azure PaaS Services (na przykład Azure Storage i SQL Database) oraz hostowanych usług partnerów/partnerskich platformy Azure w ramach [prywatnego punktu końcowego](private-endpoint-overview.md) w sieci wirtualnej.

Ruch między siecią wirtualną a usługą porusza się w sieci szkieletowej firmy Microsoft. Ujawnienie usługi do publicznej sieci Internet nie jest już konieczne. Możesz utworzyć własną [prywatną usługę linku](private-link-service-overview.md) w sieci wirtualnej i dostarczyć jej klientom. Konfiguracja i użycie przy użyciu prywatnego linku platformy Azure jest spójne w ramach usług Azure PaaS, należących do klienta i współużytkowanych partnerów.

> [!IMPORTANT]
> Usługa Azure Private link jest teraz ogólnie dostępna. Ogólnie dostępne są zarówno prywatne, jak i prywatne usługi linkowe (usługa za usługę w warstwie Standardowa). Różne harmonogramy zostaną dołączone do prywatnego linku platformy Azure PaaS. Sprawdź [dostępność linku prywatnego](availability.md) w celu uzyskania dokładnego stanu usługi Azure PaaS na linku prywatnym. Aby uzyskać znane ograniczenia, zobacz [prywatny punkt końcowy](private-endpoint-overview.md#limitations) i [Usługa łącza prywatnego](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Prywatne centrum połączenia platformy Azure w Azure Portal" border="false":::

## <a name="key-benefits"></a>Najważniejsze korzyści
Połączenie prywatne platformy Azure zapewnia następujące korzyści:  
- **Usługi dostępu prywatnego na platformie Azure**: łączenie sieci wirtualnej z usługami na platformie Azure bez publicznego adresu IP w źródle lub miejscu docelowym. Dostawcy usług mogą renderować swoje usługi w swoich własnych sieciach wirtualnych, a konsumenci mogą uzyskiwać dostęp do tych usług w lokalnej sieci wirtualnej. Platforma linków prywatnych będzie obsługiwała łączność między klientem i usługami za pośrednictwem sieci szkieletowej platformy Azure. 
 
- **Sieci lokalne i równorzędne**: usługi dostępu uruchomione na platformie Azure pochodzą z zasobów lokalnych za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, tuneli VPN i równorzędnych sieci wirtualnych za pomocą prywatnych punktów końcowych. Nie ma potrzeby konfigurowania ExpressRoute komunikacji równorzędnej firmy Microsoft lub przechodzenia przez Internet w celu uzyskania dostępu do usługi. Link prywatny umożliwia bezpieczną metodę migrowania obciążeń do platformy Azure.
 
- **Ochrona przed wyciekiem danych**: prywatny punkt końcowy jest mapowany do wystąpienia zasobu PaaS zamiast całej usługi. Konsumenci mogą łączyć się tylko z określonym zasobem. Dostęp do dowolnego innego zasobu w usłudze jest zablokowany. Mechanizm ten zapewnia ochronę przed ryzykiem wycieku danych. 
 
- **Globalne zasięg**: Połącz się prywatnie z usługami działającymi w innych regionach. Sieć wirtualna konsumenta może znajdować się w regionie A i może łączyć się z usługami za linkiem prywatnym w regionie B.  
 
- Zapoznaj **się z własnymi usługami**: Włącz te same czynności i funkcje, aby umożliwić prywatną pracę usługi dla klientów na platformie Azure. Przez umieszczenie usługi za standardową Azure Load Balancer można ją włączyć dla linku prywatnego. Konsument może następnie połączyć się bezpośrednio z usługą przy użyciu prywatnego punktu końcowego we własnej sieci wirtualnej. Żądania połączenia można zarządzać przy użyciu przepływu wywołań zatwierdzenia. Połączenie prywatne platformy Azure działa dla klientów i usług należących do różnych dzierżawców Azure Active Directory. 

## <a name="availability"></a>Dostępność 

Aby uzyskać informacje na temat usług platformy Azure, które obsługują link prywatny, zobacz [dostępność linku prywatnego platformy Azure](availability.md).

Aby zapoznać się z najbardziej aktualnymi powiadomieniami, sprawdź [stronę aktualizacje linku prywatnego platformy Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Połączenie prywatne platformy Azure ma integrację z usługą Azure Monitor. Ta kombinacja umożliwia:

 - Archiwizowanie dzienników na koncie magazynu.
 - Przesyłanie strumieniowe zdarzeń do centrum zdarzeń.
 - Rejestrowanie usługi Azure Monitor

Na Azure Monitor można uzyskać dostęp do następujących informacji: 
- **Prywatny punkt końcowy**: 
    - Dane przetworzone przez prywatny punkt końcowy (WE/OUT)
 
- **Usługa link prywatny**:
    - Dane przetwarzane przez usługę linku prywatnego (WE/wychodzącym)
    - Dostępność portów NAT  
 
## <a name="pricing"></a>Cennik   
Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Azure Private link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Często zadawane pytania  
Często zadawane pytania można znaleźć w sekcji [często zadawane pytania dotyczące linków prywatnych platformy Azure](private-link-faq.md).
 
## <a name="limits"></a>Limity  
Aby uzyskać limity, zobacz [limity linków prywatnych platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Umowa dotycząca poziomu usług
W przypadku umowy SLA Zobacz temat [Umowa SLA dla prywatnego linku platformy Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal](create-private-endpoint-portal.md)
- [Szybki Start: Tworzenie usługi linku prywatnego przy użyciu Azure Portal](create-private-link-service-portal.md)
