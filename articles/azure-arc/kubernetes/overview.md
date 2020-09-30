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
ms.openlocfilehash: 61317f7f5f2bf17c88fc019294574993c1854e59
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540646"
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

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Obsługiwane regiony 

Usługa Azure ARC z włączonym Kubernetes jest obecnie obsługiwana w następujących regionach: 

* East US 
* West Europe

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* Jaka jest różnica między funkcją Azure Arc Kubernetes a usługą Azure Kubernetes Service (AKS)?

    Usługa Azure Kubernetes Service (AKS) to oferta zarządzana Kubernetes oferowana przez platformę Azure. AKS ułatwia wdrażanie zarządzanego klastra Kubernetes na platformie Azure. Usługa AKS zmniejsza złożoność i nakłady operacyjne związane z zarządzaniem rozwiązaniem Kubernetes, przenosząc znaczną część tej odpowiedzialności na platformę Azure. Wzorce rozwiązania Kubernetes są zarządzane przez platformę Azure. Zarządzasz tylko węzłami agentów i obsługujesz je.

    Usługa Azure Arc Kubernetes umożliwia łączenie klastrów Kubernetes z platformą Azure w celu rozszerzania możliwości zarządzania platformy Azure, takich jak Azure Monitor i Azure Policy. Konserwacja bazowego klastra Kubernetes jest wykonywana przez użytkownika.

* Czy muszę połączyć klastry usługi Azure Kubernetes z systemem na platformie Azure z usługą Azure Arc?

    Nie. Wszystkie funkcje usługi Azure ARC z włączonym Kubernetes, takie jak Azure Monitor, Azure Policy (strażnik) są natywnie dostępne dla AKS, który już ma reprezentację zasobów na platformie Azure. Konfiguracja klastra (GitOps) jest również dostępna natywnie na AKS i obecnie w prywatnej wersji zapoznawczej. Użyj tego [formularza rejestracji](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5acO18Lmx5Bk_qao2CrOcFUQ0UyRllDR1BEV1BPNENYRERYN1pFWTQ4WC4u) , aby uzyskać dostęp do tej funkcji.
    
* Czy należy połączyć klaster AKS na Azure Stack HCL do usługi Azure Arc? Co o klastrach Kubernetes działających na Azure Stack koncentratorze lub aparacie Azure Stack?

    Tak, połączenie tych klastrów z usługą Azure Arc ma zalety. Zapewnia reprezentację zasobów dla tych klastrów Kubernetes w Azure Resource Manager. Przy użyciu tej reprezentacji zasobów można rozszerzyć takie możliwości jak konfiguracja klastra, Azure Monitor, Azure Policy (strażnik) do tych klastrów Kubernetes

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra](./connect-cluster.md)
