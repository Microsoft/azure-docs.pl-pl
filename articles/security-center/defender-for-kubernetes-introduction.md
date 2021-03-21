---
title: Azure Defender for Kubernetes — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender for Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 83d0215ebca9d60d61937cb20bb82c7ccb30aac1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100631"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Wprowadzenie do usługi Azure Defender dla Kubernetes

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft służąca do opracowywania i wdrażania aplikacji kontenerowych oraz zarządzania nimi.

Azure Security Center i AKS tworzą natywną i niezależną od chmury ofertę zabezpieczeń Kubernetes z ograniczeniami środowiska, ochroną obciążeń i ochroną w czasie wykonywania, jak opisano w temacie [zabezpieczenia kontenerów w Security Center](container-security.md).

W celu wykrywania zagrożeń dla klastrów Kubernetes Włącz **usługę Azure Defender dla Kubernetes**.

Wykrywanie zagrożeń na poziomie hosta dla węzłów AKS systemu Linux jest dostępne po włączeniu [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md) i agenta log Analytics. Jeśli jednak klaster AKS został wdrożony w zestawie skalowania maszyn wirtualnych, Agent Log Analytics nie jest obecnie obsługiwany.

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|**Usługa Azure Defender for Kubernetes** jest rozliczana zgodnie z [cennikiem Security Center](https://azure.microsoft.com/pricing/details/security-center/)|
|Wymagane role i uprawnienia:|**Administrator zabezpieczeń** może odrzucić alerty.<br>**Czytelnik zabezpieczeń** może przeglądać wyniki.|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Jakie korzyści zapewnia usługa Azure Defender for Kubernetes?

Usługa Azure Defender for Kubernetes zapewnia **ochronę przed zagrożeniami na poziomie klastra** przez monitorowanie usług zarządzanych przez program AKS za pomocą dzienników pobranych przez usługę Azure Kubernetes Service (AKS).

Przykłady zdarzeń zabezpieczeń, które usługa Azure Defender dla monitorów Kubernetes obejmują uwidocznione pulpity nawigacyjne Kubernetes, tworzenie ról o wysokim poziomie uprawnień i Tworzenie instalacji poufnych. Pełną listę alertów na poziomie klastra AKS można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Alerty kontenera można symulować, postępując zgodnie z instrukcjami zawartymi w [tym wpisie w blogu](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Ponadto nasz globalny zespół badaczy ds. zabezpieczeń stale monitoruje zagrożenie w poziomie. Po ich odnalezieniu są dodawane alerty i luki specyficzne dla kontenera.

>[!NOTE]
> Security Center generuje alerty zabezpieczeń dotyczące akcji i wdrożeń usługi Azure Kubernetes, które są wykonywane **po** włączeniu usługi Azure Defender dla Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender for Kubernetes — często zadawane pytania

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Czy nadal mogę uzyskać ochronę AKSą bez agenta Log Analytics?

**Usługa Azure Defender for Kubernetes** plan zapewnia ochronę na poziomie klastra. Jeśli wdrożono również agenta Log Analytics **usługi Azure Defender dla serwerów**, uzyskasz ochronę przed zagrożeniami dla węzłów, które są dostępne w ramach tego planu. Dowiedz się więcej w temacie [wprowadzenie do usługi Azure Defender dla serwerów](defender-for-servers-introduction.md).

Zalecamy wdrożenie obu tych metod w celu zapewnienia najwyższej możliwej ochrony.

Jeśli nie zdecydujesz się na zainstalowanie agenta na hostach, otrzymasz tylko podzestaw korzyści z ochrony przed zagrożeniami i alertów zabezpieczeń. Nadal będziesz otrzymywać alerty dotyczące analizy sieci i komunikacji ze złośliwymi serwerami.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Czy AKS można instalować niestandardowe rozszerzenia maszyny wirtualnej w węzłach AKS?
Do monitorowania węzłów AKS w usłudze Azure Defender musi być uruchomiony agent Log Analytics. 

AKS jest usługą zarządzaną, a ponieważ agent usługi log Analytics jest rozszerzeniem zarządzanym przez firmę Microsoft, jest również obsługiwany w klastrach AKS.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Czy w klastrze Azure Monitor dla agenta kontenerów jest już potrzebny Agent Log Analytics?
Do monitorowania węzłów AKS w usłudze Azure Defender musi być uruchomiony agent Log Analytics.

Jeśli w klastrach jest już uruchomiony Azure Monitor dla agenta kontenerów, można zainstalować agenta Log Analytics, a dwa agenci mogą działać równolegle ze sobą bez jakichkolwiek problemów.

[Dowiedz się więcej na temat Azure monitor dla agenta kontenerów](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje na temat Security Center ochrony Kubernetes, w tym usługi Azure Defender dla Kubernetes. 

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](enable-azure-defender.md)

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- [Przesyłanie strumieniowe alertów do rozwiązania SIEM, o lub zarządzania usługami IT](export-to-siem.md)
- [Tabela referencyjna alertów](alerts-reference.md)
