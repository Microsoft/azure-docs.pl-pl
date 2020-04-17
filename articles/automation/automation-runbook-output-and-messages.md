---
title: Dane wyjściowe i komunikaty w usłudze Azure Automation
description: W tym artykule opisano sposób tworzenia i pobierania komunikatów wyjściowych i komunikatów o błędach z uruchomieniu witryn w usłudze Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535540"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Dane wyjściowe i komunikaty w usłudze Azure Automation

Większość śliczna usługi Azure Automation ma jakąś formę danych wyjściowych. To dane wyjściowe mogą być komunikatem o błędzie dla użytkownika lub złożonym obiektem przeznaczonym do użycia z innym elementem runbook. Program Windows PowerShell udostępnia wiele strumieni do wysyłania danych [wyjściowych](/powershell/module/microsoft.powershell.core/about/about_redirection) ze skryptu lub przepływu pracy. Usługa Azure Automation działa z każdym z tych strumieni inaczej. Należy postępować zgodnie z najlepszymi praktykami dotyczące korzystania ze strumieni podczas tworzenia ego księgi runbook.

W poniższej tabeli pokrótce opisano każdy strumień z jego zachowaniem w witrynie Azure portal dla opublikowanych elementów runbook i podczas [testowania elementów runbook.](automation-testing-runbook.md) Strumień wyjściowy jest głównym strumieniem używanym do komunikacji między systemami runbook. Inne strumienie są klasyfikowane jako strumienie komunikatów, przeznaczone do przekazywania informacji do użytkownika. 

| Strumień | Opis | Opublikowany | Testowanie |
|:--- |:--- |:--- |:--- |
| Błąd |Komunikat o błędzie przeznaczony dla użytkownika. W przeciwieństwie do wyjątku element runbook jest kontynuowany po domyślnym komunikacie o błędzie. |Zapisy do historii zadań |Wyświetlane w okienku danych wyjściowych testu |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie powinny być używane w żyłaczach. |Nie jest napisane w historii zadań |Nie są wyświetlane w okienku danych wyjściowych testu |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy Runbook. |Zapisy do historii zadań |Wyświetlane w okienku danych wyjściowych testu |
| Postęp |Rekordy generowane automatycznie przed każdym działaniem w elemencie Runbook i po nim. Element runbook nie powinien próbować tworzyć własne rekordy postępu, ponieważ są one przeznaczone dla użytkownika interaktywnego. |Zapisywane w historii zadań tylko wtedy, gdy rejestrowanie postępu jest włączone dla systemu runbook |Nie są wyświetlane w okienku danych wyjściowych testu |
| Pełny |Komunikaty, które dają ogólne lub debugowanie informacji. |Zapisywane w historii zadań tylko wtedy, gdy pełne rejestrowanie jest włączone dla ego księgi runbook |Wyświetlane w okienku Testuj `VerbosePreference` dane wyjściowe tylko wtedy, gdy zmienna jest ustawiona na Kontynuuj w zestawieniu |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisy do historii zadań |Wyświetlane w okienku danych wyjściowych testu |

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="output-stream"></a>Strumień wyjściowy

Strumień wyjściowy jest używany do danych wyjściowych obiektów utworzonych przez skrypt lub przepływ pracy, gdy działa poprawnie. Usługa Azure Automation używa głównie tego strumienia dla obiektów, które mają być używane przez nadrzędne elementy runbook, które wywołują [bieżącą element runbook](automation-child-runbooks.md). Gdy rodzic [wywołuje element runbook wbudowany,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)element podrzędny zwraca dane ze strumienia output do nadrzędnego. 

System runbook używa strumienia danych wyjściowych do przekazywania ogólnych informacji do klienta tylko wtedy, gdy nigdy nie jest wywoływana przez inny system runbook. Najlepszym rozwiązaniem jest jednak to, że podręczniki runbook powinny zazwyczaj używać [strumienia pełnej](#verbose-stream) do przekazywania użytkownikowi ogólnych informacji.

Niech twój program runbook zapisuje dane do strumienia wyjściowego przy użyciu [zapisu-output](https://technet.microsoft.com/library/hh849921.aspx). Alternatywnie można umieścić obiekt na własnym wierszu w skrypcie.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Obsługa wyjścia z funkcji

Gdy funkcja elementu runbook zapisuje do strumienia output, dane wyjściowe są przekazywane z powrotem do elementu runbook. Jeśli runbook przypisuje dane wyjściowe do zmiennej, dane wyjściowe nie są zapisywane w strumieniu danych wyjściowych. Zapisywanie do innych strumieni z wewnątrz funkcji zapisuje do odpowiedniego strumienia dla elementu runbook. Należy wziąć pod uwagę następujące przykładowe uruchomienie przepływu pracy programu PowerShell.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Strumień wyjściowy dla zadania runbook jest:

```output
Output inside of function
Output outside of function
```

Strumień pełny dla zadania runbook jest:

```output
Verbose outside of function
Verbose inside of function
```

Po opublikowaniu obiektu runbook i przed jego uruchomieniem należy również włączyć pełne rejestrowanie w ustawieniach runbooka, aby uzyskać pełne dane wyjściowe strumienia.

### <a name="declaring-output-data-type"></a>Deklarowanie typu danych wyjściowych

Poniżej przedstawiono przykłady typów danych wyjściowych:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarowanie typu danych wyjściowych w przepływie pracy

Przepływ pracy określa typ danych jego danych wyjściowych przy użyciu [atrybutu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Ten atrybut nie ma wpływu w czasie wykonywania, ale zapewnia wskazanie w czasie projektowania oczekiwanych danych wyjściowych wiązki uruchomieniu. W miarę jak zestaw narzędzi dla elementów runbook nadal ewoluuje, zwiększa się znaczenie deklarowania typów danych wyjściowych w czasie projektowania. W związku z tym jest najlepszym rozwiązaniem, aby uwzględnić tę deklarację w wszystkich licznych, które tworzysz.

Następujący przykładowy element Runbook generuje obiekt ciągu i zawiera deklarację typu jego danych wyjściowych. Jeśli element Runbook generuje tablicę pewnego typu, należy także określić typ, a nie tablicę typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarowanie typu danych wyjściowych w graficznym uruchomieniu

Aby zadeklarować typ danych wyjściowych w graficznym lub graficznym elemście roboczym przepływu programu PowerShell, można wybrać opcję menu **Wejście i wyjście** oraz wprowadzić typ danych wyjściowych. Zaleca się użycie pełnej nazwy klasy .NET, aby typ był łatwy do zidentyfikowania, gdy nadrzędny element runbook odwołuje się do niego. Przy użyciu pełnej nazwy udostępnia wszystkie właściwości klasy do magistrali danych w uruchomieniu i zwiększa elastyczność, gdy właściwości są używane do logiki warunkowej, rejestrowania i odwoływania się jako wartości dla innych działań elementu runbook.<br> ![Opcja Wprowadzanie i wyjście eksmisja runbooka](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po wprowadzeniu wartości w polu **Typ wyjściowy** w okienku Właściwości Wejścia i Dane wyjściowe należy kliknąć poza formantem, aby rozpoznał wpis.

W poniższym przykładzie przedstawiono dwa graficzne elementy runbook, aby zademonstrować funkcję Wejście i Dane wyjściowe. Stosując modułowy model projektu elementów runbook, masz jeden element runbook jako szablon uwierzytelniania uwierzytelniania uwierzytelniania za pomocą platformy Azure przy użyciu konta Uruchom jako. Drugi element runbook, który zwykle wykonuje podstawową logikę do automatyzacji danego scenariusza, w tym przypadku wykonuje szablon uwierzytelniania runbook. Wyświetla wyniki do okienka danych wyjściowych testu. W normalnych warunkach należy ten projekt runbook zrobić coś przeciwko zasobu wykorzystując dane wyjściowe z egoście podrzędnego.

Oto podstawowa logika egoisty **AuthenticateTo-Azure.**<br> ![Przykład szablonu uwierzytelniania uwierzytelnienia](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Elementu runbook zawiera `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`typ danych wyjściowych, który zwraca właściwości profilu uwierzytelniania.<br> ![Przykład typu wyjściowego eksuszka](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Chociaż ten element runbook jest prosty, istnieje jeden element konfiguracji, aby wywołać tutaj. Ostatnie działanie wykonuje `Write-Output` polecenie cmdlet do zapisu danych profilu do zmiennej przy użyciu wyrażenia programu PowerShell dla parametru. `Inputobject` Ten parametr jest `Write-Output`wymagany dla .

Drugi projekt runbook w tym przykładzie, o nazwie **Test-ChildOutputType**, po prostu definiuje dwa działania.<br> ![Przykładowy podrzędny typ wydruku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Pierwsze działanie wywołuje **wiązek Uwierzytelnienia do platformy Azure.** Drugie działanie uruchamia `Write-Verbose` polecenie cmdlet ze **źródłem danych** ustawionym na **Dane wyjściowe działania**. Ponadto **ścieżka pola** jest ustawiona na **Context.Subscription.SubscriptionName**, dane wyjściowe kontekstu z elementów runbook **AuthenticateTo-Azure.**<br> ![Źródło danych parametru polecenia cmdlet zapisu](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Wynikowe dane wyjściowe to nazwa subskrypcji.<br> ![Wyniki elementu runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Strumienie wiadomości

W przeciwieństwie do strumienia wyjściowego strumienie komunikatów przekazują informacje do użytkownika. Istnieje wiele strumieni komunikatów dla różnych rodzajów informacji, a usługa Azure Automation obsługuje każdy strumień inaczej.

### <a name="warning-and-error-streams"></a>Strumienie ostrzeżeń i błędów

Ostrzeżenie i błąd strumienie problemów dziennika, które występują w uruchomieniu. Amili. Usługa Azure Automation zapisuje te strumienie do historii zadań podczas wykonywania ego księgi runbook. Automatyzacja zawiera strumienie w okienku danych wyjściowych testu w witrynie Azure portal, gdy jest testowany podręcznik. 

Domyślnie element runbook jest nadal wykonywany po ostrzeżeniu lub błędzie. Można określić, że projekt runbook powinien zawiesić ostrzeżenie lub błąd, ustawiając [zmienną preferencji](#preference-variables) przed utworzeniem wiadomości. Na przykład, aby spowodować, że element runbook zawiesić na `ErrorActionPreference` błąd, jak to ma w przypadku wyjątku, należy ustawić zmienną zatrzymać.

Utwórz ostrzeżenie lub komunikat o błędzie przy użyciu polecenia cmdlet [Ostrzeżenie o zapisie](https://technet.microsoft.com/library/hh849931.aspx) lub [Błąd zapisu.](https://technet.microsoft.com/library/hh849962.aspx) Działania można również zapisywać w strumieniach ostrzeżenie i błąd.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Strumień debugowania

Usługa Azure Automation używa strumienia komunikatów debugowania dla użytkowników interaktywnych. Nie należy go używać w śmiuciach śmiętych.

### <a name="verbose-stream"></a>Strumień pełny

Strumień komunikatów verbose obsługuje ogólne informacje o operacji uruchomieniu. 1079. Ponieważ strumień debugowania nie jest dostępny dla uruchomieniu, system runbook należy użyć pełnych komunikatów dla informacji debugowania. 

Domyślnie historia zadań nie przechowuje pełnych wiadomości z opublikowanych liksów runbook, ze względu na wydajność. Aby przechowywać pełne wiadomości, użyj karty **Konfigurowanie** portalu Azure z ustawieniem **Rejestrowanie pełnych rekordów,** aby skonfigurować opublikowane programy runbook do rejestrowania pełnych wiadomości. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania. W większości przypadków należy zachować domyślne ustawienie nie rejestrowania pełnych rekordów.

Podczas [testowania śmigłości](automation-testing-runbook.md)pełne komunikaty nie są wyświetlane, nawet jeśli projekt runbook jest skonfigurowany do rejestrowania rekordów pełnych. Aby wyświetlić pełne komunikaty podczas [testowania śmigieł,](automation-testing-runbook.md)należy ustawić zmienną `VerbosePreference` na Kontynuuj. Z tego zestawu zmiennych pełne komunikaty są wyświetlane w okienku danych wyjściowych testu witryny Azure portal.

Poniższy kod tworzy pełny komunikat przy użyciu polecenia cmdlet [Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Rekordy postępu

Za pomocą karty **Konfiguruj** w witrynie Azure Portal można skonfigurować program runbook do rejestrowania rekordów postępu. Domyślnym ustawieniem jest nie rejestrowanie rekordów, aby zmaksymalizować wydajność. W większości przypadków należy zachować ustawienie domyślne. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania. 

Jeśli włączysz rejestrowanie rekordów postępu, element runbook zapisuje rekord do historii zadań przed i po uruchomieniu każdego działania. Testowanie systemu runbook nie wyświetla komunikatów postępu, nawet jeśli projekt runbook jest skonfigurowany do rejestrowania rekordów postępu.

>[!NOTE]
>Polecenie cmdlet [write-progress](https://technet.microsoft.com/library/hh849902.aspx) nie jest prawidłowe w życiorniku, ponieważ to polecenie cmdlet jest przeznaczone do użytku z użytkownikiem interaktywnym.

## <a name="preference-variables"></a>Zmienne preferencji

Można ustawić niektóre [zmienne preferencji](https://technet.microsoft.com/library/hh847796.aspx) programu Windows PowerShell w yciu, aby kontrolować odpowiedź na dane wysyłane do różnych strumieni wyjściowych. W poniższej tabeli wymieniono zmienne preferencji, które mogą być używane w elementach runbook, z ich wartościami domyślnymi i prawidłowymi. Dodatkowe wartości są dostępne dla zmiennych preferencji, gdy są używane w programie Windows PowerShell poza usługą Azure Automation.

| Zmienna | Wartość domyślna | Prawidłowe wartości |
|:--- |:--- |:--- |
| `WarningPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `ErrorActionPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

Następna tabela zawiera listę zachowań dla wartości zmiennych preferencji, które są prawidłowe w likwidach.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu Runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu Runbook bez rejestrowania komunikatu. Ta wartość powoduje ignorowanie wiadomości. |
| Stop |Rejestruje komunikat i zawiesza element Runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Pobieranie danych wyjściowych i komunikatów ekscesów

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Pobieranie danych wyjściowych i wiadomości w witrynie Azure portal

Szczegóły zadania uruchomieniu można w witrynie Azure portal przy użyciu karty **Zadania** dla żyłgodnika. Podsumowanie zadania wyświetla parametry wejściowe i [strumień wyjściowy,](#output-stream)oprócz ogólnych informacji o zadaniu i wszelkich wyjątkach, które wystąpiły. Historia zadań zawiera komunikaty ze strumienia wyjściowego oraz [strumienie ostrzeżeń i błędów](#warning-and-error-streams). Zawiera również komunikaty ze [strumienia verbose](#verbose-stream) i [rekordów postępu,](#progress-records) jeśli runbook jest skonfigurowany do rejestrowania rekordów pełnych i postępu.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Pobieranie danych wyjściowych i komunikatów w programie Windows PowerShell

W programie Windows PowerShell można pobierać dane wyjściowe i wiadomości z zestawu runbook przy użyciu polecenia cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) To polecenie cmdlet wymaga identyfikatora zadania i `Stream` ma parametr o nazwie, w którym można określić strumień do pobrania. Można określić wartość Any dla tego parametru, aby pobrać wszystkie strumienie dla zadania.

W poniższym przykładzie jest uruchamiany przykładowy element Runbook, a następnie kod czeka na jego zakończenie. Po zakończeniu wykonywania runbook skrypt zbiera strumień wyjściowy runbook z zadania.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Pobieranie danych wyjściowych i wiadomości w elementach runbook graficznych

W przypadku eks-ów graficznych dodatkowe rejestrowanie danych wyjściowych i wiadomości jest dostępne w formie śledzenia na poziomie aktywności. Istnieją dwa poziomy śledzenia: podstawowe i szczegółowe. Podstawowe śledzenie wyświetla czas rozpoczęcia i zakończenia dla każdego działania w yjnie, a także informacje związane z próbami ponawiania działań. Niektóre przykłady to liczba prób i godzina rozpoczęcia działania. Szczegółowe śledzenie obejmuje podstawowe funkcje śledzenia oraz rejestrowanie danych wejściowych i wyjściowych dla każdego działania. 

Obecnie śledzenie na poziomie działania zapisuje rekordy przy użyciu strumienia szczegółowego. W związku z tym należy włączyć pełne rejestrowanie po włączeniu śledzenia. W przypadku wiązków graficznych z włączonym śledzeniem nie ma potrzeby rejestrowania rekordów postępu. Podstawowe śledzenie służy temu samejej celowi i jest bardziej pouczające.

![Graficzny widok strumieni zadań tworzenia zadań](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Na obrazie widać, że włączenie szczegółowego rejestrowania i śledzenia dla wiązek graficznych udostępnia znacznie więcej informacji w widoku **strumieni zadań** produkcyjnych. Te dodatkowe informacje mogą być niezbędne do rozwiązywania problemów produkcyjnych z elementem runbook. 

Jednak jeśli nie są wymagane te informacje do śledzenia postępu wiązki uruchomieniu do rozwiązywania problemów, można zachować śledzenie wyłączone jako ogólną praktykę. Rekordy śledzenia mogą być szczególnie liczne. Dzięki graficznemu śledzeniu śmigłążka można uzyskać od dwóch do czterech rekordów na działanie, w zależności od konfiguracji śledzenia podstawowego lub szczegółowego.

**Aby włączyć śledzenie na poziomie działania:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmięty.
3. Na stronie Runbooks wybierz graficzny element runbook z listy ekscesów.
4. W obszarze **Ustawienia**kliknij pozycję **Rejestrowanie i śledzenie**.
5. Na stronie Rejestrowanie i śledzenie w obszarze **Logowanie pełne rekordy**kliknij przycisk **Włącz,** aby włączyć pełne rejestrowanie.
6. W obszarze **Śledzenie na poziomie aktywności**zmień poziom śledzenia na **Podstawowy** lub **Szczegółowy**, na podstawie wymaganego poziomu śledzenia.<br>

   ![Graficzna strona rejestrowania i śledzenia tworzenia autorstwa](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Pobieranie danych wyjściowych i wiadomości w dziennikach monitora platformy Microsoft Azure

Usługa Azure Automation może wysyłać stan zadania i strumienie zadań do obszaru roboczego usługi Log Analytics. Usługa Azure Monitor obsługuje dzienniki, które umożliwiają:

* Uzyskaj wgląd w swoje zadania automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania egoisty, na przykład Niepowodzenie lub Zawieszenie.
* Pisanie zaawansowanych zapytań w strumieniach zadań.
* Skorelowanie zadań między kontami automatyzacji.
* Wizualizuj historię zadań.

Aby uzyskać więcej informacji na temat konfigurowania integracji z dziennikami usługi Azure Monitor do zbierania, korelowania i działania na podstawie danych zadań, zobacz [Przekazywanie stanu zadania i strumieni zadań z automatyzacji do dzienników usługi Azure Monitor.](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat wykonywania uruchomieniu, monitorowania zadań umnienia uruchomieniu i innych szczegółów technicznych, zobacz [Śledzenie zadania umownego](automation-runbook-execution.md).
* Aby dowiedzieć się, jak projektować i używać śmięty podrzędnych, zobacz [Podrzędne elementy runbook w usłudze Azure Automation.](automation-child-runbooks.md)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów referencyjnych i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](/powershell/scripting/overview).
