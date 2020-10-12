---
title: Monitoruj dane wyjściowe elementu Runbook w Azure Automation
description: W tym artykule opisano sposób monitorowania danych wyjściowych i komunikatów elementów Runbook.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: e4be7934002730253b77b1c129165ad9f19f23b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185980"
---
# <a name="monitor-runbook-output"></a>Monitorowanie wyniku uruchomienia elementu runbook

Większość elementów Runbook Azure Automation ma pewną formę danych wyjściowych. Ten wynik może być komunikatem o błędzie dla użytkownika lub obiektu złożonego, który ma być używany z innym elementem Runbook. Program Windows PowerShell udostępnia [wiele strumieni](/powershell/module/microsoft.powershell.core/about/about_redirection) do wysyłania danych wyjściowych ze skryptu lub przepływu pracy. Azure Automation działa z każdym z tych strumieni inaczej. Podczas tworzenia elementu Runbook należy stosować najlepsze rozwiązania w zakresie używania strumieni.

W poniższej tabeli krótko opisano każdy strumień z zachowaniem w Azure Portal publikowanych elementów Runbook i podczas [testowania elementu Runbook](./manage-runbooks.md). Strumień wyjściowy jest głównym strumieniem używanym do komunikacji między elementami Runbook. Inne strumienie są klasyfikowane jako strumienie komunikatów, przeznaczone do przekazywania informacji do użytkownika. 

| Strumień | Opis | Opublikowany | Testowanie |
|:--- |:--- |:--- |:--- |
| Błąd |Komunikat o błędzie przeznaczony dla użytkownika. W przeciwieństwie do wyjątku, element Runbook jest domyślnie kontynuowany po komunikacie o błędzie. |Zapisano w historii zadań |Wyświetlane w okienku danych wyjściowych testu |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie należy używać w elementach Runbook. |Nie zapisano w historii zadań |Niewyświetlane w okienku danych wyjściowych testu |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy Runbook. |Zapisano w historii zadań |Wyświetlane w okienku danych wyjściowych testu |
| Postęp |Rekordy generowane automatycznie przed każdym działaniem w elemencie Runbook i po nim. Element Runbook nie powinien próbować tworzyć własnych rekordów postępu, ponieważ są one przeznaczone dla użytkownika interaktywnego. |Zapisywane w historii zadań tylko wtedy, gdy jest włączone rejestrowanie postępu dla elementu Runbook |Niewyświetlane w okienku danych wyjściowych testu |
| Pełny |Komunikaty, które zawierają ogólne lub debugowane informacje. |Zapisywane w historii zadań tylko wtedy, gdy dla elementu Runbook jest włączone pełne rejestrowanie |Wyświetlane w okienku danych wyjściowych testu tylko wtedy `VerbosePreference` , gdy zmienna jest ustawiona na Kontynuuj w elemencie Runbook |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisano w historii zadań |Wyświetlane w okienku danych wyjściowych testu |

## <a name="use-the-output-stream"></a>Korzystanie z strumienia wyjściowego

Strumień wyjściowy jest używany do wyprowadzania obiektów utworzonych przez skrypt lub przepływ pracy, gdy działa poprawnie. Azure Automation używa głównie tego strumienia dla obiektów, które mają być używane przez nadrzędne elementy Runbook, które wywołują [bieżący element Runbook](automation-child-runbooks.md). Gdy element nadrzędny [wywołuje element Runbook w tekście](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), element podrzędny zwraca dane z strumienia wyjściowego do elementu nadrzędnego. 

Element Runbook używa strumienia wyjściowego do przekazywania ogólnych informacji do klienta tylko wtedy, gdy nie jest on wywoływany przez inny element Runbook. Najlepszym rozwiązaniem jest jednak, aby elementy Runbook zwykle używały [pełnego strumienia](#monitor-verbose-stream) do przekazywania użytkownikowi ogólnych informacji.

Czy element Runbook zapisuje dane do strumienia wyjściowego przy użyciu polecenia [Write-Output](/powershell/module/microsoft.powershell.utility/write-output). Alternatywnie można umieścić obiekt w osobnym wierszu skryptu.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Obsługa danych wyjściowych z funkcji

Gdy funkcja Runbook zapisuje w strumieniu wyjściowym, dane wyjściowe są przekazywane z powrotem do elementu Runbook. Jeśli element Runbook przypisze te dane wyjściowe do zmiennej, dane wyjściowe nie są zapisywane w strumieniu wyjściowym. Zapisywanie do innych strumieni z wewnątrz funkcji zapisuje do odpowiedniego strumienia dla elementu Runbook. Rozważmy następujący przykładowy element Runbook przepływu pracy programu PowerShell.

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

Strumień wyjściowy dla zadania elementu Runbook to:

```output
Output inside of function
Output outside of function
```

Pełny strumień zadania elementu Runbook to:

```output
Verbose outside of function
Verbose inside of function
```

Po opublikowaniu elementu Runbook i przed jego rozpoczęciem należy również włączyć pełne rejestrowanie w ustawieniach elementu Runbook, aby uzyskać pełne dane wyjściowe strumienia.

### <a name="declare-output-data-type"></a>Zadeklaruj typ danych wyjściowych

Poniżej przedstawiono przykłady typów danych wyjściowych:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarowanie typu danych wyjściowych w przepływie pracy

Przepływ pracy określa typ danych wyjściowych przy użyciu [atrybutu OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Ten atrybut nie ma wpływu na środowisko uruchomieniowe, ale udostępnia wskazanie w czasie projektowania oczekiwanych danych wyjściowych elementu Runbook. Ponieważ zestaw narzędzi dla elementów Runbook jest w dalszym ciągu rozwijany, znaczenie deklarowania typów danych wyjściowych w czasie projektowania rośnie. W związku z tym najlepszym rozwiązaniem jest dołączenie tej deklaracji do utworzonych elementów Runbook.

Następujący przykładowy element Runbook generuje obiekt ciągu i zawiera deklarację typu jego danych wyjściowych. Jeśli element Runbook generuje tablicę pewnego typu, należy także określić typ, a nie tablicę typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Zadeklaruj typ danych wyjściowych w graficznym elemencie Runbook

Aby zadeklarować typ danych wyjściowych w graficznym lub graficznym elemencie Runbook przepływu pracy programu PowerShell, można wybrać opcję menu **dane wejściowe i wyjściowe** i wprowadzić typ danych wyjściowych. Zaleca się użycie pełnej nazwy klasy .NET, aby ułatwić jej identyfikację, gdy nadrzędny element Runbook odwołuje się do niego. Użycie pełnej nazwy ujawnia wszystkie właściwości klasy magistrali danych w elemencie Runbook i zwiększa elastyczność, gdy właściwości są używane do logiki warunkowej, rejestrowania i odwoływania się jako wartości dla innych działań elementu Runbook.<br> ![Opcja danych wejściowych i wyjściowych elementu Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po wprowadzeniu wartości w polu **Typ danych wyjściowych** w okienku właściwości danych wejściowych i wyjściowych, pamiętaj, aby kliknąć poza formantem, aby rozpoznaje swój wpis.

W poniższym przykładzie przedstawiono dwa graficzne elementy Runbook, które demonstrują funkcję wejścia i wyjścia. Zastosowanie modelowego modelu projektowego elementu Runbook ma jeden element Runbook jako uwierzytelnianie szablonu elementu Runbook Zarządzanie uwierzytelnianiem za pomocą platformy Azure przy użyciu konta Uruchom jako. Drugi element Runbook, który zwykle wykonuje logikę podstawową w celu zautomatyzowania danego scenariusza, w tym przypadku wykonuje szablon uwierzytelnianie elementu Runbook. Wyświetla wyniki w okienku danych wyjściowych testu. W normalnych warunkach ten element Runbook będzie miał zawartość dla zasobu wykorzystującego dane wyjściowe z podrzędnego elementu Runbook.

Oto podstawowa logika elementu Runbook **AuthenticateTo-Azure** .<br> ![Przykład uwierzytelniania szablonu elementu Runbook ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) .

Element Runbook zawiera typ danych wyjściowych `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , który zwraca właściwości profilu uwierzytelniania.<br> ![Przykład typu danych wyjściowych elementu Runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Chociaż ten element Runbook jest prosty, istnieje jeden element konfiguracji do wywołania w tym miejscu. Ostatnie działanie wykonuje `Write-Output` polecenie cmdlet w celu zapisania danych profilu w zmiennej przy użyciu wyrażenia programu PowerShell dla `Inputobject` parametru. Ten parametr jest wymagany w przypadku programu `Write-Output` .

Drugi element Runbook w tym przykładzie o nazwie **test-ChildOutputType**, po prostu definiuje dwie działania.<br> ![Przykład elementu Runbook podrzędnego typu wyjściowego](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Pierwsze działanie wywołuje element Runbook **AuthenticateTo-Azure** . Drugie działanie uruchamia `Write-Verbose` polecenie cmdlet ze **źródłem danych** ustawionym na **dane wyjściowe działania**. Ponadto **ścieżka pola** jest ustawiona na **Context. Subscription. subscriptionname**, dane wyjściowe kontekstu z elementu Runbook **AuthenticateTo-Azure** .<br> ![Zapisz — pełne źródło danych parametru polecenia cmdlet](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Wynikowe dane wyjściowe to nazwa subskrypcji.<br> ![Wyniki Test-ChildOutputType elementu Runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="monitor-message-streams"></a>Monitoruj strumienie komunikatów

W przeciwieństwie do strumienia wyjściowego strumienie komunikatów komunikują się z informacjami do użytkownika. Istnieje wiele strumieni komunikatów dla różnych rodzajów informacji, a Azure Automation obsługuje każdy strumień w różny sposób.

### <a name="monitor-warning-and-error-streams"></a>Monitorowanie strumieni ostrzeżeń i błędów

Strumień ostrzeżeń i błędów rejestruje problemy występujące w elemencie Runbook. Azure Automation zapisuje te strumienie do historii zadań podczas wykonywania elementu Runbook. Automatyzacja obejmuje strumienie w okienku danych wyjściowych testu w Azure Portal podczas testowania elementu Runbook. 

Domyślnie element Runbook jest nadal wykonywany po wystąpieniu ostrzeżenia lub błędu. Możesz określić, że element Runbook ma zostać zawieszony na ostrzeżenie lub błąd, ponieważ element Runbook ustawi [zmienną preferencji](#work-with-preference-variables) przed utworzeniem komunikatu. Na przykład, aby spowodować zawieszenie elementu Runbook w przypadku błędu, jak w przypadku wyjątku, należy ustawić `ErrorActionPreference` zmienną do zatrzymania.

Utwórz ostrzeżenie lub komunikat o błędzie za pomocą polecenia cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) lub [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) . Działania mogą także zapisywać strumienie ostrzegawcze i błędy.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="monitor-debug-stream"></a>Monitoruj strumień debugowania

Azure Automation używa strumienia komunikatów debugowania dla użytkowników interaktywnych. Nie należy jej używać w elementach Runbook.

### <a name="monitor-verbose-stream"></a>Monitoruj pełny strumień

Pełny strumień komunikatów obsługuje ogólne informacje o operacjach elementu Runbook. Ponieważ strumień debugowania nie jest dostępny dla elementu Runbook, element Runbook powinien używać pełnych komunikatów do debugowania informacji. 

Domyślnie historia zadania nie przechowuje pełnych komunikatów z opublikowanych elementów Runbook, ze względu na wydajność. Aby przechowywać pełne komunikaty, użyj ustawienia karta Azure Portal **Konfiguracja** z ustawieniem **pełne zapisy w dzienniku** , aby skonfigurować opublikowane elementy Runbook do rejestrowania pełnych komunikatów. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania. W większości przypadków należy zachować domyślne ustawienie nie rejestrowania pełnych rekordów.

Podczas [testowania elementu Runbook](./manage-runbooks.md)komunikaty pełne nie są wyświetlane, nawet jeśli element Runbook jest skonfigurowany do rejestrowania pełnych rekordów. Aby wyświetlić pełne komunikaty podczas [testowania elementu Runbook](./manage-runbooks.md), należy ustawić `VerbosePreference` zmienną do kontynuowania. W przypadku tego zestawu zmiennych w okienku danych wyjściowych testu w Azure Portal są wyświetlane pełne komunikaty.

Poniższy kod tworzy pełny komunikat przy użyciu polecenia cmdlet [Write-verbose](/powershell/module/microsoft.powershell.utility/write-verbose) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Obsługa rekordów postępu

Karta **Konfiguracja** w Azure Portal służy do konfigurowania elementu Runbook do rejestrowania rekordów postępu. Ustawieniem domyślnym jest rejestrowanie rekordów w celu zmaksymalizowania wydajności. W większości przypadków należy zachować ustawienie domyślne. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania. 

W przypadku włączenia rejestrowania rekordów postępu element Runbook zapisuje rekord w historii zadań przed i po każdym uruchomieniu działania. Testowanie elementu Runbook nie powoduje wyświetlenia komunikatów o postępie, nawet jeśli element Runbook jest skonfigurowany do rejestrowania rekordów postępu.

>[!NOTE]
>Polecenie cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) nie jest prawidłowe w elemencie Runbook, ponieważ to polecenie cmdlet jest przeznaczone do użycia z użytkownikiem interakcyjnym.

## <a name="work-with-preference-variables"></a>Pracuj z zmiennymi preferencji

Niektóre [zmienne preferencji](/powershell/module/microsoft.powershell.core/about/about_preference_variables) środowiska Windows PowerShell można ustawić w elementach Runbook, aby kontrolować odpowiedzi na dane wysyłane do różnych strumieni wyjściowych. Poniższa tabela zawiera listę zmiennych preferencji, których można używać w elementach Runbook, z wartościami domyślnymi i prawidłowymi. Dodatkowe wartości są dostępne dla zmiennych preferencji, gdy są używane w programie Windows PowerShell poza Azure Automation.

| Zmienna | Wartość domyślna | Prawidłowe wartości |
|:--- |:--- |:--- |
| `WarningPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `ErrorActionPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

W następnej tabeli przedstawiono zachowanie wartości zmiennych preferencji, które są prawidłowe w elementach Runbook.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu Runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu Runbook bez rejestrowania komunikatu. Ta wartość ma wpływ na ignorowanie komunikatu. |
| Stop |Rejestruje komunikat i zawiesza element Runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Pobieranie danych wyjściowych i komunikatów elementu Runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Pobieranie danych wyjściowych elementu Runbook i komunikatów w Azure Portal

Szczegóły zadania elementu Runbook można wyświetlić w Azure Portal przy użyciu karty **zadania** dla elementu Runbook. W podsumowaniu zadania są wyświetlane parametry wejściowe i [strumień danych wyjściowych](#use-the-output-stream), a także ogólne informacje o zadaniu i wyjątkach, które wystąpiły. Historia zadania obejmuje komunikaty ze strumienia wyjściowego oraz [strumienie ostrzeżeń i błędów](#monitor-warning-and-error-streams). Zawiera również komunikaty z [pełnych rekordów strumieni](#monitor-verbose-stream) i [postępu](#handle-progress-records) , jeśli element Runbook jest skonfigurowany pod kątem rejestrowania rekordów pełnych i informacji o postępie.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Pobieranie danych wyjściowych i komunikatów elementu Runbook w programie Windows PowerShell

W programie Windows PowerShell można pobrać dane wyjściowe i komunikaty z elementu Runbook za pomocą polecenia cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) . To polecenie cmdlet wymaga identyfikatora zadania i ma parametr o nazwie, `Stream` który umożliwia określenie strumienia do pobrania. Możesz określić wartość dla tego parametru, aby pobrać wszystkie strumienie dla zadania.

W poniższym przykładzie jest uruchamiany przykładowy element Runbook, a następnie kod czeka na jego zakończenie. Po zakończeniu wykonywania elementu Runbook skrypt zbiera strumień danych wyjściowych elementu Runbook z zadania.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Pobieranie danych wyjściowych elementu Runbook i komunikatów w graficznych elementach Runbook

W przypadku graficznych elementów Runbook dodatkowe rejestrowanie danych wyjściowych i komunikatów jest dostępne w formie śledzenia na poziomie działania. Istnieją dwa poziomy śledzenia: podstawowe i szczegółowe. Śledzenie podstawowe przedstawia czas rozpoczęcia i zakończenia każdego działania w elemencie Runbook oraz informacje związane z ponownymi próbami działania. Niektóre przykłady to liczba prób i czas rozpoczęcia działania. Szczegółowe śledzenie obejmuje podstawowe funkcje śledzenia oraz rejestrowanie danych wejściowych i wyjściowych dla każdego działania. 

Obecnie śledzenie na poziomie działania zapisuje rekordy przy użyciu pełnego strumienia. W związku z tym należy włączyć pełne rejestrowanie po włączeniu śledzenia. W przypadku graficznych elementów Runbook z włączonym śledzeniem nie ma potrzeby rejestrowania rekordów postępu. Śledzenie podstawowe służy tym samym przeznaczeniu i ma więcej informacji.

![Widok strumieni zadania tworzenia graficznego](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Z obrazu, który umożliwia pełne rejestrowanie i śledzenie graficznych elementów Runbook, można zobaczyć więcej informacji dostępnych w widoku **strumienie zadań** produkcyjnych. Te dodatkowe informacje mogą być niezbędne do rozwiązywania problemów produkcyjnych z elementem Runbook. 

Jednak jeśli nie są wymagane te informacje do śledzenia postępu elementu Runbook w celu rozwiązywania problemów, warto zachować śledzenie jako ogólne rozwiązanie. Rekordy śledzenia mogą być szczególnie liczne. Przy użyciu graficznego śledzenia elementów Runbook można uzyskać dwa do czterech rekordów na działanie, w zależności od konfiguracji podstawowego lub szczegółowego śledzenia.

**Aby włączyć śledzenie na poziomie aktywności:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów** , aby otworzyć listę elementów Runbook.
3. Na stronie elementy Runbook wybierz graficzny element Runbook z listy elementów Runbook.
4. W obszarze **Ustawienia**kliknij pozycję **Rejestrowanie i śledzenie**.
5. Na stronie Rejestrowanie i śledzenie w obszarze **Rejestruj pełne rekordy** **kliknij pozycję Włącz,** aby włączyć pełne rejestrowanie.
6. W obszarze **śledzenie na poziomie działania**Zmień poziom śledzenia na **podstawowy** lub **szczegółowy**, w zależności od wymaganego poziomu śledzenia.<br>

   ![Strona śledzenie i rejestrowanie tworzenia graficznego](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Pobieranie danych wyjściowych i komunikatów elementu Runbook w dziennikach monitora Microsoft Azure

Azure Automation może wysyłać strumienie zadań elementu Runbook i zadań do obszaru roboczego Log Analytics. Azure Monitor obsługuje dzienniki umożliwiające:

* Uzyskaj wgląd w zadania automatyzacji.
* Wyzwalanie wiadomości e-mail lub alertu na podstawie stanu zadania elementu Runbook, na przykład Niepowodzenie lub wstrzymanie.
* Zapisuj zaawansowane zapytania między strumieniami zadań.
* Skorelowanie zadań na kontach usługi Automation.
* Wizualizuj historię zadań.

Aby uzyskać więcej informacji na temat konfigurowania integracji z dziennikami Azure Monitor w celu zbierania, skorelowania i wykonywania działań dotyczących danych zadań, zobacz [przesyłanie strumieniowe stanu zadań i zadań z automatyzacji do dzienników Azure monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Następne kroki

* Aby korzystać z elementów Runbook, zobacz [Zarządzanie elementami Runbook w Azure Automation](manage-runbooks.md).
* Aby uzyskać szczegółowe informacje na temat programu PowerShell, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* * Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
