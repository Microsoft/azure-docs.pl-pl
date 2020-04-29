---
title: Zarządzanie elementami Runbook w Azure Automation
description: W tym artykule opisano sposób zarządzania elementami Runbook w programie Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676510"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie elementami Runbook w Azure Automation

Element Runbook można dodać do Azure Automation, [tworząc nowy](#creating-a-runbook) lub [importując istniejący](#importing-a-runbook) z pliku lub z [galerii elementów Runbook](automation-runbook-gallery.md). Ten artykuł zawiera informacje dotyczące tworzenia i importowania elementów Runbook z pliku. Możesz uzyskać szczegółowe informacje na temat uzyskiwania dostępu do elementów Runbook i modułów z społeczności w [elementach Runbook i galeriach modułów dla Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="creating-a-runbook"></a>Tworzenie elementu Runbook

Nowy element Runbook można utworzyć w Azure Automation przy użyciu jednego z portali platformy Azure lub środowiska Windows PowerShell. Po utworzeniu elementu Runbook można go edytować przy użyciu informacji w [przepływie pracy programu PowerShell](automation-powershell-workflow.md) i [tworzeniu grafiki w programie Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Tworzenie elementu Runbook w Azure Portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Kliknij pozycję **Utwórz element Runbook**.
4. Wprowadź nazwę elementu Runbook i wybierz jego [Typ](automation-runbook-types.md). Nazwa elementu Runbook musi rozpoczynać się od litery i może zawierać litery, cyfry, podkreślenia i łączniki.
5. Kliknij przycisk **Utwórz** , aby utworzyć element Runbook i otworzyć Edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie elementu Runbook za pomocą programu PowerShell

Do utworzenia pustego [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)można użyć polecenia cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) . Użyj `Type` parametru, aby określić jeden z typów elementów Runbook zdefiniowanych dla `New-AzAutomationRunbook`.

Poniższy przykład przedstawia sposób tworzenia nowego pustego elementu Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Importowanie elementu Runbook

Nowy element Runbook można utworzyć w Azure Automation przez zaimportowanie skryptu programu PowerShell lub przepływu pracy programu PowerShell (**. ps1**), wyeksportowanego graficznego elementu Runbook (**. graphrunbook**) lub skryptu python2 (**. PR**).  Należy określić [Typ elementu Runbook](automation-runbook-types.md) , który jest tworzony podczas importowania, biorąc pod uwagę poniższe zagadnienia.

* Plik **. ps1** , który nie zawiera przepływu pracy, można zaimportować do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks) lub [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Jeśli zaimportujesz go do elementu Runbook przepływu pracy programu PowerShell, zostanie on przekonwertowany na przepływ pracy. W takim przypadku Komentarze są zawarte w elemencie Runbook w celu opisania wprowadzonych zmian.

* Można zaimportować tylko plik **. ps1** zawierający przepływ pracy programu PowerShell do [elementu Runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Jeśli plik zawiera wiele przepływów pracy programu PowerShell, importowanie nie powiedzie się. Każdy przepływ pracy należy zapisać do własnego pliku i zaimportować osobno.

* Nie należy importować pliku **. ps1** zawierającego przepływ pracy programu PowerShell do [elementu Runbook programu PowerShell](automation-runbook-types.md#powershell-runbooks), ponieważ aparat skryptu programu PowerShell nie może go rozpoznać.

* Plik **. graphrunbook** można zaimportować tylko do nowego [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks). Należy pamiętać, że można utworzyć tylko graficzny element Runbook z pliku **. graphrunbook** .

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importuj element Runbook z pliku z Azure Portal

Aby zaimportować plik skryptu do Azure Automation, można użyć poniższej procedury.

> [!NOTE]
> Plik **. ps1** można zaimportować tylko do elementu Runbook przepływu pracy programu PowerShell przy użyciu portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Kliknij pozycję **Importuj element Runbook**.
4. Kliknij pozycję **plik Runbook** i wybierz plik do zaimportowania.
5. Jeśli pole **Nazwa** jest włączone, istnieje możliwość zmiany nazwy elementu Runbook. Nazwa musi zaczynać się od litery i może zawierać litery, cyfry, podkreślenia i kreski.
6. [Typ elementu Runbook](automation-runbook-types.md) jest wybierany automatycznie, ale można zmienić typ po wprowadzeniu odpowiednich ograniczeń do konta.
7. Kliknij przycisk **Utwórz**. Nowy element Runbook zostanie wyświetlony na liście elementów Runbook dla konta usługi Automation.
8. Aby można było uruchomić [element Runbook,](#publishing-a-runbook) należy go opublikować.

> [!NOTE]
> Po zaimportowaniu graficznego elementu Runbook lub graficznego elementu Runbook przepływu pracy programu PowerShell możesz przekonwertować go na inny typ. Nie można jednak skonwertować jednego z tych graficznych elementów Runbook do tekstowego elementu Runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importowanie elementu Runbook z pliku skryptu za pomocą programu Windows PowerShell

Użyj polecenia cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) , aby zaimportować plik skryptu jako szkic elementu Runbook przepływu pracy programu PowerShell. Jeśli element Runbook już istnieje, importowanie nie powiedzie się, o `Force` ile nie zostanie użyty parametr z poleceniem cmdlet.

Poniższy przykład pokazuje, jak zaimportować plik skryptu do elementu Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Testowanie elementu Runbook

Podczas testowania elementu Runbook jest wykonywana [wersja robocza](#publishing-a-runbook) i wszystkie wykonywane działania. Nie utworzono historii zadań, ale strumienie [wyjściowe](automation-runbook-output-and-messages.md#output-stream) i [ostrzegawcze i błędów](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych testu. Komunikaty do [pełnego strumienia](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku danych wyjściowych tylko wtedy, gdy zmienna [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) jest ustawiona na `Continue`.

Mimo że wersja robocza jest uruchamiana, element Runbook nadal wykonuje się normalnie i wykonuje wszelkie akcje dotyczące zasobów w środowisku. Z tego powodu należy przetestować elementy Runbook wyłącznie dla zasobów nieprodukcyjnych.

Procedura testowania każdego [typu elementu Runbook](automation-runbook-types.md) jest taka sama. Nie ma różnicy w testowaniu między edytorem tekstu i edytorem graficznym w Azure Portal.

1. Otwórz wersję roboczą elementu Runbook w [Edytorze](automation-edit-textual-runbook.md) tekstu lub [edytorze graficznym](automation-graphical-authoring-intro.md).
1. Kliknij przycisk **Testuj** , aby otworzyć stronę test.
1. Jeśli element Runbook ma parametry, są one wyświetlane w okienku po lewej stronie, gdzie można podać wartości do użycia w teście.
1. Jeśli chcesz uruchomić test w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md), Zmień **Parametry uruchomieniowe** na **hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie Zachowaj domyślną **platformę Azure** , aby uruchomić test w chmurze.
1. Kliknij przycisk **Uruchom** , aby rozpocząć test.
1. Za pomocą przycisków w okienku danych wyjściowych można zatrzymać lub wstrzymać [przepływ pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficzny](automation-runbook-types.md#graphical-runbooks) element Runbook podczas testowania. Gdy element Runbook zostanie wstrzymany, przed jego wstrzymaniem zostanie ukończone bieżące działanie. Po wstrzymaniu elementu Runbook można go zatrzymać lub uruchomić ponownie.
1. Sprawdź dane wyjściowe z elementu Runbook w okienku danych wyjściowych.

## <a name="publishing-a-runbook"></a>Publikowanie elementu Runbook

Podczas tworzenia lub importowania nowego elementu Runbook należy go opublikować przed jego uruchomieniem. Każdy element Runbook w Azure Automation ma wersję roboczą i opublikowaną wersję. Tylko wersję opublikowaną można uruchomić i tylko wersję roboczą można zmienić. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinna zostać udostępniona, należy ją opublikować, zastępując bieżącą opublikowaną wersję wersją roboczą.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikowanie elementu Runbook w Azure Portal

1. Otwórz element Runbook w Azure Portal.
2. Kliknij pozycję **Edytuj**.
3. Kliknij przycisk **Opublikuj** , a następnie pozycję **tak** w odpowiedzi na komunikat weryfikacyjny.

### <a name="publish-a-runbook-using-powershell"></a>Publikowanie elementu Runbook przy użyciu programu PowerShell

Użyj polecenia cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) , aby opublikować element Runbook za pomocą programu Windows PowerShell. Poniższy przykład przedstawia sposób publikowania przykładowego elementu Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Planowanie elementu Runbook w Azure Portal

Po opublikowaniu elementu Runbook można zaplanować jego działanie.

1. Otwórz element Runbook w Azure Portal.
2. Wybierz pozycję **harmonogramy** w obszarze **zasoby**.
3. Wybierz pozycję **Dodaj harmonogram**.
4. W okienku harmonogram elementu Runbook wybierz pozycję **Połącz harmonogram z elementem Runbook**.
5. Wybierz pozycję **Utwórz nowy harmonogram** w okienku harmonogram.
6. Wprowadź nazwę, opis i inne parametry w okienku Nowy harmonogram. 
7. Po utworzeniu harmonogramu zaznacz go i kliknij przycisk **OK**. Powinien być teraz połączony z elementem Runbook.
8. Poszukaj wiadomości e-mail w skrzynce pocztowej, aby powiadomić Cię o stanie elementu Runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak możesz skorzystać z galerii modułów Runbook i modułu programu PowerShell, zobacz [elementy Runbook i galerie modułów dla Azure Automation](automation-runbook-gallery.md).
* Aby dowiedzieć się więcej o edytowaniu elementów Runbook programu PowerShell i przepływu pracy programu PowerShell przy użyciu edytora tekstu, zobacz [Edytowanie tekstowych elementów Runbook w Azure Automation](automation-edit-textual-runbook.md).
* Aby dowiedzieć się więcej na temat graficznego tworzenia elementów Runbook, zobacz [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).
