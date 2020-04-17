---
title: Typy elementów runbook usługi Azure Automation
description: W tym artykule opisano różne typy elementów runbook, których można użyć w usłudze Azure Automation i zagadnienia dotyczące określania typu, którego chcesz użyć.
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535523"
---
# <a name="azure-automation-runbook-types"></a>Typy elementów runbook usługi Azure Automation

Usługa automatyzacji procesów usługi Azure Automation obsługuje kilka typów elementów runbook, zgodnie z definicją w poniższej tabeli. Aby dowiedzieć się więcej o środowisku automatyzacji procesów, zobacz [Wykonanie uruchomieniu księgi wykonawczej w usłudze Azure Automation.](automation-runbook-execution.md)

| Typ | Opis |
|:--- |:--- |
| [Element graficzny](#graphical-runbooks)|Graficzny element runbook oparty na programie Windows PowerShell i utworzony i edytowany całkowicie w edytorze graficznym w witrynie Azure portal. |
| [Graficzny przepływ pracy programu PowerShell](#graphical-runbooks)|Graficzny element runbook oparty na przepływie pracy programu Windows PowerShell i utworzony i edytowany całkowicie w edytorze graficznym w witrynie Azure portal. |
| [PowerShell](#powershell-runbooks) |Podręcznik na podstawie skryptów programu Windows PowerShell. |
| [Przepływ pracy programu PowerShell](#powershell-workflow-runbooks)|Podręcznikowy program runbook oparty na skryptach przepływu pracy programu Windows PowerShell. |
| [Python](#python-runbooks) |Podręcznik na podstawie skryptów Pythona. |

Należy wziąć pod uwagę następujące kwestie przy określaniu, który typ ma być używany dla określonego elementów runbook.

* Nie można konwertować tekstów runbook z tekstu graficznego na tekst ani na odwrót.
* Istnieją ograniczenia podczas używania elementów runbook różnych typów jako elementów runbook podrzędnych. Aby uzyskać więcej informacji, zobacz [Podrzędne elementy runbook w usłudze Azure Automation](automation-child-runbooks.md).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Graficzne programy runbook

Można tworzyć i edytować graficzne i graficzne runbooki przepływu pracy programu PowerShell za pomocą edytora graficznego w witrynie Azure portal. Nie można jednak tworzyć ani edytować tego typu śmiętu za pomocą innego narzędzia. Główne cechy graficznych elementów runbook:

* Można wyeksportować do plików na koncie automatyzacji, a następnie zaimportować do innego konta automatyzacji. 
* Generowanie kodu programu PowerShell. 
* Można przekonwertować na lub z graficznych y śkolków przepływu pracy programu PowerShell podczas importowania. 

### <a name="advantages"></a>Zalety

* Użyj wizualnego modelu tworzenia konfiguracji wstawiania łączy.
* Skoncentruj się na przepływie danych przez proces.
* Wizualnie reprezentują procesy zarządzania.
* Uwzględnij inne programy runbook jako podrzędne umowy runbook, aby utworzyć przepływy pracy wysokiego poziomu.
* Zachęcaj do programowania modułowego.

### <a name="limitations"></a>Ograniczenia

* Nie można utworzyć ani edytować poza portalem Azure.
* Może wymagać działania kodu zawierającego kod programu PowerShell do wykonywania złożonej logiki.
* Nie można przekonwertować na jeden z [formatów tekstu](automation-runbook-types.md)ani przekonwertować podręcznika na format graficzny. 
* Nie można wyświetlić ani bezpośrednio edytować kodu programu PowerShell tworzonego przez graficzny przepływ pracy. Można wyświetlić kod utworzony w dowolnej działaniach kodu.
* Nie można uruchomić uruchomieniu na udręki uruchomieniu hybrydowego systemu Linux. Zobacz [Automatyzacja zasobów w centrum danych lub w chmurze przy użyciu hybrydowego procesu roboczego żyli .](automation-hybrid-runbook-worker.md)

## <a name="powershell-runbooks"></a>Runbooki programu PowerShell

Podręczniki programu PowerShell są oparte na programie Windows PowerShell. Bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal.  Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować program runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj całą złożoną logikę za pomocą kodu programu PowerShell bez dodatkowych złożoności przepływu pracy programu PowerShell.
* Uruchamianie szybciej niż programy PowerShell Workflow runbooks, ponieważ nie muszą być kompilowane przed uruchomieniem.
* Uruchom na platformie Azure i w hybrydowych środowiskach ceł runbook dla systemów Windows i Linux.

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty programu PowerShell.
* Nie można używać [przetwarzania równoległego](automation-powershell-workflow.md#parallel-processing) do wykonywania wielu akcji równolegle.
* W przypadku wystąpienia błędu nie można użyć [punktów kontrolnych](automation-powershell-workflow.md#checkpoints) do wznowienia śmiętu.
* Za pomocą polecenia cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) można dołączyć tylko runbooki przepływu pracy programu PowerShell i graficzne programy runbook, które tworzą nowe zadanie.

### <a name="known-issues"></a>Znane problemy

Poniżej przedstawiono bieżące znane problemy z podręcznikami runbook programu PowerShell:

* Nie można pobrać niezaszyfrowanego [zasobu zmiennej](automation-variables.md) o wartości null.
* Nie można pobrać składnika aktywów zmiennych w `*~*` nazwie.
* Operacja [get-process](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7) w pętli w elemiwie programu PowerShell może ulec awarii po około 80 iteracji.
* Runbook programu PowerShell może zakończyć się niepowodzeniem, jeśli próbuje zapisać dużą ilość danych do strumienia wyjściowego na raz. Zazwyczaj można obejść ten problem, mając dane wyjściowe runbook tylko informacje potrzebne do pracy z dużymi obiektami. Na przykład zamiast używać `Get-Process` bez ograniczeń, można mieć wyjście cmdlet tylko wymagane `Get-Process | Select ProcessName, CPU`parametry, jak w .

## <a name="powershell-workflow-runbooks"></a>Runy przepływu pracy programu PowerShell

Runy przepływu pracy programu PowerShell są podręcznikami runbook opartymi na przepływie pracy programu [Windows PowerShell](automation-powershell-workflow.md). Bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal. Można również użyć dowolnego edytora tekstu w trybie offline i [zaimportować program runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Zaimplementuj całą złożoną logikę za pomocą kodu przepływu pracy programu PowerShell.
* Użyj [punktów kontrolnych,](automation-powershell-workflow.md#checkpoints) aby wznowić działanie, jeśli występuje błąd.
* Użyj [przetwarzania równoległego,](automation-powershell-workflow.md#parallel-processing) aby wykonać wiele akcji równolegle.
* Może zawierać inne graficzne programy runbook i runbook przepływu pracy programu PowerShell jako umownicza podrzędne w celu tworzenia przepływów pracy wysokiego poziomu.

### <a name="limitations"></a>Ograniczenia

* Musisz być zaznajomiony z przepływem pracy programu PowerShell.
* Programy runbook muszą radzić sobie z dodatkową złożonością przepływu pracy programu PowerShell, taką jak [obiekty deserializowane.](automation-powershell-workflow.md#code-changes)
* Uruchamianie życiórów trwa dłużej niż runbook programu PowerShell, ponieważ muszą zostać skompilowane przed uruchomieniem.
* Kreślicie programu PowerShell można dołączać tylko `Start-AzAutomationRunbook` jako podręczniki podrzędne przy użyciu polecenia cmdlet.
* Nie można uruchomić uruchomieniu w systemie Linux hybrydowy proces roboczy.

## <a name="python-runbooks"></a>Runbooki pythona

Python runbooks kompilować w Pythonie 2. Można bezpośrednio edytować kod życiówki za pomocą edytora tekstu w witrynie Azure portal. Można również użyć edytora tekstu w trybie offline i [zaimportować element runbook](manage-runbooks.md) do usługi Azure Automation.

### <a name="advantages"></a>Zalety

* Użyj niezawodnych bibliotek Języka Python.
* Można uruchomić na platformie Azure lub na systemach Linux hybrydowych cebook workers. Hybrydowe środowiska owe pracowników systemu Windows są obsługiwane z [zainstalowanym systemem Python2.7.](https://www.python.org/downloads/release/latest/python2)

### <a name="limitations"></a>Ograniczenia

* Musisz znać skrypty Pythona.
* Obecnie obsługiwany jest tylko Python 2. Wszystkie funkcje specyficzne dla języka Python 3 nie powiodą się.
* Aby korzystać z bibliotek innych firm, należy [zaimportować pakiety](python-packages.md) do konta automatyzacji.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia graficznych śmióbek runbook, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).
* Aby zrozumieć różnice między przepływami pracy programu PowerShell i PowerShell dla życiorysów, zobacz [Uczenie przepływu pracy programu Windows PowerShell](automation-powershell-workflow.md).
* Aby uzyskać więcej informacji na temat tworzenia lub importowania uruchomieniu [księgi, zobacz Zarządzanie podręcznikami runbook w usłudze Azure Automation](manage-runbooks.md).
* Aby dowiedzieć się więcej o programie PowerShell, w tym modułach referencyjnych i modułów szkoleniowych, zapoznaj się z [dokumentacją programu PowerShell.](https://docs.microsoft.com/powershell/scripting/overview)
