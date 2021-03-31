---
title: Automatyczne naprawianie węzłów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat funkcji autonaprawy węzła i sposobu, w jaki AKS rozwiązuje uszkodzone węzły procesu roboczego.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89490109"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Funkcja autonaprawy węzła usługi Azure Kubernetes Service (AKS)

AKS stale sprawdza stan kondycji węzłów procesu roboczego i wykonuje automatyczną naprawę węzłów, jeśli staną się w złej kondycji. Ten dokument informuje operatorów o sposobie zachowania funkcji automatycznej naprawy węzła dla węzłów systemu Windows i Linux. Oprócz naprawionych AKS platforma maszyn wirtualnych platformy Azure [wykonuje konserwację na Virtual Machines][vm-updates] . AKS i maszyny wirtualne platformy Azure współpracują ze sobą, aby zminimalizować przerwy w działaniu usługi dla klastrów.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS sprawdza dostępność węzłów w złej kondycji

AKS używa reguł, aby określić, czy węzeł jest w złej kondycji i wymaga naprawy. AKS używa następujących reguł, aby określić, czy automatyczna naprawa jest wymagana.

* Węzeł raportuje stan dla **niestopniowego** sprawdzania kolejnych sprawdzeń w ciągu 10 minut.
* Węzeł nie zgłasza stanu w ciągu 10 minut

Stan kondycji węzłów można sprawdzić ręcznie przy użyciu polecenia kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak działa automatyczna naprawa

> [!Note]
> AKS inicjuje operacje naprawy przy użyciu konta użytkownika **AKS-remediator**.

Jeśli węzeł jest w złej kondycji na podstawie powyższych reguł i pozostaje w złej kondycji przez 10 kolejnych minut, zostaną wykonane następujące działania.

1. Ponowne uruchamianie węzła
1. Jeśli ponowne uruchomienie nie powiedzie się, odtworzenie obrazu węzła
1. Jeśli odtwarzanie obrazu nie powiedzie się, Utwórz nowy węzeł i Odtwórz go z obrazu

Jeśli żadna z tych akcji nie powiedzie się, dodatkowe środki zaradcze są badane przez inżynierów AKS. Jeśli wiele węzłów jest w złej kondycji podczas sprawdzania kondycji, każdy węzeł jest naprawiany indywidualnie przed rozpoczęciem kolejnej naprawy.

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
