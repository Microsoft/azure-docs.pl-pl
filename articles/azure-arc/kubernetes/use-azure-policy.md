---
title: Korzystanie z usługi Azure Policy na potrzeby stosowania konfiguracji klastra na dużą skalę (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: b80e50cb4823632f054de3b7f9da71392f8578d7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560180"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Korzystanie z usługi Azure Policy na potrzeby stosowania konfiguracji klastra na dużą skalę (wersja zapoznawcza)

## <a name="overview"></a>Omówienie

Możesz użyć Azure Policy, aby wymusić jeden z następujących zasobów, aby mieć określone `Microsoft.KubernetesConfiguration/sourceControlConfigurations` zastosowanie:
*  `Microsoft.Kubernetes/connectedclusters` zasoby.
* Zasób z obsługą GitOps `Microsoft.ContainerService/managedClusters` . 

Aby użyć Azure Policy, wybierz istniejącą definicję zasad i Utwórz przypisanie zasad. Podczas tworzenia przypisania zasad:
1. Ustaw zakres przypisania.
    * Zakres będzie grupą zasobów platformy Azure lub subskrypcją. 
2. Ustaw parametry `sourceControlConfiguration` , które zostaną utworzone. 

Po utworzeniu przypisania aparat Azure Policy identyfikuje wszystkie `connectedCluster` lub `managedCluster` zasoby znajdujące się w zakresie, a następnie stosuje `sourceControlConfiguration` do każdego z nich.

Można włączyć wiele repozytoriów Git jako źródła prawdy dla każdego klastra przy użyciu wielu przypisań zasad. Każde przypisanie zasad zostanie skonfigurowane w taki sposób, aby korzystało z innego repozytorium git. na przykład jedno repozytorium dla operatora centralnego IT/klastra i innych repozytoriów dla zespołów aplikacji.

## <a name="prerequisite"></a>Wymaganie wstępne

Sprawdź, czy masz `Microsoft.Authorization/policyAssignments/write` uprawnienia do zakresu (subskrypcji lub grupy zasobów), w którym ma zostać utworzone przypisanie zasad.

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

1. W Azure Portal przejdź do **zasad**.
1. W sekcji **Tworzenie** na pasku bocznym wybierz pozycję **definicje**.
1. W kategorii "Kubernetes" Wybierz wbudowane zasady "wdróż GitOps do klastra Kubernetes". 
1. Kliknij przycisk **Przypisz**.
1. Ustaw **zakres** dla grupy zarządzania, subskrypcji lub grupy zasobów, do której ma zastosowanie przypisanie zasad.
    * Jeśli chcesz wykluczyć wszystkie zasoby z zakresu zasad, ustaw **wykluczenia**.
1. Nadaj zasadom łatwą do zidentyfikowania **nazwę** i **Opis**.
1. Upewnij się, że **wymuszanie zasad** jest ustawione na **włączone**.
1. Wybierz opcję **Dalej**.
1. Ustaw wartości parametrów, które mają być używane podczas tworzenia `sourceControlConfiguration` .
1. Wybierz opcję **Dalej**.
1. Włącz **zadanie tworzenia korygowania**.
1. Sprawdź, czy jest zaznaczona opcja **Utwórz tożsamość zarządzaną** i czy tożsamość będzie miała uprawnienia **współautora** . 
    * Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie przydziału zasad — szybki start](../../governance/policy/assign-policy-portal.md) i [korygowanie niezgodnych zasobów z Azure Policy](../../governance/policy/how-to/remediate-resources.md).
1. Wybierz pozycję **Przejrzyj i utwórz**.

Po utworzeniu przypisania zasad `sourceControlConfiguration` zostanie ono zastosowane dla dowolnego z następujących zasobów znajdujących się w zakresie przypisania:
* Nowe `connectedCluster` zasoby.
* Nowe `managedCluster` zasoby z zainstalowanymi agentami GitOps. 

W przypadku istniejących klastrów należy ręcznie uruchomić zadanie korygowania. To zadanie zazwyczaj trwa od 10 do 20 minut, aby przypisanie zasad zaczęło obowiązywać.

## <a name="verify-a-policy-assignment"></a>Weryfikowanie przypisania zasad

1. W Azure Portal przejdź do jednego z `connectedCluster` zasobów.
1. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **zasady**. 
    * ŚRODOWISKO AKS klastra nie jest jeszcze zaimplementowane.
    * Na liście zasad powinno zostać wyświetlone przypisanie zasad utworzone wcześniej przy użyciu **stanu zgodności** ustawionego jako *zgodny*.
1. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **konfiguracje**.
    * Na liście konfiguracje powinna zostać wyświetlona wartość `sourceControlConfiguration` utworzone przypisanie zasad.
1. Użyj `kubectl` , aby przejrzeć klaster. 
    * Powinna zostać wyświetlona przestrzeń nazw i artefakty, które zostały utworzone przez `sourceControlConfiguration` .
    * W ciągu 5 minut powinna zostać wyświetlona w klastrze artefakty, które są opisane w manifestach w skonfigurowanym repozytorium git.

## <a name="next-steps"></a>Następne kroki

* [Skonfiguruj Azure Monitor dla kontenerów z włączonymi Kubernetes klastrami](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
