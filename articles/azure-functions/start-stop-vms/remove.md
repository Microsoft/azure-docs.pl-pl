---
title: Usuń Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza) — Omówienie
description: W tym artykule opisano sposób usuwania funkcji uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza).
services: azure-functions
ms.subservice: ''
ms.date: 03/30/2021
ms.topic: conceptual
ms.openlocfilehash: b4308be8f7494c1cb6f6b4839fc5a2e9717668e3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111802"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>Jak usunąć Uruchamianie/zatrzymywanie maszyn wirtualnych v2 (wersja zapoznawcza)

Po włączeniu funkcji uruchamiania/zatrzymywania maszyn wirtualnych v2 (wersja zapoznawcza) w celu zarządzania stanem działania maszyn wirtualnych platformy Azure Możesz zrezygnować z korzystania z niej. Tę funkcję można usunąć, usuwając grupę zasobów dedykowaną do przechowywania następujących zasobów w ramach obsługi uruchamiania/zatrzymywania maszyn wirtualnych w wersji 2 (wersja zapoznawcza):

- Azure Functions aplikacje
- Harmonogramy w Azure Logic Apps
- Wystąpienie Application Insights
- Konto usługi Azure Storage

## <a name="delete-the-dedicated-resource-group"></a>Usuń dedykowaną grupę zasobów

Aby usunąć grupę zasobów, wykonaj kroki opisane w artykule [Azure Resource Manager grupy zasobów i usunięcie zasobu](../../azure-resource-manager/management/delete-resource-group.md) .

## <a name="next-steps"></a>Następne kroki

Aby ponownie wdrożyć tę funkcję, zobacz [wdrażanie Start/Stop v2](deploy.md) (wersja zapoznawcza).