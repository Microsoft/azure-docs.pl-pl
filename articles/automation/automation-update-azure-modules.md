---
title: Aktualizowanie modułów Azure PowerShell w programie Azure Automation
description: W tym artykule opisano sposób aktualizacji typowych modułów Azure PowerShell udostępnionych domyślnie w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 9cc5e5107784d447640ef0ae77d412885b1e5668
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185776"
---
# <a name="update-azure-powershell-modules"></a>Aktualizowanie modułów programu Azure PowerShell

Najpopularniejsze moduły programu PowerShell są udostępniane domyślnie na poszczególnych kontach usługi Automation. Zobacz [moduły domyślne](shared-resources/modules.md#default-modules). Ponieważ usługa Azure Team regularnie aktualizuje moduły platformy Azure, zmiany mogą wystąpić w przypadku dołączonych poleceń cmdlet. Te zmiany, na przykład zmiana nazwy parametru lub całkowite przestarzałe polecenie cmdlet, mogą negatywnie wpłynąć na elementy Runbook. 

> [!NOTE]
> Nie można usunąć modułów globalnych, które są modułami, które są dostępne dla usługi Automation.

## <a name="set-up-an-automation-account"></a>Konfigurowanie konta usługi Automation

Aby uniknąć wpływu elementów Runbook i procesów, które automatyzują, pamiętaj o przetestowaniu i sprawdzeniu poprawności w miarę wprowadzania aktualizacji. Jeśli nie masz dedykowanego konta usługi Automation przeznaczonego do tego celu, rozważ utworzenie go, aby umożliwić testowanie wielu różnych scenariuszy podczas opracowywania elementów Runbook. Testowanie powinno obejmować iteracyjne zmiany, takie jak aktualizowanie modułów programu PowerShell.

Upewnij się, że konto usługi Automation ma utworzone [poświadczenie konta Uruchom jako platformy Azure](manage-runas-account.md) .

W przypadku lokalnego tworzenia skryptów zaleca się, aby te same wersje modułów były lokalnie dostępne w ramach konta usługi Automation podczas testowania, aby upewnić się, że otrzymujesz te same wyniki. Po sprawdzeniu poprawności wyników i zastosowaniu wymaganych zmian można przenieść zmiany do środowiska produkcyjnego.

> [!NOTE]
> Nowe konto usługi Automation może nie zawierać najnowszych modułów.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Uzyskaj element Runbook do użycia na potrzeby aktualizacji

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musisz użyć elementu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) , który jest dostępny jako Open Source. Aby rozpocząć korzystanie z tego elementu Runbook do aktualizowania modułów platformy Azure, Pobierz go z repozytorium GitHub. Następnie można zaimportować ją do konta usługi Automation lub uruchomić jako skrypt. Aby dowiedzieć się, jak zaimportować element Runbook na koncie usługi Automation, zobacz [Importowanie elementu Runbook](manage-runbooks.md#import-a-runbook).

Element Runbook **Update-AutomationAzureModulesForAccount** obsługuje domyślnie aktualizację modułów Azure, AzureRM i AZ module. Zapoznaj się z tematem [aktualizowanie pliku Readme elementów Runbook modułów platformy Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) , aby uzyskać więcej informacji na temat aktualizowania modułów AZ. Automation przy użyciu tego elementu Runbook. Istnieją dodatkowe ważne czynniki, które należy wziąć pod uwagę podczas korzystania z polecenia AZ modules na koncie usługi Automation. Aby dowiedzieć się więcej, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Użyj aktualizacji kodu elementu Runbook jako zwykłego skryptu programu PowerShell

Możesz użyć kodu elementu Runbook jako zwykłego skryptu programu PowerShell zamiast elementu Runbook. Aby to zrobić, najpierw Zaloguj się do platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , a następnie Przekaż `-Login $false` do skryptu.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Używanie elementu Runbook aktualizacji w chmurach suwerennych

Aby użyć tego elementu Runbook w chmurach suwerennych, użyj `AzEnvironment` parametru, aby przekazać poprawne środowisko do elementu Runbook. Dopuszczalne wartości to AzureCloud (chmura publiczna Azure), AzureChinaCloud, AzureGermanCloud i AzureUSGovernment. Te wartości można pobrać przy użyciu polecenia `Get-AzEnvironment | select Name` . Jeśli wartość tego polecenia cmdlet nie zostanie przekazana, element Runbook domyślnie AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Aktualizowanie elementu Runbook w celu zaktualizowania określonej wersji modułu

Jeśli chcesz użyć określonej Azure PowerShell wersji modułu zamiast najnowszego modułu dostępnego w Galeria programu PowerShell, Przekaż te wersje do opcjonalnego `ModuleVersionOverrides` parametru elementu Runbook **Update-AutomationAzureModulesForAccount** . Przykłady można znaleźć w  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) elemencie Runbook. Moduły Azure PowerShell, które nie są wymienione w `ModuleVersionOverrides` parametrze, są aktualizowane przy użyciu najnowszych wersji modułu na Galeria programu PowerShell. Jeśli wartość Nothing nie zostanie przekazana do `ModuleVersionOverrides` parametru, wszystkie moduły są aktualizowane przy użyciu najnowszych wersji modułu na Galeria programu PowerShell. Takie zachowanie jest takie samo dla przycisku **Aktualizuj moduły platformy Azure** w Azure Portal.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje dotyczące korzystania z modułów, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).
* Aby uzyskać informacje na temat aktualizacji elementu Runbook, zobacz temat [aktualizowanie elementów Runbook modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
