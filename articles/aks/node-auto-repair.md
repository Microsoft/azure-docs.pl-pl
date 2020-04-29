---
title: Automatyczne naprawianie węzłów usługi Azure Kubernetes Service (AKS)
description: Dowiedz się więcej na temat funkcji autonaprawy węzła i sposobu, w jaki AKS rozwiązuje uszkodzone węzły procesu roboczego.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284844"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Funkcja autonaprawy węzła usługi Azure Kubernetes Service (AKS)

AKS stale sprawdza stan kondycji węzłów procesu roboczego i wykonuje automatyczną naprawę węzłów, jeśli staną się w złej kondycji. W tej dokumentacji opisano sposób, w jaki usługa Azure Kubernetes Service (AKS) monitoruje węzły procesu roboczego i naprawia węzły procesu roboczego w złej kondycji.  Dokumentacja ma na celu informowanie operatorów AKS o zachowaniu funkcjonalności naprawy węzła. Należy również pamiętać, że platforma Azure [wykonuje konserwację na Virtual Machines][vm-updates] . AKS i platforma Azure współpracują ze sobą, aby zminimalizować zakłócenia działania usług dla klastrów.

> [!Important]
> Funkcja autonaprawy węzła nie jest obecnie obsługiwana w przypadku pul węzłów systemu Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Jak AKS sprawdza dostępność węzłów w złej kondycji

> [!Note]
> AKS wykonuje akcję naprawy w węzłach przy użyciu konta użytkownika **AKS-remediator**.

AKS używa reguł, aby określić, czy węzeł jest w złej kondycji i czy wymaga naprawy. AKS używa następujących reguł, aby określić, czy automatyczna naprawa jest wymagana.

* Węzeł raportuje stan dla **niestopniowego** sprawdzania kolejnych sprawdzeń w ciągu 10 minut.
* Węzeł nie zgłasza stanu w ciągu 10 minut

Stan kondycji węzłów można sprawdzić ręcznie przy użyciu polecenia kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Jak działa automatyczna naprawa

> [!Note]
> AKS wykonuje akcję naprawy w węzłach przy użyciu konta użytkownika **AKS-remediator**.

To zachowanie jest przeznaczone dla **Virtual Machine Scale Sets**.  Funkcja autonaprawy wykonuje kilka kroków w celu naprawy uszkodzonego węzła.  Jeśli węzeł zostanie określony jako w złej kondycji, AKS próbuje wykonać kilka czynności zaradczych.  Kroki są wykonywane w następującej kolejności:

1. Gdy środowisko uruchomieniowe kontenera przestanie odpowiadać przez 10 minut, usługi uruchomieniowe zakończone niepowodzeniem zostaną ponownie uruchomione w węźle.
2. Jeśli węzeł nie jest gotowy w ciągu 10 minut, węzeł zostanie uruchomiony ponownie.
3. Jeśli węzeł nie jest gotowy w ciągu 30 minut, węzeł jest odtwarzany z obrazu.

> [!Note]
> Jeśli wiele węzłów jest w złej kondycji, są one naprawiane po jednym

## <a name="next-steps"></a>Następne kroki

Użyj [strefy dostępności][availability-zones] , aby zwiększyć wysoką dostępność przy użyciu obciążeń klastra AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
