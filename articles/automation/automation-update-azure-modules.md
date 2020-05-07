---
title: Aktualizowanie modułów Azure PowerShell w programie Azure Automation
description: W tym artykule opisano, jak można teraz aktualizować typowe moduły Azure PowerShell udostępniane domyślnie w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855557"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Aktualizowanie modułów Azure PowerShell w programie Azure Automation

Aby zaktualizować moduły platformy Azure na koncie usługi Automation, musisz użyć [elementu Runbook aktualizacji modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), który jest dostępny jako Open Source. Aby rozpocząć korzystanie z elementu Runbook **Update-AutomationAzureModulesForAccount** do aktualizowania modułów platformy Azure, Pobierz go z [repozytorium elementów Runbook aktualizacji modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w witrynie GitHub. Następnie można zaimportować ją do konta usługi Automation lub uruchomić jako skrypt. Aby dowiedzieć się, jak zaimportować element Runbook na koncie usługi Automation, zobacz [Importowanie elementu Runbook](manage-runbooks.md#import-a-runbook).

Najpopularniejsze moduły programu PowerShell są udostępniane domyślnie na poszczególnych kontach usługi Automation. Zespół platformy Azure regularnie aktualizuje moduły platformy Azure. W związku z tym, aby zachować aktualność modułów na kontach usługi Automation, należy użyć elementu Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Ponieważ moduły są regularnie aktualizowane przez grupę produktów, zmiany mogą wystąpić z dołączonymi poleceniami cmdlet. Te zmiany, na przykład zmiana nazwy parametru lub całkowite przestarzałe polecenie cmdlet, mogą negatywnie wpłynąć na elementy Runbook.

Aby uniknąć wpływu elementów Runbook i procesów, które automatyzują, Testuj i Weryfikuj przed kontynuowaniem. Jeśli nie masz dedykowanego konta usługi Automation przeznaczonego do tego celu, rozważ utworzenie go, aby umożliwić testowanie wielu różnych scenariuszy podczas opracowywania elementów Runbook. Testowanie powinno obejmować iteracyjne zmiany, takie jak aktualizowanie modułów programu PowerShell.

W przypadku lokalnego tworzenia skryptów zaleca się, aby te same wersje modułów były lokalnie dostępne w ramach konta usługi Automation podczas testowania, aby upewnić się, że otrzymasz te same wyniki. Po sprawdzeniu poprawności wyników i zastosowaniu wymaganych zmian można przenieść zmiany do środowiska produkcyjnego.

> [!NOTE]
> Nowe konto usługi Automation może nie zawierać najnowszych modułów.

> [!NOTE]
> Nie będzie można usunąć modułów globalnych, które są modułami, które są dostępne dla usługi Automation.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę w przypadku aktualizowania modułów platformy Azure przy użyciu tego artykułu:

* Element Runbook opisany w tym artykule obsługuje domyślnie aktualizowanie modułów Azure, AzureRM i AZ module. Zapoznaj się z tematem [aktualizowanie pliku Readme elementów Runbook modułów platformy Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) , aby uzyskać więcej informacji na temat aktualizowania modułów AZ. Automation przy użyciu tego elementu Runbook. Istnieją dodatkowe ważne czynniki, które należy wziąć pod uwagę podczas korzystania z polecenia AZ modules na koncie usługi Automation. Aby dowiedzieć się więcej, zobacz [Zarządzanie modułami w Azure Automation](shared-resources/modules.md).

* Przed uruchomieniem tego elementu Runbook upewnij się, że konto usługi Automation ma utworzone [poświadczenie konta Uruchom jako platformy Azure](manage-runas-account.md) .

* Możesz użyć tego kodu jako zwykłego skryptu programu PowerShell zamiast elementu Runbook: wystarczy najpierw zalogować się do platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , `-Login $false` a następnie przekazać do skryptu.

* Aby użyć tego elementu Runbook w przypadku suwerennych chmur, `AzEnvironment` Użyj parametru, aby przekazać poprawne środowisko do elementu Runbook.  Dopuszczalne wartości to AzureCloud (chmura publiczna Azure), AzureChinaCloud, AzureGermanCloud i AzureUSGovernment. Te wartości można pobrać przy użyciu `Get-AzEnvironment | select Name`polecenia. Jeśli wartość tego polecenia cmdlet nie zostanie przekazana, element Runbook domyślnie AzureCloud.

* Jeśli chcesz użyć określonej Azure PowerShell wersji modułu zamiast najnowszego modułu dostępnego w Galeria programu PowerShell, Przekaż te wersje do opcjonalnego `ModuleVersionOverrides` parametru elementu Runbook **Update-AutomationAzureModulesForAccount** . Aby zapoznać się z przykładami, zobacz element Runbook [Update-AutomationAzureModulesForAccount. ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) . Moduły Azure PowerShell, które nie są wymienione `ModuleVersionOverrides` w parametrze, są aktualizowane przy użyciu najnowszych wersji modułu na Galeria programu PowerShell. Jeśli wartość `ModuleVersionOverrides` Nothing nie zostanie przekazana do parametru, wszystkie moduły są aktualizowane przy użyciu najnowszych wersji modułu na Galeria programu PowerShell. Takie zachowanie jest takie samo, jak przycisk **Aktualizuj moduły platformy Azure** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na ten temat, odwiedź [element Runbook modułu Update modules dla usługi Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) sources.
