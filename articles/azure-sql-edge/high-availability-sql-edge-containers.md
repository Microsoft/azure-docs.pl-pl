---
title: Wysoka dostępność kontenerów usługi Azure SQL Edge — Azure SQL Edge
description: Więcej informacji na temat wysokiej dostępności kontenerów usługi Azure SQL Edge
keywords: SQL Edge, kontenery, wysoka dostępność
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937627"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Wysoka dostępność kontenerów usługi Azure SQL Edge

Twórz i Zarządzaj wystąpieniami usługi Azure SQL Edge natywnie w Kubernetes. Wdróż usługę Azure SQL Edge do kontenerów platformy Docker zarządzanych przez usługę [Kubernetes](https://kubernetes.io/). W Kubernetes kontener z wystąpieniem usługi Azure SQL Edge może zostać automatycznie odzyskany na wypadek awarii węzła klastra. Obraz kontenera programu SQL Edge można skonfigurować za pomocą Kubernetes trwałego (PVC). Kubernetes monitoruje proces programu Azure SQL Edge w kontenerze. Jeśli proces, pod, kontener lub węzeł zakończy się niepowodzeniem, Kubernetes automatycznie uruchomi kolejne wystąpienie i ponownie nawiąże połączenie z magazynem.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kontenery usługi Azure SQL Edge w witrynie Kubernetes

Kubernetes 1,6 i nowsze obsługują [*klasy magazynu*](https://kubernetes.io/docs/concepts/storage/storage-classes/), [*trwałe oświadczenia woluminów*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

W tej konfiguracji Kubernetes odgrywa rolę koordynatora kontenerów. 

![Diagram usługi Azure SQL Edge w klastrze Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

Na powyższym diagramie `azure-sql-edge` jest kontenerem w elemencie [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes organizuje zasoby w klastrze. [Zestaw replik](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) gwarantuje, że element pod zostanie automatycznie odzyskany po awarii węzła. Aplikacje nawiązują połączenie z usługą. W takim przypadku usługa reprezentuje moduł równoważenia obciążenia, który hostuje adres IP, który pozostaje taki sam po awarii `azure-sql-edge` .

Na poniższym diagramie `azure-sql-edge` kontener nie powiódł się. Ponieważ koordynator Kubernetes gwarantuje poprawną liczbę wystąpień w dobrej kondycji w zestawie replik i uruchamia nowy kontener zgodnie z konfiguracją. Program Orchestrator uruchamia nowe miejsce w tym samym węźle i `azure-sql-edge` ponownie nawiązuje połączenie z tym samym magazynem trwałym. Usługa nawiązuje połączenie z nowo utworzoną usługą `azure-sql-edge` .

![Usługa Azure SQL Edge w klastrze Kubernetes po awarii elementu pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

Na poniższym diagramie węzeł, w którym znajduje `azure-sql-edge` się kontener, nie powiódł się. Program Orchestrator uruchamia nowy element pod innym węzłem i `azure-sql-edge` nawiązuje połączenie z tym samym magazynem trwałym. Usługa nawiązuje połączenie z nowo utworzoną usługą `azure-sql-edge` .

![Usługa Azure SQL Edge w klastrze Kubernetes po awarii węzła](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Aby utworzyć kontener w programie Kubernetes, zobacz [wdrażanie kontenera usługi Azure SQL Edge w Kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć kontenery usługi Azure SQL Edge w usłudze Azure Kubernetes Service (AKS), zobacz następujące artykuły:
- [Wdrażanie kontenera usługi Azure SQL Edge w Kubernetes](deploy-Kubernetes.md)
- [Machine Learning i sztuczna inteligencja przy użyciu ONNX w programie SQL Edge](onnx-overview.md).
- [Tworzenie kompleksowego rozwiązania IoT za pomocą programu SQL Edge przy użyciu IoT Edge](tutorial-deploy-azure-resources.md).
- [Przesyłanie strumieniowe danych w usłudze Azure SQL Edge](stream-data.md)