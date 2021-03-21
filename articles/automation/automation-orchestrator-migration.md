---
title: Migrowanie z programu Orchestrator do usługi Azure Automation (wersja beta)
description: W tym artykule opisano, jak migrować elementy Runbook i pakiety integracyjne z programu Orchestrator do Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896939"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrowanie z programu Orchestrator do usługi Azure Automation (wersja beta)

Elementy Runbook w programie [System Center 2012 — Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) bazują na działaniach z pakietów integracyjnych, które są przeznaczone dla programu Orchestrator, natomiast elementy runbook w Azure Automation są oparte na programie Windows PowerShell. [Graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) w Azure Automation mają podobny wygląd do elementów Runbook programu Orchestrator, z ich działaniami, które reprezentują polecenia cmdlet środowiska PowerShell, podrzędne elementy Runbook i zasoby. Oprócz konwertowania elementów Runbook, należy przekonwertować pakiety integracyjne na działania, których elementy Runbook używają do modułów integracji za pomocą poleceń cmdlet programu Windows PowerShell. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) przechowuje i uruchamia elementy Runbook w lokalnym centrum danych, takim jak program Orchestrator, i używa tych samych modułów integracji co Azure Automation. Konwerter elementów Runbook konwertuje elementy Runbook programu Orchestrator na graficzne elementy Runbook, które nie są obsługiwane w programie SMA. Nadal można zainstalować moduł działania standardowe i moduły integracji programu System Center Orchestrator w programie SMA, ale należy ręcznie [napisać elementy Runbook](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>Pobierz zestaw narzędzi do migracji programu Orchestrator

Pierwszym krokiem w ramach migracji jest pobranie [zestawu narzędzi System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323). Ten zestaw narzędzi zawiera narzędzia ułatwiające Konwertowanie elementów Runbook z programu Orchestrator na Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Importowanie modułu działań standardowych

Zaimportuj [moduł działań standardowych](/system-center/orchestrator/standard-activities) do Azure Automation. Obejmuje to przekonwertowane wersje standardowych działań programu Orchestrator, których można użyć do przekonwertowania graficznych elementów Runbook.

## <a name="import-orchestrator-integration-modules"></a>Importuj moduły integracji programu Orchestrator

Firma Microsoft udostępnia [pakiety integracyjne](/previous-versions/system-center/packs/hh295851(v=technet.10)) do kompilowania elementów Runbook w celu zautomatyzowania składników programu System Center i innych produktów. Niektóre z tych pakietów integracyjnych są obecnie oparte na OIT, ale obecnie nie można ich przekonwertować na moduły integracji ze względu na znane problemy. Zaimportuj [moduły integracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) do Azure Automation pakietów integracyjnych używanych przez elementy Runbook, które uzyskują dostęp do programu System Center. Ten pakiet obejmuje przekonwertowane wersje pakietów integracyjnych, które można zaimportować do Azure Automation i Service Management Automation.  

## <a name="convert-integration-packs"></a>Konwertuj pakiety integracyjne

[Konwerter pakietu integracyjnego](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) umożliwia konwertowanie wszelkich pakietów integracyjnych utworzonych przy użyciu [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) na moduły integracji oparte na programie PowerShell, które można importować do Azure Automation lub Service Management Automation. Po uruchomieniu konwertera pakietu integracyjnego zostanie wyświetlony Kreator, który umożliwia wybranie pliku pakietu integracyjnego (. OIP). Następnie Kreator wyświetla listę działań objętych tym pakietem integracyjnym i umożliwia wybranie działań do migracji. Po zakończeniu pracy Kreatora tworzy moduł integracji zawierający odpowiednie polecenie cmdlet dla każdego działania w oryginalnym pakiecie integracyjnym.

> [!NOTE]
> Nie można użyć konwertera pakietu integracyjnego do przekonwertowania pakietów integracyjnych, które nie zostały utworzone za pomocą OIT. Istnieją także pakiety integracyjne udostępniane przez firmę Microsoft, które nie są obecnie konwertowane za pomocą tego narzędzia. Przekonwertowane wersje tych pakietów integracyjnych są udostępniane do pobrania, aby można je było zainstalować w Azure Automation lub Service Management Automation.

### <a name="parameters"></a>Parametry

Wszystkie właściwości działania w pakiecie integracyjnym są konwertowane na parametry odpowiedniego polecenia cmdlet w module integracji.  Polecenia cmdlet programu Windows PowerShell mają zestaw [wspólnych parametrów](/powershell/module/microsoft.powershell.core/about/about_commonparameters) , które mogą być używane z wszystkimi poleceniami cmdlet. Na przykład parametr-Verbose powoduje polecenie cmdlet do wyprowadzania szczegółowych informacji o jego operacji.  Żadne polecenie cmdlet nie może mieć parametru o takiej samej nazwie jak wspólny parametr. Jeśli działanie ma właściwość o tej samej nazwie co wspólny parametr, Kreator poprosi o podanie innej nazwy dla parametru.

### <a name="monitor-activities"></a>Monitorowanie działań

Monitoruj elementy Runbook w programie Orchestrator Rozpocznij od [działania monitorowania](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) i Kontynuuj, oczekujące na wywoływanie przez konkretne zdarzenie. Azure Automation nie obsługuje elementów Runbook monitorowania, więc żadne działania dotyczące monitorowania w pakiecie integracyjnym nie są konwertowane. Zamiast tego w module integracji zostanie utworzone zastępcze polecenie cmdlet dla działania monitorowania.  To polecenie cmdlet nie ma funkcji, ale umożliwia zainstalowanie dowolnego przekonwertowanego elementu Runbook, który go używa. Nie można uruchomić tego elementu Runbook w Azure Automation, ale można go zainstalować, aby można było go zmodyfikować.

Program Orchestrator zawiera zestaw [działań standardowych](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) , które nie są uwzględnione w pakiecie integracyjnym, ale są używane przez wiele elementów Runbook.  Moduł działania standardowe to moduł integracji, który zawiera odpowiedniki polecenia cmdlet dla każdej z tych działań. Ten moduł integracji należy zainstalować w Azure Automation przed zaimportowaniem wszystkich przekonwertowanych elementów Runbook, które używają działania standardowego.

Oprócz obsługi przekonwertowanych elementów Runbook polecenia cmdlet w module działania standardowe mogą być używane przez kogoś znanego w programie Orchestrator do tworzenia nowych elementów Runbook w programie Azure Automation. Chociaż funkcje wszystkich działań standardowych można wykonywać za pomocą poleceń cmdlet, mogą one działać inaczej. Polecenia cmdlet w module przekonwertowane działania standardowe działają w taki sam sposób jak odpowiadające im działania i używają tych samych parametrów. Może to pomóc w przejściu do Azure Automation elementów Runbook.

## <a name="convert-orchestrator-runbooks"></a>Konwertowanie elementów Runbook programu Orchestrator

Konwerter elementów Runbook programu Orchestrator konwertuje elementy Runbook programu Orchestrator na [graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) , które można zaimportować do Azure Automation. Konwerter elementów Runbook jest implementowany jako moduł programu PowerShell z poleceniem cmdlet `ConvertFrom-SCORunbook` , które wykonuje konwersję. Podczas instalacji konwertera tworzy skrót do sesji programu PowerShell, która ładuje polecenie cmdlet.   

Poniżej przedstawiono podstawowe kroki umożliwiające przekonwertowanie elementu Runbook i zaimportowanie go do Azure Automation. Szczegóły dotyczące korzystania z polecenia cmdlet znajdują się w dalszej części tej sekcji.

1. Wyeksportuj co najmniej jeden element Runbook z programu Orchestrator.
2. Uzyskaj moduły integracji dla wszystkich działań w elemencie Runbook.
3. Przekonwertuj elementy Runbook programu Orchestrator w wyeksportowanym pliku.
4. Przejrzyj informacje w dziennikach, aby zweryfikować konwersję i określić wymagane zadania ręczne.
5. Zaimportuj przekonwertowane elementy Runbook do Azure Automation.
6. Utwórz wszystkie wymagane zasoby w Azure Automation.
7. Edytuj element Runbook w Azure Automation, aby zmodyfikować wszystkie wymagane działania.

Składnia dla `ConvertFrom-SCORunbook` :

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath — ścieżka do pliku eksportu zawierającego elementy Runbook do przekonwertowania.
* Rozdzielana przecinkami lista modułów integracji zawierających działania w elementach Runbook.
* OutputFolder — ścieżka do folderu w celu utworzenia przekonwertowanych graficznych elementów Runbook.

Następujące przykładowe polecenie konwertuje elementy Runbook w pliku eksportu o nazwie **MyRunbooks.ois_export**.  Te elementy Runbook używają Active Directory i Data Protection Manager pakietów integracyjnych.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Użyj plików dziennika konwertera elementów Runbook

Konwerter elementu Runbook tworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowany element Runbook.  Jeśli pliki już istnieją, zostaną zastąpione informacjami z ostatniej konwersji.

| Plik | Zawartość |
|:--- |:--- |
| Konwerter elementu Runbook — postęp. log |Szczegółowe kroki konwersji, w tym informacje dotyczące poszczególnych działań, zostały pomyślnie przekonwertowane i ostrzeżenie dla każdego nieskonwertowanego działania. |
| Konwerter elementu Runbook — Summary. log |Podsumowanie ostatniej konwersji wraz z ostrzeżeniami i monitowanie zadań, które należy wykonać, takich jak tworzenie zmiennej wymaganej dla przekonwertowanego elementu Runbook. |

### <a name="export-runbooks-from-orchestrator"></a>Eksportowanie elementów Runbook z programu Orchestrator

Konwerter elementów Runbook współpracuje z plikiem eksportu z programu Orchestrator, który zawiera co najmniej jeden element Runbook.  Tworzy odpowiedni Azure Automation element Runbook dla każdego elementu Runbook programu Orchestrator w pliku eksportu.  

Aby wyeksportować element Runbook z programu Orchestrator, kliknij prawym przyciskiem myszy nazwę elementu Runbook w Runbook Designer a następnie wybierz pozycję **Eksportuj**.  Aby wyeksportować wszystkie elementy Runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu i wybierz pozycję **Eksportuj**.

### <a name="convert-runbook-activities"></a>Konwertowanie działań elementu Runbook

Konwerter elementów Runbook konwertuje każde działanie w elemencie Runbook programu Orchestrator na odpowiednie działanie w Azure Automation.  W przypadku tych działań, których nie można przekonwertować, w elemencie Runbook jest tworzone działanie symbol zastępczy z tekstem ostrzegawczym.  Po zaimportowaniu przekonwertowanego elementu Runbook do Azure Automation należy zastąpić dowolne z tych działań prawidłowymi działaniami, które wykonują wymagane funkcje.

Wszystkie działania programu Orchestrator w module działania standardowe są konwertowane. Istnieją pewne standardowe działania programu Orchestrator, które nie znajdują się w tym module, ale nie są konwertowane. Na przykład `Send Platform Event` nie ma Azure Automation równoważnej, ponieważ zdarzenie jest specyficzne dla programu Orchestrator.

[Działania monitorowania](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) nie są konwertowane, ponieważ nie ma ich odpowiedników w Azure Automation. Wyjątkami są działania monitorowania w przekonwertowanych pakietach integracyjnych, które są konwertowane na działanie symbolu zastępczego.

Wszelkie działania z przekonwertowanego pakietu integracyjnego są konwertowane, jeśli podano ścieżkę do modułu integracji z `modules` parametrem. Pakiety integracyjne programu System Center umożliwiają korzystanie z modułów integracji programu System Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Zarządzanie zasobami programu Orchestrator

Konwerter elementu Runbook konwertuje tylko elementy Runbook, nie inne zasoby programu Orchestrator, takie jak liczniki, zmienne lub połączenia.  Liczniki nie są obsługiwane w Azure Automation.  Obsługiwane są zmienne i połączenia, ale należy je utworzyć ręcznie. Pliki dziennika informują użytkownika, jeśli element Runbook wymaga takich zasobów i określisz odpowiednie zasoby, które należy utworzyć w Azure Automation, aby przekonwertowane elementy Runbook działały prawidłowo.

Na przykład element Runbook może użyć zmiennej, aby wypełnić określoną wartość w działaniu.  Przekonwertowane elementy Runbook konwertują działanie i określają zasób zmiennej w Azure Automation z taką samą nazwą jak zmienna programu Orchestrator. Ta akcja jest zapisywana w pliku programu **Runbook Converter-Summary. log** , który został utworzony po konwersji. Należy ręcznie utworzyć ten zasób zmiennej w Azure Automation przed użyciem elementu Runbook.

### <a name="work-with-orchestrator-input-parameters"></a>Pracuj z parametrami wejściowymi programu Orchestrator

Elementy Runbook w programie Orchestrator akceptują parametry wejściowe z `Initialize Data` działaniem.  Jeśli element Runbook, który jest konwertowany, obejmuje to działanie, dla każdego parametru w działaniu jest tworzony [parametr wejściowy](automation-graphical-authoring-intro.md#handle-runbook-input) w Azure Automation elemencie Runbook.  Działanie [sterujące skryptu przepływu pracy](automation-graphical-authoring-intro.md#use-activities) jest tworzone w przekonwertowanym elemencie Runbook, który pobiera i zwraca każdy parametr. Wszystkie działania w elemencie Runbook, które używają parametru wejściowego, odwołują się do danych wyjściowych tego działania.

Ta strategia jest używana w celu najlepszego dublowania funkcji w elemencie Runbook programu Orchestrator.  Działania w nowych graficznych elementach Runbook powinny odwoływać się bezpośrednio do parametrów wejściowych przy użyciu źródła danych wejściowych elementu Runbook.

### <a name="invoke-runbook-activity"></a>Wywołaj działanie elementu Runbook

Elementy Runbook w programie Orchestrator uruchamiają inne elementy Runbook za pomocą `Invoke Runbook` działania. Jeśli element Runbook, który jest konwertowany, obejmuje to działanie, a `Wait for completion` opcja jest ustawiona, dla niego zostanie utworzone działanie elementu Runbook w przekonwertowanym elemencie Runbook.  Jeśli ta `Wait for completion` opcja nie jest ustawiona, zostanie utworzone działanie skryptu przepływu pracy z poleceniem [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) w celu uruchomienia elementu Runbook. Po zaimportowaniu przekonwertowanego elementu Runbook do Azure Automation należy zmodyfikować to działanie za pomocą informacji określonych w działaniu.

## <a name="create-orchestrator-assets"></a>Utwórz zasoby programu Orchestrator

Konwerter elementu Runbook nie konwertuje zasobów programu Orchestrator. Musisz ręcznie utworzyć wszystkie wymagane zasoby programu Orchestrator w Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Konfigurowanie hybrydowego procesu roboczego elementu Runbook

Program Orchestrator przechowuje elementy Runbook na serwerze bazy danych i uruchamia je na serwerach Runbook, zarówno w lokalnym centrum wystawcy. Elementy Runbook w Azure Automation są przechowywane w chmurze platformy Azure i mogą być uruchamiane w lokalnym centrum danych przy użyciu [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md). Skonfiguruj proces roboczy do uruchamiania elementów Runbook przekonwertowanych z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych i uzyskiwania dostępu do zasobów lokalnych.

## <a name="related-articles"></a>Pokrewne artykuły:

* Aby uzyskać szczegółowe informacje dotyczące programu Orchestrator, zobacz [System Center 2012 — Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* Dowiedz się więcej o automatyzowaniu zarządzania usługami w [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)).
* Szczegóły działań programu Orchestrator można znaleźć w temacie [standardowe działania programu Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)).
* Aby uzyskać pakiet narzędzi do migracji programu Orchestrator, zobacz [Pobieranie programu System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* Aby zapoznać się z omówieniem Azure Automation hybrydowego procesu roboczego elementu Runbook, zobacz [Omówienie hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md).
