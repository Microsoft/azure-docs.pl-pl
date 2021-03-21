---
title: Omówienie usługi Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Ten artykuł zawiera omówienie usługi Azure Arc Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121596"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Co to jest platforma Kubernetes z włączoną usługą Azure Arc?

Dzięki usłudze Azure Arc Kubernetes można dołączać i konfigurować klastry Kubernetes znajdujące się wewnątrz lub na zewnątrz platformy Azure. Po nawiązaniu połączenia z klastrem Kubernetes do usługi Azure ARC:
* Występuje w Azure Portal z IDENTYFIKATORem Azure Resource Manager i tożsamością zarządzaną. 
* Są umieszczane w subskrypcji platformy Azure i grupie zasobów.
* Odbieraj Tagi tak samo jak każdy inny zasób platformy Azure. 

Aby połączyć klaster Kubernetes z platformą Azure, administrator klastra musi wdrożyć agentów. Ci agenci:
* Uruchom w `azure-arc` przestrzeni nazw Kubernetes jako standardowe wdrożenia Kubernetes.
* Obsługa łączności z platformą Azure.
* Zbierz dzienniki i metryki usługi Azure Arc.
* Śledzenie żądań konfiguracji. 

Usługa Azure Arc Kubernetes obsługuje standardową branżę protokołu SSL w celu zabezpieczania danych podczas przesyłania. Ponadto dane są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB, aby zapewnić poufność danych.

## <a name="supported-kubernetes-distributions"></a>Obsługiwane dystrybucje Kubernetes

Usługa Azure Arc Kubernetes współpracuje z dowolnymi klastrami Kubernetes w chmurze z obsługą rozwiązań Native Computing (CNCF). Zespół usługi Azure Arc pracował z [najważniejszymi partnerami branżowymi, aby zweryfikować zgodność](./validation-program.md) ich dystrybucji Kubernetes z włączonym Kubernetesem Azure Arc.

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
* Zachodnio-środkowe stany USA
* South Central US
* Southeast Asia
* Południowe Zjednoczone Królestwo
* Zachodnie stany USA 2
* Australia Wschodnia
* Wschodnie stany USA 2
* Europa Północna

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak połączyć klaster z usługą Azure Arc.
> [!div class="nextstepaction"]
> [Łączenie klastra z usługą Azure Arc](./quickstart-connect-cluster.md)
