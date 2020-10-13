---
title: '& wdrażania metryk w usłudze HPA przy użyciu Azure Monitor dla kontenerów | Microsoft Docs'
description: W tym artykule opisano, jakie informacje o wdrożeniu & HPA (w horyzontalnym skalowaniu automatycznego) są zbierane za pomocą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570531"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>Wdrażanie metryk & HPA z Azure Monitor dla kontenerów

Począwszy od wersji agenta *ciprod08072020*, usługa Azure monitor dla kontenerów — zintegrowany Agent teraz zbiera metryki dla wdrożeń & HPAs.

## <a name="deployment-metrics"></a>Metryki wdrożenia

Azure Monitor kontenerów automatycznie uruchamia monitorowanie wdrożeń, zbierając następujące metryki w przedziałach 60 sekund i przechowując je w tabeli **InsightMetrics** :

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Łączna liczba gotowych do użycia numerów w tym wdrożeniu (status. readyReplicas). Poniżej znajdują się wymiary tej metryki. <ul> <li> wdrożenie — nazwa wdrożenia </li> <li> k8sNamespace-Kubernetes — przestrzeń nazw dla wdrożenia </li> <li> deploymentStrategy — strategia wdrażania służąca do zastępowania elementów podstych nowymi typami (spec. Strategia. Type)</li><li> creationTime — sygnatura czasowa tworzenia wdrożenia </li> <li> spec_replicas — Liczba żądanych zasobników (specyfikacji. Replica) </li> <li>status_replicas_available — całkowita liczba dostępnych zasobników (gotowych dla co najmniej minReadySeconds) objętych tym wdrożeniem (status. availableReplicas)</li><li>status_replicas_updated — całkowita liczba niezakończonych zasobników objętych tym wdrożeniem, które mają wymaganą specyfikację szablonu (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA metryk

Azure Monitor dla kontenerów automatycznie uruchamia monitorowanie HPAs, przez gromadzenie następujących metryk w interwałach 60 sekund i przechowywanie ich w tabeli **InsightMetrics** :

|Nazwa metryki |Wymiar metryki (Tagi) |Opis |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, TargetName | Bieżąca liczba replik numerów platformy zarządzanych przez ten Autoskalowanie (status. currentReplicas). Poniżej znajdują się wymiary tej metryki. <ul> <li> hPa — nazwa HPA </li> <li> k8sNamespace-Kubernetes przestrzeń nazw dla HPA </li> <li> lastScaleTime — ostatnim razem, gdy HPA przeskalowana liczbę zasobników (stan. lastScaleTime)</li><li> Sygnatura czasowa tworzenia creationTime-HPA </li> <li> spec_max_replicas-górny limit liczby numerów osi, które mogą być ustawiane przez Autoskalowanie (spec. maxReplicas) </li> <li> spec_min_replicas dolny limit liczby replik, do których Skalowanie automatyczne można skalować w dół (spec. minReplicas) </li><li>status_desired_replicas wymagana liczba replik numerów platformy zarządzanych przez ten Autoskalowanie (status. desiredReplicas)</li><li>targetKind — typ elementu docelowego HPA (spec. scaleTargetRef. Kind) </li><li>TargetName-nazwa elementu docelowego HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Diagramy wdrożenia & HPA 

Azure Monitor kontenerów zawiera wstępnie skonfigurowane wykresy dla metryk wymienionych wcześniej w tabeli jako skoroszyt dla każdego klastra. Wdrożenia & HPA skoroszytów można znaleźć **& hPa** bezpośrednio z klastra AKS, wybierając pozycję **skoroszyty** z okienka po lewej stronie, a następnie z listy rozwijanej **Wyświetl skoroszyty** w szczegółowych informacjach.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [metryki stanu polecenia w Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) , aby dowiedzieć się więcej o metrykach polecenia-State.