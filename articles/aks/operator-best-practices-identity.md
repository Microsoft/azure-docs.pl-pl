---
title: Najlepsze rozwiązania dotyczące zarządzania tożsamościami
titleSuffix: Azure Kubernetes Service
description: Poznaj najlepsze rozwiązania operatora klastra dotyczące zarządzania uwierzytelnianiem i autoryzacją klastrów w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a63a756448f9c7202c79c3b4625fc99d4a90dc52
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682693"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze Azure Kubernetes Service (AKS)

Podczas wdrażania i konserwowania klastrów w usłudze Azure Kubernetes Service (AKS) należy zaimplementować metody zarządzania dostępem do zasobów i usług. Bez tych kontrolek konta mogą mieć dostęp do zasobów i usług, których nie potrzebują. Może być również trudno śledzić, który zestaw poświadczeń był używany do wprowadzania zmian.

Te najlepsze rozwiązania koncentrują się na sposobie zarządzania dostępem i tożsamością klastrów AKS przez operator klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Uwierzytelnianie użytkowników klastrów AKS za pomocą Azure Active Directory
> * Kontrola dostępu do zasobów za pomocą Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC)
> * Użyj funkcji RBAC platformy Azure, aby szczegółowo kontrolować dostęp do zasobu AKS i interfejsu API Kubernetes w skali, a także do kubeconfig.
> * Używanie tożsamości zarządzanej do uwierzytelniania samego samego siebie z innymi usługami

## <a name="use-azure-active-directory"></a>Korzystanie z usługi Azure Active Directory

**Wskazówki dotyczące najlepszych** rozwiązań — wdrażanie klastrów AKS z integracją z usługą Azure AD. Za pomocą usługi Azure AD scentralizowano składnik zarządzania tożsamościami. Wszelkie zmiany stanu konta użytkownika lub grupy są automatycznie aktualizowane w dostępie do klastra AKS. Użyj ról lub ClusterRoles i powiązań, jak opisano w następnej sekcji, aby ograniczyć liczbę użytkowników lub grup do najmniejszych wymaganych uprawnień.

Deweloperzy i właściciele aplikacji klastra Kubernetes potrzebują dostępu do różnych zasobów. Kubernetes nie oferuje rozwiązania do zarządzania tożsamościami, które pozwala kontrolować, którzy użytkownicy mogą korzystać z zasobów. Zamiast tego zwykle integrujesz klaster z istniejącym rozwiązaniem tożsamości. Azure Active Directory (AD) zapewnia rozwiązanie do zarządzania tożsamościami gotowe do używania w przedsiębiorstwie i można je zintegrować z klastrami AKS.

W przypadku klastrów zintegrowanych z usługą Azure AD w programie AKS tworzysz *role* lub *ClusterRoles* , które definiują uprawnienia dostępu do zasobów. Następnie można *powiązać* role z użytkownikami lub grupami z usługi Azure AD. Te Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) zostały omówione w następnej sekcji. Integrację usługi Azure AD i sposób kontrolowania dostępu do zasobów można zobaczyć na poniższym diagramie:

![Uwierzytelnianie na poziomie klastra dla integracji Azure Active Directory z usługą AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Deweloperzy są uwierzytelniani za pomocą usługi Azure AD.
1. Punkt końcowy wystawiania tokenów usługi Azure AD wystawia token dostępu.
1. Deweloper wykonuje akcję przy użyciu tokenu usługi Azure AD, np. `kubectl create pod`
1. Kubernetes sprawdza token w Azure Active Directory i pobiera członkostwa w grupach deweloperów.
1. Są stosowane Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) i zasad klastra.
1. Żądanie dewelopera zakończyło się powodzeniem lub nie jest oparte na poprzedniej weryfikacji członkostwa w grupach usługi Azure AD oraz Kubernetes RBAC i zasadach.

Aby utworzyć klaster AKS, który używa usługi Azure AD, zobacz [integrowanie Azure Active Directory z usługą AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Korzystanie z kontroli dostępu opartej na rolach Kubernetes (Kubernetes RBAC)

**Wskazówki dotyczące najlepszych** rozwiązań — Użyj Kubernetes RBAC, aby zdefiniować uprawnienia, które użytkownicy lub grupy mają do zasobów w klastrze. Utwórz role i powiązania, które przypisują najmniej wymagane uprawnienia. Integruj z usługą Azure AD, aby wszelkie zmiany stanu użytkownika lub członkostwa w grupie były automatycznie aktualizowane, a dostęp do zasobów klastra jest aktualny.

W programie Kubernetes można zapewnić szczegółową kontrolę dostępu do zasobów w klastrze. Uprawnienia są definiowane na poziomie klastra lub do określonych przestrzeni nazw. Można zdefiniować zasoby, które mogą być zarządzane, oraz z uprawnieniami. Te role są następnie stosowane do użytkowników lub grup z powiązaniem. Aby uzyskać więcej informacji na temat *ról*, *ClusterRoles* i *powiązań*, zobacz [Opcje dostępu i tożsamości dla usługi Azure Kubernetes Service (AKS)][aks-concepts-identity].

Przykładowo można utworzyć rolę, która przyznaje pełen dostęp do zasobów w przestrzeni nazw o nazwie *finanse-aplikacja*, jak pokazano w poniższym przykładzie YAML manifestu:

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

Następnie utworzono Rolębinding, która wiąże użytkownika usługi Azure AD *developer1 \@ contoso.com* z roląbinding, jak pokazano w następującym manifeście YAML:

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

Gdy *developer1 \@ contoso.com* jest uwierzytelniany w klastrze AKS, mają pełne uprawnienia do zasobów w przestrzeni nazw *"Finanse aplikacji"* . Dzięki temu można logicznie oddzielić i kontrolować dostęp do zasobów. Kubernetes RBAC należy używać w połączeniu z usługą Azure AD — integrację, zgodnie z opisem w poprzedniej sekcji.

Aby dowiedzieć się, jak za pomocą grup usługi Azure AD kontrolować dostęp do zasobów Kubernetes za pomocą Kubernetes RBAC, zobacz [Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory w AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Korzystanie z usługi Azure RBAC 
**Wskazówki dotyczące najlepszych** rozwiązań — Użyj usługi Azure RBAC, aby zdefiniować minimalne wymagane uprawnienia, które użytkownicy lub grupy muszą AKS zasoby w jednej lub kilku subskrypcjach.

Istnieją dwa poziomy dostępu, które są konieczne, aby w pełni obsługiwać klaster AKS: 
1. Uzyskaj dostęp do zasobu AKS w ramach subskrypcji platformy Azure. Ten poziom dostępu pozwala kontrolować skalowanie lub Uaktualnianie klastra przy użyciu interfejsów API AKS oraz ściąganie kubeconfig.
Aby dowiedzieć się, jak kontrolować dostęp do zasobu AKS i kubeconfig, zobacz [ograniczanie dostępu do pliku konfiguracji klastra](control-kubeconfig-access.md).

2. Dostęp do interfejsu API Kubernetes. Ten poziom dostępu jest kontrolowany przez [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradycyjnie) lub przez integrację usługi Azure RBAC z usługą AKS dla autoryzacji Kubernetes.
Aby dowiedzieć się, jak szczegółowo udzielić uprawnień do interfejsu API Kubernetes przy użyciu funkcji RBAC platformy Azure, zobacz [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](manage-azure-rbac.md).

## <a name="use-pod-identities"></a>Użyj tożsamości pod

**Wskazówki dotyczące najlepszych** rozwiązań — nie używaj stałych poświadczeń w obrębie zasobników ani obrazów kontenerów, ponieważ są one zagrożone ekspozycją lub nadużyciami. Zamiast tego należy użyć tożsamości pod, aby automatycznie żądać dostępu przy użyciu centralnego rozwiązania do obsługi tożsamości usługi Azure AD. Tożsamości pod są przeznaczone wyłącznie do użytku z magazynem systemu Linux i obrazami kontenerów.

Gdy wymagane jest uzyskanie dostępu do innych usług platformy Azure, takich jak Cosmos DB, Key Vault lub Blob Storage, wymaga to poświadczeń dostępu. Te poświadczenia dostępu można zdefiniować za pomocą obrazu kontenera lub wstrzyknąć jako wpis tajny Kubernetes, ale należy go ręcznie utworzyć i przypisać. Często poświadczenia są ponownie używane między zasobnikami i nie są regularnie obracane.

Zarządzane tożsamości dla zasobów platformy Azure (obecnie zaimplementowane jako skojarzony projekt AKS Open Source) pozwalają na automatyczne zażądanie dostępu do usług za pomocą usługi Azure AD. Nie Definiuj ręcznie poświadczeń dla zasobników, zamiast tego żądają tokenu dostępu w czasie rzeczywistym i mogą używać go do uzyskiwania dostępu do przypisanych do nich usług. W programie AKS dwa składniki są wdrażane przez operatora klastra w celu zezwolenia na używanie tożsamości zarządzanych przez y:

* **Serwer tożsamości zarządzania węzłami (NMI)** jest systemem, który działa jako elementu daemonset w każdym WĘŹLE klastra AKS. Serwer NMI nasłuchuje żądań pod względem usług platformy Azure.
* **Zarządzany kontroler tożsamości (MIC)** jest centralnym pod względem uprawnień do wysyłania zapytań do serwera interfejsu API Kubernetes i sprawdza mapowanie tożsamości platformy Azure, które odnosi się do pod.

Gdy usługa podst żąda dostępu do usługi platformy Azure, reguły sieci przekierowują ruch do serwera tożsamości zarządzania węzłami (NMI). Serwer NMI identyfikuje zasobniki, które żądają dostępu do usług platformy Azure na podstawie ich adresów zdalnych, a następnie wysyła zapytanie do zarządzanego kontrolera tożsamości (MIC). MIKROFON sprawdza mapowania tożsamości platformy Azure w klastrze AKS, a następnie serwer NMI żąda tokenu dostępu od Azure Active Directory (AD) w oparciu o mapowanie tożsamości pod. Usługa Azure AD zapewnia dostęp do serwera NMI, który jest zwracany do usługi pod. Ten token dostępu może być używany przez usługę pod, a następnie żądać dostępu do usług na platformie Azure.

W poniższym przykładzie deweloper tworzy pod, który używa tożsamości zarządzanej, aby zażądać dostępu do Azure SQL Database:

![Tożsamości pod pozwalają na automatyczne zażądanie dostępu do innych usług](media/operator-best-practices-identity/pod-identities.png)

1. Operator klastra najpierw tworzy konto usługi, które może służyć do mapowania tożsamości, gdy identyfikatory zasobów żądają dostępu do usług.
1. Serwer NMI i mikrofon są wdrażane w celu przekazywania żądań dostępu do usługi Azure AD na żądanie.
1. Deweloper wdraża usługę pod za pomocą tożsamości zarządzanej, która żąda tokenu dostępu za pomocą serwera NMI.
1. Token jest zwracany do i używany do uzyskiwania dostępu do Azure SQL Database

> [!NOTE]
> Tożsamości zarządzane pod to projekt open source, który nie jest obsługiwany przez pomoc techniczną platformy Azure.

Aby korzystać z tożsamości pod, zobacz [Azure Active Directory tożsamości dla aplikacji Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Następne kroki

W tym artykule dotyczącym najlepszych rozwiązań opisano uwierzytelnianie i autoryzację klastra i zasobów. Aby zaimplementować niektóre z tych najlepszych rozwiązań, zobacz następujące artykuły:

* [Integracja Azure Active Directory z usługą AKS][aks-aad]
* [Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure z usługą AKS][aad-pod-identity]

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
