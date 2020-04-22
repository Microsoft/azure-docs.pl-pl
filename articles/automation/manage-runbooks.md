---
title: Zarządzanie podręcznikami runbook w usłudze Azure Automation
description: W tym artykule opisano sposób zarządzania podręcznikami runbook w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676510"
---
# <a name="manage-runbooks-in-azure-automation"></a>Zarządzanie podręcznikami runbook w usłudze Azure Automation

Element runbook można dodać do usługi Azure Automation, [tworząc nowy](#creating-a-runbook) lub [importując istniejący](#importing-a-runbook) z pliku lub [Galerii Umnień.](automation-runbook-gallery.md) Ten artykuł zawiera informacje na temat tworzenia i importowania książki runbook z pliku. Możesz uzyskać wszystkie szczegóły dotyczące uzyskiwania dostępu do śmięty i modułów społeczności w [podręcznikach runbook i galeriach modułów dla usługi Azure Automation.](automation-runbook-gallery.md)

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="creating-a-runbook"></a>Tworzenie uruchomieniu

Możesz utworzyć nowy projekt runbook w usłudze Azure Automation przy użyciu jednego z portali platformy Azure lub programu Windows PowerShell. Po utworzeniu elementa runbook można go edytować przy użyciu informacji w [obiegu pracy programu PowerShell learning](automation-powershell-workflow.md) i [tworzenia graficznym w usłudze Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Tworzenie eksty w witrynie Azure portal

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmigieł.
3. Kliknij **pozycję Utwórz księgę ekscesową**.
4. Wprowadź nazwę kątem i wybierz jego [typ](automation-runbook-types.md). Nazwa kreślnika musi zaczynać się od litery i może zawierać litery, cyfry, podkreślenia i myślniki.
5. Kliknij **przycisk Utwórz,** aby utworzyć projekt runbook i otworzyć edytor.

### <a name="create-a-runbook-with-powershell"></a>Tworzenie śmiętu za pomocą programu PowerShell

Za pomocą polecenia cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) można utworzyć pusty [element runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Użyj `Type` parametru, aby określić jeden `New-AzAutomationRunbook`z typów elementów runbook zdefiniowanych dla .

W poniższym przykładzie pokazano, jak utworzyć nowy pusty system runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Importowanie ego księgi runbook

Nowy element runbook można utworzyć w usłudze Azure Automation, importując skrypt programu PowerShell lub przepływ pracy programu PowerShell (**ps1),** wyeksportowany element runbook graficzny (**.graphrunbook**) lub skrypt Python2 (**.py**).  Należy określić [typ elementów runbook,](automation-runbook-types.md) który jest tworzony podczas importowania, biorąc pod uwagę następujące zagadnienia.

* Plik **ps1,** który nie zawiera przepływu pracy, można zaimportować do wiązka pracy programu [PowerShell](automation-runbook-types.md#powershell-runbooks) ani do systemu [runbook przepływu pracy programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Jeśli importujesz go do uruchomieniu pracę programu PowerShell, zostanie on przekonwertowany na przepływ pracy. W takim przypadku komentarze są uwzględniane w zestawie runbook, aby opisać zmiany, które zostały wprowadzone.

* Do [wiązka pracy przepływu programu PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)można zaimportować tylko plik **ps1** zawierający przepływ pracy programu PowerShell . Jeśli plik zawiera wiele przepływów pracy programu PowerShell, importowanie nie powiedzie się. Każdy przepływ pracy należy zapisać we własnym pliku i zaimportować oddzielnie.

* Nie należy importować pliku **ps1** zawierającego przepływ pracy programu PowerShell do [ego księgi runbook programu PowerShell,](automation-runbook-types.md#powershell-runbooks)ponieważ aparat skryptów programu PowerShell nie może go rozpoznać.

* Plik **.graphrunbook** można zaimportować tylko do nowego [graficznego programu runbook](automation-runbook-types.md#graphical-runbooks). Należy zauważyć, że element runbook graficzny można utworzyć tylko z pliku **.graphrunbook.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importowanie ego księgi runbook z pliku za pomocą witryny Azure portal

Poniższą procedurę można użyć do zaimportowania pliku skryptu do usługi Azure Automation.

> [!NOTE]
> Plik **ps1** można zaimportować tylko do śmiwoju przepływu pracy programu PowerShell za pomocą portalu.

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. W centrum wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmigieł.
3. Kliknij **pozycję Importuj księgę runbooka**.
4. Kliknij **pozycję Plik ekwidujnika** i wybierz plik do zaimportowania.
5. Jeśli pole **Nazwa** jest włączone, istnieje możliwość zmiany nazwy uruchomieniu. ścieżek. Nazwa musi zaczynać się od litery i może zawierać litery, cyfry, podkreślenia i myślniki.
6. [Typ elementu runbook](automation-runbook-types.md) jest wybierany automatycznie, ale można go zmienić po uwzględnieniu odpowiednich ograniczeń.
7. Kliknij przycisk **Utwórz**. Nowy projekt runbook pojawi się na liście uruchomieniu dla konta automatyzacji.
8. Przed [uruchomieniem należy opublikować projekt runbook.](#publishing-a-runbook)

> [!NOTE]
> Po zaimportowaniu graficznego łańwersu lub graficznego śmiętu przepływu pracy programu PowerShell można go przekonwertować na inny typ. Nie można jednak przekonwertować jednego z tych tekstów ek?, na tekstowy element runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importowanie życiornika z pliku skryptu za pomocą programu Windows PowerShell

Polecenie cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) służy do importowania pliku skryptu jako roboczej roboczej roboczej roboczej roboczej roboczej roboczej roboczej roboczej roboczej roboczej roboczej. Jeśli projekt runbook już istnieje, importowanie `Force` kończy się niepowodzeniem, chyba że parametr jest używany z poleceniem cmdlet.

W poniższym przykładzie pokazano, jak zaimportować plik skryptu do elementów runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Testowanie ego księgi runbook

Podczas testowania śmigieł, [wersja robocza](#publishing-a-runbook) jest wykonywana i wszystkie akcje, które wykonuje są wykonywane są wykonywane. Nie jest tworzona żadna historia zadań, ale strumienie [danych wyjściowych](automation-runbook-output-and-messages.md#output-stream) i [ostrzeżenie i błąd](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku dane wyjściowe testu. Komunikaty do [strumienia Verbose](automation-runbook-output-and-messages.md#message-streams) są wyświetlane w okienku Wyjście tylko wtedy, `Continue` [gdy zmienna VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) jest ustawiona na .

Mimo że wersja robocza jest uruchamiana, system runbook nadal wykonuje normalnie i wykonuje wszelkie akcje względem zasobów w środowisku. Z tego powodu należy testować tylko śmięty śmiwoje na zasobach nieprodukcyjnych.

Procedura testowania każdego [typu śmiętu jest](automation-runbook-types.md) taka sama. Nie ma różnicy w testowaniu między edytora tekstu i edytora graficznego w witrynie Azure portal.

1. Otwórz wersję roboczą śmigieł w [edytorze tekstowym](automation-edit-textual-runbook.md) lub [edytorze graficznym](automation-graphical-authoring-intro.md).
1. Kliknij przycisk **Testuj,** aby otworzyć stronę testową.
1. Jeśli projekt runbook ma parametry, są one wymienione w lewym okienku, gdzie można podać wartości, które mają być używane do testu.
1. Jeśli chcesz uruchomić test na [hybrydowym uzywiczeniu roboczym niuansu](automation-hybrid-runbook-worker.md)hybrydowego, zmień **ustawienia uruchamiania** na **Hybrydowy proces roboczy** i wybierz nazwę grupy docelowej.  W przeciwnym razie zachowaj domyślną **platformę Azure,** aby uruchomić test w chmurze.
1. Kliknij przycisk **Start,** aby rozpocząć test.
1. Za pomocą przycisków w okienku Dane wyjściowe można zatrzymać lub zawiesić przepływ pracy programu [PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) lub [graficzny](automation-runbook-types.md#graphical-runbooks) element runbook podczas testowania. Gdy element Runbook zostanie wstrzymany, przed jego wstrzymaniem zostanie ukończone bieżące działanie. Po wstrzymaniu elementu Runbook można go zatrzymać lub uruchomić ponownie.
1. Sprawdź dane wyjściowe z uruchomieniu w okienku Dane wyjściowe.

## <a name="publishing-a-runbook"></a>Publikowanie ego księgi runbook

Podczas tworzenia lub importowania nowego likwidu należy go opublikować, zanim będzie można go uruchomić. Każdy projekt owy w usłudze Azure Automation ma wersję roboczą i opublikowaną wersję. Tylko wersję opublikowaną można uruchomić i tylko wersję roboczą można zmienić. Na wersję opublikowaną nie mają wpływu żadne zmiany wprowadzone w wersji roboczej. Gdy wersja robocza powinna zostać udostępniona, należy ją opublikować, zastępując bieżącą opublikowaną wersję wersją roboczą.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publikowanie likemi w witrynie Azure portal

1. Otwórz projekt runbook w witrynie Azure portal.
2. Kliknij pozycję **Edytuj**.
3. Kliknij **pozycję Publikuj,** a następnie **tak** w odpowiedzi na komunikat weryfikacyjny.

### <a name="publish-a-runbook-using-powershell"></a>Publikowanie likemi przy użyciu programu PowerShell

Polecenie cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) służy do publikowania likwidowania w programie Windows PowerShell. W poniższym przykładzie pokazano, jak opublikować przykładowy projekt runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Planowanie uruchomieniu w witrynie Azure portal

Po opublikowaniu egou można zaplanować jego działanie.

1. Otwórz projekt runbook w witrynie Azure portal.
2. Wybierz **pozycję Harmonogramy** w obszarze **Zasoby**.
3. Wybierz **pozycję Dodaj harmonogram**.
4. W okienku Harmonogram yjsk z nadajnikiem wybierz pozycję **Połącz harmonogram z programem runbook**.
5. Wybierz pozycję **Utwórz nowy harmonogram** w okienku Harmonogram.
6. Wprowadź nazwę, opis i inne parametry w okienku Nowy harmonogram. 
7. Po utworzeniu harmonogramu zaznacz go i kliknij przycisk **OK**. Teraz powinien być połączony z twoim runbookiem.
8. Poszukaj wiadomości e-mail w skrzynce pocztowej, aby powiadomić Cię o stanie elementami eks-owych.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak korzystać z galerii modułów runbook i programu PowerShell, zobacz [Podręcznik runbook i galerie modułów dla usługi Azure Automation.](automation-runbook-gallery.md)
* Aby dowiedzieć się więcej na temat edytowania tekstów ceń roboczych programu PowerShell i programu PowerShell za pomocą edytora tekstu, zobacz [Edytowanie tekstów tekstów w usłudze Azure Automation](automation-edit-textual-runbook.md).
* Aby dowiedzieć się więcej na temat tworzenia graficznych śmióbek runbook, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).
