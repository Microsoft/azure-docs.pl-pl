---
title: Azure Arc — Omówienie Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, łuk, Azure, kontenery
ms.custom: references_regions
ms.openlocfilehash: badade7e89d0b03f330bfbe3578d02429220001b
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194856"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co to jest usługa Azure Arc w Kubernetes (wersja zapoznawcza)

Możesz dołączać i konfigurować klastry Kubernetes wewnątrz lub na zewnątrz platformy Azure przy użyciu usługi Azure Arc Kubernetes (wersja zapoznawcza). Po dołączeniu klastra Kubernetes do usługi Azure Arc zostanie on wyświetlony w witrynie Azure Portal, mieć identyfikator Azure Resource Manager i zarządzaną tożsamość. Klastry są dołączone do standardowych subskrypcji platformy Azure, na żywo w grupie zasobów i mogą otrzymywać Tagi podobne do innych zasobów platformy Azure. 


Połączenie klastra Kubernetes z platformą Azure wymaga od administratora klastra wdrożenia agentów. Ci agenci działają w przestrzeni nazw Kubernetes o nazwie `azure-arc` i są wdrożeniami Standard Kubernetes. Agenci są odpowiedzialni za łączność z platformą Azure, zbieranie dzienników i metryk usługi Azure ARC oraz monitorowanie żądań konfiguracji.  
 
 > [!NOTE]
> Usługa Azure ARC z włączonym Kubernetes jest dostępna w wersji zapoznawczej i nie jest zalecana w przypadku obciążeń produkcyjnych. 


## <a name="supported-scenarios"></a>Scenariusze obsługiwane 

Usługa Azure ARC z włączonym Kubernetes obsługuje następujące scenariusze: 

* Łączenie Kubernetes działających poza platformą Azure na potrzeby spisu, grupowania i tagowania 

* Wdrażanie aplikacji i stosowanie konfiguracji przy użyciu funkcji zarządzania konfiguracją opartą na GitOps 

* Używanie Azure Monitor do kontenerów do wyświetlania i monitorowania klastrów 

* Stosowanie zasad przy użyciu Azure Policy dla Kubernetes 

 
## <a name="supported-regions"></a> Obsługiwane regiony 

Usługa Azure ARC z włączonym Kubernetes jest obecnie obsługiwana w następujących regionach: 

* Wschodnie stany USA 
* Europa Zachodnia 


## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra](./connect-cluster.md)
