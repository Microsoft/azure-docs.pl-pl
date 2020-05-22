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
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779947"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Użyj Azure Policy, aby wymusić `Microsoft.Kubernetes/connectedclusters` , że każdy lub zasób z włączoną obsługą git-Ops `Microsoft.ContainerService/managedClusters` został określony `Microsoft.KubernetesConfiguration/sourceControlConfigurations` .  Aby użyć Azure Policy należy wybrać istniejącą definicję zasad i utworzyć przypisanie zasad.  Podczas tworzenia przypisania zasad należy ustawić zakres przypisania: będzie to grupa zasobów platformy Azure lub subskrypcja.  Należy również ustawić parametry `sourceControlConfiguration` , które zostaną utworzone.  Po utworzeniu przypisania aparat zasad zidentyfikuje wszystkie `connectedCluster` lub `managedCluster` zasoby, które znajdują się w zakresie i zastosuje `sourceControlConfiguration` do każdego z nich.

Jeśli używasz wielu repozytoriów Git jako źródeł prawdy dla każdego klastra (na przykład jedno repozytorium dla operatora centralnego IT/klastra i innych repozytoriów dla zespołów aplikacji), możesz je włączyć przy użyciu wielu przypisań zasad, każde przypisanie zasad skonfigurowane do korzystania z innego repozytorium git.

## <a name="create-a-custom-policy-definition"></a>Tworzenie niestandardowej definicji zasad

1. W Azure Portal przejdź do zasad i w sekcji **Tworzenie** na pasku bocznym wybierz pozycję **definicje**.
2. Wybierz pozycję **+ Definicja zasad**.
3. Ustaw **lokalizację definicji** na subskrypcję lub grupę zarządzania.  Spowoduje to określenie najszerszego zakresu, w którym można użyć definicji zasad.
4. Podaj **nazwę** i **Opis**zasad.
5. W obszarze Kategoria wybierz kolejno pozycje **Utwórz nowy**i Zapisz *klaster Kubernetes — Azure Arc*
6. W polu edycji **reguły zasad** Skopiuj/wklej zawartość tej [przykładowej definicji zasad](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Zapisz**.

Ten krok tworzenia niestandardowej definicji zasad nie będzie konieczny po zakończeniu pracy, aby wykonać to wbudowane zasady.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

1. W Azure Portal przejdź do zasad i w sekcji **Tworzenie** na pasku bocznym wybierz pozycję **definicje**.
2. Znajdź utworzoną przez siebie definicję i wybierz ją.
3. Na stronie Akcje wybierz pozycję **Przypisz**.
4. Ustaw **zakres** dla grupy zarządzania, subskrypcji lub grupy zasobów, w której będą stosowane przypisania zasad.
5. Jeśli chcesz wykluczyć wszystkie zasoby z zakresu zasad, ustaw **wykluczenia**.
6. Nadaj zasadom **nazwę** i **Opis** , których można użyć do łatwego identyfikowania.
7. Upewnij się, że **wymuszanie zasad** jest ustawione na *włączone*.
8. Wybierz pozycję **Dalej**.
9. Ustaw wartości parametrów, które będą używane podczas tworzenia `sourceControlConfiguration` .
10. Wybierz pozycję **Dalej**.
11. Włącz **zadanie tworzenia korygowania**.
12. Należy upewnić się, że jest zaznaczona opcja **Utwórz tożsamość zarządzaną** oraz że tożsamość będzie mieć uprawnienia **współautora** .  Zobacz [ten dokument](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) i [komentarz w tym dokumencie](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) , aby uzyskać więcej informacji na temat potrzebnych uprawnień.
13. Wybierz pozycję **Przegląd + utwórz**.

Po utworzeniu przypisania zasad dla każdego nowego `connectedCluster` zasobu (lub `managedCluster` zasobu z zainstalowanymi agentami GitOps), który znajduje się w zakresie przypisania, `sourceControlConfiguration` zostanie zastosowane.  W przypadku istniejących klastrów należy ręcznie uruchomić zadanie korygowania.  Przydzielenie zasad zwykle trwa od 10-20 minut.

## <a name="verify-a-policy-assignment"></a>Weryfikowanie przypisania zasad

1. W Azure Portal przejdź do jednego z `connectedCluster` zasobów, a następnie w sekcji **Ustawienia** na pasku bocznym wybierz pozycję **zasady**. (Środowisko użytkownika dla klastra zarządzanego przez AKS nie jest jeszcze zaimplementowane, ale jest dostępne).
2. Na liście powinien zostać wyświetlony utworzony powyżej przydział zasad, a **stan zgodności** powinien być *zgodny*.
3. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **konfiguracje**.
4. Na liście powinna zostać wyświetlona wartość `sourceControlConfiguration` utworzone przypisanie zasad.
5. Użyj **polecenia kubectl** do przejrzeć klastra: powinna zostać wyświetlona przestrzeń nazw i artefakty, które zostały utworzone przez `sourceControlConfiguration` .
6. W ciągu 5 minut powinna zostać wyświetlona w klastrze artefakty, które są opisane w manifestach w skonfigurowanym repozytorium git.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj Azure Monitor dla kontenerów z włączonymi Kubernetes klastrami](./deploy-azure-monitor-for-containers.md)
