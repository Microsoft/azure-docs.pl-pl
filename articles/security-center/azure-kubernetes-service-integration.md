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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436178"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integracja usług Azure Kubernetes Services z usługą Security Center

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft służąca do opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi. 

Używaj AKS razem z warstwą standardowa Azure Security Center (zobacz [Cennik](security-center-pricing.md)), aby uzyskać lepszy wgląd w węzły AKS, ruch w chmurze i mechanizmy zabezpieczeń.

Security Center zapewnia korzyści związane z zabezpieczeniami dla klastrów AKS przy użyciu danych już zebranych przez główny węzeł AKS. 

![Omówienie usług Azure Security Center i Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Razem te dwa narzędzia tworzą najlepszą natywną Kubernetesą ofertę zabezpieczeń w chmurze. 

## <a name="benefits-of-integration"></a>Zalety integracji

Korzystanie z dwóch usług razem zapewnia:

* **Zalecenia dotyczące zabezpieczeń** — Security Center IDENTYFIKUJE zasoby AKS i klasyfikuje je: z klastrów do poszczególnych maszyn wirtualnych. Następnie można wyświetlić zalecenia dotyczące zabezpieczeń na zasób. Aby uzyskać więcej informacji, zobacz zalecenia dotyczące kontenerów na [liście referencyjnej zaleceń](recommendations-reference.md#recs-containers). 

* **Zabezpieczanie środowiska** — Security Center stale monitoruje konfigurację klastrów Kubernetes i konfiguracji platformy Docker. Następnie generuje zalecenia dotyczące zabezpieczeń, które odzwierciedlają standardy branżowe.

* **Ochrona w czasie wykonywania** — dzięki ciągłej analizie następujących źródeł AKS Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie klastra hosta *i* AKS:
    * Surowe zdarzenia zabezpieczeń, takie jak dane sieciowe i tworzenie procesów
    * Dziennik inspekcji Kubernetes

    Aby uzyskać więcej informacji, zobacz [Ochrona przed zagrożeniami dla kontenerów platformy Azure](threat-protection.md#azure-containers)

    Aby uzyskać listę możliwych alertów, zobacz następujące sekcje w tabeli referencyjnej alertów: [AKS alertów na poziomie klastra](alerts-reference.md#alerts-akscluster) i na [poziomie hosta kontenera](alerts-reference.md#alerts-containerhost).  

![Azure Security Center i usługa Azure Kubernetes Service (AKS) w bardziej szczegółowy sposób](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Niektóre dane skanowane przez Azure Security Center ze środowiska Kubernetes mogą zawierać informacje poufne.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Container Registry](azure-container-registry-integration.md)

* [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) — zawiera opis zasad dotyczących danych usług firmy Microsoft (w tym Azure, Intune i Office 365), szczegółów zarządzania danymi firmy Microsoft oraz zasad przechowywania, które mają wpływ na dane
