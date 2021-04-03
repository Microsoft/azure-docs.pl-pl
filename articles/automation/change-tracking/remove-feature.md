---
title: Usuwanie funkcji Azure Automation Change Tracking i spisu
description: W tym artykule opisano, jak zatrzymać korzystanie z Change Tracking i spisu oraz odłączyć konto usługi Automation od obszaru roboczego Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92210266"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Usuń Change Tracking i spis z konta usługi Automation

Po włączeniu zarządzania maszynami wirtualnymi za pomocą Azure Automation Change Tracking i spisu można zrezygnować z korzystania z niej i usunąć konfigurację z obszaru roboczego Log Analytics konta i połączonej usługi. W tym artykule opisano sposób całkowitego usuwania Change Tracking i spisu z zarządzanych maszyn wirtualnych, konta usługi Automation i Log Analytics obszaru roboczego.

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Usuń zarządzanie maszynami wirtualnymi

Przed usunięciem Change Tracking i spisem należy najpierw przerwać zarządzanie maszynami wirtualnymi. Zobacz [usuwanie maszyn wirtualnych z Change Tracking](remove-vms-from-change-tracking.md) , aby wyrejestrować je z funkcji.

## <a name="remove-changetracking-solution"></a>Usuń rozwiązanie śledzenia zmian

Aby można było odłączyć konto usługi Automation od obszaru roboczego, należy wykonać następujące kroki, aby całkowicie usunąć Change Tracking i spis. Rozwiązanie **śledzenia zmian** zostanie usunięte z obszaru roboczego.

1. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz pozycję **Log Analytics**.

2. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy wybrany podczas włączania Change Tracking i spisu.

3. Po lewej stronie wybierz pozycję **rozwiązania**.  

4. Na liście rozwiązań wybierz pozycję **śledzenia zmian (nazwa obszaru roboczego)**. Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

## <a name="unlink-workspace-from-automation-account"></a>Odłączanie obszaru roboczego od konta usługi Automation

1. W Azure Portal wybierz pozycję **konta usługi Automation**.

2. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

3. Na stronie **Odłącz obszar roboczy** wybierz opcję **Odłącz obszar roboczy** i odpowiadaj na instrukcje.

   ![Odłącz stronę obszaru roboczego](media/remove-feature/automation-unlink-workspace-blade.png)

Podczas próby odłączenia obszaru roboczego Log Analytics można śledzić postęp w obszarze **powiadomienia** z menu.

Alternatywnie możesz odłączyć obszar roboczy Log Analytics z konta usługi Automation z poziomu obszaru roboczego:

1. W witrynie Azure Portal wybierz opcję **Log Analytics**.

2. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**.

3. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

Podczas próby odłączenia konta usługi Automation można śledzić postęp w obszarze **powiadomienia** z menu.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć tę funkcję, zobacz [włączanie Change Tracking i spisu na podstawie konta usługi Automation](enable-from-automation-account.md), [Włącz Change Tracking i spis, przeglądając Azure Portal](enable-from-portal.md), [włączyć Change Tracking i spis z elementu Runbook](enable-from-runbook.md), lub [włączyć Change Tracking i spis z maszyny wirtualnej platformy Azure](enable-from-vm.md).
