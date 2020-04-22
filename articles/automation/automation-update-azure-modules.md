---
title: Aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation
description: W tym artykule opisano, jak można teraz aktualizować typowe moduły programu Azure PowerShell dostarczane domyślnie w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: bb73b3d644e96f9596f887faaf62eb15f01956ab
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769667"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Aktualizowanie modułów programu Azure PowerShell w usłudze Azure Automation

Aby zaktualizować moduły platformy Azure na koncie automatyzacji, należy użyć [identyfikatora runbooka Update Azure modules](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), który jest dostępny jako open source. Aby rozpocząć korzystanie z **update-AutomationAzureModulesForAccount** runbook, aby zaktualizować moduły platformy Azure, pobierz go z [repozytorium umulatek umownych modułów platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) w usłudze GitHub. Następnie można zaimportować go do konta automatyzacji lub uruchomić go jako skrypt. Aby dowiedzieć się, jak zaimportować system runbook na konto automatyzacji, zobacz [Importowanie ekwidujnika](manage-runbooks.md#importing-a-runbook).

Najczęściej moduły programu PowerShell są dostarczane domyślnie na każdym koncie automatyzacji. Zespół platformy Azure regularnie aktualizuje moduły platformy Azure. W związku z tym, aby zachować aktualność modułów na kontach automatyzacji, należy użyć [update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook.

Ponieważ moduły są regularnie aktualizowane przez grupę produktów, mogą wystąpić zmiany w dołączonych poleceniach cmdlet. Te zmiany, na przykład, zmiana nazwy parametru lub przestarzałe polecenie cmdlet całkowicie, może negatywnie wpłynąć na kreślenia.

Aby uniknąć wpływu na swoje programy runbook i procesy, które automatyzują, testują i weryfikują przed kontynuowaniem. Jeśli nie masz dedykowanego konta automatyzacji przeznaczonego do tego celu, należy rozważyć utworzenie jednego, aby można było przetestować wiele różnych scenariuszy podczas tworzenia śmiób ekustrych. Ta testowanie powinna zawierać zmiany iteracyjne, takie jak aktualizowanie modułów programu PowerShell.

Jeśli skrypty są rozwijane lokalnie, zaleca się, aby mieć te same wersje modułów lokalnie, które masz na koncie automatyzacji podczas testowania, aby upewnić się, że otrzymasz te same wyniki. Po sprawdzeniu poprawności wyników i zastosowaniu wszelkich wymaganych zmian można przenieść zmiany do produkcji.

> [!NOTE]
> Nowe konto automatyzacji może nie zawierać najnowszych modułów.

> [!NOTE]
> Nie będzie można usunąć modułów globalnych, które są modułami, które automatyzacja zapewnia po wyjęciu z pudełka.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Zagadnienia do rozważenia

Poniżej przedstawiono kilka kwestii, które należy wziąć pod uwagę podczas korzystania z tego artykułu, aby zaktualizować moduły platformy Azure:

* Element runbook opisany w tym artykule obsługuje domyślnie aktualizowanie modułów azure, azurerm i az. Zapoznaj się z [aktualizacją modułów platformy Azure README, aby](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) uzyskać więcej informacji na temat aktualizowania modułów Az.Automation za pomocą tego systemu runbook. Istnieją dodatkowe ważne czynniki, które należy wziąć pod uwagę podczas korzystania z modułów Az na koncie automatyzacji. Aby dowiedzieć się więcej, zobacz [Zarządzanie modułami w usłudze Azure Automation](shared-resources/modules.md).

* Przed uruchomieniem tego systemu runbook upewnij się, że konto automatyzacji ma utworzone [poświadczenia konta usługi Azure Uruchom jako.](manage-runas-account.md)

* Ten kod można użyć jako zwykłego skryptu programu PowerShell zamiast przewodniczącego: wystarczy zalogować się na platformę `-Login $false` Azure przy użyciu polecenia cmdlet [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) a następnie przekazać do skryptu.

* Aby użyć tego śmigieł `AzEnvironment` w chmurach suwerennych, należy użyć parametru, aby przekazać poprawne środowisko do egonatu.  Dopuszczalne wartości to AzureCloud (chmura publiczna azure), AzureChinaCloud, AzureGermanCloud i AzureUSGovernment. Te wartości można pobrać `Get-AzEnvironment | select Name`za pomocą programu . Jeśli nie przekażesz wartości do tego polecenia cmdlet, element runbook domyślnie azurecloud.

* Jeśli chcesz użyć określonej wersji modułu programu Azure PowerShell zamiast najnowszego modułu dostępnego `ModuleVersionOverrides` w Galerii programu PowerShell, przekaż te wersje do opcjonalnego **parametru podręcznika Update-AutomationAzureModulesForAccount.** Na przykład zobacz [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Moduły programu Azure PowerShell, które `ModuleVersionOverrides` nie są wymienione w parametrze są aktualizowane o najnowsze wersje modułów w galerii programu PowerShell. Jeśli nic nie `ModuleVersionOverrides` przekażesz do parametru, wszystkie moduły są aktualizowane o najnowsze wersje modułów w Galerii programu PowerShell. To zachowanie jest takie samo jak przycisk **Aktualizuj moduły platformy Azure.**

## <a name="next-steps"></a>Następne kroki

Odwiedź system [uruchamiania modułów aktualizacji platformy Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) typu open source, aby dowiedzieć się więcej na ten temat.
