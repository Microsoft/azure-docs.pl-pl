---
title: Konfiguracje i GitOps — usługa Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera ogólne omówienie możliwości GitOps i konfiguracji na platformie Azure Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery, konfiguracja, GitOps
ms.openlocfilehash: a4193783a976f205b46cb8c5df29d4cef9da1d2c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022878"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfiguracje i GitOps za pomocą usługi Azure Arc Kubernetes

W odniesieniu do Kubernetes, GitOps jest celem deklarowania żądanego stanu konfiguracji klastra Kubernetes (wdrożenia, przestrzenie nazw itp.) w repozytorium git. Po tej deklaracji następuje rozmieszczenie i wdrażanie tych konfiguracji klastra przy użyciu operatora. Repozytorium git może zawierać:
* YAML manifesty opisujące wszystkie ważne zasoby Kubernetes, w tym obszary nazw, ConfigMaps, wdrożenia, DaemonSets itp.
* Wykresy Helm na potrzeby wdrażania aplikacji.

[Strumień](https://docs.fluxcd.io/), popularne narzędzie typu "open source" w przestrzeni GitOps, można wdrożyć w klastrze Kubernetes w celu ułatwienia przepływu konfiguracji z repozytorium git do klastra Kubernetes. Strumień obsługuje wdrożenie jego operatora zarówno w zakresie klastra, jak i przestrzeni nazw. Operator strumienia wdrożony z zakresem przestrzeni nazw może wdrażać tylko obiekty Kubernetes należące do tej konkretnej przestrzeni nazw. Możliwość wyboru między zakresem klastra lub przestrzeni nazw ułatwia osiągnięcie wzorców wdrożenia z wieloma dzierżawcami w tym samym klastrze Kubernetes.

## <a name="configurations"></a>Konfiguracje

[![Architektura ](./media/conceptual-configurations.png) konfiguracji](./media/conceptual-configurations.png#lightbox)

Połączenie między klastrem a repozytorium git jest tworzone jako zasób konfiguracji ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) na górze zasobu Kubernetes z włączoną funkcją Azure ARC (reprezentowane przez `Microsoft.Kubernetes/connectedClusters` ) w Azure Resource Manager. 

Właściwości zasobu konfiguracji służą do wdrażania operatora strumienia w klastrze z odpowiednimi parametrami, takimi jak repozytorium git, z którego mają być ściągane manifesty, oraz interwału sondowania, w którym mają być ściągane. Dane zasobu konfiguracji są przechowywane w postaci zaszyfrowanej w bazie danych Azure Cosmos DB w celu zapewnienia poufności danych.

`config-agent`Działający w klastrze jest odpowiedzialny za:
* Śledzenie nowych lub zaktualizowanych zasobów konfiguracji w ramach zasobu Kubernetes z włączoną funkcją Azure Arc.
* Wdrożenie operatora strumieniowego w celu obejrzenia repozytorium git dla każdego zasobu konfiguracji.
* Stosowanie wszelkich aktualizacji dokonanych w dowolnym zasobie konfiguracyjnym. 

Można utworzyć wiele zasobów konfiguracji z zakresem przestrzeni nazw w tym samym klastrze Kubernetes z obsługą usługi Azure Arc w celu osiągnięcia wielu dzierżawców.

> [!NOTE]
> * `config-agent` monitoruje nowe lub zaktualizowane zasoby konfiguracji, aby były dostępne dla zasobu Kubernetes z włączonym łukiem. W ten sposób Agenci muszą mieć łączność dla żądanego stanu do ściągnięcia do klastra. Jeśli agenci nie mogą nawiązać połączenia z platformą Azure, istnieje opóźnienie propagacji żądanego stanu do klastra.
> * Poufne dane dotyczące klienta, takie jak klucz prywatny, znane hosty zawartości, nazwa użytkownika protokołu HTTPS i token/hasło, nie są przechowywane dłużej niż 48 godzin w ramach usług Azure Arc Kubernetes Services. Jeśli używasz poufnych danych wejściowych do konfiguracji, przełącz klastry w tryb online tak jak to możliwe.

## <a name="apply-configurations-at-scale"></a>Zastosuj konfiguracje na dużą skalę

Ponieważ Azure Resource Manager zarządza konfiguracjami, można zautomatyzować tworzenie tej samej konfiguracji we wszystkich zasobach Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Policy, w zakresie subskrypcji lub grupy zasobów. 

To wymuszenie skalowania w poziomie zapewnia wspólną konfigurację linii bazowej (zawierającą konfiguracje, takie jak ClusterRoleBindings, RoleBindings i NetworkPolicy), które mogą być stosowane do całej floty lub spisu klastrów Kubernetes z włączoną funkcją Azure Arc.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z naszym przewodnikiem Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* Masz już klaster Kubernetes połączony z usługą Azure Arc? [Utwórz konfiguracje w klastrze Kubernetes z włączonym łukiem](./tutorial-use-gitops-connected-cluster.md).
* Dowiedz się [, jak używać Azure Policy do stosowania konfiguracji w odpowiedniej skali](./use-azure-policy.md).