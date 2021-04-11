---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029147"
---
Usługa Azure Defender zapewnia ochronę przed zagrożeniami w czasie rzeczywistym w środowiskach kontenerów i generuje alerty dla podejrzanych działań. Te informacje pozwalają na szybkie rozwiązywanie problemów dotyczących zabezpieczeń i poprawę bezpieczeństwa kontenerów.

Usługa Azure Defender zapewnia ochronę przed zagrożeniami na różnych poziomach: 

* **Poziom hosta (dostarczany przez usługę Azure Defender dla serwerów)** — za pomocą tego samego agenta log Analytics, który Security Center używany na innych maszynach wirtualnych, usługa Azure Defender monitoruje węzły Kubernetes systemu Linux pod kątem podejrzanych działań, takich jak wykrywanie powłoki sieci Web i połączenie z znanymi podejrzanymi adresami IP. Agent monitoruje również dla analiz specyficznych dla kontenera, takich jak tworzenie kontenera uprzywilejowanego, podejrzany dostęp do serwerów interfejsu API i serwery Secure Shell (SSH) działające wewnątrz kontenera Docker.

    Jeśli zdecydujesz się nie instalować agentów na hostach, będziesz otrzymywać tylko podzbiór korzyści z ochrony przed zagrożeniami i alerty zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

    >[!IMPORTANT]
    > Obecnie nie obsługujemy instalacji agenta Log Analytics w klastrach usługi Azure Kubernetes, które działają w ramach zestawów skalowania maszyn wirtualnych.

    Listę alertów na poziomie klastra można znaleźć w [tabeli referencyjnej alertów](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Poziom klastra (dostarczany przez usługę Azure Defender for Kubernetes)** — na poziomie klastra ochrona przed zagrożeniami opiera się na analizie dzienników inspekcji Kubernetes. Aby włączyć monitorowanie **bez agenta** , Włącz usługę Azure Defender. Jeśli klaster działa lokalnie lub w innym dostawcy chmury, Włącz [Kubernetes z włączonym łukiem i rozszerzeniem usługi Azure Defender](../articles/security-center/defender-for-kubernetes-azure-arc.md).

    Aby można było generować alerty na tym poziomie, usługa Azure Defender monitoruje dzienniki klastrów. Przykłady zdarzeń na tym poziomie obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie uprzywilejowanych ról i tworzenie poufnych instalacji.

    >[!NOTE]
    > Usługa Azure Defender generuje alerty zabezpieczeń dotyczące akcji i wdrożeń, które wystąpiły po włączeniu usługi Defender for Kubernetes w ramach subskrypcji. 

    Listę alertów na poziomie klastra można znaleźć w [tabeli referencyjnej alertów](../articles/security-center/alerts-reference.md#alerts-akscluster).

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.

> [!TIP]
> Alerty kontenera można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).