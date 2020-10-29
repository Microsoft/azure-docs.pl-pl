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
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905310"
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

W obszarze [Azure App Service](/azure/app-service/app-service-value-prop-what-is)wybierz [Plan App Service](/azure/app-service/overview-hosting-plans) , który oferuje wiele wystąpień. W przypadku usługi Azure Cloud Services skonfiguruj każdą rolę, aby użyć [wielu wystąpień](/azure/cloud-services/cloud-services-choose-me). W przypadku [usługi Azure Virtual Machines](../virtual-machines/index.yml)upewnij się, że architektura maszyny wirtualnej (VM) zawiera więcej niż jedną maszynę wirtualną i że każda maszyna wirtualna jest uwzględniona w [zestawie dostępności](../virtual-machines/windows/tutorial-availability-sets.md). Zalecamy używanie [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md) do obsługi funkcji skalowania automatycznego.

## <a name="defense-in-depth"></a>Ochrona w głębi systemu

Pomysłem związanym z obroną jest zarządzanie ryzykiem przy użyciu różnorodnych strategii obronnych. Zabezpieczenia warstw zabezpieczeń w aplikacji zmniejszają prawdopodobieństwo pomyślnego ataku. Zalecamy wdrożenie bezpiecznych projektów dla aplikacji przy użyciu wbudowanych funkcji platformy Azure.

Na przykład ryzyko ataku zwiększa się wraz z rozmiarem ( *obszar powierzchni* ) aplikacji. Obszar powierzchniowy można zmniejszyć przy użyciu listy zatwierdzania, aby zamknąć uwidocznioną przestrzeń adresów IP i porty nasłuchujące, które nie są potrzebne w przypadku modułów równoważenia obciążenia ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) i [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)). [Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)](/azure/virtual-network/security-overview) to inny sposób zmniejszania podatności na ataki.
Możesz użyć [tagów usługi](/azure/virtual-network/security-overview#service-tags) i [grup zabezpieczeń aplikacji](/azure/virtual-network/security-overview#application-security-groups) , aby zminimalizować złożoność tworzenia reguł zabezpieczeń i konfigurowania zabezpieczeń sieci, jako naturalnego rozszerzenia struktury aplikacji.

W miarę możliwości należy wdrożyć usługi platformy Azure w [sieci wirtualnej](/azure/virtual-network/virtual-networks-overview) . Dzięki temu zasoby usług mogą komunikować się za poorednictwem prywatnych adresów IP. Ruch usługi platformy Azure z sieci wirtualnej domyślnie używa publicznych adresów IP jako źródłowych adresów IP. Korzystanie z [punktów końcowych usługi](/azure/virtual-network/virtual-network-service-endpoints-overview) spowoduje przełączenie ruchu usługi w taki sposób, aby używały prywatnych adresów sieci wirtualnej jako źródłowych adresów IP podczas uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej.

Często widzimy, że zasoby lokalne klientów atakują razem ze swoimi zasobami na platformie Azure. W przypadku łączenia środowiska lokalnego z platformą Azure zalecamy zminimalizowanie zagrożeń zasobów lokalnych do publicznej sieci Internet. Możesz użyć funkcji skalowania i zaawansowanej ochrony DDoS na platformie Azure, wdrażając dobrze znane jednostki publiczne na platformie Azure. Ponieważ te publicznie dostępne jednostki są często celem ataków DDoS, umieszczenie ich na platformie Azure zmniejsza wpływ zasobów lokalnych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).