---
title: Omówienie Kubernetes z funkcją Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ten artykuł zawiera omówienie Kubernetes z funkcją Azure Arc.
keywords: Kubernetes, łuk, Azure, kontenery
ms.custom: references_regions
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309938"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co to jest usługa Azure Arc — wersja zapoznawcza Kubernetes?

Możesz dołączać i konfigurować klastry Kubernetes wewnątrz lub na zewnątrz platformy Azure przy użyciu usługi Azure Arc w wersji zapoznawczej. Po dołączeniu klastra Kubernetes do usługi Azure Arc zostanie on wyświetlony w Azure Portal. Będzie on miał identyfikator Azure Resource Manager i zarządzaną tożsamość. Klastry są dołączone do standardowych subskrypcji platformy Azure, znajdują się w grupie zasobów i mogą odbierać Tagi tak samo jak każdy inny zasób platformy Azure. 

Aby połączyć klaster Kubernetes z platformą Azure, administrator klastra musi wdrożyć agentów. Ci agenci działają w przestrzeni nazw Kubernetes o nazwie `azure-arc` i są wdrożeniami Standard Kubernetes. Agenci są odpowiedzialni za łączność z platformą Azure, zbieranie dzienników i metryk usługi Azure ARC oraz monitorowanie żądań konfiguracji. 

Usługa Azure Arc Kubernetes obsługuje standardową branżę protokołu SSL w celu zabezpieczania danych podczas przesyłania. Ponadto dane są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB, aby zapewnić poufność danych.
 
 > [!NOTE]
> Kubernetes z funkcją Azure Arc jest w wersji zapoznawczej. Nie jest to zalecane w przypadku obciążeń produkcyjnych. 


## <a name="supported-scenarios"></a>Obsługiwane scenariusze 

Usługa Azure Arc w Kubernetes obsługuje następujące scenariusze: 

* Połącz Kubernetes uruchomione poza platformą Azure w celu spisu, grupowania i tagowania.

* Wdrażaj aplikacje i stosuj konfigurację przy użyciu funkcji zarządzania konfiguracją opartą na GitOps. 

* Użyj Azure Monitor dla kontenerów do wyświetlania i monitorowania klastrów. 

* Stosowanie zasad przy użyciu Azure Policy dla Kubernetes. 

 
## <a name="supported-regions"></a>Obsługiwane regiony 

Usługa Azure Kubernetes z włączoną obsługą Arc jest obecnie obsługiwana w następujących regionach: 

* Wschodnie stany USA 
* Europa Zachodnia 

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra](./connect-cluster.md)
