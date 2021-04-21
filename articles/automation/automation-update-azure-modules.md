---
title: Aktualizowanie Azure PowerShell modułów w programie Azure Automation
description: W tym artykule opisano sposób aktualizowania typowych modułów Azure PowerShell dostarczanych domyślnie w Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c1632da35864fc6822b385adac06d7f124aea061
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830416"
---
# <a name="update-azure-powershell-modules"></a>Aktualizowanie modułów programu Azure PowerShell

Najbardziej typowe moduły programu PowerShell są domyślnie udostępniane na każdym koncie usługi Automation. Zobacz [Moduły domyślne.](shared-resources/modules.md#default-modules) Gdy zespół platformy Azure regularnie aktualizuje moduły platformy Azure, mogą wystąpić zmiany w uwzględnionych poleceniach cmdlet. Te zmiany, na przykład zmiana nazwy parametru lub całkowicie wycofane polecenie cmdlet, mogą negatywnie wpłynąć na twoje podręczniki Runbook. 

> [!NOTE]
> Nie można usuwać modułów globalnych, które są modułami zapewnianych przez usługę Automation.

## <a name="set-up-an-automation-account"></a>Konfigurowanie konta usługi Automation

Aby uniknąć wpływu na twoje podręczniki i zautomatyzowane procesy, przetestuj je i zweryfikuj podczas aktualizacji. Jeśli nie masz dedykowanego konta usługi Automation przeznaczonego do tego celu, rozważ utworzenie go, aby można było przetestować wiele różnych scenariuszy podczas opracowywania swoich podręczników Runbook. Testowanie powinno obejmować zmiany iteracyjne, takie jak aktualizowanie modułów programu PowerShell.

Upewnij się, że twoje konto usługi Automation ma [utworzone konto Uruchom jako platformy Azure.](automation-security-overview.md#run-as-accounts)

W przypadku tworzenia skryptów lokalnie zaleca się używanie lokalnie tych samych wersji modułu, które są dostępne na koncie usługi Automation podczas testowania w celu zapewnienia, że otrzymasz te same wyniki. Po weryfikacji wyników i zastosowaniu wymaganych zmian można przenieść je do produkcji.

> [!NOTE]
> Nowe konto usługi Automation może nie zawierać najnowszych modułów.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Uzyskiwanie runbook do użycia z aktualizacjami

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, należy użyć runbook [Update-AutomationAzureModulesForAccount,](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) który jest dostępny open source. Aby rozpocząć używanie tego runbook do aktualizowania modułów platformy Azure, pobierz go z repozytorium GitHub. Następnie możesz zaimportować go na konto usługi Automation lub uruchomić jako skrypt. Aby dowiedzieć się, jak zaimportować podręcznik Runbook na koncie usługi Automation, zobacz [Importowanie go.](manage-runbooks.md#import-a-runbook)

Runbook **Update-AutomationAzureModulesForAccount** domyślnie obsługuje aktualizowanie modułów Azure, AzureRM i Az. Aby uzyskać więcej informacji na temat aktualizowania modułów Az.Automation za pomocą tego runbook, zapoznaj się z tematem [Update Azure modules runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) (Aktualizowanie modułów platformy Azure przy użyciu technologii README). Istnieją dodatkowe ważne czynniki, które należy wziąć pod uwagę podczas korzystania z modułów Az na koncie usługi Automation. Aby dowiedzieć się więcej, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Używanie aktualizacji kodu runbook jako zwykłego skryptu programu PowerShell

Możesz użyć kodu runbook jako zwykłego skryptu programu PowerShell, a nie jako runbook. Aby to zrobić, zaloguj się do platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount,](/powershell/module/az.accounts/connect-azaccount) a następnie przekaż `-Login $false` go do skryptu.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Używanie aktualizacji runbook w suwerennych chmurach

Aby użyć tego runbook w suwerennych chmurach, użyj parametru , `AzEnvironment` aby przekazać poprawne środowisko do tego runbook. Dopuszczalne wartości to AzureCloud (publiczna chmura Azure), AzureChinaCloud, AzureGermanCloud i AzureUSGovernment. Te wartości można pobrać przy użyciu funkcji `Get-AzEnvironment | select Name` . Jeśli nie przekażemy wartości do tego polecenia cmdlet, wartość domyślna tego polecenia runbook to AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Aktualizowanie określonej wersji modułu za pomocą update runbook

Jeśli chcesz użyć określonej wersji modułu Azure PowerShell zamiast najnowszego modułu dostępnego na platformie Galeria programu PowerShell, przekaż te wersje do opcjonalnego parametru `ModuleVersionOverrides` runbook **Update-AutomationAzureModulesForAccount.** Przykłady można znaleźć w  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook. Azure PowerShell modułów, które nie są wymienione w parametrze , są aktualizowane przy użyciu najnowszych wersji modułu na `ModuleVersionOverrides` Galeria programu PowerShell. Jeśli nie przekażemy niczego do parametru , wszystkie moduły zostaną zaktualizowane o najnowsze wersje modułu na `ModuleVersionOverrides` Galeria programu PowerShell. To zachowanie jest takie samo dla **przycisku Aktualizuj moduły platformy Azure** w Azure Portal.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowe informacje o używaniu modułów, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).
* Aby uzyskać informacje na temat aktualizacji runbook, zobacz [Aktualizowanie runbook modułów platformy Azure.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
