---
title: Azure Security Center i usługa Azure Kubernetes
description: Poznaj integrację Azure Security Center z usługami Azure Kubernetes Services
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800185"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integracja usług Azure Kubernetes Services z usługą Security Center

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft służąca do opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi. 

Jeśli jesteś w warstwie Standardowa Azure Security Center, możesz dodać pakiet AKS (zobacz [Cennik](security-center-pricing.md)), aby uzyskać lepszy wgląd w węzły AKS, ruch w chmurze i mechanizmy kontroli zabezpieczeń.

Wspólnie Security Center i AKS tworzą najlepszą Kubernetesą w chmurze.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Jakie są składniki Security Center ochrony Kubernetes?

Security Center ochrony Kubernetes są udostępniane przez kombinację dwóch elementów:

- **Azure Security Center ochronę przed zagrożeniami dla maszyn wirtualnych** — przy użyciu tego samego agenta log Analytics, który Security Center jest używany na innych maszynach wirtualnych, Security Center mogą pokazać problemy z zabezpieczeniami występujące w węzłach AKS. Agent monitoruje również obsługę analizy specyficznej dla kontenera.

- **Opcjonalny pakiet Kubernetes Azure Security Center** — pakiet Kubernetes odbiera dzienniki i informacje z podsystemu Kubernetes za pośrednictwem usługi AKS. Te dzienniki są już dostępne na platformie Azure za pomocą usługi AKS. Po włączeniu pakietu Kubernetes Security Center można udzielić Security Center dostępu do dzienników. Dlatego Security Center zapewnia korzyści z zabezpieczeń dla klastrów AKS przy użyciu danych już zebranych przez główny węzeł AKS. Niektóre dane skanowane przez Azure Security Center ze środowiska Kubernetes mogą zawierać informacje poufne.

    ![Omówienie usług Azure Security Center i Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Jakie zabezpieczenia są udostępniane?

Korzystanie z dwóch usług razem zapewnia:

* **Zalecenia dotyczące zabezpieczeń** — Security Center IDENTYFIKUJE zasoby AKS i klasyfikuje je: z klastrów do poszczególnych maszyn wirtualnych. Następnie można wyświetlić zalecenia dotyczące zabezpieczeń na zasób. Aby uzyskać więcej informacji, zobacz zalecenia dotyczące kontenerów na [liście referencyjnej zaleceń](recommendations-reference.md#recs-containers). 

* **Zabezpieczanie środowiska** — Security Center stale monitoruje konfigurację klastrów Kubernetes i konfiguracji platformy Docker. Następnie generuje zalecenia dotyczące zabezpieczeń, które odzwierciedlają standardy branżowe.

* **Ochrona w czasie wykonywania** — dzięki ciągłej analizie następujących źródeł AKS Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie klastra hosta *i* AKS. [Dowiedz się więcej o ochronie przed zagrożeniami dla kontenerów](threat-protection.md#azure-containers).


     

![Azure Security Center i usługa Azure Kubernetes Service (AKS) w bardziej szczegółowy sposób](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS z Security Center często zadawane pytania

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Czy nadal mogę uzyskać ochronę AKSą bez agenta Log Analytics?

Jak wspomniano powyżej, opcjonalny pakiet Kubernetes zapewnia ochronę na poziomie klastra, Log Analytics Agent Azure Security Center warstwy Standardowa chroni węzły. 

Zalecamy wdrożenie obu tych metod w celu zapewnienia najwyższej możliwej ochrony.

Jeśli nie zdecydujesz się na zainstalowanie agenta na hostach, będziesz otrzymywać tylko podzbiór korzyści z ochrony przed zagrożeniami i alerty zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Container Registry](azure-container-registry-integration.md)

* [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) — zawiera opis zasad dotyczących danych usług firmy Microsoft (w tym Azure, Intune i Microsoft 365), szczegółów zarządzania danymi firmy Microsoft oraz zasad przechowywania, które mają wpływ na dane
