---
title: Omówienie usługi Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ten artykuł zawiera omówienie usługi Azure Arc Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652534"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Co to jest platforma Kubernetes z włączoną usługą Azure Arc?

Dzięki usłudze Azure Arc Kubernetes można dołączać i konfigurować klastry Kubernetes znajdujące się wewnątrz lub na zewnątrz platformy Azure. Po nawiązaniu połączenia z klastrem Kubernetes do usługi Azure ARC:
* Występuje w Azure Portal z IDENTYFIKATORem Azure Resource Manager i tożsamością zarządzaną. 
* Być dołączane do standardowych subskrypcji platformy Azure.
* Należy umieścić w grupie zasobów.
* Odbieraj Tagi tak samo jak każdy inny zasób platformy Azure. 

Aby połączyć klaster Kubernetes z platformą Azure, administrator klastra musi wdrożyć agentów. Ci agenci:
* Uruchom w `azure-arc` przestrzeni nazw Kubernetes jako standardowe wdrożenia Kubernetes.
* Obsługa łączności z platformą Azure.
* Zbierz dzienniki i metryki usługi Azure Arc.
* Śledzenie żądań konfiguracji. 

Usługa Azure Arc Kubernetes obsługuje standardową branżę protokołu SSL w celu zabezpieczania danych podczas przesyłania. Te dane są przechowywane w postaci zaszyfrowanej i w bazie danych Azure Cosmos DB, aby zapewnić poufność danych.
 
## <a name="supported-kubernetes-distributions"></a>Obsługiwane dystrybucje Kubernetes

Usługa Azure Arc Kubernetes współpracuje z dowolnym klastrem Kubernetes w chmurze z certyfikatem macierzystym (CNCF), takim jak:
* AKS — silnik na platformie Azure
* AKS — silnik w Azure Stack Hub
* GKE
* EKS
* VMware vSphere

Funkcja Kubernetes z funkcją Azure Arc została przetestowana przez zespół Arc w następujących dystrybucjach:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanoniczny panel Kubernetes 1,18
* Aparat usługi AKS
* Aparat AKS w centrum Azure Stack
* AKS na Azure Stack HCL
* Dostawca interfejsu API klastra Azure

## <a name="supported-scenarios"></a>Obsługiwane scenariusze 

Usługa Azure ARC z włączonym Kubernetes obsługuje następujące scenariusze: 

* Połącz Kubernetes uruchomione poza platformą Azure w celu spisu, grupowania i tagowania.

* Wdrażaj aplikacje i stosuj konfigurację przy użyciu funkcji zarządzania konfiguracją opartą na GitOps. 

* Wyświetlanie i monitorowanie klastrów przy użyciu Azure Monitor kontenerów. 

* Zastosuj zasady za pomocą Azure Policy dla Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Obsługiwane regiony 

Usługa Azure ARC z włączonym Kubernetes jest obecnie obsługiwana w następujących regionach: 

* East US 
* West Europe

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra](./connect-cluster.md)
