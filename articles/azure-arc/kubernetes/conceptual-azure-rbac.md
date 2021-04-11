---
title: Azure RBAC — włączono usługę Azure Arc Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Ten artykuł zawiera ogólne omówienie możliwości usługi Azure RBAC na platformie Azure z włączoną obsługą Kubernetes
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451101"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Funkcja RBAC platformy Azure w usłudze Azure Arc Kubernetes

Typy obiektów Kubernetes [ClusterRoleBinding i rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) ułatwiają Definiowanie autoryzacji w Kubernetes natywnie. Korzystając z usługi Azure RBAC, można użyć przypisań ról Azure Active Directory (Azure AD) i roli na platformie Azure do kontrolowania kontroli autoryzacji w klastrze.

Dzięki tej funkcji wszystkie korzyści wynikające z przypisań ról platformy Azure, takie jak dzienniki aktywności, pokazujące wszystkie zmiany RBAC na platformie Azure w ramach zasobu platformy Azure, teraz mają zastosowanie do klastra Kubernetes z włączonym użyciem usługi Azure Arc.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architektura — funkcja RBAC platformy Azure w usłudze Azure Arc Kubernetes

[![Architektura ](./media/conceptual-azure-rbac.png) RBAC platformy Azure](./media/conceptual-azure-rbac.png#lightbox)

Aby można było kierować wszystkie operacje sprawdzania dostępu do autoryzacji do usługi autoryzacji na platformie Azure, w klastrze jest wdrażany serwer webhook ([Guard](https://github.com/appscode/guard)).

`apiserver`Klaster jest skonfigurowany do używania [uwierzytelniania tokenów elementu webhook](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) i [autoryzacji elementu webhook](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) , tak `TokenAccessReview` Aby `SubjectAccessReview` żądania były kierowane do serwera elementu webhook ochrony. `TokenAccessReview`Żądania i `SubjectAccessReview` są wyzwalane przez żądania dotyczące zasobów Kubernetes wysyłanych do `apiserver` .

Następnie funkcja Guard wywołuje `checkAccess` usługę autoryzacji na platformie Azure, aby sprawdzić, czy żądanie jednostki usługi Azure AD jest dostępne dla zasobu zainteresowania. 

Jeśli istnieje rola w przypisaniu, która umożliwia dostęp do tego dostępu, wówczas `allowed` odpowiedź jest wysyłana z ochrony usługi autoryzacji. Z kolei funkcja Guard wysyła `allowed` odpowiedź do `apiserver` , umożliwiając jednostce wywołującej dostęp do żądanego zasobu Kubernetes.


Jeśli rola w przypisaniu zezwalająca na dostęp nie istnieje, `denied` odpowiedź jest wysyłana z usługi autoryzacji do ochrony. Funkcja Guard wysyła `denied` odpowiedź do `apiserver` , co zapewnia 403 jednostce wywołującej błąd zabroniony dla żądanego zasobu.

## <a name="next-steps"></a>Następne kroki

* Skorzystaj z naszego przewodnika Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* [Skonfiguruj funkcję RBAC platformy Azure](./azure-rbac.md) w klastrze klastrów Kubernetes z obsługą usługi Azure Arc.