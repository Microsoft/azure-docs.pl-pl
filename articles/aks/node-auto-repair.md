---
title: Automatyczne naprawianie węzłów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat funkcji autonaprawy węzła i sposobu, w jaki AKS rozwiązuje uszkodzone węzły procesu roboczego.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105208"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Funkcja autonaprawy węzła usługi Azure Kubernetes Service (AKS)

AKS nieustannie monitoruje stan kondycji węzłów procesu roboczego i wykonuje automatyczne naprawy węzłów, jeśli staną się w złej kondycji. Platforma Azure Virtual Machine (VM) [wykonuje konserwację na maszynach wirtualnych z][vm-updates] problemami. 

AKS i maszyny wirtualne platformy Azure współpracują ze sobą, aby zminimalizować przerwy w działaniu usługi dla klastrów.

W tym dokumencie dowiesz się, jak funkcja automatycznej naprawy węzła zachowuje się zarówno dla węzłów systemu Windows, jak i Linux. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS sprawdza dostępność węzłów w złej kondycji

AKS używa następujących reguł, aby określić, czy węzeł jest w złej kondycji i wymaga naprawy: 
* Węzeł zgłasza stan **Niebieżniki** dla kolejnych sprawdzeń w ciągu 10 minut.
* Węzeł nie zgłasza żadnych stanów w ciągu 10 minut.

Stan kondycji węzłów można sprawdzić ręcznie przy użyciu polecenia kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak działa automatyczna naprawa

> [!Note]
> AKS inicjuje operacje naprawy przy użyciu konta użytkownika **AKS-remediator**.

Jeśli AKS identyfikuje węzeł w złej kondycji, który pozostaje w złej kondycji przez 10 minut, AKS wykonuje następujące czynności:

1. Uruchom ponownie węzeł.
1. Jeśli ponowne uruchomienie nie powiedzie się, odtworzenie obrazu węzła.
1. Jeśli odtwarzanie obrazu nie powiedzie się, Utwórz nowy węzeł i Odtwórz go z obrazu.

Alternatywne rozwiązania zaradcze są badane przez inżynierów AKS, jeśli naprawa jest niepomyślna. 

Jeśli AKS odnajdzie wiele węzłów w złej kondycji podczas sprawdzania kondycji, każdy węzeł jest naprawiany indywidualnie przed rozpoczęciem innej naprawy.

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
