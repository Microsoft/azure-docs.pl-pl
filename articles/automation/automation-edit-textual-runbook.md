---
title: Edytowanie tekstów tekstów w usłudze Azure Automation
description: Ten artykuł zawiera różne procedury pracy z uruchomieniu kamiłonami programu PowerShell i powershell w usłudze Azure Automation przy użyciu edytora tekstu.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406026"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Edytowanie tekstów tekstów w usłudze Azure Automation

Edytor tekstu w usłudze Azure Automation może być używany do edytowania [śmiób ekshell](automation-runbook-types.md#powershell-runbooks) i [uruchomieniu tekstów programu PowerShell.](automation-runbook-types.md#powershell-workflow-runbooks) Ten edytor ma typowe funkcje innych edytorów kodu, takich jak IntelliSense. Posiada również kodowanie kolorów z dodatkowymi specjalnymi funkcjami, które pomagają w uzyskiwaniu dostępu do zasobów wspólnych dla elementów runbook. 

Edytor tekstu zawiera funkcję wstawiania kodu poleceń cmdlet, zasobów i elementów runbook podrzędnych do elementu runbook. Zamiast wpisywać w kodzie samodzielnie, można wybrać z listy dostępnych zasobów i edytor wstawia odpowiedni kod do emuńczy.

Każdy projekt runbook w usłudze Azure Automation ma dwie wersje, Wersja robocza i Opublikowane. Edytuj wersję roboczą likścień, a następnie publikujesz ją, aby można było ją wykonać. Nie można edytować wersji opublikowanej. Aby uzyskać więcej informacji, zobacz [Publikowanie likmy](manage-runbooks.md#publishing-a-runbook).

Ten artykuł zawiera szczegółowe kroki wykonywania różnych funkcji za pomocą tego edytora. Nie mają one zastosowania do [graficznych śmięty.](automation-runbook-types.md#graphical-runbooks) Aby pracować z tymi elementami runbook, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="editing-a-runbook-with-the-azure-portal"></a>Edytowanie uruchomieniu za pomocą witryny Azure portal

Poniższa procedura służy do otwierania elementów runbook do edycji w edytorze tekstowym.

1. W witrynie Azure portal wybierz swoje konto automatyzacji.
2. W obszarze **AUTOMATYZACJA PROCESÓW**wybierz **runbooks,** aby otworzyć listę śmięty.
3. Wybierz program runbook do edycji, a następnie kliknij pozycję **Edytuj**.
4. Edytuj program runbook.
5. Po zakończeniu edycji kliknij **pozycję Zapisz.**
6. Kliknij **przycisk Publikuj,** jeśli chcesz opublikować najnowszą wersję roboczą roboczej systemu runbook.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Wstawianie polecenia cmdlet do śmi tego systemu

1. W kanwie edytora tekstu umieść kursor w miejscu, w którym chcesz umieścić polecenie cmdlet.
2. Rozwiń węzeł **Polecenia cmdlets** w formancie Biblioteka.
3. Rozwiń moduł zawierający polecenie cmdlet do użycia.
4. Kliknij prawym przyciskiem myszy nazwę polecenia cmdlet, aby wstawić, a następnie wybierz pozycję **Dodaj do obszaru roboczego**. Jeśli polecenie cmdlet ma więcej niż jeden zestaw parametrów, edytor dodaje zestaw domyślny. Można również rozwinąć polecenie cmdlet, aby wybrać inny zestaw parametrów.
5. Należy zauważyć, że kod polecenia cmdlet jest wstawiany z całą listą parametrów.
6. Podać odpowiednią wartość zamiast wartości otoczonej nawiasami kątowymi (<>) dla wszystkich wymaganych parametrów. Usuń wszystkie parametry, które nie są potrzebne.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Wstawianie kodu podrzędnego eksmisji do egonatu

1. W kanwie edytora tekstu umieść kursor w miejscu, w którym chcesz umieścić kod [podrzędnego podręcznika](automation-child-runbooks.md).
2. Rozwiń węzeł **Elementy runbook w** formancie Biblioteka.
3. Kliknij prawym przyciskiem myszy projekt runbook, aby wstawić, a następnie wybierz pozycję **Dodaj do kanwy**.
4. Kod podrzędnego śmiękacza jest wstawiany z dowolnymi symbolami zastępczymi dla dowolnych parametrów uruchomieniu. Chyli dowolne.
5. Zastąp symbole zastępcze odpowiednimi wartościami dla każdego parametru.

### <a name="insert-an-asset-into-a-runbook"></a>Wstawianie zasobu do elementa runbook

1. W kanwy formantu edytora tekstu, umieść kursor, gdzie chcesz umieścić kod dla podstawowego podstawowego podstawowego.
2. Rozwiń węzeł **Zasoby** w formancie Biblioteka.
3. Rozwiń węzeł dla żądanego typu zasobu.
4. Kliknij prawym przyciskiem myszy nazwę zasobu, aby wstawić, a następnie wybierz pozycję **Dodaj do kanwy**. W przypadku [zasobów zmiennych](automation-variables.md)wybierz opcję **Dodaj "Pobierz zmienną" do kanwy** lub **Dodaj "Ustaw zmienną" do kanwy**, w zależności od tego, czy chcesz uzyskać, czy ustawić zmienną.
5. Należy zauważyć, że kod zasobu jest wstawiany do ego księgi śrucie.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Edytowanie uruchomieniu usługi Azure Automation przy użyciu programu Windows PowerShell

Aby edytować program runbook za pomocą programu Windows PowerShell, użyj wybranego edytora i zapisz go w pliku **ps1.** Do pobrania zawartości śmigieł, można użyć polecenia cmdlet [Export-AzAutomationRunbook.](/powershell/module/Az.Automation/Export-AzAutomationRunbook) Za pomocą polecenia cmdlet [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) można zastąpić istniejący projekt owy podręcznik zmodyfikowany.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Pobieranie zawartości życióru przy użyciu programu Windows PowerShell

W następujących przykładowych poleceniach pokazano, jak pobrać skrypt dla elementu Runbook, a następnie zapisać go jako plik skryptu. W tym przykładzie jest pobierana wersja robocza. Istnieje również możliwość pobrania opublikowanej wersji systemu runbook, chociaż tej wersji nie można zmienić.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Zmienianie zawartości życióru przy użyciu programu Windows PowerShell

Poniższe przykładowe polecenia pokazują, jak zastąpić istniejącą zawartość elementów runbook zawartością pliku skryptu. Jest to ta sama przykładowa procedura, jak w [polu Aby zaimportować program runbook z pliku skryptu za pomocą programu Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Pokrewne artykuły:

* [Zarządzanie podręcznikami runbook w usłudze Azure Automation](manage-runbooks.md)
* [Nauka przepływu pracy programu PowerShell](automation-powershell-workflow.md)
* [Tworzenie grafiki w usłudze Azure Automation](automation-graphical-authoring-intro.md)
* [Certyfikaty](automation-certificates.md)
* [Połączenia](automation-connections.md)
* [Poświadczenia](automation-credentials.md)
* [Harmonogramy](automation-schedules.md)
* [Zmienne](automation-variables.md)
* [Dokumentacja poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
