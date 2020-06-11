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
ms.openlocfilehash: 80ffd70b830483c548f29a029d448bb6b480a8d4
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84676203"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Użyj Azure Policy, aby wymusić, że dla każdego zasobu `Microsoft.Kubernetes/connectedclusters` lub dla zasobu z włączoną obsługą operacji git-Ops `Microsoft.ContainerService/managedClusters` `Microsoft.KubernetesConfiguration/sourceControlConfigurations` zastosowano konkretną funkcję. Aby użyć Azure Policy należy wybrać istniejącą definicję zasad i utworzyć przypisanie zasad. Podczas tworzenia przypisania zasad należy ustawić zakres przypisania: będzie to grupa zasobów platformy Azure lub subskrypcja. Należy również ustawić parametry `sourceControlConfiguration` , które zostaną utworzone. Po utworzeniu przypisania aparat zasad zidentyfikuje wszystkie `connectedCluster` lub `managedCluster` zasoby, które znajdują się w zakresie i zastosuje `sourceControlConfiguration` do każdego z nich.

Jeśli używasz wielu repozytoriów Git jako źródeł prawdy dla każdego klastra (na przykład jedno repozytorium dla operatora centralnego IT/klastra i innych repozytoriów dla zespołów aplikacji), możesz je włączyć przy użyciu wielu przypisań zasad, każde przypisanie zasad skonfigurowane do korzystania z innego repozytorium git.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

1. W Azure Portal przejdź do zasad i w sekcji **Tworzenie** na pasku bocznym wybierz pozycję **definicje**.
2. Wybierz wbudowane zasady "wdróż GitOps do Kubernetes Cluster" w kategorii "Kubernetes", a następnie kliknij pozycję **Wybierz**.
3. Ustaw **zakres** dla grupy zarządzania, subskrypcji lub grupy zasobów, w której będą stosowane przypisania zasad.
4. Jeśli chcesz wykluczyć wszystkie zasoby z zakresu zasad, ustaw **wykluczenia**.
5. Nadaj zasadom **nazwę** i **Opis** , których można użyć do łatwego identyfikowania.
6. Upewnij się, że **wymuszanie zasad** jest ustawione na *włączone*.
7. Wybierz przycisk **Dalej**.
8. Ustaw wartości parametrów, które będą używane podczas tworzenia `sourceControlConfiguration` .
9. Wybierz przycisk **Dalej**.
10. Włącz **zadanie tworzenia korygowania**.
11. Należy upewnić się, że jest zaznaczona opcja **Utwórz tożsamość zarządzaną** oraz że tożsamość będzie mieć uprawnienia **współautora** . Zobacz [ten dokument](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) i [komentarz w tym dokumencie](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) , aby uzyskać więcej informacji na temat potrzebnych uprawnień.
12. Wybierz pozycję **Przegląd + utwórz**.

Po utworzeniu przypisania zasad dla każdego nowego `connectedCluster` zasobu (lub `managedCluster` zasobu z zainstalowanymi agentami GitOps), który znajduje się w zakresie przypisania, `sourceControlConfiguration` zostanie zastosowane. W przypadku istniejących klastrów należy ręcznie uruchomić zadanie korygowania. Przydzielenie zasad zwykle trwa od 10-20 minut.

## <a name="verify-a-policy-assignment"></a>Weryfikowanie przypisania zasad

1. W Azure Portal przejdź do jednego z `connectedCluster` zasobów, a następnie w sekcji **Ustawienia** na pasku bocznym wybierz pozycję **zasady**. (Środowisko użytkownika dla klastra zarządzanego przez AKS nie jest jeszcze zaimplementowane, ale jest dostępne).
2. Na liście powinien zostać wyświetlony utworzony powyżej przydział zasad, a **stan zgodności** powinien być *zgodny*.
3. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **konfiguracje**.
4. Na liście powinna zostać wyświetlona wartość `sourceControlConfiguration` utworzone przypisanie zasad.
5. Użyj **polecenia kubectl** do przejrzeć klastra: powinna zostać wyświetlona przestrzeń nazw i artefakty, które zostały utworzone przez `sourceControlConfiguration` .
6. W ciągu 5 minut powinna zostać wyświetlona w klastrze artefakty, które są opisane w manifestach w skonfigurowanym repozytorium git.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj Azure Monitor dla kontenerów z włączonymi Kubernetes klastrami](./deploy-azure-monitor-for-containers.md)
