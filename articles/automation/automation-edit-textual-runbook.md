---
title: Edytowanie tekstowych Azure Automation
description: W tym artykule opisano sposób używania edytora Azure Automation tekstowego do pracy z podręcznikami Runbook programu PowerShell i przepływu pracy programu PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829390"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Edytowanie tekstowych Azure Automation

Edytor tekstu w programie umożliwia edytowanie Azure Automation [Runbook](automation-runbook-types.md#powershell-runbooks) programu PowerShell i [runbook przepływu pracy programu PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Ten edytor zawiera typowe funkcje innych edytorów kodu, takie jak IntelliSense. Korzysta również z kodowania kolorami z dodatkowymi funkcjami specjalnymi, aby pomóc w uzyskiwaniu dostępu do zasobów wspólnych dla elementów Runbook. 

Edytor tekstowy zawiera funkcję wstawiania kodu dla polecenia cmdlet, zasobów i podrzędnego elementu Runbook do elementu Runbook. Zamiast wpisywać kod samodzielnie, możesz wybrać z listy dostępnych zasobów, a edytor wstawi odpowiedni kod do elementów Runbook.

Każdy z nich w Azure Automation ma dwie wersje: Wersja robocza i Opublikowano. Możesz edytować wersję roboczą tego runbook, a następnie opublikować go, aby można go było wykonać. Nie można edytować wersji opublikowanej. Aby uzyskać więcej informacji, zobacz [Publikowanie podręcznika Runbook.](manage-runbooks.md#publish-a-runbook)

Ten artykuł zawiera szczegółowe instrukcje dotyczące wykonywania różnych funkcji za pomocą tego edytora. Nie mają one zastosowania do [graficznych elementów Runbook.](automation-runbook-types.md#graphical-runbooks) Aby pracować z tymi elementami Runbook, zobacz [Graphical authoring in Azure Automation (Tworzenie graficzne w programie Azure Automation](automation-graphical-authoring-intro.md)).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Edytowanie runbook za pomocą Azure Portal

1. W Azure Portal wybierz swoje konto usługi Automation.
2. W **obszarze AUTOMATYZACJA** PROCESÓW wybierz pozycję **Runbook,** aby otworzyć listę elementów Runbook.
3. Wybierz pozycję runbook do edycji, a następnie kliknij pozycję **Edytuj.**
4. Edytuj ten runbook.
5. Po **zakończeniu** edycji kliknij przycisk Zapisz.
6. Kliknij **pozycję Publikuj,** jeśli chcesz opublikować najnowszą wersję roboczą tego runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Wstawianie polecenia cmdlet do runbook

1. Na kanwie edytora tekstów umieść kursor w miejscu, w którym chcesz umieścić polecenie cmdlet.
2. Rozwiń **węzeł Polecenia cmdlet** w kontrolce Biblioteka.
3. Rozwiń moduł zawierający polecenie cmdlet do użycia.
4. Kliknij prawym przyciskiem myszy nazwę polecenia cmdlet do wstawienia, a następnie wybierz **polecenie Dodaj do kanwy.** Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, edytor dodaje zestaw domyślny. Możesz również rozwinąć polecenie cmdlet , aby wybrać inny zestaw parametrów.
5. Zwróć uwagę, że kod polecenia cmdlet jest wstawiany wraz z całą listą parametrów.
6. Podaj odpowiednią wartość w miejsce wartości otaczającej nawiasy kątowe (<>) dla wymaganych parametrów. Usuń wszystkie parametry, które nie są potrzebne.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Wstawianie kodu dla podrzędnego podręcznika Runbook do runbook

1. Na kanwie edytora tekstów umieść kursor w miejscu, w którym chcesz umieścić kod podrzędnego [podręcznika Runbook.](automation-child-runbooks.md)
2. Rozwiń węzeł **Runbook** w kontrolce Biblioteka.
3. Kliknij prawym przyciskiem myszy pozycję runbook, aby wstawić, a następnie wybierz **polecenie Dodaj do kanwy.**
4. Kod podrzędnego elementu Runbook jest wstawiany z dowolnymi symbolami zastępczymi dla parametrów elementu Runbook.
5. Zastąp symbole zastępcze odpowiednimi wartościami dla każdego parametru.

### <a name="insert-an-asset-into-a-runbook"></a>Wstawianie zasobu do runbook

1. W kontrolce Kanwa edytora tekstów umieść kursor w miejscu, w którym chcesz umieścić kod podrzędnego podręcznika Runbook.
2. Rozwiń **węzeł Zasoby** w kontrolce Biblioteka.
3. Rozwiń węzeł dla żądanego typu zasobu.
4. Kliknij prawym przyciskiem myszy nazwę zasobu do wstawienia, a następnie wybierz **pozycję Dodaj do kanwy.** W [przypadku zasobów zmiennych](./shared-resources/variables.md)wybierz opcję Dodaj "Pobierz zmienną" do kanwy lub Dodaj "Ustaw zmienną" do kanwy w zależności od tego, czy chcesz pobrać, czy ustawić zmienną.  
5. Należy pamiętać, że kod zasobu jest wstawiany do runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Edytowanie Azure Automation Runbook przy użyciu Windows PowerShell

Aby edytować ten Windows PowerShell, użyj wybranego edytora i zapisz go w **pliku ps1.** Możesz użyć polecenia cmdlet [Export-AzAutomationRunbook,](/powershell/module/Az.Automation/Export-AzAutomationRunbook) aby pobrać zawartość tego runbook. Możesz użyć polecenia cmdlet  [Import-AzAutomationRunbook,](/powershell/module/Az.Automation/import-azautomationrunbook) aby zastąpić istniejący roboczy plik Runbook zmodyfikowanym.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Pobieranie zawartości podręcznika Runbook przy użyciu Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak pobrać skrypt dla elementu Runbook, a następnie zapisać go jako plik skryptu. W tym przykładzie jest pobierana wersja robocza. Można również pobrać opublikowaną wersję runbook, chociaż tej wersji nie można zmienić.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Zmienianie zawartości podręcznika Runbook przy użyciu Windows PowerShell

Następujące przykładowe polecenia pokazują, jak zastąpić istniejącą zawartość runbook zawartością pliku skryptu. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami Runbook w programie Azure Automation](manage-runbooks.md).
* [Poznasz przepływ pracy programu PowerShell.](automation-powershell-workflow.md)
* [Graficzne tworzenie w programie Azure Automation](automation-graphical-authoring-intro.md).
* [Certyfikaty](./shared-resources/certificates.md).
* [Połączenia](automation-connections.md).
* [Poświadczenia](./shared-resources/credentials.md).
* [Harmonogramy](./shared-resources/schedules.md).
* [Zmienne](./shared-resources/variables.md).
* [Informacje o poleceniach cmdlet programu PowerShell:](/powershell/module/az.automation).
