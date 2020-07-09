---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800612"
---
Security Center zapewnia ochronę przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów i generuje alerty dla podejrzanych działań. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Security Center zapewnia ochronę przed zagrożeniami na różnych poziomach: 

* **Poziom hosta** — Agent log Analytics monitoruje system Linux dla podejrzanych działań. Agent wyzwala alerty dotyczące podejrzanych działań pochodzących z węzła lub kontenera, w którym jest uruchomiona. Przykłady takich działań obejmują wykrywanie i nawiązywanie powłoki sieci Web przy użyciu znanych podejrzanych adresów IP.

    Aby uzyskać dokładniejszy wgląd w zabezpieczenia środowiska kontenerowego, agent monitoruje analizę specyficzną dla kontenera. Spowoduje to wyzwolenie alertów dotyczących zdarzeń, takich jak tworzenie kontenera uprzywilejowanego, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera Docker.

    >[!IMPORTANT]
    > Jeśli zdecydujesz się nie instalować agentów na hostach, będziesz otrzymywać tylko podzbiór korzyści z ochrony przed zagrożeniami i alerty zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

    Listę alertów na poziomie hosta AKS można znaleźć w [tabeli referencyjnej alertów](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost).


* Na **poziomie klastra AKS**ochrona przed zagrożeniami jest oparta na analizie dzienników inspekcji Kubernetes. Aby włączyć monitorowanie **bez wykorzystania agentów** , Dodaj opcję Kubernetes do subskrypcji na stronie **ustawień & cenowej** (zobacz [Cennik](https://docs.microsoft.com/azure/security-center/security-center-pricing)). Aby generować alerty na tym poziomie, Security Center monitoruje usługi zarządzane przez AKS przy użyciu dzienników pobranych przez AKS. Przykłady zdarzeń na tym poziomie obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie uprzywilejowanych ról i tworzenie poufnych instalacji.

    >[!NOTE]
    > Security Center generuje alerty zabezpieczeń dotyczące akcji i wdrożeń usługi Azure Kubernetes w przypadku włączenia opcji Kubernetes w ustawieniach subskrypcji. 

    Listę alertów na poziomie klastra AKS można znaleźć w [tabeli referencyjnej alertów](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster).

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.

> [!TIP]
> Alerty kontenera można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).