---
title: Rozszerzenia klastra — usługa Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera omówienie pojęć dotyczących możliwości rozszerzeń klastra z włączoną funkcją Azure Arc Kubernetes
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451173"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Rozszerzenia klastrów w usłudze Azure ARC z włączonym Kubernetes

[Wykresy Helm](https://helm.sh/) ułatwiają zarządzanie aplikacjami Kubernetes przez dostarczanie bloków konstrukcyjnych wymaganych do definiowania, instalowania i uaktualniania nawet najbardziej skomplikowanych aplikacji Kubernetes. Funkcja rozszerzenia klastra poszukuje składników pakietu Helm. Jest to możliwe dzięki udostępnieniu Azure Resource Managerego środowiska do zarządzania instalacją i cyklem życia rozszerzeń klastra, takich jak Azure Monitor i Azure Defender dla Kubernetes. Funkcja rozszerzeń klastra zapewnia następujące dodatkowe korzyści powyżej i powyżej, co jest już dostępne natywnie z wykresami Helm:

- Pobierz spis wszystkich klastrów i rozszerzeń zainstalowanych w tych klastrach.
- Użyj Azure Policy, aby zautomatyzować wdrożenie rozszerzeń klastra na skalę.
- Subskrybuj wydanie pociągów każdego rozszerzenia.
- Skonfiguruj funkcję autouaktualniania dla rozszerzeń.
- Obsługa zdarzeń związanych z tworzeniem wystąpień rozszerzeń i zarządzaniem cyklem życia aktualizacji i usuwania.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

[![Architektura ](./media/conceptual-extensions.png) rozszerzeń klastra](./media/conceptual-extensions.png#lightbox)

Wystąpienie rozszerzenia klastra jest tworzone jako rozszerzenie Azure Resource Manager Resource ( `Microsoft.KubernetesConfiguration/extensions` ) na górze zasobu Kubernetes z włączoną funkcją Azure ARC (reprezentowane przez `Microsoft.Kubernetes/connectedClusters` ) w Azure Resource Manager. Reprezentacja w Azure Resource Manager umożliwia tworzenie zasad, które sprawdzają, czy dla wszystkich zasobów Kubernetes z obsługą usługi Azure ARC z określonym rozszerzeniem klastra lub bez niego. Po ustaleniu, które klastry nie mają rozszerzeń klastra z żądanymi wartościami właściwości, można skorygować te niezgodne zasoby przy użyciu Azure Policy.

`config-agent`Działający w klastrze śledzi nowe lub zaktualizowane zasoby rozszerzeń w zasobie usługi Azure ARC z włączonym Kubernetes. `extensions-manager`Działający w klastrze pobiera wykres Helm z Azure Container Registry lub Microsoft Container Registry i instaluje go w klastrze. 

Zarówno `config-agent` składniki programu, jak i `extensions-manager` uruchomione w klastrze obsługują aktualizacje wersji i usuwanie wystąpienia rozszerzenia.

> [!NOTE]
> * `config-agent` monitoruje nowe lub zaktualizowane zasoby rozszerzenia, które mają być dostępne dla zasobu Kubernetes z włączonym łukiem. W ten sposób Agenci muszą mieć łączność dla żądanego stanu do ściągnięcia do klastra. Jeśli agenci nie mogą połączyć się z platformą Azure, Propagacja żądanego stanu do klastra jest opóźniona.
> * Ustawienia konfiguracji chronionej dla rozszerzenia są przechowywane przez maksymalnie 48 godzin w usługach Kubernetes z włączoną funkcją Azure Arc. W związku z tym Jeśli klaster zostanie rozłączony w ciągu 48 godzin od momentu utworzenia zasobu rozszerzenia na platformie Azure, rozszerzenie przechodzi ze `Pending` stanu do `Failed` stanu. Firma Microsoft zaleca, aby klastry były przełączane w tryb online tak jak to możliwe.

## <a name="next-steps"></a>Następne kroki

* Skorzystaj z naszego przewodnika Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* [Wdróż rozszerzenia klastra](./extensions.md) w klastrze Kubernetes z obsługą usługi Azure Arc.