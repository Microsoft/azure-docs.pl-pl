---
title: Obsługa błędów w graficznych elementach Runbook Azure Automation
description: W tym artykule opisano sposób implementacji logiki obsługi błędów w graficznych elementach Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 24c7aaf08b4d22706bee8f37025b12a656ceaff5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895904"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Obsługa błędów w graficznych elementach runbook

Kluczową zasadą projektowania do uwzględnienia w Azure Automation graficzny element Runbook jest identyfikacja problemów, które element Runbook może napotkać podczas wykonywania. Przykładowe problemy to powodzenie, oczekiwane stany błędu i nieoczekiwane warunki błędu.

Często Jeśli wystąpi błąd niepowodujący zakończenia, który występuje w przypadku działania elementu Runbook, program Windows PowerShell obsługuje działanie przez przetwarzanie dowolnego działania, które następuje, niezależnie od błędu. Błąd prawdopodobnie wygeneruje wyjątek, ale kolejne działanie może zostać uruchomione mimo tego.

Graficzny element Runbook powinien zawierać kod obsługi błędu, aby rozwiązać problemy z wykonywaniem. Aby sprawdzić poprawność danych wyjściowych działania lub obsłużyć błąd, można użyć działania Code programu PowerShell, zdefiniować logikę warunkową dla linku wyjściowego działania lub zastosować inną metodę.

Graficzne elementy runbook usługi Azure Automation ulepszono o możliwość uwzględnienia obsługi błędów. Teraz możesz przekształcać wyjątki w błędy niepowodujące zakończenia oraz tworzyć linki błędów między działaniami. Ulepszony proces umożliwia elementowi Runbook przechwytywanie błędów i zarządzanie zrealizowanymi lub nieoczekiwanymi warunkami. 

## <a name="powershell-error-types"></a>Typy błędów programu PowerShell

Typy błędów programu PowerShell, które mogą wystąpić podczas wykonywania elementu Runbook, przerywają błędy i błędy niepowodujące zakończenia.
 
### <a name="terminating-error"></a>Błąd powodujący zakończenie

Błąd powodujący przerwanie to poważny błąd podczas wykonywania, który całkowicie zatrzymuje polecenie lub wykonywanie skryptu. Przykłady zawierają nieistniejące polecenia cmdlet, błędy składniowe, które uniemożliwiają uruchomienie polecenia cmdlet, oraz inne błędy krytyczne.

### <a name="non-terminating-error"></a>Błąd niepowodujący zakończenia

Błąd niepowodujący zakończenia to nieważny błąd, który umożliwia kontynuowanie wykonywania pomimo błędu. Przykładami mogą być błędy operacyjne, takie jak nie znaleziono plików i problemy z uprawnieniami.

## <a name="when-to-use-error-handling"></a>Kiedy używać obsługi błędów

Użyj obsługi błędów w elemencie Runbook, gdy działanie krytyczne zgłasza błąd lub wyjątek. Ważne jest, aby zapobiec przetwarzaniu następnego działania w elemencie Runbook i odpowiednio obsłużyć błąd. Obsługa tego błędu jest szczególnie istotna, gdy elementy Runbook obsługują proces operacji biznesowej lub usług.

## <a name="add-error-links"></a>Dodaj linki do błędów

Dla każdego działania, które może powodować błąd, można dodać łącze błędu wskazujące na inne działanie. Działanie docelowe może być dowolnego typu, w tym działania związane z kodem, wywołania polecenia cmdlet, wywołania innego elementu Runbook i tak dalej. Działanie docelowe może również mieć linki wychodzące, zwykłe lub błędy. Linki umożliwiają elementowi Runbook zaimplementowanie złożonej logiki obsługi błędów bez konieczności działania kodu.

Zalecaną metodą jest utworzenie dedykowanego elementu Runbook obsługi błędów z typowymi funkcjami, ale nie jest to wymagane. Rozważmy na przykład element Runbook, który próbuje uruchomić maszynę wirtualną i zainstalować na niej aplikację. Jeśli maszyna wirtualna nie zostanie uruchomiona poprawnie,:

1. Wysyła powiadomienie o tym problemie.
2. Uruchamia inny element Runbook, który zamiast tego automatycznie inicjuje nową maszynę wirtualną.

Jednym z rozwiązań jest posiadanie linku błędu w elemencie Runbook wskazującego na działanie, które obsługuje krok jeden. Na przykład element Runbook może podłączyć `Write-Warning` polecenie cmdlet do działania dla kroku 2, takiego jak polecenie cmdlet [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) .

Można również uogólnić to zachowanie w wielu elementach Runbook, umieszczając te dwa działania w oddzielnym elemencie Runbook obsługującym błędy. Zanim oryginalny element Runbook wywoła ten błąd obsługi elementu Runbook, może utworzyć niestandardowy komunikat z jego danych, a następnie przekazać go jako parametr do elementu Runbook obsługującego błędy.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Przekształcanie wyjątków w błędy niepowodujące zakończenia

Każde działanie w elemencie Runbook ma ustawienie konfiguracji, które powoduje zamianę wyjątków na błędy niepowodujące zakończenia. To ustawienie jest domyślnie wyłączone. Zalecamy włączenie tego ustawienia dla wszystkich działań, w których element Runbook obsługuje błędy. To ustawienie zapewnia, że element Runbook obsługuje zarówno błędy kończące, jak i niepowodujące zakończenia w działaniu jako błędy niepowodujące zakończenia, przy użyciu linku błędu.  

Po włączeniu ustawienia konfiguracji należy utworzyć element Runbook, który obsługuje ten błąd. Jeśli działanie powoduje jakikolwiek błąd, następuje wychodzące łącza błędów. Regularne linki nie są wykonywane nawet wtedy, gdy działanie generuje regularne dane wyjściowe.<br><br> ![Przykład linka błędu elementu runbook usługi Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

W poniższym przykładzie element Runbook pobiera zmienną, która zawiera nazwę komputera maszyny wirtualnej. Następnie próbuje uruchomić maszynę wirtualną przy użyciu następnego działania.<br><br> ![Przykład obsługi błędu elementu Runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable`Działanie i polecenie cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) są skonfigurowane do konwertowania wyjątków na błędy. Jeśli wystąpią problemy z pobraniem zmiennej lub uruchomieniem maszyny wirtualnej, kod generuje błędy.<br><br> ![Ustawienia działania obsługi błędów elementu Runbook usługi Automation ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) .

Linki błędów przepływają z tych działań do jednego `error management` działania związanego z kodem. To działanie jest skonfigurowane za pomocą prostego wyrażenia programu PowerShell, które używa `throw` słowa kluczowego, aby zatrzymać przetwarzanie, oraz `$Error.Exception.Message` uzyskać komunikat, który opisuje bieżący wyjątek.<br><br> ![Przykład kodu obsługi błędu elementu Runbook usługi Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Następne kroki

* Informacje o rozwiązywaniu graficznych błędów elementów Runbook można znaleźć w temacie [Rozwiązywanie problemów z elementem Runbook](troubleshoot/runbooks.md).
