---
title: Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121460"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Użyj Azure Policy, aby zastosować konfiguracje klastra na dużą skalę

Za pomocą Azure Policy można zastosować konfiguracje ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Typ zasobu) na dużą skalę w przypadku klastrów Kubernetes () z włączoną funkcją Azure ARC ( `Microsoft.Kubernetes/connectedclusters` ).

Aby użyć Azure Policy, wybierz istniejącą definicję zasad i Utwórz przypisanie zasad. Podczas tworzenia przypisania zasad:
1. Ustaw zakres przypisania.
    * Zakres będzie grupą zasobów platformy Azure lub subskrypcją. 
2. Ustaw parametry konfiguracji, która zostanie utworzona. 

Po utworzeniu przypisania aparat Azure Policy identyfikuje wszystkie klastry Kubernetes z obsługą usługi Azure Arc znajdujące się w danym zakresie i zastosuje konfigurację do poszczególnych klastrów.

Można utworzyć wiele konfiguracji, z których każdy wskazuje na inne repozytorium git przy użyciu wielu przypisań zasad. Na przykład jedno repozytorium dla głównego operatora IT/klastra i innych repozytoriów dla zespołów aplikacji.

## <a name="prerequisite"></a>Wymaganie wstępne

Sprawdź, czy masz `Microsoft.Authorization/policyAssignments/write` uprawnienia do zakresu (subskrypcji lub grupy zasobów), w którym zostanie utworzone przypisanie zasad.

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

Po utworzeniu przypisania zasad konfiguracja zostanie zastosowana do nowych klastrów Kubernetes z obsługą usługi Azure Arc utworzonych w ramach przypisania zasad.

W przypadku istniejących klastrów należy ręcznie uruchomić zadanie korygowania. To zadanie zazwyczaj trwa od 10 do 20 minut, aby przypisanie zasad zaczęło obowiązywać.

## <a name="verify-a-policy-assignment"></a>Weryfikowanie przypisania zasad

1. W Azure Portal przejdź do jednego z klastrów Kubernetes z obsługą usługi Azure Arc.
1. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **zasady**. 
    * Na liście zasad powinno zostać wyświetlone przypisanie zasad utworzone wcześniej przy użyciu **stanu zgodności** ustawionego jako *zgodny*.
1. W sekcji **Ustawienia** na pasku bocznym wybierz pozycję **konfiguracje**.
    * Na liście konfiguracje powinna zostać wyświetlona konfiguracja utworzona przez przypisanie zasady.
1. Użyj `kubectl` , aby przejrzeć klaster. 
    * Powinna zostać wyświetlona przestrzeń nazw i artefakty, które zostały utworzone przez zasoby konfiguracji.
    * W ciągu 5 minut (przy założeniu, że klaster ma łączność sieciową z platformą Azure), należy wyświetlić obiekty opisane przez manifesty w repozytorium git i utworzyć je w klastrze.

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj Azure monitor dla kontenerów za pomocą klastrów Kubernetes z obsługą usługi Azure Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
