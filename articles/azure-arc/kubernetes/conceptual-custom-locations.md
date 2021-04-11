---
title: Lokalizacje niestandardowe — usługa Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera omówienie pojęć dotyczących możliwości lokalizacji niestandardowych w usłudze Azure Arc Kubernetes
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451130"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Lokalizacje niestandardowe na górze usługi Azure Arc Kubernetes

Jako rozszerzenie konstrukcji lokalizacji platformy Azure *niestandardowe lokalizacje* umożliwiają administratorom dzierżawy używanie klastrów Kubernetes z obsługą usługi Azure Arc jako lokalizacji docelowych na potrzeby wdrażania wystąpień usług platformy Azure. Przykłady zasobów platformy Azure obejmują wystąpienie zarządzane SQL z włączonym usługą Azure Arc i PostgreSQL.

Podobnie jak w przypadku lokalizacji platformy Azure, użytkownicy końcowi w dzierżawie z dostępem do lokalizacji niestandardowych mogą wdrażać zasoby przy użyciu prywatnych obliczeń firmy.

[![Warstwy ](./media/conceptual-arc-platform-layers.png) platformy Arc](./media/conceptual-arc-platform-layers.png#lightbox)

Niestandardowe lokalizacje można wizualizować jako warstwę abstrakcji na platformie Azure z włączoną obsługą klastrów Kubernetes, łączenia klastrów i klastrów. Lokalizacje niestandardowe tworzą szczegółowe [RoleBindings i ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) niezbędne dla innych usług platformy Azure, aby uzyskać dostęp do klastra. Te inne usługi platformy Azure wymagają dostępu do klastra w celu zarządzania zasobami, które klient chce wdrożyć w swoich klastrach.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

Gdy administrator włączy funkcję lokalizacji niestandardowych w klastrze, ClusterRoleBinding jest tworzona w klastrze, autoryzując aplikację usługi Azure AD używaną przez dostawcę zasobów lokalizacji niestandardowych (RP). Po autoryzowaniu Lokacje niestandardowe RP mogą tworzyć ClusterRoleBindings lub RoleBindings, które są konieczne przez inne usługi Azure RPS pliku do tworzenia zasobów niestandardowych w tym klastrze. Rozszerzenia klastra zainstalowane w klastrze określają listę RPS pliku do autoryzacji.

[![Korzystanie z lokalizacji ](./media/conceptual-custom-locations-usage.png) niestandardowych](./media/conceptual-custom-locations-usage.png#lightbox)

Gdy użytkownik tworzy wystąpienie usługi danych w klastrze: 
1. Żądanie PUT jest wysyłane do Azure Resource Manager.
1. Żądanie PUT jest przekazywane do usługi Azure ARC z włączonym Data Services RP. 
1. Składnik RP pobiera `kubeconfig` plik skojarzony z klastrem Kubernetes z włączonym łukiem platformy Azure, na którym istnieje lokalizacja niestandardowa. 
   * Lokalizacja niestandardowa jest przywoływana `extendedLocation` w oryginalnym ŻĄDANIU Put. 
1. Usługa Azure Arc włączona Data Services RP korzysta z programu, `kubeconfig` Aby komunikować się z klastrem w celu utworzenia niestandardowego zasobu usługi Azure ARC z włączonym Data Services typem w przestrzeni nazw mapowanej na lokalizację niestandardową. 
   * Podczas tworzenia rozszerzenia klastra został wdrożony operator Data Services przy użyciu usługi Azure Arc. 
1. Operator Data Services przy użyciu usługi Azure Arc odczytuje nowy niestandardowy zasób utworzony w klastrze i tworzy kontroler danych, dokonując translacji do realizacji żądanego stanu w klastrze. 

Sekwencja kroków służących do tworzenia wystąpienia zarządzanego SQL i wystąpienia PostgreSQL są takie same jak w przypadku sekwencji kroków opisanych powyżej.

## <a name="next-steps"></a>Następne kroki

* Skorzystaj z naszego przewodnika Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* [Utwórz niestandardową lokalizację](./custom-locations.md) w klastrze Kubernetes z włączonym łukiem platformy Azure.