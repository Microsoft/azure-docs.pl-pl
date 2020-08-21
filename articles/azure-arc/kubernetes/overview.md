---
title: Omówienie usługi Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ten artykuł zawiera omówienie usługi Azure Arc Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery
ms.custom: references_regions
ms.openlocfilehash: 885c96b83edb83cfb62fc117d9b4406792827056
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723728"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co to jest usługa Azure Arc w wersji zapoznawczej Kubernetes?

Możesz dołączać i konfigurować klastry Kubernetes wewnątrz lub na zewnątrz platformy Azure przy użyciu usługi Azure Arc w wersji zapoznawczej Kubernetes. Po dołączeniu klastra Kubernetes do usługi Azure Arc zostanie on wyświetlony w Azure Portal. Będzie on miał identyfikator Azure Resource Manager i zarządzaną tożsamość. Klastry są dołączone do standardowych subskrypcji platformy Azure, znajdują się w grupie zasobów i mogą odbierać Tagi tak samo jak każdy inny zasób platformy Azure. 

Aby połączyć klaster Kubernetes z platformą Azure, administrator klastra musi wdrożyć agentów. Ci agenci działają w przestrzeni nazw Kubernetes o nazwie `azure-arc` i są wdrożeniami Standard Kubernetes. Agenci są odpowiedzialni za łączność z platformą Azure, zbieranie dzienników i metryk usługi Azure ARC oraz monitorowanie żądań konfiguracji. 

Usługa Azure Arc Kubernetes obsługuje standardową branżę protokołu SSL w celu zabezpieczania danych podczas przesyłania. Ponadto dane są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB, aby zapewnić poufność danych.
 
> [!NOTE]
> Usługa Azure ARC z włączonym Kubernetes jest dostępna w wersji zapoznawczej. Nie jest to zalecane w przypadku obciążeń produkcyjnych.

## <a name="supported-kubernetes-distributions"></a>Obsługiwane dystrybucje Kubernetes

Usługa Azure ARC z włączonym Kubernetes współpracuje z dowolnym klastrem Kubernetes w chmurze z certyfikatem macierzystym (CNCF), takim jak aparat AKS na platformie Azure, AKS-Engine w Azure Stack Hub, GKE, EKS i VMware vSphere.

Funkcja Kubernetes z funkcją Azure Arc została przetestowana przez zespół Arc w następujących dystrybucjach:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanoniczny panel Kubernetes 1,18
* Aparat AKS
* Aparat AKS w centrum Azure Stack
* Dostawca interfejsu API klastra Azure

## <a name="supported-scenarios"></a>Obsługiwane scenariusze 

Usługa Azure ARC z włączoną obsługą Kubernetes obsługuje następujące scenariusze: 

* Połącz Kubernetes uruchomione poza platformą Azure w celu spisu, grupowania i tagowania.

* Wdrażaj aplikacje i stosuj konfigurację przy użyciu funkcji zarządzania konfiguracją opartą na GitOps. 

* Użyj Azure Monitor dla kontenerów do wyświetlania i monitorowania klastrów. 

* Stosowanie zasad przy użyciu Azure Policy dla Kubernetes. 

## <a name="supported-regions"></a>Obsługiwane regiony 

Usługa Azure ARC z włączonym Kubernetes jest obecnie obsługiwana w następujących regionach: 

* East US 
* West Europe

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra](./connect-cluster.md)
