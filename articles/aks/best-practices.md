---
title: Najlepsze rozwiązania dotyczące usługi Azure Kubernetes Service (AKS)
description: Kolekcja operatorów klastra i najlepszych rozwiązań dla deweloperów do kompilowania aplikacji i zarządzania nimi w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: e8f9c1946567ac3c3ea1045a216d6b0486fd959a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94686263"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące operatorów i deweloperów do kompilowania aplikacji i zarządzania nimi w usłudze Azure Kubernetes Service (AKS)

Aby pomyślnie kompilować i uruchamiać aplikacje w usłudze Azure Kubernetes Service (AKS), należy zapoznać się z najważniejszymi zagadnieniami dotyczącymi zrozumienia i implementacji. Te obszary obejmują funkcje obejmujące wiele dzierżaw i usługi Scheduler, klaster i zabezpieczenia, a także ciągłość biznesową i odzyskiwanie po awarii. Następujące najlepsze rozwiązania są pogrupowane w celu ułatwienia operatorom i deweloperom klastrów zrozumienia zagadnień dla każdego z tych obszarów i wdrożenia odpowiednich funkcji.

Te najlepsze rozwiązania i artykuły koncepcyjne zostały nadane w połączeniu z grupą produktów AKS, zespołami inżynieryjnymi i zespołami pól, w tym na globalnych białych bełtch (GBBs).

## <a name="cluster-operator-best-practices"></a>Najlepsze rozwiązania operatorów klastrów

Jako operator klastra współpracują z właścicielami i programistami aplikacji, aby zrozumieć ich potrzeby. W razie potrzeby można użyć następujących najlepszych rozwiązań w celu skonfigurowania klastrów AKS.

**Obsługa wielu dzierżawców**

* [Najlepsze rozwiązania związane z izolacją klastra](operator-best-practices-cluster-isolation.md)
    * Zawiera podstawowe składniki i izolację logiczną z przestrzeni nazw.
* [Najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler](operator-best-practices-scheduler.md)
    * Obejmuje korzystanie z przydziałów zasobów i budżetów na przerwy.
* [Najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler](operator-best-practices-advanced-scheduler.md)
    * Obejmuje użycie przyrządów i tolerowania, selektorów węzłów i koligacji oraz koligacji między i.
* [Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji](operator-best-practices-identity.md)
    * Obejmuje integrację z Azure Active Directory przy użyciu kontroli dostępu opartej na rolach (Kubernetes RBAC) przy użyciu usług Azure RBAC i tożsamości pod.

**Bezpieczeństwo**

* [Najlepsze rozwiązania dotyczące zabezpieczeń i uaktualnień klastra](operator-best-practices-cluster-security.md)
    * Obejmuje zabezpieczenie dostępu do serwera interfejsu API, ograniczenie dostępu do kontenera oraz zarządzanie uaktualnieniami i ponownymi uruchomieniami węzła.
* [Najlepsze rozwiązania dotyczące zarządzania obrazami kontenerów i zabezpieczeń](operator-best-practices-container-image-management.md)
    * Obejmuje zabezpieczenie obrazu i środowiska uruchomieniowego oraz zautomatyzowane kompilacje w ramach aktualizacji obrazu podstawowego.
* [Najlepsze rozwiązania dotyczące zabezpieczeń pod kątem bezpieczeństwa](developer-best-practices-pod-security.md)
    * Obejmuje Zabezpieczanie dostępu do zasobów, ograniczanie zagrożeń poświadczeń oraz korzystanie z tożsamości i magazynów kluczy cyfrowych.

**Sieć i magazyn**

* [Najlepsze rozwiązania związane z łącznością sieciową](operator-best-practices-network.md)
    * Obejmuje różne modele sieci przy użyciu protokołu przychodzącego i zapory aplikacji sieci Web (WAF) oraz zabezpieczanie węzłów dostępu SSH.
* [Najlepsze rozwiązania dotyczące przechowywania i tworzenia kopii zapasowych](operator-best-practices-storage.md)
    * Obejmuje wybór odpowiedniego typu magazynu i rozmiaru węzła, dynamiczne Inicjowanie obsługi woluminów i kopii zapasowych danych.

**Uruchamianie obciążeń gotowych dla przedsiębiorstw**

* [Najlepsze rozwiązania związane z ciągłością biznesową i odzyskiwaniem po awarii](operator-best-practices-multi-region.md)
    * Obejmuje używanie par regionów, wielu klastrów z platformą Azure Traffic Manager i replikację geograficzną obrazów kontenerów.

## <a name="developer-best-practices"></a>Najlepsze rozwiązania dla deweloperów

Jako twórca lub właściciel aplikacji możesz uprościć środowisko programistyczne i zdefiniować wymagania dotyczące wydajności aplikacji.

* [Najlepsze rozwiązania dotyczące zarządzania zasobami dla deweloperów aplikacji](developer-best-practices-resource-management.md)
    * Obejmuje definiowanie żądań zasobów i limitów, Konfigurowanie narzędzi programistycznych i sprawdzanie problemów z aplikacjami.
* [Najlepsze rozwiązania dotyczące zabezpieczeń pod kątem bezpieczeństwa](developer-best-practices-pod-security.md)
    * Obejmuje Zabezpieczanie dostępu do zasobów, ograniczanie zagrożeń poświadczeń oraz korzystanie z tożsamości i magazynów kluczy cyfrowych.

## <a name="kubernetes--aks-concepts"></a>Koncepcje Kubernetes/AKS

Aby ułatwić zrozumienie niektórych funkcji i składników tych najlepszych rozwiązań, można także zapoznać się z następującymi artykułami koncepcyjnymi dotyczącymi klastrów w usłudze Azure Kubernetes Service (AKS):

* [Podstawowe pojęcia Kubernetes](concepts-clusters-workloads.md)
* [Dostęp i tożsamość](concepts-identity.md)
* [Pojęcia dotyczące zabezpieczeń](concepts-security.md)
* [Pojęcia dotyczące sieci](concepts-network.md)
* [Opcje magazynu](concepts-storage.md)
* [Opcje skalowania](concepts-scale.md)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz rozpocząć pracę z programem AKS, wykonaj jedną z przewodników Szybki Start, aby wdrożyć klaster usługi Azure Kubernetes Service (AKS) przy użyciu [interfejsu wiersza polecenia platformy Azure](kubernetes-walkthrough.md) lub [Azure Portal](kubernetes-walkthrough-portal.md).
