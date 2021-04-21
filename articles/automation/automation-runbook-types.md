---
title: Azure Automation typów elementów Runbook
description: W tym artykule opisano typy elementów Runbook, których można używać w programie Azure Automation, oraz zagadnienia dotyczące określania typu do użycia.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3e4f90372c2da22e8df3430ce340477352e5033b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830452"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation typów elementów Runbook

Funkcja Azure Automation Process Automation obsługuje kilka typów elementów Runbook, zgodnie z definicją w poniższej tabeli. Aby dowiedzieć się więcej o środowisku automatyzacji procesów, zobacz Runbook execution in Azure Automation (Wykonywanie [runbook w programie Azure Automation).](automation-runbook-execution.md)

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|Graficzny element Runbook oparty na Windows PowerShell oraz utworzony i edytowany całkowicie w edytorze graficznym w Azure Portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|Graficzny element Runbook oparty na Windows PowerShell Workflow oraz utworzony i edytowany w edytorze graficznym w Azure Portal. |
| [Program PowerShell](#powershell-runbooks) |Tekstowy runbook oparty na Windows PowerShell skryptów. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Tekstowy runbook oparty na Windows PowerShell skryptów przepływu pracy. |
| [Python](#python-runbooks) |Tekstowy runbook oparty na skryptach języka Python. |

Podczas określania typu, którego należy użyć dla określonego obiektu Runbook, należy wziąć pod uwagę następujące kwestie.

* Nie można konwertować elementów Runbook z graficznego na typ tekstowy ani w drugą stronę.
* Istnieją ograniczenia dotyczące używania elementów Runbook różnych typów jako podrzędnych elementów Runbook. Aby uzyskać więcej informacji, zobacz [Podrzędne Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Graficzne elementy Runbook

Graficzne i graficzne elementy Runbook przepływu pracy programu PowerShell można tworzyć i edytować przy użyciu edytora graficznego w Azure Portal. Nie można jednak tworzyć ani edytować tego typu runbook za pomocą innego narzędzia. Główne funkcje graficznych elementów Runbook:

* Wyeksportowane do plików na koncie usługi Automation, a następnie zaimportowane do innego konta usługi Automation.
* Wygeneruj kod programu PowerShell.
* Konwertowane na graficzne elementy Runbook przepływu pracy programu PowerShell lub z tych elementów podczas importowania.

### <a name="advantages"></a>Zalety

* Użyj wizualnego modelu tworzenia wstawiania i konfigurowania linku.
* Skoncentruj się na procesów przepływu danych.
* Wizualnie reprezentują procesy zarządzania.
* Dołącz inne podręczniki Runbook jako podrzędne podręczniki runbook, aby tworzyć przepływy pracy wysokiego poziomu.
* Zachęcaj do programowania modułowego.

### <a name="limitations"></a>Ograniczenia

* Nie można tworzyć ani edytować poza Azure Portal.
* Wykonanie złożonej logiki może wymagać działania kodu zawierającego kod programu PowerShell.
* Nie można przekonwertować na jeden z [formatów tekstowych](automation-runbook-types.md)ani przekonwertować tekstowego element runbook na format graficzny. 
* Nie można wyświetlać ani bezpośrednio edytować kodu programu PowerShell, który tworzy graficzny przepływ pracy. Kod, który tworzysz, można wyświetlić w dowolnych działaniach kodu.
* Nie można uruchamiać runbook w hybrydowym procesie roboczy runbook systemu Linux. Zobacz [Automatyzowanie zasobów w centrum danych lub chmurze przy użyciu hybrydowego procesu roboczego runbook.](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>PowerShell Runbook

Podręczniki Runbook programu PowerShell są oparte na Windows PowerShell. Kod runbook można edytować bezpośrednio przy użyciu edytora tekstu w Azure Portal.  Możesz również użyć dowolnego edytora tekstów w trybie offline i [zaimportować go](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Implementowanie całej złożonej logiki za pomocą kodu programu PowerShell bez innych złożonych funkcji przepływu pracy programu PowerShell.
* Uruchamianie szybsze niż uruchamianie podręczników Runbook przepływu pracy programu PowerShell, ponieważ nie trzeba ich kompilować przed uruchomieniem.
* Uruchamianie na platformie Azure i w hybrydowych środowiskach workers runbook dla systemów Windows i Linux.

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty programu PowerShell.
* Przy użyciu przetwarzania równoległego w ramach elementów Runbook nie [można](automation-powershell-workflow.md#use-parallel-processing) równolegle wykonywać wielu akcji.
* W przypadku wystąpienia błędu element Runbook nie może używać [punktów](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) kontrolnych do wznawiania działania.
* Jako podrzędne elementy Runbook można uwzględnić tylko elementy Runbook przepływu pracy programu PowerShell i graficzne elementy Runbook za pomocą polecenia cmdlet [Start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) które tworzy nowe zadanie.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono znane obecnie problemy dotyczące elementy Runbook programu PowerShell:

* W elementach Runbook programu PowerShell nie można pobrać niezaszyfrowanego zasobu zmiennej [o](./shared-resources/variables.md) wartości null.
* W programie PowerShell nie można pobrać zasobu zmiennej o `*~*` nazwie .
* Operacja [Get-Process](/powershell/module/microsoft.powershell.management/get-process) w pętli w elementach Runbook programu PowerShell może ulegać awarii po około 80 iteracjach.
* Jeśli program PowerShell spróbuje jednocześnie zapisać dużą ilość danych do strumienia wyjściowego, może się nie powieść. Ten problem można zwykle omiąć, mając dane wyjściowe runbook tylko te informacje potrzebne do pracy z dużymi obiektami. Na przykład zamiast używać polecenia bez ograniczeń, dane wyjściowe polecenia cmdlet mogą mieć tylko `Get-Process` wymagane parametry, tak jak w przypadku polecenia `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>Runbook przepływu pracy programu PowerShell

Pliki Runbook przepływu pracy programu PowerShell są tekstami runbook opartymi na Windows PowerShell [Przepływ pracy](automation-powershell-workflow.md)programu . Kod runbook można edytować bezpośrednio przy użyciu edytora tekstu w Azure Portal. Możesz również użyć dowolnego edytora tekstów w trybie offline i [zaimportować go](manage-runbooks.md) do Azure Automation.

### <a name="advantages"></a>Zalety

* Implementowanie całej złożonej logiki za pomocą kodu przepływu pracy programu PowerShell.
* Użyj [punktów kontrolnych,](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) aby wznowić działanie w przypadku wystąpienia błędu.
* Przetwarzanie [równoległe jest równoległe](automation-powershell-workflow.md#use-parallel-processing) w celu równoległego prowadzenia wielu akcji.
* Może zawierać inne graficzne elementy Runbook i elementy Runbook przepływu pracy programu PowerShell jako podrzędne elementy Runbook, aby tworzyć przepływy pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Musisz znać przepływ pracy programu PowerShell.
* Element runbook musi radzić sobie z dodatkowymi złożonościami przepływu pracy programu PowerShell, takimi jak [deserializowane obiekty](automation-powershell-workflow.md#deserialized-objects).
* Uruchamianie podręczników Runbook trwa dłużej niż uruchamianie programu PowerShell, ponieważ muszą one zostać skompilowane przed uruchomieniem.
* Za pomocą polecenia cmdlet można uwzględnić tylko podrzędne podręczniki Runbook programu `Start-AzAutomationRunbook` PowerShell.
* Nie można uruchamiać runbook w hybrydowym procesu roboczego runbook systemu Linux.

## <a name="python-runbooks"></a>Python Runbook

Aplikacje Runbook języka Python są kompilowane w językach Python 2 i Python 3. Aplikacje Runbook języka Python 3 są obecnie dostępne w wersji zapoznawczej. Kod runbook można edytować bezpośrednio przy użyciu edytora tekstów w Azure Portal. Można również użyć edytora tekstów w trybie offline i [zaimportować element Runbook](manage-runbooks.md) do Azure Automation.

Elementy Runbook języka Python 3 są obsługiwane w następujących globalnych infrastrukturach platformy Azure:

* Globalna platforma Azure
* Azure Government

### <a name="advantages"></a>Zalety

* Korzystaj z niezawodnych bibliotek języka Python.
* Można uruchamiać na platformie Azure lub w hybrydowych środowiskach pracy runbook.
* W przypadku języka Python 2 hybrydowe pracownicy runbook systemu Windows są obsługiwani z [zainstalowanym środowiskiem Python 2.7.](https://www.python.org/downloads/release/latest/python2)
* W przypadku zadań w chmurze języka Python 3 obsługiwana jest wersja 3.8 języka Python. Skrypty i pakiety z dowolnej wersji 3.x mogą działać, jeśli kod jest zgodny w różnych wersjach.  
* W przypadku zadań hybrydowych języka Python 3 na maszynach z systemem Windows możesz zainstalować dowolną wersję 3.x, której chcesz użyć.  
* W przypadku zadań hybrydowych języka Python 3 na maszynach z systemem Linux zależymy od wersji języka Python 3 zainstalowanej na maszynie w celu uruchomienia konfiguracji DSC OMSConfig i hybrydowego procesu roboczego systemu Linux. Zalecamy zainstalowanie wersji 3.6 na maszynach z systemem Linux. Jednak różne wersje powinny również działać, jeśli nie ma żadnych zmian przełomowych w podpisach metod lub kontraktach między wersjami języka Python 3.

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty języka Python.
* Aby korzystać z bibliotek innych firm, należy [zaimportować pakiety](python-packages.md) do konta usługi Automation.
* Uruchamianie nie działa przy użyciu polecenia cmdlet **Start-AutomationRunbook** w przepływie pracy programu   PowerShell/programu PowerShell, aby uruchomić runbook języka Python 3 (wersja zapoznawcza). Aby omiąć to ograniczenie, można użyć polecenia cmdlet **Start-AzAutomationRunbook** z modułu Az.Automation lub **start-AzureRmAutomationRunbook** z modułu AzureRm.Automation.  
* Pakiety i książki Runbook języka Python 3 (wersja zapoznawcza) nie działają z programem PowerShell.
* Azure Automation nie obsługuje **sys.stderr.**

### <a name="known-issues"></a>Znane problemy

Zadania języka Python 3 czasami kończy się niepowodzeniem z komunikatem o wyjątku *nieprawidłowa ścieżka pliku wykonywalnego interpretera*. Ten wyjątek może wystąpić, jeśli zadanie jest opóźnione, zaczyna się więcej niż 10 minut lub uruchamia się przy użyciu **start-AutomationRunbook** języka Python 3. Jeśli zadanie jest opóźnione, ponowne uruchomienie runbook powinno być wystarczające.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o runbookach programu PowerShell, [zobacz Samouczek: tworzenie runbook programu PowerShell.](learn/automation-tutorial-runbook-textual-powershell.md)
* Aby dowiedzieć się więcej o runbookach przepływu pracy programu PowerShell, zobacz [Samouczek: tworzenie runbook przepływu pracy programu PowerShell.](learn/automation-tutorial-runbook-textual.md)
* Aby dowiedzieć się więcej o graficznych elementach Runbook, [zobacz Samouczek: tworzenie graficznego element runbook.](learn/automation-tutorial-runbook-graphical.md)
* Aby dowiedzieć się więcej o runbookach języka Python, [zobacz Samouczek: tworzenie runbook języka Python.](learn/automation-tutorial-runbook-textual-python2.md)
