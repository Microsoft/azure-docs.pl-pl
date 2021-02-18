---
title: Konfiguracje i GitOps — usługa Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera ogólne omówienie możliwości GitOps i konfiguracji na platformie Azure Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery, konfiguracja, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652551"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfiguracje i GitOps za pomocą usługi Azure Arc Kubernetes

W odniesieniu do Kubernetes, GitOps jest celem deklarowania żądanego stanu konfiguracji klastra Kubernetes (wdrożenia, przestrzenie nazw itp.) w repozytorium git. Po tej deklaracji następuje rozmieszczenie i wdrażanie tych konfiguracji klastra przy użyciu operatora. Repozytorium git może zawierać:
* YAML manifesty opisujące wszystkie ważne zasoby Kubernetes, w tym obszary nazw, ConfigMaps, wdrożenia, DaemonSets itp.
* Wykresy Helm na potrzeby wdrażania aplikacji.

[Strumień](https://docs.fluxcd.io/), popularne narzędzie typu "open source" w przestrzeni GitOps, można wdrożyć w klastrze Kubernetes w celu ułatwienia przepływu konfiguracji z repozytorium git do klastra Kubernetes. Strumień obsługuje wdrożenie jego operatora zarówno w zakresie klastra, jak i przestrzeni nazw. Operator strumienia wdrożony z zakresem przestrzeni nazw może wdrażać tylko obiekty Kubernetes należące do tej konkretnej przestrzeni nazw. Możliwość wyboru między zakresem klastra lub przestrzeni nazw ułatwia osiągnięcie wzorców wdrożenia z wieloma dzierżawcami w tym samym klastrze Kubernetes.

## <a name="configurations"></a>Konfiguracje

[![Architektura ](./media/conceptual-configurations.png) konfiguracji](./media/conceptual-configurations.png#lightbox)

Połączenie między klastrem a repozytorium git jest tworzone jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` zasób rozszerzenia na górze zasobu Kubernetes z włączoną funkcją Azure ARC (reprezentowane przez `Microsoft.Kubernetes/connectedClusters` ) w Azure Resource Manager. 

`sourceControlConfiguration`Właściwości zasobów służą do wdrażania operatora strumienia w klastrze z odpowiednimi parametrami, takimi jak repozytorium git, z którego mają być pobierane manifesty, oraz interwału sondowania, w którym mają być ściągane. `sourceControlConfiguration`Dane są przechowywane w postaci zaszyfrowanej i w bazie danych Azure Cosmos DB, aby zapewnić poufność danych.

`config-agent`Działający w klastrze jest odpowiedzialny za:
* Śledzenie nowych lub zaktualizowanych `sourceControlConfiguration` zasobów rozszerzeń w zasobie usługi Azure ARC z włączonym Kubernetes.
* Wdrożenie operatora strumieniowego w celu obejrzenia repozytorium git dla każdej z nich `sourceControlConfiguration` .
* Zastosowanie aktualizacji do dowolnego z nich `sourceControlConfiguration` . 

Można utworzyć wiele zasobów o zakresie przestrzeni nazw `sourceControlConfiguration` w tym samym klastrze Kubernetes z obsługą usługi Azure Arc w celu osiągnięcia wielu dzierżawców.

> [!NOTE]
> * `config-agent` ciągle monitoruje nowe lub zaktualizowane `sourceControlConfiguration` zasoby rozszerzeń dostępne w zasobie usługi Azure Arc Kubernetes. W ten sposób Agenci muszą mieć spójną łączność, aby ściągnąć właściwości żądanego stanu do klastra. Jeśli agenci nie mogą połączyć się z platformą Azure, żądany stan nie zostanie zastosowany w klastrze.
> * Poufne dane dotyczące klienta, takie jak klucz prywatny, znane hosty zawartości, nazwa użytkownika protokołu HTTPS i token lub hasło, są przechowywane przez maksymalnie 48 godzin w ramach usług Azure Arc Kubernetes Services. Jeśli używasz poufnych danych wejściowych do konfiguracji, przełącz klastry w tryb online tak jak to możliwe.

## <a name="apply-configurations-at-scale"></a>Zastosuj konfiguracje na dużą skalę

Ponieważ Azure Resource Manager zarządza konfiguracjami, można zautomatyzować tworzenie tej samej konfiguracji we wszystkich zasobach Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Policy, w zakresie subskrypcji lub grupy zasobów. 

To wymuszenie skalowania w poziomie zapewnia wspólną konfigurację linii bazowej (zawierającą konfiguracje, takie jak ClusterRoleBindings, RoleBindings i NetworkPolicy), które mogą być stosowane do całej floty lub spisu klastrów Kubernetes z włączoną funkcją Azure Arc.

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra z usługą Azure Arc](./connect-cluster.md)
* [Tworzenie konfiguracji w klastrze Kubernetes z włączonym łukiem](./use-gitops-connected-cluster.md)
* [Użyj Azure Policy, aby zastosować konfiguracje na dużą skalę](./use-azure-policy.md)