---
title: Usługa Azure ARC z włączonym Kubernetes często zadawane pytania
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących usługi Azure Arc Kubernetes
keywords: Kubernetes, łuk, Azure, kontenery, konfiguracja, GitOps, często zadawane pytania
ms.openlocfilehash: d7facb842509bdf852957d46d91dfcb3912e99ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121715"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Często zadawane pytania — usługa Azure ARC z włączonym Kubernetes

W tym artykule opisano często zadawane pytania dotyczące usługi Azure Arc Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Jaka jest różnica między funkcją Azure Arc Kubernetes a usługą Azure Kubernetes Service (AKS)?

AKS to Kubernetesa oferta zarządzana przez platformę Azure. AKS upraszcza wdrażanie zarządzanego klastra Kubernetes na platformie Azure przez odciążenie wielu złożoności i obciążenia operacyjnego na platformie Azure. Ponieważ wzorce Kubernetes są zarządzane przez platformę Azure, można zarządzać węzłami agentów tylko i obsługiwać je.

Usługa Azure Arc Kubernetes umożliwia rozszerzonie możliwości zarządzania platformą Azure (np. Azure Monitor i Azure Policy) przez połączenie klastrów Kubernetes z platformą Azure. Należy zachować sam klaster Kubernetes.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Czy muszę połączyć moje klastry AKS uruchomione na platformie Azure z usługą Azure Arc?

Nie. Wszystkie funkcje Kubernetes z funkcją Azure ARC, w tym Azure Monitor i Azure Policy (strażnik), są dostępne w AKS (zasób natywny w Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Czy należy połączyć klaster AKS-HCL i klastry Kubernetes w centrum Azure Stack i Azure Stack Edge do usługi Azure Arc?

Tak, połączenie klastra AKS-HCl lub klastrów Kubernetes w usłudze Azure Stack Edge lub Azure Stack Hub do usługi Azure Arc zapewnia klastry z reprezentacją zasobów w Azure Resource Manager. Ta reprezentacja zasobów rozszerza możliwości, takie jak konfiguracja klastra, Azure Monitor i Azure Policy (strażnik) do połączonych klastrów Kubernetes.

Jeśli klaster Kubernetes z obsługą usługi Azure Arc jest włączony Azure Stack Edge, AKS na Azure Stack HCl (aktualizacja >2021 Update) lub AKS w systemie Windows Server 2019 Datacenter (>= kwiecień 2021 Update), wówczas konfiguracja Kubernetes jest dołączona bezpłatnie.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Jak rozwiązać wygasłe zasoby usługi Azure Arc Kubernetes?

Certyfikat tożsamość usługi zarządzanej (MSI) skojarzony z włączonym Kubernetesem usługi Azure Arc ma okno wygasające o 90 dni. Po wygaśnięciu tego certyfikatu zasób jest brany pod uwagę, `Expired` a wszystkie funkcje (takie jak konfiguracja, monitorowanie i zasady) przestają działać w tym klastrze. Aby ponownie rozpocząć pracę klastra Kubernetes z usługą Azure ARC:

1. Usuń Kubernetes zasobów i agentów usługi Azure Arc w klastrze. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Utwórz ponownie zasób Kubernetes z funkcją Azure ARC, wdrażając agentów w klastrze.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` spowoduje również usunięcie konfiguracji w klastrze. Po uruchomieniu `az connectedk8s connect` programu należy ponownie utworzyć konfiguracje w klastrze, ręcznie lub przy użyciu Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Czy w przypadku używania potoków ciągłej integracji i ciągłego wdrażania można nadal używać Kubernetes i konfiguracji usługi Azure Arc?

Tak. można nadal używać konfiguracji w klastrze, który otrzymuje wdrożenia za pośrednictwem potoku ciągłej integracji/ciągłego dostarczania. W porównaniu z tradycyjnymi potokami ciągłej integracji/ciągłego wdrażania, konfiguracje funkcji mają dwie dodatkowe zalety:

**Uzgadnianie dryfu**

Potok CI/CD stosuje zmiany tylko raz podczas uruchomienia potoku. Jednak operator GitOps w klastrze ciągle sonduje repozytorium Git w celu pobrania żądanego stanu zasobów Kubernetes w klastrze. Jeśli operator GitOps odnajdzie żądany stan zasobów, które różnią się od rzeczywistego stanu zasobów w klastrze, nastąpi uzgadnianie tego dryfu.

**Zastosuj GitOps na dużą skalę**

Potoki ciągłej integracji/ciągłego wdrażania są przydatne w przypadku wdrożeń opartych na zdarzeniach w klastrze Kubernetes (na przykład wypychania do repozytorium Git). Jeśli jednak chcesz wdrożyć tę samą konfigurację we wszystkich klastrach Kubernetes, musisz ręcznie skonfigurować poświadczenia klastra Kubernetes w potoku ciągłej integracji/ciągłego wdrażania. 

W przypadku usługi Azure ARC z włączonym Kubernetes, ponieważ program Azure Resource Manager zarządza konfiguracjami, można zautomatyzować tworzenie tej samej konfiguracji we wszystkich zasobach Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Policy, w zakresie subskrypcji lub grupy zasobów. Ta funkcja jest nawet stosowana do zasobów Kubernetes z obsługą usługi Azure Arc utworzonych po przypisaniu zasad.

Ta funkcja stosuje konfiguracje bazowe (takie jak zasady sieci, powiązania ról i zasady zabezpieczeń na poziomie) w całym spisie klastra Kubernetes, aby spełnić wymagania dotyczące zgodności i zarządzania.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z naszym przewodnikiem Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./connect-cluster.md).
* Masz już klaster Kubernetes połączony z usługą Azure Arc? [Utwórz konfiguracje w klastrze Kubernetes z włączonym łukiem](./use-gitops-connected-cluster.md).
* Dowiedz się [, jak używać Azure Policy do stosowania konfiguracji w odpowiedniej skali](./use-azure-policy.md).