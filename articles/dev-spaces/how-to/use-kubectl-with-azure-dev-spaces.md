---
title: Jak używać polecenia kubectl z Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Dowiedz się, jak używać poleceń polecenia kubectl w obszarze dev w klastrze usługi Azure Kubernetes z włączonym Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960168"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Korzystanie z polecenia kubectl z przestrzenią dev platformy Azure

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Możesz uzyskać dostęp do klastra Kubernetes w przestrzeni usługi Azure dev i korzystać z istniejących narzędzi Kubernetes, takich jak `kubectl` .

Uruchomienie `az aks use-dev-spaces` polecenia spowoduje automatyczne dodanie `kubectl` kontekstu konfiguracji, więc polecenia kubectl powinna być już połączona z klastrem usługi Azure dev Spaces Kubernetes. Przykłady:
- Potwierdź bieżący kontekst: `kubectl config current-context`
- Wyświetl wszystkie dostępne konteksty: `kubectl config get-contexts` . 
- Kontekst zmiany: `kubectl config use-context <context-name>`
- Wyświetl pulpit nawigacyjny Kubernetes: Uruchom `kubectl proxy` polecenie, a następnie otwórz przeglądarkę na adres, który to polecenie emituje (Dołącz `/ui` do adresu URL, aby przejść do pulpitu nawigacyjnego Kubernetes).
- Wyświetl listę uruchomionych usług w domyślnym obszarze Azure Dev Spaces nazwa *Domyślna*: `kubectl get services --namespace=default`

