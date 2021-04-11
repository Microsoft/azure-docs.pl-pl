---
title: Połączenie z klastrem — usługa Azure ARC z włączonym Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera ogólne omówienie możliwości łączenia z klastrem w usłudze Azure Arc Kubernetes
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451098"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Połączenie klastra w usłudze Azure ARC z włączonym Kubernetes

Funkcja *Połącz z klastrem* usługi Azure Arc Kubernetes umożliwia łączność z `apiserver` klastrem bez konieczności włączania żadnego portu wejściowego w zaporze. Odwrotny Agent proxy uruchomiony w klastrze może bezpiecznie uruchomić sesję z usługą Azure Arc w sposób wychodzący. 

Połączenie z klastrem umożliwia deweloperom dostęp do swoich klastrów z dowolnego miejsca w celu interaktywnego programowania i debugowania. Umożliwia ona również użytkownikom klastrów i administratorom dostęp do swoich klastrów i zarządzanie nimi z dowolnego miejsca. Możesz nawet używać hostowanych agentów/programów uruchamiających Azure Pipelines, akcji GitHub lub innych hostowanych usług ciągłej integracji/ciągłego wdrażania aplikacji w klastrach Premium, bez konieczności korzystania z agentów samoobsługowych.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architektura

[![Architektura ](./media/conceptual-cluster-connect.png) łączenia klastra](./media/conceptual-cluster-connect.png#lightbox)

Po stronie klastra Agent zwrotnego serwera proxy o nazwie `clusterconnect-agent` wdrożony jako część wykresu Helm agenta wykonuje wywołania wychodzące do usługi Azure Arc w celu ustanowienia sesji.

Gdy użytkownik wywołuje `az connectedk8s proxy` :
1. Plik binarny serwera proxy usługi Azure Arc jest pobierany i wznosił się do procesu na komputerze klienckim. 
1. Usługa Azure Arc proxy pobiera `kubeconfig` plik skojarzony z klastrem Kubernetes z obsługą usługi Azure ARC, na którym `az connectedk8s proxy` jest wywoływana.
    * Serwer proxy usługi Azure Arc używa tokenu dostępu platformy Azure dla obiektu wywołującego i nazwy identyfikatora Azure Resource Manager. 
1. `kubeconfig`Plik zapisany na maszynie przez serwer proxy usługi Azure Arc wskazuje adres URL serwera do punktu końcowego w procesie serwera proxy usługi Azure Arc.

Gdy użytkownik wysyła żądanie przy użyciu tego `kubeconfig` pliku:
1. Serwer proxy usługi Azure Arc mapuje punkt końcowy, który otrzymuje żądanie, do usługi Azure Arc. 
1. Usługa Azure Arc następnie przekazuje żądanie do `clusterconnect-agent` uruchomionego w klastrze. 
1. `clusterconnect-agent`Przekazuje żądanie do `kube-aad-proxy` składnika, który wykonuje uwierzytelnianie usługi Azure AD w jednostce wywołującej. 
1. Po uwierzytelnieniu usługi Azure AD program `kube-aad-proxy` używa funkcji [personifikacji użytkownika](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) Kubernetes do przesyłania dalej żądania do klastra `apiserver` .

## <a name="next-steps"></a>Następne kroki

* Skorzystaj z naszego przewodnika Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* Bezpiecznie [Uzyskuj dostęp do klastra](./cluster-connect.md) z dowolnego miejsca przy użyciu funkcji Połącz z klastrem.