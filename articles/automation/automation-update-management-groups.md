---
title: Używanie grup dynamicznych z Azure Automation Update Management
description: W tym artykule opisano sposób działania grup dynamicznych z Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617435"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z rozwiązaniem Update Management

Update Management umożliwia kierowanie do dynamicznych grup platformy Azure lub maszyn wirtualnych innych niż platformy Azure na potrzeby wdrożeń aktualizacji. Te grupy zdefiniowane przez zapytania są oceniane w czasie wdrażania, aby nie trzeba było edytować wdrożenia w celu dodania maszyn.

## <a name="azure-machines"></a>Maszyny platformy Azure

Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi. Podczas definiowania zapytania następujące elementy mogą być używane razem do wypełniania grupy dynamicznej:

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Wybieranie grup](./media/automation-update-management/select-groups.png)

Aby wyświetlić podgląd wyników grupy dynamicznej, kliknij przycisk **Podgląd**. Wersja zapoznawcza pokazuje członkostwo w grupie w bieżącym momencie. W tym przykładzie wyszukiwane są maszyny z tagiem `Role` dla grupy **BackendServer**. Jeśli ten tag został dodany przez więcej maszyn, są one dodawane do wszystkich przyszłych wdrożeń względem tej grupy.

![Podgląd grup](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Maszyny spoza platformy Azure

W przypadku maszyn spoza platformy Azure zapisane wyszukiwania, nazywane również grupami komputerów, są używane do tworzenia grupy dynamicznej. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu grupy możesz wybrać ją z listy zapisanych wyszukiwań. Kliknij pozycję **Podgląd** , aby wyświetlić w tym momencie komputery z zapisanym wyszukiwaniem.

![Wybieranie grup](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu grupy dynamicznej można [utworzyć wdrożenie aktualizacji](automation-tutorial-update-management.md).
