---
title: Migrowanie z programu Orchestrator do usługi Azure Automation (wersja beta)
description: W tym artykule opisano sposób migrowania podręczników Runbook i pakietów integracyjnych z Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830506"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrowanie z programu Orchestrator do usługi Azure Automation (wersja beta)

Książki Runbook w [programie System Center 2012](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) — Orchestrator są oparte na działaniach z pakietów integracyjnych napisanych specjalnie dla orkiestratora, natomiast Azure Automation są oparte na Windows PowerShell. [Graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) Azure Automation są podobne do elementów Runbook programu Orchestrator, a ich działania reprezentują polecenia cmdlet programu PowerShell, podrzędne elementy Runbook i zasoby. Oprócz konwertowania samych runbook należy przekonwertować pakiety integracyjne z działaniami, których używają te książki, na moduły integracji z Windows PowerShell cmdlet. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) przechowuje i uruchamia w lokalnym centrum danych, na przykład program Orchestrator, i używa tych samych modułów integracji, co program Azure Automation. Program Runbook Converter konwertuje elementy Runbook orkiestratora na graficzne elementy Runbook, które nie są obsługiwane w programie SMA. Moduł działań standardowych i moduł System Center Orchestrator można nadal zainstalować w programie SMA, ale należy ręcznie ponownie napisać [swoje podręczniki Runbook.](/system-center/sma/authoring-automation-runbooks)

## <a name="download-the-orchestrator-migration-toolkit"></a>Pobieranie zestawu narzędzi do migracji orkiestratora

Pierwszym krokiem migracji jest pobranie zestawu [narzędzi System Center Orchestrator Migration Toolkit.](https://www.microsoft.com/download/details.aspx?id=47323) Ten zestaw narzędzi zawiera narzędzia, które pomagają konwertować elementy Runbook z usługi Orchestrator na Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Importowanie modułu Działań standardowych

[Zaimportuj moduł działań standardowych](/system-center/orchestrator/standard-activities) do Azure Automation. Obejmuje to przekonwertowane wersje standardowych działań programu Orchestrator, których mogą używać przekonwertowane graficzne elementy Runbook.

## <a name="import-orchestrator-integration-modules"></a>Importowanie modułów integracji programu Orchestrator

Firma Microsoft udostępnia [pakiety integracyjne](/previous-versions/system-center/packs/hh295851(v=technet.10)) do tworzenia elementów Runbook w celu System Center składników i innych produktów. Niektóre z tych pakietów integracyjnych są obecnie oparte na OIT, ale obecnie nie można ich przekonwertować na moduły integracji z powodu znanych problemów. [Zaimportuj System Center Integration Modules programu Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) do Azure Automation pakietów integracyjnych używanych przez twoje System Center. Ten pakiet zawiera przekonwertowane wersje pakietów integracyjnych, które można zaimportować do Azure Automation i Service Management Automation.  

## <a name="convert-integration-packs"></a>Konwertowanie pakietów integracyjnych

Użyj programu [Integration Pack Converter,](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) aby przekonwertować wszystkie pakiety integracyjne utworzone przy użyciu [programu Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) na moduły integracji oparte na programie PowerShell, które można zaimportować do Azure Automation Service Management Automation. Po uruchomieniu programu Integration Pack Converter zostanie uruchomiony kreator, który umożliwia wybranie pliku pakietu integracyjnych (oip). Kreator wyświetla listę działań zawartych w tym pakiecie integracyjnym i umożliwia wybranie działań do migracji. Po zakończeniu pracy kreatora tworzy moduł integracji, który zawiera odpowiednie polecenie cmdlet dla każdego działania w oryginalnym pakiecie integracyjnym.

> [!NOTE]
> Nie można używać konwertera pakietów integracyjnych do konwertowania pakietów integracyjnych, które nie zostały utworzone za pomocą OIT. Istnieją również pakiety integracyjne dostarczane przez firmę Microsoft, których obecnie nie można przekonwertować za pomocą tego narzędzia. Przekonwertowane wersje tych pakietów integracyjnych są udostępniane do pobrania, aby można je było zainstalować w Azure Automation lub Service Management Automation.

### <a name="parameters"></a>Parametry

Wszystkie właściwości działania w pakiecie integracyjnym są konwertowane na parametry odpowiedniego polecenia cmdlet w module integracji.  Windows PowerShell cmdlet mają zestaw typowych [parametrów,](/powershell/module/microsoft.powershell.core/about/about_commonparameters) których można używać ze wszystkimi poleceniami cmdlet. Na przykład parametr -Verbose powoduje, że polecenie cmdlet wyprowadza szczegółowe informacje o jego operacji.  Żadne polecenie cmdlet nie może mieć parametru o takiej samej nazwie jak wspólny parametr. Jeśli działanie ma właściwość o takiej samej nazwie jak wspólny parametr, kreator wyświetli monit o podanie innej nazwy parametru.

### <a name="monitor-activities"></a>Monitorowanie działań

Monitorowanie podręczników Runbook w programie Orchestrator rozpoczyna się od [działania monitora i](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) jest stale uruchamiane w oczekiwaniu na wywołanie określonego zdarzenia. Azure Automation nie obsługuje monitorowania podręczników Runbook, więc żadne działania monitorowania w pakiecie integracyjnym nie są konwertowane. Zamiast tego w module integracji dla działania monitora jest tworzone zastępcze polecenie cmdlet.  To polecenie cmdlet nie ma żadnych funkcji, ale umożliwia jego zainstalowanie dla wszystkich przekonwertowanych, które go wykorzystują. Tego runbook nie można uruchomić w programie Azure Automation, ale można go zainstalować, aby można go było zmodyfikować.

Program Orchestrator zawiera zestaw [standardowych działań, które](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) nie są zawarte w pakiecie integracyjnym, ale są używane przez wiele elementy Runbook.  Moduł działań standardowych to moduł integracji, który zawiera odpowiednik polecenia cmdlet dla każdego z tych działań. Ten moduł integracji należy zainstalować w programie Azure Automation przed zaimportowanie jakichkolwiek przekonwertowanych książek Runbook, które używają działania standardowego.

Oprócz obsługi przekonwertowanych runbook, polecenia cmdlet w module standardowych działań mogą być używane przez kogoś, kto zna program Orchestrator, do kompilowania nowych Azure Automation. Chociaż funkcjonalność wszystkich standardowych działań może być wykonywana za pomocą polecenia cmdlet, mogą one działać inaczej. Polecenia cmdlet w module przekonwertowanych działań standardowych działają w taki sam sposób jak odpowiadające im działania i używają tych samych parametrów. Może to pomóc w przejściu do Azure Automation Runbook.

## <a name="convert-orchestrator-runbooks"></a>Konwertowanie podręczników Runbook orkiestratora

Program Orchestrator Runbook Converter konwertuje elementy Runbook orkiestratora na graficzne elementy [Runbook,](automation-runbook-types.md#graphical-runbooks) które można zaimportować do Azure Automation. Program Runbook Converter jest implementowany jako moduł programu PowerShell za pomocą polecenia cmdlet , `ConvertFrom-SCORunbook` które wykonuje konwersję. Zainstalowanie konwertera powoduje utworzenie skrótu do sesji programu PowerShell, która ładuje polecenie cmdlet.   

Poniżej znajdują się podstawowe kroki konwertowania i importowania go do Azure Automation. Szczegóły dotyczące korzystania z polecenia cmdlet znajdują się w dalszej części tej sekcji.

1. Wyeksportuj co najmniej jeden podręcznik Runbook z orkiestratora.
2. Uzyskaj moduły integracji dla wszystkich działań w tym programie Runbook.
3. Przekonwertuj pliki Runbook programu Orchestrator w wyeksportowany plik.
4. Przejrzyj informacje w dziennikach, aby zweryfikować konwersję i określić wymagane zadania ręczne.
5. Zaimportuj przekonwertowane Azure Automation.
6. Utwórz wszystkie wymagane zasoby w Azure Automation.
7. Edytuj pozycję Runbook w Azure Automation, aby zmodyfikować wszystkie wymagane działania.

Składnia polecenia `ConvertFrom-SCORunbook` jest następująca:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath — ścieżka do pliku eksportu zawierającego te, które mają zostać przekonwertowane.
* Moduł — rozdzielana przecinkami lista modułów integracji zawierających działania w ramach elementów Runbook.
* OutputFolder — ścieżka do folderu w celu utworzenia przekonwertowanych graficznych elementów Runbook.

Następujące przykładowe polecenie konwertuje elementy Runbook w pliku eksportu o **nazwie MyRunbooks.ois_export**.  Te podręczniki Runbook używają usługi Active Directory Data Protection Manager pakietów integracyjnych.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Korzystanie z plików dziennika programu Runbook Converter

Program Runbook Converter tworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowany plik Runbook.  Jeśli pliki już istnieją, są zastępowane informacjami z ostatniej konwersji.

| Plik | Zawartość |
|:--- |:--- |
| Runbook Converter — Progress.log |Szczegółowe kroki konwersji, w tym informacje o każdym pomyślnym przekonwertowaniu działania i ostrzeżenia dla każdego nieskonwertowanych działań. |
| Runbook Converter — Summary.log |Podsumowanie ostatniej konwersji, w tym wszelkie ostrzeżenia i kolejne zadania, które należy wykonać, takie jak utworzenie zmiennej wymaganej dla przekonwertowanych działań runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Eksportowanie podręczników Runbook z orkiestratora

Program Runbook Converter współpracuje z plikiem eksportu z programu Orchestrator, który zawiera co najmniej jeden element Runbook.  Tworzy on odpowiedni Azure Automation runbook dla każdego runbook programu Orchestrator w pliku eksportu.  

Aby wyeksportować program Runbook z orkiestratora, kliknij prawym przyciskiem myszy nazwę tego runbook w programie Runbook Designer wybierz polecenie **Eksportuj.**  Aby wyeksportować wszystkie książki Runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu i wybierz polecenie **Eksportuj**.

### <a name="convert-runbook-activities"></a>Konwertowanie działań runbook

Program Runbook Converter konwertuje każde działanie w programie Orchestrator Runbook na odpowiednie działanie w Azure Automation.  W przypadku działań, których nie można przekonwertować, w elementie Runbook jest tworzone działanie zastępcze z tekstem ostrzeżenia.  Po zaimportowaniu przekonwertowanych działań Runbook do Azure Automation należy zastąpić dowolne z tych działań prawidłowymi działaniami, które wykonują wymagane funkcje.

Wszystkie działania orkiestratora w module działań standardowych są konwertowane. Istnieją jednak pewne standardowe działania orkiestratora, których nie ma w tym module i które nie są konwertowane. Na przykład `Send Platform Event` program nie ma Azure Automation równoważnej, ponieważ zdarzenie jest specyficzne dla orkiestratora.

[Działania monitorowania](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) nie są konwertowane, ponieważ nie ma odpowiedników w Azure Automation. Wyjątki to działania monitorowania w skonwertowanych pakietach integracyjnych, które są konwertowane na działanie symbolu zastępczego.

Każde działanie z przekonwertowanych pakietu integracyjne jest konwertowane, jeśli ścieżka do modułu integracji zostanie podana za pomocą `modules` parametru . W System Center integracyjnych można użyć modułów integracji System Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Zarządzanie zasobami orkiestratora

Program Runbook Converter konwertuje tylko książki Runbook, a nie inne zasoby orkiestratora, takie jak liczniki, zmienne lub połączenia.  Liczniki nie są obsługiwane w Azure Automation.  Zmienne i połączenia są obsługiwane, ale należy utworzyć je ręcznie. Pliki dziennika informują o tym, czy taki podręcznik runbook wymaga takich zasobów, i określają odpowiednie zasoby, które należy utworzyć w programie Azure Automation, aby przekonwertowany typ runbook działał prawidłowo.

Na przykład element Runbook może używać zmiennej do wypełniania określonej wartości w działaniu.  Przekonwertowany składnik Runbook konwertuje działanie i określa zasób zmiennej w Azure Automation o takiej samej nazwie jak zmienna Orchestrator. Ta akcja jest zanotowana w pliku **Runbook Converter - Summary.log** utworzonym po konwersji. Należy ręcznie utworzyć ten zasób zmiennej w programie Azure Automation przed użyciem tego runbook.

### <a name="work-with-orchestrator-input-parameters"></a>Praca z parametrami wejściowymi orkiestratora

W programie Orchestrator w programie Orchestrator są akceptowane parametry wejściowe z `Initialize Data` działaniem .  Jeśli konwertowany element Runbook obejmuje to [](automation-graphical-authoring-intro.md#handle-runbook-input) działanie, parametr wejściowy w Azure Automation runbook jest tworzony dla każdego parametru w działaniu.  Działanie [sterowania skryptem przepływu](automation-graphical-authoring-intro.md#use-activities) pracy jest tworzone w przekonwertowanych runbook, który pobiera i zwraca każdy parametr. Wszystkie działania w elementach Runbook, które używają parametru wejściowego, odwołują się do danych wyjściowych tego działania.

Ta strategia jest używana w celu najlepszego zdu duplikatu funkcjonalności w runbook programu Orchestrator.  Działania w nowych graficznych elementach Runbook powinny odwoływać się bezpośrednio do parametrów wejściowych przy użyciu wejściowego źródła danych runbook.

### <a name="invoke-runbook-activity"></a>Wywoływanie działania runbook

W programie Orchestrator w programie Orchestrator inne podręczniki Runbook uruchamiają `Invoke Runbook` działanie . Jeśli konwertowany elementy Runbook obejmują to działanie i ustawiono opcję , zostanie dla niego utworzone działanie runbook w `Wait for completion` przekonwertowanych elementy Runbook.  Jeśli ta opcja nie jest ustawiona, tworzone jest działanie Skrypt przepływu pracy, które uruchamia go za pomocą polecenia `Wait for completion` [Start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook) Po zaimportowaniu przekonwertowanych runbook do Azure Automation należy zmodyfikować to działanie przy użyciu informacji określonych w działaniu.

## <a name="create-orchestrator-assets"></a>Tworzenie zasobów orkiestratora

Program Runbook Converter nie konwertuje zasobów orkiestratora. Wszystkie wymagane zasoby orkiestratora należy utworzyć ręcznie w programie Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Konfigurowanie hybrydowego procesu roboczego runbook

Program Orchestrator przechowuje podręczniki Runbook na serwerze bazy danych i uruchamia je na serwerach Runbook, zarówno w lokalnym centrum danych. Pliki Runbook w Azure Automation są przechowywane w chmurze platformy Azure i mogą być uruchamiane w lokalnym centrum danych przy użyciu hybrydowego procesu [roboczego runbook.](automation-hybrid-runbook-worker.md) Skonfiguruj proces roboczy do uruchamiania twoich runbook przekonwertowanych z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych i uzyskiwania dostępu do zasobów lokalnych.

## <a name="related-articles"></a>Pokrewne artykuły:

* Aby uzyskać szczegółowe informacje na temat System Center Orchestrator, [zobacz System Center 2012 — Orchestrator.](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))
* Dowiedz się więcej na temat automatyzacji zarządzania usługami w [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Szczegółowe informacje o działaniach orkiestratora można znaleźć w te [tematach: Orchestrator Standard Activities (Standardowe działania orkiestratora).](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))
* Aby uzyskać zestaw narzędzi migracji orkiestratora, zobacz Download System Center Orchestrator Migration Toolkit (Pobieranie zestawu narzędzi System Center [Orchestrator Migration Toolkit).](https://www.microsoft.com/download/details.aspx?id=47323)
* Aby uzyskać omówienie hybrydowego procesu roboczego Azure Automation Runbook, zobacz Omówienie hybrydowego procesu [roboczego runbook](automation-hybrid-runbook-worker.md).
