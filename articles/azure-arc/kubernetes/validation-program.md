---
title: Program Azure ARC z obsługą weryfikacji Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Opisuje program Arc Validation dla dystrybucji Kubernetes
keywords: Kubernetes, łuk, Azure, K8s, Walidacja
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121375"
---
# <a name="azure-arc-validation-program"></a>Program weryfikacji usługi Azure Arc

Usługa Azure Arc Kubernetes współpracuje z dowolnymi klastrami Kubernetes w chmurze z obsługą rozwiązań Native Computing (CNCF). Zespół usługi Azure Arc również pracował z firmowymi dostawcami ofert Kubernetes, aby sprawdzić, czy usługa Azure Arc Kubernetes z ich dystrybucjami Kubernetes. Przyszłe główne i pomocnicze wersje dystrybucji Kubernetes wydane przez tych dostawców będą weryfikowane pod kątem zgodności z usługą Azure ARC z włączonym Kubernetes.

## <a name="validated-distributions"></a>Sprawdzone dystrybucje

Poniższe firmy Microsoft Kubernetese dystrybucje i dostawcy infrastruktury pomyślnie przekazały testy zgodności dla Kubernetes z funkcją Azure ARC:

| Dostawca dystrybucji i infrastruktury | Wersja |
| ---------------------------------------- | ------- |
| Dostawca interfejsu API klastra na platformie Azure            | Wersja wydania: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Wersja Kubernetes: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS na Azure Stack HCL                   | Wersja wydania: [grudzień 2020 Update](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Wersja Kubernetes: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

Następujący dostawcy i odpowiadające im dystrybucje Kubernetes pomyślnie przekazały testy zgodności dla Kubernetes z funkcją Azure ARC:

| Nazwa dostawcy | Nazwa dystrybucji | Wersja |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Siatka Tanzu Kubernetes](https://tanzu.vmware.com/kubernetes-grid) | Wersja Kubernetes: v 1.17.5 |
| Canonical    | [Kubernetes z panelami](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Aparat Kubernetes Rancher](https://rancher.com/products/rke/) | Wersja interfejsu wiersza polecenia RKE: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Wersje Kubernetes: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Wersja 2.2.1 |

Zespół usługi Azure Arc również uruchomił testy zgodności i sprawdzone scenariusze Kubernetes z obsługą usługi Azure ARC dla następujących dostawców chmury publicznej:

| Nazwa dostawcy chmury publicznej | Nazwa dystrybucji | Wersja |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Usługa Elastic Kubernetes Service (EKS) | 1.18.9 v  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | 1.17.15 v |

## <a name="scenarios-validated"></a>Zweryfikowane scenariusze

Testy zgodności są uruchamiane jako część usługi Azure ARC, w której włączono weryfikację Kubernetes, obejmują następujące scenariusze:

1. Połącz klastry Kubernetes z usługą Azure ARC: 
    * Wdróż Kubernetes Agent Helm agenta w klastrze.
    * Skonfiguruj certyfikat tożsamości systemu zarządzanego (MSI) w klastrze.
    * Agenci wysyłają metadane klastra do platformy Azure.

2. Konfiguracja: 
    * Utwórz konfigurację na górze zasobu Kubernetes z włączoną funkcją Azure Arc.
    * [Strumień](https://docs.fluxcd.io/), który jest wymagany do skonfigurowania przepływu pracy GitOps, jest wdrażany w klastrze.
    * Strumień pobiera manifesty i wykresy Helm z demonstracji repozytorium git i wdraża je w klastrze.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak połączyć klaster z usługą Azure Arc.
> [!div class="nextstepaction"]
> [Łączenie klastra z usługą Azure Arc](./quickstart-connect-cluster.md)
