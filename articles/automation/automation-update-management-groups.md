---
title: Używanie grup dynamicznych za pomocą usługi Azure Automation Update Management
description: W tym artykule opisano, jak grupy dynamiczne współpracują z usługą Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617435"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z rozwiązaniem Update Management

Usługa Zarządzanie aktualizacjami umożliwia kierowanie na dynamiczną grupę maszyn wirtualnych platformy Azure lub maszyn wirtualnych innych niż Azure w celu wdrożenia aktualizacji. Te grupy, zdefiniowane przez kwerendy, są oceniane w czasie wdrażania, dzięki czemu nie trzeba edytować wdrożenia, aby dodać maszyny.

## <a name="azure-machines"></a>Maszyny platformy Azure

Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi. Podczas definiowania kwerendy można użyć następujących elementów do wypełniania grupy dynamicznej:

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Zaznaczanie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupy dynamicznej, kliknij pozycję **Podgląd**. Wersja zapoznawcza pokazuje członkostwo w grupie w bieżącym czasie. W tym przykładzie szukamy maszyn mających `Role` tag dla grupy **BackendServer**. Jeśli więcej maszyn ma ten tag dodany, są one dodawane do przyszłych wdrożeń przeciwko tej grupie.

![grupy w wersji zapoznawczej](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Maszyny nienawiązane z platformą Azure

W przypadku maszyn innych niż azure zapisane wyszukiwania, nazywane również grupami komputerów, są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy można ją wybrać z listy zapisanych wyszukiwań. Kliknij **pozycję Podgląd,** aby wyświetlić podgląd komputerów w zapisanym wyszukiwaniu w tym czasie.

![Zaznaczanie grup](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu grupy dynamicznej można [utworzyć wdrożenie aktualizacji](automation-tutorial-update-management.md).
