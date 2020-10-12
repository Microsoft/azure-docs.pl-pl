---
title: Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050036"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Użyj Azure Policy, aby wymusić, że `Microsoft.Kubernetes/connectedclusters` dla każdego zasobu lub Git-Ops włączonych `Microsoft.ContainerService/managedClusters` zasobów `Microsoft.KubernetesConfiguration/sourceControlConfigurations` zastosowano określone zasoby. Aby użyć Azure Policy należy wybrać istniejącą definicję zasad i utworzyć przypisanie zasad. Podczas tworzenia przypisania zasad należy ustawić zakres przypisania: będzie to grupa zasobów platformy Azure lub subskrypcja. Należy również ustawić parametry `sourceControlConfiguration` , które zostaną utworzone. Po utworzeniu przypisania aparat zasad zidentyfikuje wszystkie `connectedCluster` lub `managedCluster` zasoby, które znajdują się w zakresie i zastosuje `sourceControlConfiguration` do każdego z nich.

Jeśli używasz wielu repozytoriów Git jako źródeł prawdy dla każdego klastra (na przykład jedno repozytorium dla operatora centralnego IT/klastra i innych repozytoriów dla zespołów aplikacji), możesz je włączyć przy użyciu wielu przypisań zasad, każde przypisanie zasad skonfigurowane do korzystania z innego repozytorium git.

## <a name="prerequisite"></a>Wymaganie wstępne

Upewnij się, że masz `Microsoft.Authorization/policyAssignments/write` uprawnienia do zakresu (subskrypcji lub grupy zasobów), w którym ma zostać utworzone przypisanie zasad.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

1. W Azure Portal przejdź do zasad i w sekcji **Tworzenie** na pasku bocznym wybierz pozycję **definicje**.
2. Wybierz wbudowane zasady "wdróż GitOps do Kubernetes Cluster" w kategorii "Kubernetes", a następnie kliknij przycisk **Assign (Przypisz**).
3. Ustaw **zakres** dla grupy zarządzania, subskrypcji lub grupy zasobów, w której będą stosowane przypisania zasad.
4. Jeśli chcesz wykluczyć wszystkie zasoby z zakresu zasad, ustaw **wykluczenia**.
5. Nadaj zasadom **nazwę** i **Opis** , których można użyć do łatwego identyfikowania.
6. Upewnij się, że **wymuszanie zasad** jest ustawione na *włączone*.
7. Wybierz opcję **Dalej**.
8. Ustaw wartości parametrów, które będą używane podczas tworzenia `sourceControlConfiguration` .
9. Wybierz opcję **Dalej**.
10. Włącz **zadanie tworzenia korygowania**.
11. Należy upewnić się, że jest zaznaczona opcja **Utwórz tożsamość zarządzaną** oraz że tożsamość będzie mieć uprawnienia **współautora** . Zobacz [ten dokument](../../governance/policy/assign-policy-portal.md) i [komentarz w tym dokumencie](../../governance/policy/how-to/remediate-resources.md) , aby uzyskać więcej informacji na temat potrzebnych uprawnień.
12. Wybierz pozycję **Przeglądanie + tworzenie**.

Po utworzeniu przypisania zasad dla każdego nowego `connectedCluster` zasobu (lub `managedCluster` zasobu z zainstalowanymi agentami GitOps), który znajduje się w zakresie przypisania, `sourceControlConfiguration` zostanie zastosowane. W przypadku istniejących klastrów należy ręcznie uruchomić zadanie korygowania. Przydzielenie zasad zwykle trwa od 10-20 minut.

## <a name="verify-a-policy-assignment"></a>Weryfikowanie przypisania zasad

1. W Azure Portal przejdź do jednego z `connectedCluster` zasobów, a następnie w sekcji **Ustawienia** na pasku bocznym wybierz pozycję **zasady**. (Środowisko użytkownika dla klastra AKS nie jest jeszcze zaimplementowane, ale jest dostępne).
2. Na liście powinien zostać wyświetlony utworzony powyżej przydział zasad, a **stan zgodności** powinien być *zgodny*.
3. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **konfiguracje**.
4. Na liście powinna zostać wyświetlona wartość `sourceControlConfiguration` utworzone przypisanie zasad.
5. Użyj **polecenia kubectl** do przejrzeć klastra: powinna zostać wyświetlona przestrzeń nazw i artefakty, które zostały utworzone przez `sourceControlConfiguration` .
6. W ciągu 5 minut powinna zostać wyświetlona w klastrze artefakty, które są opisane w manifestach w skonfigurowanym repozytorium git.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj Azure Monitor dla kontenerów z włączonymi Kubernetes klastrami](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
