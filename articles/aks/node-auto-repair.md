---
title: Automatyczne naprawianie węzłów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat funkcji autonaprawy węzła i sposobu, w jaki AKS rozwiązuje uszkodzone węzły procesu roboczego.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 91384461567634faabaaa1dd588d6e7ec6ece60e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735629"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Funkcja autonaprawy węzła usługi Azure Kubernetes Service (AKS)

AKS stale sprawdza stan kondycji węzłów procesu roboczego i wykonuje automatyczną naprawę węzłów, jeśli staną się w złej kondycji. Ten dokument informuje operatorów o sposobie zachowania funkcji automatycznego naprawiania węzłów. Oprócz naprawionych AKS platforma maszyn wirtualnych platformy Azure [wykonuje konserwację na Virtual Machines][vm-updates] . AKS i maszyny wirtualne platformy Azure współpracują ze sobą, aby zminimalizować przerwy w działaniu usługi dla klastrów.

> [!Important]
> Funkcja autonaprawy węzła nie jest obecnie obsługiwana w przypadku pul węzłów systemu Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS sprawdza dostępność węzłów w złej kondycji

> [!Note]
> AKS wykonuje akcję naprawy w węzłach przy użyciu konta użytkownika **AKS-remediator**.

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

Automatyczna naprawa jest domyślnie obsługiwana w przypadku klastrów z typem zestawu maszyn wirtualnych **Virtual Machine Scale Sets**. Jeśli węzeł zostanie określony jako w złej kondycji na podstawie powyższych reguł, AKS ponownie uruchamia węzeł po 10 kolejnych minutach w złej kondycji. Jeśli węzły pozostaną w złej kondycji po początkowej operacji naprawy, dodatkowe środki zaradcze są badane przez inżynierów AKS.
  
Jeśli wiele węzłów jest w złej kondycji podczas sprawdzania kondycji, każdy węzeł jest naprawiany indywidualnie przed rozpoczęciem kolejnej naprawy.

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
