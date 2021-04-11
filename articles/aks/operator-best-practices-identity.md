---
title: Najlepsze rozwiązania dotyczące zarządzania tożsamościami
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące zarządzania uwierzytelnianiem i autoryzacją klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105106"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze Azure Kubernetes Service (AKS)

Podczas wdrażania i konserwowania klastrów w usłudze Azure Kubernetes Service (AKS) można zaimplementować metody zarządzania dostępem do zasobów i usług. Bez następujących kontrolek:
* Konta mogą mieć dostęp do niepotrzebnych zasobów i usług. 
* Śledzenie, którego zestawu poświadczeń użyto do wprowadzania zmian, może być trudne.

Te najlepsze rozwiązania koncentrują się na sposobie zarządzania dostępem i tożsamością klastrów AKS przez operator klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Uwierzytelnianie użytkowników klastrów AKS za pomocą Azure Active Directory.
> * Kontroluj dostęp do zasobów za pomocą Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC).
> * Za pomocą funkcji RBAC platformy Azure można szczegółowo kontrolować dostęp do zasobu AKS, Kubernetes API w odpowiedniej skali i `kubeconfig` .
> * Za pomocą tożsamości zarządzanej można uwierzytelniać same same wystąpienia z innymi usługami.

## <a name="use-azure-active-directory-azure-ad"></a>Korzystanie z Azure Active Directory (Azure AD)

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Wdróż klastry AKS z integracją usługi Azure AD. Za pomocą usługi Azure AD scentralizowano składnik zarządzania tożsamościami. Wszelkie zmiany stanu konta użytkownika lub grupy są automatycznie aktualizowane w dostępie do klastra AKS. Określanie zakresu użytkowników lub grup w celu uzyskania minimalnej wartości uprawnień przy użyciu [ról, ClusterRoles lub powiązań](#use-kubernetes-role-based-access-control-kubernetes-rbac).

Deweloperzy klastrów Kubernetes i właściciele aplikacji muszą mieć dostęp do różnych zasobów. Kubernetes nie ma rozwiązania do zarządzania tożsamościami do kontrolowania zasobów, z którymi mogą korzystać użytkownicy. Zamiast tego zwykle integrujesz klaster z istniejącym rozwiązaniem tożsamości. Wprowadź usługę Azure AD: rozwiązanie do zarządzania tożsamościami gotowe dla przedsiębiorstwa, które integruje się z klastrami AKS.

W przypadku klastrów zintegrowanych z usługą Azure AD w programie AKS tworzysz *role* lub *ClusterRoles* definiują uprawnienia dostępu do zasobów. Następnie można *powiązać* role z użytkownikami lub grupami z usługi Azure AD. Więcej informacji na temat tych Kubernetes RBAC [znajduje się w następnej sekcji](#use-kubernetes-role-based-access-control-kubernetes-rbac). Integracja z usługą Azure AD i sposób kontrolowania dostępu do zasobów można zobaczyć na poniższym diagramie:

![Uwierzytelnianie na poziomie klastra dla integracji Azure Active Directory z usługą AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Deweloperzy są uwierzytelniani za pomocą usługi Azure AD.
1. Punkt końcowy wystawiania tokenów usługi Azure AD wystawia token dostępu.
1. Deweloper wykonuje akcję przy użyciu tokenu usługi Azure AD, np `kubectl create pod` ..
1. Kubernetes sprawdza token w usłudze Azure AD i pobiera członkostwa w grupach deweloperów.
1. Są stosowane Kubernetes RBAC i zasady klastra.
1. Żądanie dewelopera zakończyło się pomyślnie na podstawie poprzedniej weryfikacji członkostwa w grupach usługi Azure AD oraz Kubernetes RBAC i zasady.

Aby utworzyć klaster AKS, który używa usługi Azure AD, zobacz [integrowanie Azure Active Directory z usługą AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC)

> **Wskazówki dotyczące najlepszych rozwiązań**
> 
> Zdefiniuj uprawnienia użytkowników lub grup do zasobów klastra za pomocą Kubernetes RBAC. Utwórz role i powiązania, które przypisują najmniej wymagane uprawnienia. Integruj się z usługą Azure AD, aby automatycznie aktualizować wszelkie zmiany stanu użytkownika lub członkostwa w grupie oraz zapewnić aktualność dostępu do zasobów klastra.

W programie Kubernetes zapewniasz szczegółową kontrolę dostępu do zasobów klastra. Użytkownik definiuje uprawnienia na poziomie klastra lub do określonych przestrzeni nazw. Można określić, które zasoby mogą być zarządzane, oraz z uprawnieniami. Następnie należy zastosować te role do użytkowników lub grup z powiązaniem. Aby uzyskać więcej informacji na temat *ról*, *ClusterRoles* i *powiązań*, zobacz [Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)][aks-concepts-identity].

Przykładowo utworzysz rolę z pełnymi prawami dostępu do zasobów w przestrzeni nazw o nazwie *finanse-aplikacja*, jak pokazano w poniższym przykładzie YAML manifestu:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Następnie utworzysz Rolębinding i powiążesz użytkownika usługi Azure AD *developer1 \@ contoso.com* z rolą rolebinding, jak pokazano w następującym manifeście YAML:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Gdy *developer1 \@ contoso.com* jest uwierzytelniany w klastrze AKS, mają pełne uprawnienia do zasobów w przestrzeni nazw *"Finanse aplikacji"* . Dzięki temu można logicznie oddzielić i kontrolować dostęp do zasobów. Używaj Kubernetes RBAC w połączeniu z usługą Azure AD — integrację.

Aby dowiedzieć się, jak za pomocą grup usługi Azure AD kontrolować dostęp do zasobów Kubernetes za pomocą Kubernetes RBAC, zobacz [Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory w AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Korzystanie z usługi Azure RBAC 
> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Funkcja RBAC platformy Azure umożliwia definiowanie minimalnych wymaganych uprawnień użytkowników i grup w celu AKS zasobów w co najmniej jednej subskrypcji.

Istnieją dwa poziomy dostępu, które są konieczne, aby w pełni obsługiwać klaster AKS: 
1. Uzyskaj dostęp do zasobu AKS w ramach subskrypcji platformy Azure. 

    Ten poziom dostępu umożliwia:
      * Kontrolowanie skalowania lub Uaktualnianie klastra przy użyciu interfejsów API AKS
      * Ściągnij `kubeconfig` .

    Aby dowiedzieć się, jak kontrolować dostęp do zasobu AKS i `kubeconfig` , zobacz temat [ograniczanie dostępu do pliku konfiguracji klastra](control-kubeconfig-access.md).

2. Dostęp do interfejsu API Kubernetes. 
    
    Ten poziom dostępu jest kontrolowany przez:
    * [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradycyjnie) lub 
    * Przez integrację usługi Azure RBAC z usługą AKS for Kubernetes Authorization.

    Aby dowiedzieć się, jak szczegółowo udzielić uprawnień do interfejsu API Kubernetes przy użyciu funkcji RBAC platformy Azure, zobacz [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Korzystanie z tożsamości zarządzanych przez program

> **Wskazówki dotyczące najlepszych rozwiązań** 
> 
> Nie używaj stałych poświadczeń w obrębie zasobników ani obrazów kontenerów, ponieważ są one zagrożone ekspozycją lub nadużyciami. Zamiast tego należy użyć *tożsamości pod* , aby automatycznie żądać dostępu przy użyciu centralnego rozwiązania do obsługi tożsamości usługi Azure AD. 

> [!NOTE]
> Tożsamości pod są przeznaczone wyłącznie do użytku z magazynem systemu Linux i obrazami kontenerów. Obsługa tożsamości zarządzanych przez usługi w przypadku kontenerów systemu Windows jest dostępna wkrótce.

Aby uzyskać dostęp do innych usług platformy Azure, takich jak Cosmos DB, Key Vault lub Blob Storage, musi on mieć poświadczenia dostępu. Można zdefiniować poświadczenia dostępu przy użyciu obrazu kontenera lub wstrzyknąć je jako klucz tajny Kubernetes. W obu przypadkach należy ręcznie utworzyć i przypisać te elementy. Zwykle te poświadczenia są ponownie używane w różnych zasobnikach i nie są regularnie obracane.

Z tożsamościami zarządzanymi pod kątem zasobów platformy Azure automatycznie żądasz dostępu do usług za pomocą usługi Azure AD. Tożsamości zarządzane w ramach usługi są obecnie dostępne w wersji zapoznawczej dla usługi AKS. Zapoznaj się z artykułem [Azure Active Directory korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza) [(zobacz https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) dokumentację, aby rozpocząć pracę. 

Zamiast ręcznego definiowania poświadczeń dla podstaw, tożsamości zarządzane przez usługę żądają tokenu dostępu w czasie rzeczywistym, przy użyciu którego uzyskują dostęp tylko do przypisanych do nich usług. W programie AKS istnieją dwa składniki, które obsługują operacje zezwalające na używanie tożsamości w modułach zarządzania:

* **Serwer tożsamości zarządzania węzłami (NMI)** jest systemem, który działa jako elementu daemonset w każdym WĘŹLE klastra AKS. Serwer NMI nasłuchuje żądań pod względem usług platformy Azure.
* **Dostawca zasobów platformy Azure** wysyła zapytanie do serwera interfejsu API Kubernetes i sprawdza mapowanie tożsamości platformy Azure, które odnosi się do pod.

Gdy usługa podst żąda dostępu do usługi platformy Azure, reguły sieci przekierowują ruch do serwera NMI. 
1. Serwer NMI:
    * Identyfikuje żądania dostępu do usług platformy Azure w oparciu o ich adresy zdalne.
    * Wysyła zapytanie do dostawcy zasobów platformy Azure. 
1. Dostawca zasobów platformy Azure sprawdza mapowania tożsamości platformy Azure w klastrze AKS.
1. Serwer NMI żąda tokenu dostępu z usługi Azure AD w oparciu o mapowanie tożsamości na podstawie. 
1. Usługa Azure AD zapewnia dostęp do serwera NMI, który jest zwracany do usługi pod. 
    * Ten token dostępu może być używany przez usługę pod, a następnie żądać dostępu do usług na platformie Azure.

W poniższym przykładzie deweloper tworzy pod, który używa tożsamości zarządzanej, aby zażądać dostępu do Azure SQL Database:

![Tożsamości pod pozwalają na automatyczne zażądanie dostępu do innych usług](media/operator-best-practices-identity/pod-identities.png)

1. Operator klastra tworzy konto usługi do mapowania tożsamości, gdy identyfikatory zasobów żądają dostępu do usług.
1. Serwer NMI został wdrożony w celu przekazywania dowolnych żądań pod względem dostawcy zasobów platformy Azure, aby uzyskać tokeny dostępu do usługi Azure AD.
1. Deweloper wdraża usługę pod za pomocą tożsamości zarządzanej, która żąda tokenu dostępu za pomocą serwera NMI.
1. Token jest zwracany do i używany do uzyskiwania dostępu do Azure SQL Database

> [!NOTE]
> Tożsamości zarządzane w ramach programu są obecnie w stanie wersji zapoznawczej.

Aby korzystać z tożsamości zarządzanych przez program, zobacz [Azure Active Directory korzystanie z tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Następne kroki

W tym artykule dotyczącym najlepszych rozwiązań opisano uwierzytelnianie i autoryzację klastra i zasobów. Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Integracja Azure Active Directory z usługą AKS][aks-aad]
* [Korzystanie z Azure Active Directory tożsamości zarządzanych w usłudze Azure Kubernetes Service (wersja zapoznawcza)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące najlepsze rozwiązania:

* [Wielodostęp i izolacja klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje usługi Kubernetes Scheduler][aks-best-practices-scheduler]
* [Zaawansowane funkcje usługi Scheduler Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
