---
title: Wdrożenie & HPA metryk z usługą Container Insights | Microsoft Docs
description: W tym artykule opisano, jakie informacje o wdrożeniu & HPA (w horyzontalnym skalowaniu automatycznego) są zbierane za pomocą usługi Container Insights.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717710"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Wdrażanie metryk & HPA przy użyciu usługi Container Insights

Począwszy od wersji *ciprod08072020* agenta, Agent usługi Application Insights — jest teraz zbierał metryki dla wdrożeń & HPAs.

## <a name="deployment-metrics"></a>Metryki wdrożenia

Usługi Container Insights automatycznie zaczynają monitorować wdrożenia, zbierając następujące metryki w przedziałach 60 sekund i przechowując je w tabeli **InsightMetrics** :

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Łączna liczba gotowych do użycia numerów w tym wdrożeniu (status. readyReplicas). Poniżej znajdują się wymiary tej metryki. <ul> <li> wdrożenie — nazwa wdrożenia </li> <li> k8sNamespace-Kubernetes — przestrzeń nazw dla wdrożenia </li> <li> deploymentStrategy — strategia wdrażania służąca do zastępowania elementów podstych nowymi typami (spec. Strategia. Type)</li><li> creationTime — sygnatura czasowa tworzenia wdrożenia </li> <li> spec_replicas — Liczba żądanych zasobników (specyfikacji. Replica) </li> <li>status_replicas_available — całkowita liczba dostępnych zasobników (gotowych dla co najmniej minReadySeconds) objętych tym wdrożeniem (status. availableReplicas)</li><li>status_replicas_updated — całkowita liczba niezakończonych zasobników objętych tym wdrożeniem, które mają wymaganą specyfikację szablonu (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA metryk

Usługi Container Insights automatycznie zaczynają monitorowanie HPAs, przez gromadzenie następujących metryk w interwałach 60 sekund i przechowywanie ich w tabeli **InsightMetrics** :

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, TargetName | Bieżąca liczba replik numerów platformy zarządzanych przez ten Autoskalowanie (status. currentReplicas). Poniżej znajdują się wymiary tej metryki. <ul> <li> hPa — nazwa HPA </li> <li> k8sNamespace-Kubernetes przestrzeń nazw dla HPA </li> <li> lastScaleTime — ostatnim razem, gdy HPA przeskalowana liczbę zasobników (stan. lastScaleTime)</li><li> Sygnatura czasowa tworzenia creationTime-HPA </li> <li> spec_max_replicas-górny limit liczby numerów osi, które mogą być ustawiane przez Autoskalowanie (spec. maxReplicas) </li> <li> spec_min_replicas dolny limit liczby replik, do których Skalowanie automatyczne można skalować w dół (spec. minReplicas) </li><li>status_desired_replicas wymagana liczba replik numerów platformy zarządzanych przez ten Autoskalowanie (status. desiredReplicas)</li><li>targetKind — typ elementu docelowego HPA (spec. scaleTargetRef. Kind) </li><li>TargetName-nazwa elementu docelowego HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Diagramy wdrożenia & HPA 

Informacje o kontenerze zawierają wstępnie skonfigurowane wykresy dla metryk wymienionych wcześniej w tabeli jako skoroszyt dla każdego klastra. Wdrożenia & HPA skoroszytów można znaleźć **& hPa** bezpośrednio z klastra AKS, wybierając pozycję **skoroszyty** z okienka po lewej stronie, a następnie z listy rozwijanej **Wyświetl skoroszyty** w szczegółowych informacjach.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [metryki stanu polecenia w Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) , aby dowiedzieć się więcej o metrykach polecenia-State.