---
title: Edytowanie tekstowych elementów Runbook w Azure Automation
description: Ten artykuł zawiera różne procedury pracy z elementami Runbook programu PowerShell i programu PowerShell w programie Azure Automation przy użyciu edytora tekstu.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406026"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edytowanie tekstowych elementów Runbook w Azure Automation

Edytor tekstowy w Azure Automation może służyć do edytowania [elementów Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) i [elementów Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Ten Edytor ma typowe funkcje innych edytorów kodu, takich jak IntelliSense. Ma także kolorowe kodowanie z dodatkowymi funkcjami specjalnymi ułatwiającymi uzyskiwanie dostępu do zasobów wspólnych dla elementów Runbook. 

Edytor tekstowy zawiera funkcję wstawiania kodu dla poleceń cmdlet, zasobów i podrzędnych elementów Runbook do elementu Runbook. Zamiast wpisywać kod samodzielnie, możesz wybrać z listy dostępnych zasobów, a Edytor wstawia odpowiedni kod do elementu Runbook.

Każdy element Runbook w Azure Automation ma dwie wersje, wersję roboczą i opublikowaną. Można edytować wersję roboczą elementu Runbook, a następnie opublikować ją, aby można ją było wykonać. Nie można edytować wersji opublikowanej. Aby uzyskać więcej informacji, zobacz [Publikowanie elementu Runbook](manage-runbooks.md#publishing-a-runbook).

Ten artykuł zawiera szczegółowe instrukcje dotyczące wykonywania różnych funkcji za pomocą tego edytora. Nie mają one zastosowania do [graficznych elementów Runbook](automation-runbook-types.md#graphical-runbooks). Aby obejść te elementy Runbook, zobacz temat [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Edytowanie elementu Runbook za pomocą Azure Portal

Aby otworzyć element Runbook do edycji w edytorze tekstu, należy wykonać czynności opisane w poniższej procedurze.

1. W Azure Portal wybierz konto usługi Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **elementy Runbook** , aby otworzyć listę elementów Runbook.
3. Wybierz element Runbook do edycji, a następnie kliknij przycisk **Edytuj**.
4. Edytuj element Runbook.
5. Po zakończeniu edycji kliknij przycisk **Zapisz** .
6. Kliknij przycisk **Opublikuj** , aby opublikować najnowszą wersję roboczą elementu Runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Wstaw polecenie cmdlet do elementu Runbook

1. Na kanwie edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić polecenie cmdlet.
2. Rozwiń węzeł **polecenia cmdlet** w kontrolce Biblioteka.
3. Rozwiń moduł zawierający polecenie cmdlet do użycia.
4. Kliknij prawym przyciskiem myszy nazwę polecenia cmdlet, aby wstawić i wybrać pozycję **Dodaj do kanwy**. Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, Edytor dodaje zestaw domyślny. Możesz również rozwinąć polecenie cmdlet, aby wybrać inny zestaw parametrów.
5. Należy zauważyć, że kod dla polecenia cmdlet zostanie wstawiony wraz z całą listą parametrów.
6. Podaj odpowiednią wartość zamiast wartości ujętej w nawiasy kątowe (<>) dla wszystkich wymaganych parametrów. Usuń wszystkie parametry, które nie są potrzebne.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Wstawianie kodu dla podrzędnego elementu Runbook do elementu Runbook

1. Na kanwie edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić kod dla [podrzędnego elementu Runbook](automation-child-runbooks.md).
2. Rozwiń węzeł **elementy Runbook** w kontrolce Biblioteka.
3. Kliknij prawym przyciskiem myszy element Runbook, a następnie wybierz pozycję **Dodaj do kanwy**.
4. Kod podrzędnego elementu Runbook jest wstawiany z dowolnym symbolem zastępczym dla każdego z parametrów elementu Runbook.
5. Zastąp symbole zastępcze odpowiednimi wartościami dla każdego parametru.

### <a name="insert-an-asset-into-a-runbook"></a>Wstawianie elementu zawartości do elementu Runbook

1. W kontrolce kanwy edytora tekstu Umieść kursor w miejscu, w którym chcesz umieścić kod dla podrzędnego elementu Runbook.
2. Rozwiń węzeł **zasobów** w kontrolce Biblioteka.
3. Rozwiń węzeł żądanego typu elementu zawartości.
4. Kliknij prawym przyciskiem myszy nazwę zasobu, aby wstawić, a następnie wybierz pozycję **Dodaj do kanwy**. W przypadku [zasobów zmiennych](automation-variables.md), wybierz opcję **"Pobierz zmienną" do kanwy** lub **Dodaj "Ustaw zmienną" do kanwy**, w zależności od tego, czy chcesz uzyskać lub ustawić zmienną.
5. Należy zauważyć, że kod dla elementu zawartości jest wstawiany do elementu Runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Edytowanie elementu Runbook Azure Automation przy użyciu programu Windows PowerShell

Aby edytować element Runbook za pomocą programu Windows PowerShell, Użyj wybranego edytora i Zapisz element Runbook w pliku **. ps1** . Aby pobrać zawartość elementu Runbook, można użyć polecenia cmdlet [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) . Możesz użyć polecenia cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) , aby zamienić istniejący element Runbook o zmienionej wersji.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Pobieranie zawartości elementu Runbook za pomocą programu Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak pobrać skrypt dla elementu Runbook, a następnie zapisać go jako plik skryptu. W tym przykładzie jest pobierana wersja robocza. Istnieje również możliwość pobrania opublikowanej wersji elementu Runbook, chociaż nie można zmienić tej wersji.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Zmiana zawartości elementu Runbook za pomocą programu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak zastąpić istniejącą zawartość elementu Runbook zawartością pliku skryptu. Jest to taka sama Przykładowa procedura jak w programie w [celu zaimportowania elementu Runbook z pliku skryptu za pomocą programu Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md)
* [Uczenie przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md)
* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Harmonogramy](automation-schedules.md)
* [Zmienne](automation-variables.md)
* [Dokumentacja poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
