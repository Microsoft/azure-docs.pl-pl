---
title: Usuń funkcję Update Management Azure Automation
description: W tym artykule opisano, jak przestać używać Update Management i odłączyć konto usługi Automation od obszaru roboczego Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222585"
---
# <a name="remove-update-management-from-automation-account"></a>Usuń Update Management z konta usługi Automation

Po włączeniu zarządzania aktualizacjami na maszynach wirtualnych za pomocą Azure Automation Update Management można zrezygnować z korzystania z niej i usunąć konfigurację z obszaru roboczego konta i połączonej Log Analytics.  W tym artykule opisano, jak całkowicie usunąć Update Management z zarządzanych maszyn wirtualnych, konta usługi Automation i Log Analytics obszaru roboczego.

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Usuń zarządzanie maszynami wirtualnymi

Przed usunięciem Update Management należy najpierw przerwać zarządzanie maszynami wirtualnymi. Zobacz [usuwanie maszyn wirtualnych z Update Management](remove-vms.md) , aby wyrejestrować je z funkcji.

## <a name="remove-updatemanagement-solution"></a>Usuń rozwiązanie UpdateManagement

Aby można było odłączyć konto usługi Automation od obszaru roboczego, należy wykonać następujące kroki, aby całkowicie usunąć Update Management. Rozwiązanie **aktualizacji** zostanie usunięte z obszaru roboczego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal wybierz pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu wpisywania lista filtruje sugestie w oparciu o dane wejściowe. Wybierz pozycję **Log Analytics**.

3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy wybrany podczas włączania Update Management.

4. Po lewej stronie wybierz pozycję **rozwiązania**.  

5. Na liście rozwiązań wybierz pozycję **aktualizacje (nazwa obszaru roboczego)**. Na stronie **Przegląd** rozwiązania wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

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

## <a name="cleanup-automation-account"></a>Konto automatyzacji oczyszczania

Jeśli Update Management został skonfigurowany do obsługi wcześniejszych wersji usługi Azure SQL monitoring, Instalator funkcji mógł utworzyć zasoby automatyzacji, które należy usunąć. W przypadku Update Management można chcieć usunąć następujące elementy, które nie są już potrzebne:

   * Harmonogramy aktualizacji — każdy ma nazwę zgodną z utworzonym wdrożeniem aktualizacji.
   * Grupy hybrydowych procesów roboczych utworzone dla Update Management — każdy ma nazwę podobną do *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Następne kroki

Aby ponownie włączyć tę funkcję, zobacz [włączanie Update Management z poziomu konta usługi Automation](enable-from-automation-account.md), [Włączanie Update Management przez przeglądanie Azure Portal](enable-from-portal.md), [Włączanie Update Management z elementu Runbook](enable-from-runbook.md)lub [Włączanie Update Management z maszyny wirtualnej platformy Azure](enable-from-vm.md).
