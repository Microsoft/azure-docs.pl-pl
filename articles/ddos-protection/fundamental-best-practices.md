---
title: Azure DDoS Protection podstawowe najlepsze rozwiązania
description: Poznaj najlepsze rozwiązania w zakresie zabezpieczeń korzystające z ochrony DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 66c1ab1cb5ed478aa34825fb6903e4d06f834097
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94989477"
---
# <a name="fundamental-best-practices"></a>Podstawowe najlepsze rozwiązania

Poniższe sekcje zawierają wskazówki dotyczące tworzenia DDoSych odpornych na błędy usług na platformie Azure.

## <a name="design-for-security"></a>Projektowanie pod kątem zabezpieczeń

Upewnij się, że zabezpieczenia są priorytetem całego cyklu życia aplikacji, od projektowania i implementacji wdrożenia i operacji. Aplikacje mogą mieć usterkę, która pozwala na stosunkowo małą liczbę żądań w celu użycia nadmiernej ilości zasobów, co spowodowało awarię usługi. 

Aby pomóc w ochronie usługi działającej na Microsoft Azure, należy dobrze zrozumieć swoją architekturę aplikacji i skupić się na [pięciu filarach jakości oprogramowania](/azure/architecture/guide/pillars).
Należy znać typowe woluminy ruchu, model łączności między aplikacją i innymi aplikacjami oraz punkty końcowe usługi, które są dostępne dla publicznego Internetu.

Upewnienie się, że aplikacja jest wystarczająco odporna na obsługę odmowy usługi, która jest przeznaczona dla samej aplikacji, jest najważniejsza. Zabezpieczenia i prywatność są wbudowane w platformę Azure, począwszy od [cyklu projektowania zabezpieczeń (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL eliminuje bezpieczeństwo w każdej fazie tworzenia i gwarantuje, że platforma Azure jest stale aktualizowana w celu zapewnienia jeszcze większego bezpieczeństwa.

## <a name="design-for-scalability"></a>Projektowanie pod kątem skalowalności

Skalowalność to szybkość, z jaką system może obsłużyć większe obciążenie. Zaprojektuj aplikacje w celu [skalowania w poziomie](/azure/architecture/guide/design-principles/scale-out) , aby spełnić zapotrzebowanie na rozbudowane obciążenie, w zależności od przypadku ataku DDoS. Jeśli aplikacja zależy od pojedynczego wystąpienia usługi, tworzy single point of failure. Inicjowanie obsługi wielu wystąpień systemu sprawia, że system jest bardziej odporny i bardziej skalowalny.

W obszarze [Azure App Service](../app-service/overview.md)wybierz [Plan App Service](../app-service/overview-hosting-plans.md) , który oferuje wiele wystąpień. W przypadku usługi Azure Cloud Services skonfiguruj każdą rolę, aby użyć [wielu wystąpień](../cloud-services/cloud-services-choose-me.md). W przypadku [usługi Azure Virtual Machines](../virtual-machines/index.yml)upewnij się, że architektura maszyny wirtualnej (VM) zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w [zestawie dostępności](../virtual-machines/windows/tutorial-availability-sets.md). Zalecamy używanie [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md) do obsługi funkcji skalowania automatycznego.

## <a name="defense-in-depth"></a>Ochrona w głębi systemu

Pomysłem związanym z obroną jest zarządzanie ryzykiem przy użyciu różnorodnych strategii obronnych. Zabezpieczenia warstw zabezpieczeń w aplikacji zmniejszają prawdopodobieństwo pomyślnego ataku. Zalecamy wdrożenie bezpiecznych projektów dla aplikacji przy użyciu wbudowanych funkcji platformy Azure.

Na przykład ryzyko ataku zwiększa się wraz z rozmiarem (*obszar powierzchni*) aplikacji. Obszar powierzchniowy można zmniejszyć przy użyciu listy zatwierdzania, aby zamknąć uwidocznioną przestrzeń adresów IP i porty nasłuchujące, które nie są potrzebne w przypadku modułów równoważenia obciążenia ([Azure Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md) i [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/network-security-groups-overview.md) to inny sposób zmniejszania podatności na ataki.
Możesz użyć [tagów usługi](../virtual-network/network-security-groups-overview.md#service-tags) i [grup zabezpieczeń aplikacji](../virtual-network/network-security-groups-overview.md#application-security-groups) , aby zminimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieci, jako naturalnego rozszerzenia struktury aplikacji.

W miarę możliwości należy wdrożyć usługi platformy Azure w [sieci wirtualnej](../virtual-network/virtual-networks-overview.md) . Dzięki temu zasoby usług mogą komunikować się za poorednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP. Korzystanie z [punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md) spowoduje przełączenie ruchu usługi w taki sposób, aby używały prywatnych adresów sieci wirtualnej jako źródłowych adresów IP podczas uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów atakują razem ze swoimi zasobami na platformie Azure. W przypadku łączenia środowiska lokalnego z platformą Azure zalecamy zminimalizowanie zagrożeń zasobów lokalnych do publicznej sieci Internet. Możesz użyć funkcji skalowania i zaawansowanej ochrony DDoS na platformie Azure, wdrażając dobrze znane jednostki publiczne na platformie Azure. Ponieważ te publicznie dostępne jednostki są często celem ataków DDoS, umieszczenie ich na platformie Azure zmniejsza wpływ zasobów lokalnych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).