---
title: Używanie grup dynamicznych z Azure Automation Update Management
description: W tym artykule opisano sposób używania grup dynamicznych z Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 20e6d26808964c8e697c694bd796af2851e7ca48
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450549"
---
# <a name="use-dynamic-groups-with-update-management"></a>Używanie grup dynamicznych z rozwiązaniem Update Management

Update Management umożliwia kierowanie do dynamicznych grup platformy Azure lub maszyn wirtualnych innych niż platformy Azure na potrzeby wdrożeń aktualizacji. Użycie grupy dynamicznej uniemożliwia edytowanie wdrożenia w celu aktualizacji maszyn.

> [!NOTE]
> Grupy dynamiczne nie działają z klasycznymi maszynami wirtualnymi.

Możesz definiować grupy dynamiczne dla maszyn platformy Azure lub spoza platformy Azure z usługi **Update Management** w Azure Portal. Zobacz [Zarządzanie aktualizacjami maszyn wirtualnych](update-mgmt-manage-updates-for-vm.md).

Grupa dynamiczna jest definiowana przez zapytanie, które Azure Automation oblicza w czasie wdrażania. Nawet jeśli zapytanie grupy dynamicznej pobiera dużą liczbę maszyn, Azure Automation może przetwarzać maksymalnie 1000 maszyn jednocześnie. Zobacz [Azure subscription and service limits, quotas, and constraints](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management) (Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).

> [!NOTE]
> Jeśli planujesz aktualizację więcej niż 1000 maszyn, zalecamy rozdzielenie aktualizacji między wiele harmonogramów aktualizacji. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definiowanie grup dynamicznych dla maszyn platformy Azure

Podczas definiowania zapytania grupy dynamicznej dla maszyn platformy Azure można użyć następujących elementów do wypełnienia grupy dynamicznej:

* Subskrypcja
* Grupy zasobów
* Lokalizacje
* Tagi

![Wybieranie grup](./media/update-mgmt-groups/select-groups.png)

Aby wyświetlić podgląd wyników zapytania grupy dynamicznej, kliknij przycisk **Podgląd**. Wersja zapoznawcza pokazuje członkostwo w grupie w bieżącym momencie. W tym przykładzie wyszukiwane są maszyny z tagiem `Role` dla grupy **BackendServer**. Jeśli ten tag został dodany przez więcej maszyn, są one dodawane do wszystkich przyszłych wdrożeń względem tej grupy.

![Podgląd grup](./media/update-mgmt-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definiowanie grup dynamicznych dla maszyn spoza platformy Azure

Grupa dynamiczna dla maszyn nieopartych na platformie Azure używa zapisanych wyszukiwań, nazywanych również grupami komputerów. Aby dowiedzieć się, jak utworzyć zapisane wyszukiwanie, zobacz [Tworzenie grupy komputerów](../../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po utworzeniu zapisanego wyszukiwania możesz wybrać je z listy zapisanych wyszukiwań w programie **Update Management** w Azure Portal. Kliknij pozycję **Podgląd** , aby wyświetlić podgląd komputerów w zapisanym wyszukiwaniu.

![Wybieranie grup](./media/update-mgmt-groups/select-groups-2.png)

## <a name="next-steps"></a>Następne kroki

Możesz [wykonywać zapytania dotyczące dzienników Azure monitor](update-mgmt-query-logs.md) , aby analizować oceny aktualizacji, wdrożenia i inne powiązane zadania zarządzania. Zawiera wstępnie zdefiniowane zapytania ułatwiające rozpoczęcie pracy.
