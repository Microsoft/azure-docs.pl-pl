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
ms.openlocfilehash: 7f43f50fc35b5ecbc9720224036ea13e2ba753f5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894941"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integracja usług Azure Kubernetes Services z usługą Security Center

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft służąca do opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi. 

Włącz usługę Azure **Defender for Kubernetes** , aby uzyskać lepszy wgląd w węzły AKS, ruch w chmurze i mechanizmy kontroli zabezpieczeń.

Wspólnie Security Center i AKS tworzą najlepszą Kubernetesą w chmurze.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Jakie są składniki Security Center ochrony Kubernetes?

Security Center ochrony Kubernetes są udostępniane przez kombinację dwóch elementów:

- **Azure Security Center ochronę przed zagrożeniami dla maszyn wirtualnych** — przy użyciu tego samego agenta log Analytics, który Security Center jest używany na innych maszynach wirtualnych, Security Center mogą pokazać problemy z zabezpieczeniami występujące w węzłach AKS. Agent monitoruje również obsługę analizy specyficznej dla kontenera.

- **Opcjonalny Azure Security Center usługi Azure Defender for Kubernetes** — plan Kubernetes odbiera dzienniki i informacje z podsystemu Kubernetes za pośrednictwem usługi AKS. Te dzienniki są już dostępne na platformie Azure za pomocą usługi AKS. Po włączeniu usługi Azure Defender dla Kubernetes można przyznać Security Center dostęp do dzienników. Dlatego Security Center zapewnia korzyści z zabezpieczeń dla klastrów AKS przy użyciu danych już zebranych przez główny węzeł AKS. Niektóre dane skanowane przez Azure Security Center ze środowiska Kubernetes mogą zawierać informacje poufne.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Container Registry](azure-container-registry-integration.md)

* [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) — zawiera opis zasad dotyczących danych usług firmy Microsoft (w tym Azure, Intune i Microsoft 365), szczegółów zarządzania danymi firmy Microsoft oraz zasad przechowywania, które mają wpływ na dane
