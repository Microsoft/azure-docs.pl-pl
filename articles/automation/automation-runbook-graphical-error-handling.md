---
title: Obsługa błędów w graficznych Azure Automation runbook
description: W tym artykule opisano sposób implementacji logiki obsługi błędów w graficznych elementach Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00ed73a1b95558f256caa01ad8eafbefe11b0f32
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830488"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Obsługa błędów w graficznych elementach runbook

Kluczową zasadą projektowania, która należy wziąć pod uwagę Azure Automation graficzny element Runbook, jest identyfikacja problemów, które element Runbook może wystąpić podczas wykonywania. Przykładowe problemy to powodzenie, oczekiwane stany błędu i nieoczekiwane warunki błędu.

Często, jeśli występuje błąd niepowiązywający z działaniem runbook, program Windows PowerShell obsługuje działanie, przetwarzając dowolne działanie, które następuje po nim, niezależnie od błędu. Błąd prawdopodobnie wygeneruje wyjątek, ale kolejne działanie może zostać uruchomione mimo tego.

Graficzny element Runbook powinien zawierać kod obsługi błędów, aby radzić sobie z problemami z wykonywaniem. Aby zweryfikować dane wyjściowe działania lub obsłużyć błąd, możesz użyć działania kodu programu PowerShell, zdefiniować logikę warunkową w linku wyjściowym działania lub zastosować inną metodę.

Graficzne elementy runbook usługi Azure Automation ulepszono o możliwość uwzględnienia obsługi błędów. Teraz możesz przekształcać wyjątki w błędy niepowodujące zakończenia oraz tworzyć linki błędów między działaniami. Ulepszony proces umożliwia programowi Runbook wychwytanie błędów i zarządzanie zrealizowanymi lub nieoczekiwanymi warunkami. 

## <a name="powershell-error-types"></a>Typy błędów programu PowerShell

Typy błędów programu PowerShell, które mogą wystąpić podczas wykonywania elementów Runbook, to błędy kończące i niepowiąząające się z tym błędy.
 
### <a name="terminating-error"></a>Błąd zakończenia

Błąd kończący to poważny błąd podczas wykonywania, który całkowicie zatrzymuje wykonywanie polecenia lub skryptu. Przykłady obejmują nieistniejący polecenia cmdlet, błędy składni, które uniemożliwiają uruchomienie polecenia cmdlet, i inne błędy krytyczne.

### <a name="non-terminating-error"></a>Błąd niepowiązywający z zakończeniem

Błąd niepowiązywający się z błędem jest błędem innym niż poważny, który umożliwia kontynuowanie wykonywania pomimo warunku błędu. Przykłady obejmują błędy operacyjne, takie jak błędy nie znaleziono pliku i problemy z uprawnieniami.

## <a name="when-to-use-error-handling"></a>Kiedy używać obsługi błędów

Używaj obsługi błędów w elementach Runbook, gdy krytyczne działanie zgłasza błąd lub wyjątek. Ważne jest, aby zapobiec przetwarzaniu kolejnego działania w elementach Runbook i odpowiednio obsłużyć błąd. Obsługa błędu jest szczególnie krytyczna, gdy twoje element runbook obsługuje proces operacji biznesowych lub usług.

## <a name="add-error-links"></a>Dodawanie linków błędów

Dla każdego działania, które może powodować błąd, możesz dodać link błędu, który wskaże dowolne inne działanie. Działanie docelowe może być dowolnego typu, w tym działanie kodu, wywołania polecenia cmdlet, wywołania innego runbook i tak dalej. Działanie docelowe może również zawierać linki wychodzące, zwykłe lub linki błędów. Linki umożliwiają implementację złożonej logiki obsługi błędów przez program Runbook bez konieczności użycia działania kodu.

Zalecaną praktyką jest utworzenie dedykowanego element runbook do obsługi błędów o typowych funkcjach, ale nie jest to obowiązkowe. Rozważmy na przykład element Runbook, który próbuje uruchomić maszynę wirtualną i zainstalować na nim aplikację. Jeśli maszyna wirtualna nie zostanie prawidłowo uruchamiana, będzie ona:

1. Wysyła powiadomienie o tym problemie.
2. Uruchamia inny runbook, który automatycznie apowiuje nową maszynę wirtualną.

Jednym z rozwiązań jest połączenie błędu w elementie Runbook z działaniem, które obsługuje krok pierwszy. Na przykład element Runbook może połączyć polecenie cmdlet z działaniem dla kroku 2, takim jak `Write-Warning` polecenie cmdlet [Start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook)

Można również uogólnić to zachowanie do użycia w wielu podręcznikach Runbook, umieszczając te dwa działania w oddzielnym podręczniku obsługi błędów. Zanim oryginalny runbook wywoła ten błąd obsługi runbook, może skonstruować niestandardowy komunikat na jego danych, a następnie przekazać go jako parametr do runbook obsługi błędów.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Przekróć wyjątki w błędy niepowiązyjące zakończenia

Każde działanie w twoim runbook ma ustawienie konfiguracji, które zmienia wyjątki w błędy niepowiązywające zakończenia. To ustawienie jest domyślnie wyłączone. Zalecamy włączenie tego ustawienia dla każdego działania, w którym twój runbook obsługuje błędy. To ustawienie zapewnia, że element Runbook obsługuje błędy przerywania i niepowiązywające zakończenia w działaniu jako błędy niepowiązywające się z błędami przy użyciu linku błędu.  

Po włączeniu ustawienia konfiguracji element Runbook musi utworzyć działanie, które obsługuje błąd. Jeśli działanie generuje błąd, następuje wychodzące linki błędów. Zwykłe linki nie są przestrzegane, nawet jeśli działanie generuje również regularne dane wyjściowe.<br><br> ![Przykład linka błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

W poniższym przykładzie elementy Runbook pobierają zmienną zawierającą nazwę komputera maszyny wirtualnej. Następnie próbuje uruchomić maszynę wirtualną przy użyciu następnego działania.<br><br> ![Przykład obsługi błędu runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Działanie `Get-AutomationVariable` i polecenie cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) są skonfigurowane do konwertowania wyjątków na błędy. Jeśli występują problemy z pobieraniem zmiennej lub uruchamianiem maszyny wirtualnej, kod generuje błędy.<br><br> ![Ustawienia działania obsługi błędów dla runbook usługi ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) Automation.

Linki błędów przepływają z tych działań do pojedynczego `error management` działania kodu. To działanie jest konfigurowane przy użyciu prostego wyrażenia programu PowerShell, które używa słowa kluczowego , aby zatrzymać przetwarzanie, oraz w celu uzyskania komunikatu, `throw` `$Error.Exception.Message` który opisuje bieżący wyjątek.<br><br> ![Przykład kodu obsługi błędu runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat rozwiązywania błędów graficznych elementów Runbook, zobacz [Rozwiązywanie problemów z elementami Runbook.](troubleshoot/runbooks.md)
