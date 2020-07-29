---
title: Odłączanie obszaru roboczego od konta usługi Automation dla Update Management
description: Ten artykuł zawiera informacje dotyczące rozłączania obszaru roboczego Log Analytics z konta usługi Automation dla Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83835788"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Odłączanie obszaru roboczego od konta usługi Automation dla Update Management

Podczas operacji [Update Management](automation-update-management.md) można zdecydować, aby nie zintegrować konta usługi Automation z obszarem roboczym log Analytics. Ten artykuł zawiera informacje dotyczące rozłączenia obszaru roboczego z Twojego konta.

1. Zaloguj się do platformy Azure pod adresem https://portal.azure.com .

2. Usuń Update Management dla maszyn wirtualnych. Zobacz [usuwanie maszyn wirtualnych z Update Management](automation-remove-vms-from-update-management.md).

3. Jeśli Update Management obejmuje wcześniejsze wersje usługi Azure SQL monitoring, Instalator funkcji mógł utworzyć zasoby automatyzacji, które należy usunąć. W przypadku Update Management można chcieć usunąć następujące elementy, które nie są już potrzebne:

   * Harmonogramy aktualizacji — każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
   * Grupy hybrydowych procesów roboczych utworzone dla Update Management — każda nazwa jest podobna do maszyna1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Otwórz konto usługi Automation i wybierz opcję **połączony obszar roboczy** w obszarze **powiązane zasoby** po lewej stronie.

5. Na stronie Odłącz obszar roboczy kliknij **Odłącz obszar roboczy** i odpowiadaj na polecenia monitujące.

   ![Odłącz stronę obszaru roboczego](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Gdy Azure Automation próbuje odłączyć obszar roboczy Log Analytics, można śledzić postęp w obszarze **powiadomienia** z menu.

Alternatywnie możesz odłączyć obszar roboczy Log Analytics z konta usługi Automation z poziomu obszaru roboczego:

1. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. 
2. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

* Aby korzystać z tej funkcji, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać problemy z błędami funkcji, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md).
* Rozwiązywanie problemów z błędami programu Windows Update Agent można znaleźć w temacie [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z błędami agenta aktualizacji systemu Linux, zobacz [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
