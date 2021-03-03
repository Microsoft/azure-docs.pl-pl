---
title: Uaktualnianie agentów Kubernetes z włączonym usługą Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Aktualizacje agentów kontroli dla usługi Azure ARC z włączoną obsługą Kubernetes
keywords: Kubernetes, łuk, Azure, K8s, kontenery, Agent, uaktualnienie
ms.openlocfilehash: 3a5355269db4ccfc91612f661b464556b22c1d35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662821"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Uaktualnianie agentów Kubernetes z włączonym usługą Azure Arc

Usługa Azure ARC z włączonym Kubernetes udostępnia funkcje autouaktualniania i ręcznego aktualizowania dla swoich agentów. Jeśli użyjesz Disable autoupgrade i zamiast tego polegasz na uaktualnieniu ręcznym, zasady obsługi wersji mają zastosowanie do agentów Arc i bazowego klastra Kubernetes.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Włącz lub wyłącz funkcję autouaktualniania podczas łączenia klastra z usługą Azure Arc

Usługa Azure ARC z włączonym Kubernetes zapewnia swoim agentom możliwość korzystania z wbudowanych funkcji autouaktualniania.

Następujące polecenie łączy klaster z usługą Azure ARC z **włączonym** autouaktualnianiem:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Po włączeniu autouaktualniania agent sonduje system Azure co godzinę, aby uzyskać dostęp do nowszej wersji agentów. Jeśli Agent znajdzie dostępną nowszą wersję, wyzwala uaktualnienie wykresu Helm dla agentów usługi Azure Arc.

Aby zrezygnować z autouaktualniania, określ `--disable-auto-upgrade` parametr podczas łączenia klastra z usługą Azure Arc. Następujące polecenie łączy klaster z usługą Azure ARC z **wyłączonym** autouaktualnianiem:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Jeśli planujesz wyłączenie autouaktualniania, zapoznaj się z [zasadami obsługi wersji](#version-support-policy) usługi Azure Arc Kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Włącz/Wyłącz funkcję autouaktualniania po połączeniu klastra z usługą Azure Arc

Po nawiązaniu połączenia między klastrem a usługą Azure Arc można przełączać możliwość autouaktualniania przy użyciu `az connectedk8s update` polecenia, jak pokazano poniżej:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Ręcznie uaktualnij agentów

Jeśli wyłączono funkcję autouaktualnienia dla agentów, można ręcznie zainicjować uaktualnienia dla tych agentów przy użyciu `az connectedk8s upgrade` polecenia, jak pokazano poniżej:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Usługa Azure Arc Kubernetes jest zgodna ze standardowym [schematem wersji semantycznej](https://semver.org/) programu `MAJOR.MINOR.PATCH` w celu zapewnienia wersji jego agentów. 

Każda liczba w wersji wskazuje ogólną zgodność z poprzednią wersją:

* **Wersje główne** zmieniają się, gdy istnieją niezgodne aktualizacje interfejsu API lub zgodność z poprzednimi wersjami.
* **Wersje pomocnicze** zmieniają się, gdy zmiany funkcjonalności są wstecznie zgodne z innymi wersjami pomocniczymi.
* **Wersje poprawek** zmieniają się w przypadku wprowadzenia poprawek błędów zgodnych z poprzednimi wersjami.

## <a name="version-support-policy"></a>Zasady obsługi wersji

Podczas tworzenia problemów z pomocą techniczną usługa Azure ARC z włączonymi rozwiązaniami Kubernetes ma następujące zasady pomocy technicznej:

* Agenci Kubernetes z obsługą usługi Azure Arc mają okna obsługi "N-2", gdzie "N" to Najnowsza wersja pomocnicza agentów. 
  * Na przykład jeśli usługa Azure Arc Kubernetes wprowadza 0.28. a dzisiaj, wersje 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e i 0.26. f są obsługiwane przez usługę Azure Arc.

* Klastry Kubernetes łączące się z usługą Azure Arc mają okno pomocy technicznej "N-2", gdzie "N" to najnowsza stabilna wersja [Kubernetes nadrzędnego](https://github.com/kubernetes/kubernetes/releases). 
  * Na przykład, jeśli Kubernetes wprowadza 1.20. a dzisiaj, wersje 1.20. a, 1.20. b, 1.19. c, 1.19. d, 1.18. e i 1.18. f są obsługiwane.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Jak często dostępne są pomocnicze wersje usługi Azure ARC z włączoną obsługą Kubernetes?

Jedna wersja pomocnicza agentów Kubernetes z włączoną funkcją Azure Arc jest wydawana około raz w miesiącu.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Co się stanie, jeśli używam wersji agenta lub wersji Kubernetes spoza oficjalnego okna wsparcia?

"Poza działem pomocy technicznej" oznacza, że używane wersje są poza "N-2" obsługiwanymi wersjami agentów i nadrzędnych klastrów Kubernetes. Aby kontynuować problem z pomocą techniczną, użytkownik zostanie poproszony o uaktualnienie klastra i agentów do obsługiwanej wersji.

## <a name="next-steps"></a>Następne kroki

* [Łączenie klastra z usługą Azure Arc](./connect-cluster.md)
* [Tworzenie konfiguracji w klastrze Kubernetes z włączonym łukiem](./use-gitops-connected-cluster.md)
* [Użyj Azure Policy, aby zastosować konfiguracje na dużą skalę](./use-azure-policy.md)