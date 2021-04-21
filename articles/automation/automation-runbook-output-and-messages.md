---
title: Konfigurowanie strumieni komunikatów i danych wyjściowych runbook
description: W tym artykule opisano sposób implementacji logiki obsługi błędów oraz opisano strumienie danych wyjściowych i komunikatów w Azure Automation Runbook.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 033900ddd0bd19332b4a9a996c68b3b187d631c4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833566"
---
# <a name="configure-runbook-output-and-message-streams"></a>Konfigurowanie strumieni komunikatów i danych wyjściowych runbook

Większość Azure Automation runbook ma postać danych wyjściowych. Te dane wyjściowe mogą być komunikatem o błędzie dla użytkownika lub złożonym obiektem przeznaczonym do pracy z innym elementem Runbook. Windows PowerShell udostępnia [wiele strumieni do](/powershell/module/microsoft.powershell.core/about/about_redirection) wysyłania danych wyjściowych ze skryptu lub przepływu pracy. Azure Automation w różny sposób współpracuje z każdym z tych strumieni. Podczas tworzenia runbook należy stosować najlepsze rozwiązania dotyczące używania strumieni.

W poniższej tabeli krótko opisano zachowanie każdego strumienia w Azure Portal dla opublikowanych elementy Runbook oraz podczas [testowania.](./manage-runbooks.md) Strumień wyjściowy jest głównym strumieniem używanym do komunikacji między elementami Runbook. Pozostałe strumienie są klasyfikowane jako strumienie komunikatów przeznaczone do przekazywania informacji użytkownikowi.

| Stream | Opis | Opublikowany | Testowanie |
|:--- |:--- |:--- |:--- |
| Błąd |Komunikat o błędzie przeznaczony dla użytkownika. W przeciwieństwie do wyjątku, element Runbook domyślnie kontynuuje pracę po komunikacie o błędzie. |Zapisane w historii zadań |Wyświetlane w okienku Danych wyjściowych testu |
| Debugowanie |Komunikaty przeznaczone dla użytkownika interaktywnego. Nie należy używać w podręcznikach Runbook. |Nie jest zapisywany w historii zadań |Nie są wyświetlane w okienku Danych wyjściowych testu |
| Dane wyjściowe |Obiekty, które mają być używane przez inne elementy Runbook. |Zapisane w historii zadań |Wyświetlane w okienku Danych wyjściowych testu |
| Postęp |Rekordy generowane automatycznie przed każdym działaniem w elemencie Runbook i po nim. Element Runbook nie powinien próbować tworzyć własnych rekordów postępu, ponieważ są one przeznaczone dla użytkownika interakcyjnego. |Zapisywany w historii zadań tylko wtedy, gdy rejestrowanie postępu jest włączone dla tego runbook |Nie są wyświetlane w okienku Danych wyjściowych testu |
| Pełny |Komunikaty, które zawierają ogólne informacje lub informacje debugowania. |Zapisywany w historii zadań tylko wtedy, gdy dla tego runbook jest włączone pełne rejestrowanie |Wyświetlane w okienku Danych wyjściowych testu tylko wtedy, `VerbosePreference` gdy zmienna jest ustawiona na wartość Kontynuuj w programie Runbook |
| Ostrzeżenie |Komunikat ostrzegawczy przeznaczony dla użytkownika. |Zapisane w historii zadań |Wyświetlane w okienku Dane wyjściowe testu |

## <a name="use-the-output-stream"></a>Używanie strumienia wyjściowego

Strumień wyjściowy jest używany dla danych wyjściowych obiektów utworzonych przez skrypt lub przepływ pracy, gdy działa prawidłowo. Azure Automation używa tego strumienia przede wszystkim dla obiektów, które mają być używane przez nadrzędne element runbook, które wywołują [bieżący element Runbook.](automation-child-runbooks.md) Gdy element nadrzędny [wywołuje element runbook w tekście](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), element podrzędny zwraca dane ze strumienia wyjściowego do elementu nadrzędnego.

Twój runbook używa strumienia wyjściowego do przekazywania informacji ogólnych do klienta tylko wtedy, gdy nigdy nie jest wywoływany przez inny. Jednak najlepszym rozwiązaniem jest, aby w celu [](#write-output-to-verbose-stream) przekazywania informacji ogólnych użytkownikowi w podręcznikach runbook był używany pełny strumień.

Za pomocą funkcji [Write-Output](/powershell/module/microsoft.powershell.utility/write-output)należy zapisać dane w strumieniu wyjściowym. Alternatywnie można umieścić obiekt we własnym wierszu w skrypcie.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Obsługa danych wyjściowych z funkcji

Gdy funkcja runbook zapisuje dane w strumieniu wyjściowym, dane wyjściowe są przekazywane z powrotem do tego runbook. Jeśli runbook przypisze te dane wyjściowe do zmiennej, dane wyjściowe nie zostaną zapisane w strumieniu wyjściowym. Zapisywanie w innych strumieniach z funkcji zapisuje do odpowiedniego strumienia dla runbook. Rozważmy następujący przykładowy runbook przepływu pracy programu PowerShell.

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

Strumień wyjściowy zadania runbook to:

```output
Output inside of function
Output outside of function
```

Pełny strumień zadania runbook to:

```output
Verbose outside of function
Verbose inside of function
```

Po opublikowaniu runbook i przed jego uruchomieniem należy również włączyć pełne rejestrowanie w ustawieniach runbook, aby uzyskać pełne dane wyjściowe strumienia.

### <a name="declare-output-data-type"></a>Deklarowanie typu danych wyjściowych

Poniżej przedstawiono przykłady typów danych wyjściowych:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarowanie typu danych wyjściowych w przepływie pracy

Przepływ pracy określa typ danych wyjściowych przy użyciu [atrybutu OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Ten atrybut nie ma wpływu w czasie wykonywania, ale zapewnia wskazanie w czasie projektowania oczekiwanych danych wyjściowych tego runbook. W związku z rozwojem zestawu narzędzi dla elementów Runbook zwiększa się znaczenie deklarowania typów danych wyjściowych w czasie projektowania. W związku z tym najlepszym rozwiązaniem jest dołącznie tej deklaracji do wszystkich tworzyć podręczniki Runbook.

Następujący przykładowy element Runbook generuje obiekt ciągu i zawiera deklarację typu jego danych wyjściowych. Jeśli element Runbook generuje tablicę pewnego typu, należy także określić typ, a nie tablicę typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarowanie typu danych wyjściowych w graficznym elementie Runbook

Aby zadeklarować typ danych wyjściowych w graficznym lub graficznym elementie Runbook przepływu pracy programu PowerShell, możesz wybrać opcję menu **Wejście** i wyjście i wprowadzić typ danych wyjściowych. Zaleca się użycie pełnej nazwy klasy .NET, aby można było łatwo zidentyfikować typ, gdy odwołuje się do niego nadrzędny element Runbook. Użycie pełnej nazwy uwidacznia wszystkie właściwości klasy magistrali danych w ramach runbook i zwiększa elastyczność, gdy właściwości są używane do logiki warunkowej, rejestrowania i odwoływania się jako wartości dla innych działań runbook.<br> ![Opcja Wejście i wyjście dla runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po wprowadzeniu wartości w polu **Typ** danych wyjściowych w okienku właściwości Dane wejściowe i wyjściowe kliknij poza kontrolką, aby rozpoznała wpis.

W poniższym przykładzie pokazano dwa graficzne elementy Runbook, aby zademonstrować funkcję Danych wejściowych i wyjściowych. Stosując model projektowania modularnych obiektów Runbook, masz jeden elementy Runbook jako szablon uwierzytelniania runbook zarządzający uwierzytelnianiem na platformie Azure przy użyciu konta Uruchom jako. Drugi element Runbook, który zwykle wykonuje podstawową logikę w celu zautomatyzowania danego scenariusza, w tym przypadku wykonuje szablon uwierzytelniania runbook. Wyniki zostaną wyświetlone w okienku Danych wyjściowych testu. W normalnych warunkach ten runbook może wykonać jakś czynności względem zasobu wykorzystującego dane wyjściowe z podrzędnego runbook.

Oto podstawowa logika runbook **AuthenticateTo-Azure.**<br> ![Przykład uwierzytelniania szablonu ](media/automation-runbook-output-and-messages/runbook-authentication-template.png) runbook.

Ten runbook zawiera typ danych `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` wyjściowych , który zwraca właściwości profilu uwierzytelniania.<br> ![Przykład typu danych wyjściowych runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Chociaż ten element Runbook jest prosty, w tym miejscu należy wywołać jeden element konfiguracji. Ostatnie działanie wykonuje polecenie cmdlet w celu zapisu danych profilu w zmiennej przy `Write-Output` użyciu wyrażenia programu PowerShell dla `Inputobject` parametru . Ten parametr jest wymagany dla `Write-Output` .

Drugi w tym przykładzie runbook o nazwie **Test-ChildOutputType** po prostu definiuje dwa działania.<br> ![Przykładowy podrzędny typ danych wyjściowych Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Pierwsze działanie wywołuje runbook **AuthenticateTo-Azure.** Drugie działanie uruchamia polecenie `Write-Verbose` cmdlet ze źródłem **danych** ustawionym na **dane wyjściowe działania**. Ponadto ścieżka **pola jest** ustawiona na **wartość Context.Subscription.SubscriptionName**, czyli kontekstowe dane wyjściowe z runbook **AuthenticateTo-Azure.**<br> ![Źródło danych parametru polecenia cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Wynikowe dane wyjściowe to nazwa subskrypcji.<br> ![Test-ChildOutputType runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Praca ze strumieniami komunikatów

W przeciwieństwie do strumienia wyjściowego strumienie komunikatów przekazują informacje użytkownikowi. Istnieje wiele strumieni komunikatów dla różnych rodzajów informacji, a Azure Automation obsługuje każdy strumień w inny sposób.

### <a name="write-output-to-warning-and-error-streams"></a>Zapis danych wyjściowych w strumieniach ostrzeżeń i błędów

Strumienie ostrzeżeń i błędów rejestrują problemy występujące w runbook. Azure Automation zapisuje te strumienie w historii zadań podczas wykonywania runbook. Automatyzacja obejmuje strumienie w okienku Danych wyjściowych testu w Azure Portal podczas testowania runbook.

Domyślnie po ostrzeżeniu lub błędzie w dalszym ciągu jest wykonywany. Można określić, że element Runbook powinien zostać wstrzymany z [](#work-with-preference-variables) ostrzeżeniem lub błędem, ustawiając zmienną preferencji przed utworzeniem komunikatu. Aby na przykład spowodować wstrzymanie przez element Runbook błędu, tak jak w przypadku wyjątku, ustaw zmienną `ErrorActionPreference` na wartość Zatrzymaj.

Utwórz ostrzeżenie lub komunikat o błędzie za pomocą polecenia cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) [lub Write-Error.](/powershell/module/microsoft.powershell.utility/write-error) Działania mogą również zapisywać w strumieniach ostrzeżeń i błędów.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Zapis danych wyjściowych w strumieniu debugowania

Azure Automation używa strumienia komunikatów debugowania dla użytkowników interakcyjnych. Domyślnie program Azure Automation żadnych danych strumienia debugowania, przechwytywane są tylko dane wyjściowe, błędy i ostrzeżenia, a także pełne dane, jeśli program Runbook jest skonfigurowany do ich przechwytywania.

Aby przechwycić dane strumienia debugowania, należy wykonać dwie akcje w swoich runbookach:

1. Ustaw zmienną `$GLOBAL:DebugPreference="Continue"` , która nakazuje programowi PowerShell kontynuowanie za każdym razem, gdy zostanie napotkany komunikat debugowania.  Część **$GLOBAL:** informuje program PowerShell, aby wykonał tę instrukcje w zakresie globalnym, a nie w zakresie lokalnym, w jakim znajduje się skrypt w czasie wykonywania instrukcji.

1. Przekieruj strumień debugowania, który nie jest przechwytywany, do przechwyconego strumienia, takiego jak *wyjściowy .* Odbywa się to przez ustawienie przekierowania programu PowerShell względem instrukcji do wykonania. Aby uzyskać więcej informacji na temat przekierowywania programu PowerShell, zobacz [About Redirection (Informacje o przekierowaniu).](/powershell/module/microsoft.powershell.core/about/about_redirection)

#### <a name="examples"></a>Przykłady

W tym przykładzie runbook jest konfigurowany przy użyciu polecenia cmdlet i z zamiarem `Write-Output` `Write-Debug` wyprowadzenia dwóch różnych strumieni.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Gdyby ten elementy Runbook były wykonywane w takim stanie, jak to jest, okienko danych wyjściowych zadania runbook będzie przesyłać strumieniowo następujące dane wyjściowe:

```output
This is an output message.
```

W tym przykładzie runbook jest skonfigurowany podobnie jak w poprzednim przykładzie, z tą różnicą, że instrukcja jest dołączona do dodatku na `$GLOBAL:DebugPreference="Continue"` `5>&1` końcu instrukcji `Write-Debug` .

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Gdyby ten elementy Runbook zostały wykonane, okienko danych wyjściowych zadania runbook przesyłałoby strumieniowo następujące dane wyjściowe:

```output
This is an output message.
This is a debug message.
```

Dzieje się tak, ponieważ instrukcja nakazuje programowi PowerShell wyświetlanie komunikatów debugowania, a dodanie do końca instrukcji nakazuje programowi PowerShell przekierowywanie strumienia `$GLOBAL:DebugPreference="Continue"` `5>&1` `Write-Debug` 5 (debugowanie) do strumienia 1 (dane wyjściowe).

### <a name="write-output-to-verbose-stream"></a>Zapis danych wyjściowych w pełnych strumieniach

Pełny strumień komunikatów obsługuje ogólne informacje o operacji na runbook. Ponieważ strumień debugowania nie jest dostępny dla tego runbooka, powinien on używać pełnych komunikatów do informacji debugowania.

Domyślnie historia zadań nie przechowuje pełnych komunikatów z opublikowanych podręczników Runbook ze względu na wydajność. Aby przechowywać pełne komunikaty, użyj karty  Azure Portal z  ustawieniem Rejestrowanie pełnych rekordów, aby skonfigurować opublikowane podręczniki do rejestrować pełne komunikaty. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania. W większości przypadków należy zachować domyślne ustawienie nie rejestrowania pełnych rekordów.

Podczas [testowania runbook](./manage-runbooks.md)pełne komunikaty nie są wyświetlane, nawet jeśli jest on skonfigurowany do rejestrować pełne rekordy. Aby wyświetlać pełne komunikaty podczas [testowania runbook,](./manage-runbooks.md)należy ustawić `VerbosePreference` zmienną na wartość Kontynuuj. W przypadku tego zestawu zmiennych pełne komunikaty są wyświetlane w okienku Danych wyjściowych testu Azure Portal.

Poniższy kod tworzy pełny komunikat przy użyciu polecenia cmdlet [Write-Verbose.](/powershell/module/microsoft.powershell.utility/write-verbose)

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Obsługa rekordów postępu

Możesz użyć karty **Konfiguracja** w Azure Portal, aby skonfigurować program Runbook do rejestrować rekordy postępu. Ustawieniem domyślnym jest to, aby nie rejestrować rekordów w celu zmaksymalizowania wydajności. W większości przypadków należy zachować ustawienie domyślne. Włącz tę opcję tylko podczas rozwiązywania problemów z elementem Runbook lub jego debugowania.

Jeśli włączysz rejestrowanie rekordów postępu, twój runbook zapisze rekord w historii zadań przed każdym działaniem i po jego uruchomieniu. Testowanie w programie Runbook nie powoduje wyświetlenia komunikatów o postępie, nawet jeśli jest on skonfigurowany do rejestrować rekordy postępu.

>[!NOTE]
>Polecenie cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) nie jest prawidłowe w elementach Runbook, ponieważ to polecenie cmdlet jest przeznaczone do użytku z interakcyjną użytkownikiem.

## <a name="work-with-preference-variables"></a>Praca ze zmiennymi preferencji

Niektóre zmienne preferencji Windows PowerShell [można](/powershell/module/microsoft.powershell.core/about/about_preference_variables) ustawić w elementach Runbook, aby kontrolować odpowiedź na dane wysyłane do różnych strumieni wyjściowych. W poniższej tabeli wymieniono zmienne preferencji, które mogą być używane w elementach Runbook, wraz z ich domyślnymi i prawidłowymi wartościami. Dodatkowe wartości są dostępne dla zmiennych preferencji, gdy są używane Windows PowerShell poza Azure Automation.

| Zmienna | Wartość domyślna | Prawidłowe wartości |
|:--- |:--- |:--- |
| `WarningPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `ErrorActionPreference` |Kontynuuj |Stop<br>Kontynuuj<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Kontynuuj<br>SilentlyContinue |

W następnej tabeli wymieniono zachowanie wartości zmiennych preferencji, które są prawidłowe w elementach Runbook.

| Wartość | Zachowanie |
|:--- |:--- |
| Kontynuuj |Rejestruje komunikat i kontynuuje wykonywanie elementu Runbook. |
| SilentlyContinue |Kontynuuje wykonywanie elementu Runbook bez rejestrowania komunikatu. Ta wartość ma wpływ na zignorowanie komunikatu. |
| Stop |Rejestruje komunikat i zawiesza element Runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Pobieranie danych wyjściowych i komunikatów dla runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Pobieranie danych wyjściowych i komunikatów runbook w Azure Portal

Szczegółowe informacje o zadaniu runbook można wyświetlić w Azure Portal przy użyciu karty **Zadania** dla tego runbook. Podsumowanie zadania zawiera parametry wejściowe i strumień wyjściowy [,](#use-the-output-stream)oprócz ogólnych informacji o zadaniu i wyjątkach, które wystąpiły. Historia zadań zawiera komunikaty ze strumienia wyjściowego oraz [strumienie ostrzeżeń i błędów](#write-output-to-warning-and-error-streams). Zawiera również komunikaty z pełnych [](#handle-progress-records) [rekordów](#write-output-to-verbose-stream) strumienia i postępu, jeśli elementy Runbook są skonfigurowane do rejestrować pełne rekordy i rekordy postępu.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Pobieranie danych wyjściowych i komunikatów runbook w Windows PowerShell

W Windows PowerShell można pobrać dane wyjściowe i komunikaty z runbook za pomocą polecenia cmdlet [Get-AzAutomationJobOutput.](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) To polecenie cmdlet wymaga identyfikatora zadania i ma parametr o nazwie , w którym `Stream` należy określić strumień do pobrania. Można określić wartość Any dla tego parametru, aby pobrać wszystkie strumienie dla zadania.

W poniższym przykładzie jest uruchamiany przykładowy element Runbook, a następnie kod czeka na jego zakończenie. Po zakończeniu wykonywania tego zadania skrypt zbiera strumień wyjściowy runbook z zadania.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Pobieranie danych wyjściowych i komunikatów elementów Runbook w graficznych elementach Runbook

W przypadku graficznych elementów Runbook dodatkowe rejestrowanie danych wyjściowych i komunikatów jest dostępne w formie śledzenia na poziomie działania. Istnieją dwa poziomy śledzenia: Podstawowy i Szczegółowy. Śledzenie podstawowe wyświetla czas rozpoczęcia i zakończenia dla każdego działania w programie Runbook oraz informacje związane z dowolnymi ponownych próbami działania. Niektóre przykłady to liczba prób i czas rozpoczęcia działania. Szczegółowe śledzenie obejmuje podstawowe funkcje śledzenia oraz rejestrowanie danych wejściowych i wyjściowych dla każdego działania. 

Obecnie śledzenie na poziomie działania zapisuje rekordy przy użyciu verbose strumienia. Dlatego podczas włączania śledzenia należy włączyć pełne rejestrowanie. W przypadku graficznych elementów Runbook z włączonym śledzeniem nie ma potrzeby rejestrować rekordów postępu. Podstawowe śledzenie służy do tego samego celu i zawiera więcej informacji.

![Widok strumieni zadań tworzenia graficznego](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Na obrazie widać, że włączenie większej liczby funkcji rejestrowania i śledzenia dla graficznych elementów Runbook powoduje, że w widoku strumieni zadań produkcyjnych jest dostępnych **znacznie** więcej informacji. Te dodatkowe informacje mogą być niezbędne do rozwiązywania problemów produkcyjnych z elementem Runbook. 

Jednak jeśli te informacje nie są wymagane do śledzenia postępu działania runbook w celu rozwiązywania problemów, warto zachować śledzenie wyłączone jako ogólną praktykę. Rekordy śledzenia mogą być szczególnie liczne. Dzięki graficznemu śledzeniu elementów Runbook można uzyskać od dwóch do czterech rekordów na działanie, w zależności od konfiguracji śledzenia podstawowego lub szczegółowego.

**Aby włączyć śledzenie na poziomie działania:**

1. W witrynie Azure Portal otwórz konto usługi Automation.
2. Wybierz **pozycję Runbook** w obszarze **Automatyzacja procesów,** aby otworzyć listę elementów Runbook.
3. Na stronie Elementy Runbook wybierz graficzny element Runbook z listy elementów Runbook.
4. W **obszarze Ustawienia** kliknij pozycję Rejestrowanie i **śledzenie.**
5. Na stronie Rejestrowanie i śledzenie w obszarze **Rejestrowanie pełnych rekordów** kliknij przycisk **Włączone,** aby włączyć pełne rejestrowanie.
6. W **obszarze Śledzenie na poziomie działania** zmień poziom śledzenia na **Podstawowy** lub Szczegółowy **na** podstawie wymaganego poziomu śledzenia.<br>

   ![Graficzne rejestrowanie i śledzenie tworzenia strony](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Pobieranie danych wyjściowych i komunikatów runbook w dziennikach Microsoft Azure Monitor

Azure Automation wysyłać stany zadań i strumieni zadań runbook do obszaru roboczego usługi Log Analytics. Azure Monitor obsługuje dzienniki, które umożliwiają:

* Uzyskaj wgląd w zadania usługi Automation.
* Wyzwolić wiadomość e-mail lub alert na podstawie stanu zadania runbook, na przykład Niepowodzenie lub Wstrzymanie.
* Pisanie zaawansowanych zapytań w różnych strumieniach zadań.
* Korelowanie zadań między kontami usługi Automation.
* Wizualizowanie historii zadań.

Aby uzyskać więcej informacji na temat konfigurowania integracji z dziennikami usługi Azure Monitor w celu zbierania i korelowania danych zadań oraz działania na nich, zobacz Forward job status and job streams from Automation to Azure Monitor Logs (Przekazywanie stanu zadania i strumieni zadań z usługi Automation do dzienników [Azure Monitor).](automation-manage-send-joblogs-log-analytics.md)

## <a name="next-steps"></a>Następne kroki

* Aby pracować z podręcznikami Runbook, zobacz [Manage runbook in Azure Automation (Zarządzanie Azure Automation Runbook).](manage-runbooks.md)
* Jeśli nie masz informacji o skryptach programu PowerShell, zobacz [dokumentację programu PowerShell.](/powershell/scripting/overview)
* Aby uzyskać Azure Automation polecenia cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
