---
title: Debugowanie Azure Functions programu PowerShell lokalnie
description: Dowiedz się, jak debugować funkcje programu PowerShell podczas uruchamiania lokalnego.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422964"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debugowanie Azure Functions programu PowerShell lokalnie

Azure Functions umożliwia tworzenie funkcji jako skryptów programu PowerShell.

Można debugować funkcje programu PowerShell lokalnie, podobnie jak wszystkie skrypty programu PowerShell, używając następujących standardowych narzędzi programistycznych:

* [Visual Studio Code](https://code.visualstudio.com/): Edytor tekstów bezpłatny, lekki i typu open source firmy Microsoft z rozszerzeniem programu PowerShell, który oferuje pełne środowisko programistyczne programu PowerShell.
* Konsola programu PowerShell: debugowanie przy użyciu tych samych poleceń, które służą do debugowania dowolnego innego procesu programu PowerShell.

[Azure Functions Core Tools](functions-run-local.md) obsługuje lokalne debugowanie Azure Functions, w tym funkcje programu PowerShell.

## <a name="example-function-app"></a>Przykładowa aplikacja funkcji

Aplikacja funkcji używana w tym artykule ma pojedynczą funkcję wyzwalaną przez protokół HTTP i ma następujące pliki:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ta aplikacja funkcji jest podobna do tej, którą uzyskasz po zakończeniu [przewodnika Szybki Start dla programu PowerShell](./create-first-function-vs-code-powershell.md).

Kod funkcji w programie `run.ps1` wygląda jak poniższy skrypt:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Ustawianie punktu dołączania

Aby debugować każdą funkcję programu PowerShell, funkcja musi zostać zatrzymana w celu dołączenia debugera. `Wait-Debugger`Polecenie cmdlet przerywa wykonywanie i czeka na debuger.

>[!NOTE]
>W przypadku korzystania z programu PowerShell 7 nie trzeba dodawać `Wait-Debugger` wywołania w kodzie.

Wszystko, co musisz zrobić, to dodanie wywołania `Wait-Debugger` polecenia cmdlet tuż powyżej instrukcji w `if` następujący sposób:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Debugowanie rozpocznie się w `if` instrukcji. 

`Wait-Debugger`W programie można teraz debugować funkcje przy użyciu Visual Studio Code lub konsoli programu PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debugowanie w Visual Studio Code

Aby debugować funkcje programu PowerShell w Visual Studio Code, musisz mieć zainstalowane następujące elementy:

* [Rozszerzenie programu PowerShell dla Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code](./create-first-function-cli-powershell.md)
* [Program PowerShell Core 6,2 lub nowszy](/powershell/scripting/install/installing-powershell-core-on-windows)

Po zainstalowaniu tych zależności Załaduj istniejący projekt funkcji programu PowerShell lub [Utwórz pierwszy projekt funkcji programu PowerShell](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Jeśli projekt nie ma wymaganych plików konfiguracji, zostanie wyświetlony monit o ich dodanie.

### <a name="set-the-powershell-version"></a>Ustaw wersję programu PowerShell

Instalacja programu PowerShell Core obok siebie przy użyciu programu Windows PowerShell. Ustaw dla programu PowerShell rdzeń jako wersję programu PowerShell, która ma być używana z rozszerzeniem programu PowerShell dla Visual Studio Code.

1. Naciśnij klawisz F1, aby wyświetlić paletę poleceń, a następnie wyszukaj ciąg `Session` .

1. Wybierz polecenie **PowerShell: Pokaż menu sesji**.

1. Jeśli **Bieżąca sesja** nie jest w programie **PowerShell Core 6** , wybierz polecenie **Przełącz do: PowerShell Core 6**.

Gdy plik programu PowerShell jest otwarty, zobaczysz wersję wyświetlaną w kolorze zielonym w prawym dolnym rogu okna. Wybranie tego tekstu spowoduje również wyświetlenie menu sesji. Aby dowiedzieć się więcej, zobacz [Wybieranie wersji programu PowerShell, która ma być używana z rozszerzeniem](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Uruchom aplikację funkcji

Sprawdź, czy `Wait-Debugger` jest ustawiona w funkcji, w której chcesz dołączyć debuger.  `Wait-Debugger`Po dodaniu można debugować aplikację funkcji przy użyciu Visual Studio Code.

Wybierz okienko **debugowanie** , a następnie **Dołącz do funkcji programu PowerShell**.

![oknie](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Możesz również nacisnąć klawisz F5, aby rozpocząć debugowanie.

Operacja Rozpocznij debugowanie wykonuje następujące zadania:

* Działa `func extensions install` w terminalu, aby zainstalować wszystkie rozszerzenia Azure Functions wymagane przez aplikację funkcji.
* Działa `func host start` w terminalu, aby uruchomić aplikację funkcji na hoście funkcji.
* Dołącz debuger programu PowerShell do obszaru działania programu PowerShell w środowisku uruchomieniowym funkcji.

>[!NOTE]
> Należy upewnić się, że PSWorkerInProcConcurrencyUpperBound jest ustawiona na 1, aby zapewnić poprawne środowisko debugowania w Visual Studio Code. Jest to opcja domyślna.

Gdy aplikacja funkcji jest uruchomiona, potrzebna jest osobna konsola programu PowerShell do wywoływania funkcji wyzwalanej przez protokół HTTP.

W takim przypadku konsola programu PowerShell jest klientem programu. `Invoke-RestMethod`Służy do wyzwalania funkcji.

W konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważysz, że odpowiedź nie jest natychmiast zwracana. Dzieje się tak, ponieważ `Wait-Debugger` został podłączony debuger i wykonanie programu PowerShell zakończyło się w trybie przerwania, jak tylko to możliwe. Jest to spowodowane [koncepcją BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), która została omówiona w dalszej części. Po naciśnięciu `continue` przycisku debuger teraz przerwie się w wierszu po prawej stronie `Wait-Debugger` .

W tym momencie debuger jest dołączony i można wykonać wszystkie normalne operacje debugera. Aby uzyskać więcej informacji na temat używania debugera w Visual Studio Code, zobacz [oficjalną dokumentację](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Po zakończeniu i pełnym wywołaniu skryptu można zauważyć, że:

* Konsola programu PowerShell, która `Invoke-RestMethod` spowodowała zwrócenie wyniku
* Zintegrowana konsola programu PowerShell w Visual Studio Code oczekuje na wykonanie skryptu

Później po wywołaniu tej samej funkcji debuger w rozszerzeniu programu PowerShell zostaje podzielony bezpośrednio po `Wait-Debugger` .

## <a name="debugging-in-a-powershell-console"></a>Debugowanie w konsoli programu PowerShell

>[!NOTE]
> W tej sekcji założono, że użytkownik przeczytał [Azure Functions Core Tools dokumenty](functions-run-local.md) i wie, jak używać `func host start` polecenia, aby uruchomić aplikację funkcji.

Otwórz konsolę programu, `cd` w katalogu aplikacji funkcji, a następnie uruchom następujące polecenie:

```sh
func host start
```

Po uruchomieniu aplikacji funkcji i `Wait-Debugger` w miejscu możesz dołączyć do procesu. Potrzebujesz dwóch dodatkowych konsol programu PowerShell.

Jedna z konsol działa jako klient. Z tego wywołania wywołasz `Invoke-RestMethod` funkcję. Na przykład możesz uruchomić następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważ, że nie zwróci odpowiedzi, która jest wynikiem `Wait-Debugger` . Obszar działania programu PowerShell czeka teraz na dołączenie debugera. Przyjrzyjmy się.

W innej konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Get-PSHostProcessInfo
```

To polecenie cmdlet zwraca tabelę, która wygląda jak następujące dane wyjściowe:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Zanotuj `ProcessId` dla elementu w tabeli przy użyciu `ProcessName` jako `dotnet` . Ten proces to aplikacja funkcji.

Następnie uruchom następujący fragment kodu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po uruchomieniu debuger przerwie i pokazuje coś, jak następujące dane wyjściowe:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

W tym momencie nastąpi zatrzymanie w punkcie przerwania w [debugerze programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). W tym miejscu można wykonywać wszystkie typowe operacje debugowania, przekroczyć, wkroczć, kontynuować, zakończyć i inne. Aby wyświetlić kompletny zestaw poleceń debugowania dostępnych w konsoli programu, uruchom `h` `?` polecenia lub.

Możesz również ustawić punkty przerwania na tym poziomie przy użyciu `Set-PSBreakpoint` polecenia cmdlet.

Gdy będziesz kontynuować i w pełni wywoływać swój skrypt, Zauważ, że:

* Uruchomiona konsola programu PowerShell `Invoke-RestMethod` zwraca wynik.
* Uruchomiona konsola programu PowerShell `Debug-Runspace` oczekuje na wykonanie skryptu.

Tę samą funkcję można wywołać ponownie ( `Invoke-RestMethod` na przykład przy użyciu polecenia), a debuger przerwie się po prawej stronie `Wait-Debugger` .

## <a name="considerations-for-debugging"></a>Zagadnienia dotyczące debugowania

Podczas debugowania kodu funkcji należy pamiętać o następujących kwestiach.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` może spowodować przerwanie debugera w nieoczekiwanym miejscu

Rozszerzenie programu PowerShell używa `Debug-Runspace` , które z kolei jest zależne od funkcji programu PowerShell `BreakAll` . Ta funkcja informuje program PowerShell o zatrzymaniu pierwszego polecenia, które jest wykonywane. Takie zachowanie daje możliwość ustawienia punktów przerwania w debugowanym obszarze działania.

Środowisko uruchomieniowe Azure Functions uruchamia kilka poleceń przed faktycznym wywołaniem `run.ps1` skryptu, dlatego istnieje możliwość, że debuger zakończy działanie w ramach `Microsoft.Azure.Functions.PowerShellWorker.psm1` lub `Microsoft.Azure.Functions.PowerShellWorker.psd1` .

Jeśli ta przerwanie ma miejsce, `continue` Uruchom `c` polecenie lub, aby pominąć ten punkt przerwania. Następnie zatrzymasz się w oczekiwanym punkcie przerwania.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli występują problemy podczas debugowania, należy sprawdzić następujące kwestie:

| Zaznacz | Akcja |
|------|------|
| Uruchom `func --version` z terminalu. Jeśli zostanie wyświetlony komunikat o błędzie `func` , którego nie można znaleźć, w zmiennej lokalnej może brakować podstawowych narzędzi (func.exe) `path` .| [Zainstaluj ponownie podstawowe narzędzia](functions-run-local.md#v2).|  
| W Visual Studio Code domyślnym terminalem musi być dostęp do func.exe. Upewnij się, że nie korzystasz z domyślnego terminalu, w którym nie zainstalowano podstawowych narzędzi, takich jak podsystem Windows dla systemu Linux (WSL).  | Ustaw powłokę domyślną w Visual Studio Code na program PowerShell 7 (zalecane) lub Windows PowerShell 5,1.|
  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat opracowywania funkcji przy użyciu programu PowerShell, zobacz [Azure Functions przewodnik dewelopera programu PowerShell](functions-reference-powershell.md).
