---
title: Migrowanie z programu Orchestrator do Azure Automation
description: Opisuje sposób migrowania elementów Runbook i pakietów integracyjnych z programu System Center Orchestrator do Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457556"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrowanie z programu Orchestrator do Azure Automation (wersja beta)

Elementy Runbook w programie [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) bazują na działaniach z pakietów integracyjnych, które są przeznaczone dla programu Orchestrator, podczas gdy elementy runbook w Azure Automation są oparte na programie Windows PowerShell.  [Graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) w Azure Automation mają podobny wygląd do elementów Runbook programu Orchestrator wraz z działaniami, które reprezentują polecenia cmdlet środowiska PowerShell, podrzędne elementy Runbook i zasoby.

Zestaw narzędzi do [migracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) zawiera narzędzia ułatwiające Konwertowanie elementów Runbook z programu Orchestrator do Azure Automation.  Oprócz konwertowania elementów Runbook, należy przekonwertować pakiety integracyjne na działania, których elementy Runbook używają do modułów integracji za pomocą poleceń cmdlet programu Windows PowerShell.  

Poniżej przedstawiono podstawowy proces konwersji elementów Runbook programu Orchestrator na Azure Automation.  Każdy z tych kroków został szczegółowo opisany w poniższych sekcjach.

1. Pobierz zestaw narzędzi do [migracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) zawierający narzędzia i moduły omówione w tym artykule.
2. Importuj [moduł działań standardowych](#standard-activities-module) do Azure Automation.  Obejmuje to przekonwertowane wersje standardowych działań programu Orchestrator, które mogą być używane przez przekonwertowane elementy Runbook.
3. Zaimportuj [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) do Azure Automation dla tych pakietów integracyjnych używanych przez elementy Runbook, które uzyskują dostęp do programu System Center.
4. Konwertowanie pakietów integracyjnych niestandardowych i innych firm przy użyciu [konwertera pakietu integracyjnego](#integration-pack-converter) i importowanie do Azure Automation.
5. Przekonwertuj elementy Runbook programu Orchestrator przy użyciu [konwertera elementów Runbook](#runbook-converter) i zainstaluj program w Azure Automation.
6. Ręcznie Utwórz wymagane zasoby programu Orchestrator w Azure Automation, ponieważ konwerter elementu Runbook nie konwertuje tych zasobów.
7. Skonfiguruj [hybrydowy proces roboczy elementu Runbook](#hybrid-runbook-worker) w lokalnym centrum danych, aby uruchomić przekonwertowane elementy Runbook, które będą uzyskiwać dostęp do zasobów lokalnych.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Instrukcje dotyczące instalacji polecenia AZ module w hybrydowym procesie roboczym elementu Runbook znajdują się w temacie [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). W przypadku konta usługi Automation można zaktualizować moduły do najnowszej wersji przy użyciu [sposobu aktualizowania modułów Azure PowerShell w programie Azure Automation](automation-update-azure-modules.md).

## <a name="service-management-automation"></a>Automatyzacja zarządzania usługami

[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) przechowuje i uruchamia elementy Runbook w lokalnym centrum danych, takim jak program Orchestrator, i używa tych samych modułów integracji co Azure Automation. [Konwerter elementów Runbook](#runbook-converter) konwertuje elementy Runbook programu Orchestrator na graficzne elementy Runbook, chociaż nie są obsługiwane w programie SMA.  Nadal można zainstalować [moduł działania standardowe](#standard-activities-module) i [moduły integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) w programie SMA, ale należy ręcznie [napisać elementy Runbook](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

Elementy Runbook w programie Orchestrator są przechowywane na serwerze bazy danych i uruchamiane na serwerach Runbook, zarówno w lokalnym centrum danych.  Elementy Runbook w Azure Automation są przechowywane w chmurze platformy Azure i mogą być uruchamiane w lokalnym centrum danych przy użyciu [hybrydowego procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md).  W ten sposób zwykle można uruchamiać elementy Runbook konwertowane z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych.

## <a name="integration-pack-converter"></a>Konwerter pakietu integracyjnego

Konwerter pakietu integracyjnego konwertuje pakiety integracyjne, które zostały utworzone przy użyciu [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) do modułów integracji opartych na programie Windows PowerShell, które można zaimportować do Azure Automation lub Service Management Automation.  

Po uruchomieniu konwertera pakietu integracyjnego zostanie wyświetlony Kreator, który umożliwi wybranie pliku pakietu integracyjnego (. OIP).  Następnie Kreator wyświetla listę działań objętych tym pakietem integracyjnym i umożliwia wybranie migracji.  Po zakończeniu pracy Kreatora tworzy moduł integracji zawierający odpowiednie polecenie cmdlet dla każdego działania w oryginalnym pakiecie integracyjnym.

### <a name="parameters"></a>Parametry

Wszystkie właściwości działania w pakiecie integracyjnym są konwertowane na parametry odpowiedniego polecenia cmdlet w module integracji.  Polecenia cmdlet programu Windows PowerShell mają zestaw [wspólnych parametrów](https://technet.microsoft.com/library/hh847884.aspx) , które mogą być używane z wszystkimi poleceniami cmdlet. Na przykład parametr-Verbose powoduje polecenie cmdlet do wyprowadzania szczegółowych informacji o jego operacji.  Żadne polecenie cmdlet nie może mieć parametru o takiej samej nazwie jak wspólny parametr. Jeśli działanie ma właściwość o tej samej nazwie co wspólny parametr, Kreator wyświetli monit o podanie innej nazwy dla parametru.

### <a name="monitor-activities"></a>Monitorowanie działań

Monitoruj elementy Runbook w programie Orchestrator Rozpocznij od [działania monitorowania](https://technet.microsoft.com/library/hh403827.aspx) i Kontynuuj, oczekujące na wywoływanie przez konkretne zdarzenie.  Azure Automation nie obsługuje elementów Runbook monitorowania, więc żadne działania dotyczące monitorowania w pakiecie integracyjnym nie zostaną przekonwertowane.  Zamiast tego w module integracji zostanie utworzone zastępcze polecenie cmdlet dla działania monitorowania.  To polecenie cmdlet nie ma funkcji, ale umożliwia zainstalowanie dowolnego przekonwertowanego elementu Runbook, który go używa.  Ten element Runbook nie będzie mógł działać w Azure Automation, ale można go zainstalować, aby można było go zmodyfikować.

### <a name="integration-packs-that-cannot-be-converted"></a>Pakiety integracyjne, których nie można przekonwertować

Pakietów integracyjnych, które nie zostały utworzone za pomocą OIT, nie można przekonwertować przy użyciu konwertera pakietu integracyjnego. Istnieją także pewne pakiety integracyjne udostępniane przez firmę Microsoft, które nie mogą być obecnie konwertowane za pomocą tego narzędzia.  Przekonwertowane wersje tych pakietów integracyjnych zostały [udostępnione do pobrania](#system-center-orchestrator-integration-modules) , aby można je było zainstalować w Azure Automation lub Service Management Automation.

## <a name="standard-activities-module"></a>Moduł działań standardowych

Program Orchestrator zawiera zestaw [działań standardowych](https://technet.microsoft.com/library/hh403832.aspx) , które nie są uwzględnione w pakiecie integracyjnym, ale są używane przez wiele elementów Runbook.  Moduł działania standardowe to moduł integracji, który zawiera odpowiedniki polecenia cmdlet dla każdej z tych działań.  Ten moduł integracji należy zainstalować w Azure Automation przed zaimportowaniem wszystkich przekonwertowanych elementów Runbook, które używają działania standardowego.

Oprócz obsługi przekonwertowanych elementów Runbook polecenia cmdlet w module działania standardowe mogą być używane przez kogoś znanego w programie Orchestrator do tworzenia nowych elementów Runbook w programie Azure Automation.  Chociaż funkcje wszystkich działań standardowych można wykonywać za pomocą poleceń cmdlet, mogą one działać inaczej.  Polecenia cmdlet w module przekonwertowane działania standardowe będą działały tak samo jak odpowiadające im działania i używają tych samych parametrów.  Może to pomóc istniejącemu autorowi elementu Runbook programu Orchestrator w przejściu do Azure Automation elementów Runbook.

## <a name="system-center-orchestrator-integration-modules"></a>Moduły integracji programu System Center Orchestrator

Firma Microsoft udostępnia [pakiety integracyjne](https://technet.microsoft.com/library/hh295851.aspx) do kompilowania elementów Runbook w celu zautomatyzowania składników programu System Center i innych produktów.  Niektóre z tych pakietów integracyjnych są obecnie oparte na OIT, ale obecnie nie można ich przekonwertować na moduły integracji ze względu na znane problemy.  [Moduły integracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) zawierają przekonwertowane wersje tych pakietów integracyjnych, które można zaimportować do Azure Automation i Service Management Automation.  

Wersja RTM tego narzędzia zawiera zaktualizowane wersje pakietów integracyjnych opartych na OIT, które można przekonwertować przy użyciu konwertera pakietu integracyjnego.  Zostaną również udostępnione wskazówki ułatwiające Konwertowanie elementów Runbook przy użyciu działań z pakietów integracyjnych, które nie są oparte na OIT.

## <a name="runbook-converter"></a>Konwerter elementów Runbook

Konwerter elementów Runbook konwertuje elementy Runbook programu Orchestrator na [graficzne elementy Runbook](automation-runbook-types.md#graphical-runbooks) , które można zaimportować do Azure Automation.  

Konwerter elementów Runbook został zaimplementowany jako moduł programu PowerShell z wywołanym `ConvertFrom-SCORunbook` poleceniem cmdlet, które wykonuje konwersję.  Po zainstalowaniu narzędzia zostanie utworzony skrót do sesji programu PowerShell ładującej polecenie cmdlet.   

Poniżej przedstawiono podstawowy proces konwersji elementu Runbook programu Orchestrator i zaimportowania go do Azure Automation.  W poniższych sekcjach znajdują się dalsze szczegółowe informacje na temat używania narzędzia i pracy z przekonwertowane elementy Runbook.

1. Wyeksportuj co najmniej jeden element Runbook z programu Orchestrator.
2. Uzyskaj moduły integracji dla wszystkich działań w elemencie Runbook.
3. Przekonwertuj elementy Runbook programu Orchestrator w wyeksportowanym pliku.
4. Przejrzyj informacje w dziennikach, aby zweryfikować konwersję i określić wymagane zadania ręczne.
5. Zaimportuj przekonwertowane elementy Runbook do Azure Automation.
6. Utwórz wszystkie wymagane zasoby w Azure Automation.
7. Edytuj element Runbook w Azure Automation, aby zmodyfikować wszystkie wymagane działania.

### <a name="using-runbook-converter"></a>Używanie konwertera elementów Runbook

Składnia polecenia **ConvertFrom-SCORunbook** jest następująca:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath — ścieżka do pliku eksportu zawierającego elementy Runbook do przekonwertowania.
* Rozdzielana przecinkami lista modułów integracji zawierających działania w elementach Runbook.
* OutputFolder — ścieżka do folderu w celu utworzenia przekonwertowanych graficznych elementów Runbook.

Następujące przykładowe polecenie konwertuje elementy Runbook w pliku eksportu o nazwie **Webrunbooks. ois_export**.  Te elementy Runbook używają Active Directory i Data Protection Manager pakietów integracyjnych.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Pliki dziennika

Konwerter elementu Runbook utworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowany element Runbook.  Jeśli pliki już istnieją, zostaną zastąpione informacjami z ostatniej konwersji.

| Plik | Spis treści |
|:--- |:--- |
| Konwerter elementu Runbook — postęp. log |Szczegółowe kroki konwersji, w tym informacje dotyczące poszczególnych działań, zostały pomyślnie przekonwertowane i ostrzeżenie dla każdego nieskonwertowanego działania. |
| Konwerter elementu Runbook — Summary. log |Podsumowanie ostatniej konwersji wraz z ostrzeżeniami i monitowanie zadań, które należy wykonać, takich jak tworzenie zmiennej wymaganej dla przekonwertowanego elementu Runbook. |

### <a name="exporting-runbooks-from-orchestrator"></a>Eksportowanie elementów Runbook z programu Orchestrator

Konwerter elementów Runbook współpracuje z plikiem eksportu z programu Orchestrator, który zawiera co najmniej jeden element Runbook.  Spowoduje to utworzenie odpowiedniego elementu Runbook Azure Automation dla każdego elementu Runbook programu Orchestrator w pliku eksportu.  

Aby wyeksportować element Runbook z programu Orchestrator, kliknij prawym przyciskiem myszy nazwę elementu Runbook w Runbook Designer a następnie wybierz pozycję **Eksportuj**.  Aby wyeksportować wszystkie elementy Runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu i wybierz pozycję **Eksportuj**.

### <a name="runbook-activities"></a>Działania elementu Runbook

Konwerter elementów Runbook konwertuje każde działanie w elemencie Runbook programu Orchestrator na odpowiednie działanie w Azure Automation.  W przypadku tych działań, których nie można przekonwertować, w elemencie Runbook jest tworzone działanie symbol zastępczy z tekstem ostrzegawczym.  Po zaimportowaniu przekonwertowanego elementu Runbook do Azure Automation należy zastąpić dowolne z tych działań prawidłowymi działaniami, które wykonują wymagane funkcje.

Wszystkie działania programu Orchestrator w [module działania standardowe](#standard-activities-module) zostaną przekonwertowane.  Istnieją pewne standardowe działania programu Orchestrator, które nie znajdują się w tym module, ale nie są konwertowane.  Na przykład nie `Send Platform Event` ma Azure Automation równoważnej, ponieważ zdarzenie jest specyficzne dla programu Orchestrator.

[Działania monitorowania](https://technet.microsoft.com/library/hh403827.aspx) nie są konwertowane, ponieważ nie ma ich odpowiedników w Azure Automation.  Wyjątkiem są działania monitorowania w [przekonwertowanych pakietach integracyjnych](#integration-pack-converter) , które zostaną przekonwertowane na działanie symbolu zastępczego.

Wszelkie działania z [przekonwertowanego pakietu integracyjnego](#integration-pack-converter) zostaną przekonwertowane, jeśli podano ścieżkę do modułu integracji z `modules` parametrem. Pakiety integracyjne programu System Center umożliwiają korzystanie z [modułów integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Zasoby programu Orchestrator

Konwerter elementu Runbook konwertuje tylko elementy Runbook, nie inne zasoby programu Orchestrator, takie jak liczniki, zmienne lub połączenia.  Liczniki nie są obsługiwane w Azure Automation.  Obsługiwane są zmienne i połączenia, ale należy je utworzyć ręcznie.  Pliki dziennika będą informować o tym, że element Runbook wymaga takich zasobów i określisz odpowiednie zasoby, które należy utworzyć w Azure Automation, aby przekonwertowane elementy Runbook działały prawidłowo.

Na przykład element Runbook może użyć zmiennej, aby wypełnić określoną wartość w działaniu.  Przekonwertowany element Runbook wykona konwersję działania i określi zmienną zasobów w Azure Automation z taką samą nazwą jak zmienna programu Orchestrator.  Zostanie to odnotowane w pliku programu **Runbook Converter-Summary. log** , który został utworzony po konwersji.  Należy ręcznie utworzyć ten zasób zmiennej w Azure Automation przed użyciem elementu Runbook.

### <a name="input-parameters"></a>Parametry wejściowe

Elementy Runbook w programie Orchestrator akceptują parametry wejściowe `Initialize Data` z działaniem.  Jeśli element Runbook, który jest konwertowany, obejmuje to działanie, dla każdego parametru w działaniu jest tworzony [parametr wejściowy](automation-graphical-authoring-intro.md#runbook-input-and-output) w Azure Automation elemencie Runbook.  Działanie [sterujące skryptu przepływu pracy](automation-graphical-authoring-intro.md#activities) jest tworzone w przekonwertowanym elemencie Runbook, który pobiera i zwraca każdy parametr.  Wszystkie działania w elemencie Runbook, które używają parametru wejściowego, odwołują się do danych wyjściowych tego działania.

Ta strategia jest używana w celu najlepszego dublowania funkcji w elemencie Runbook programu Orchestrator.  Działania w nowych graficznych elementach Runbook powinny odwoływać się bezpośrednio do parametrów wejściowych przy użyciu źródła danych wejściowych elementu Runbook.

### <a name="invoke-runbook-activity"></a>Wywołaj działanie elementu Runbook

Elementy Runbook w programie Orchestrator uruchamiają inne elementy Runbook `Invoke Runbook` za pomocą działania. Jeśli element Runbook, który jest konwertowany, obejmuje to `Wait for completion` działanie, a opcja jest ustawiona, dla niego zostanie utworzone działanie elementu Runbook w przekonwertowanym elemencie Runbook.  Jeśli ta `Wait for completion` opcja nie jest ustawiona, zostanie utworzone działanie skryptu przepływu pracy z poleceniem [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) w celu uruchomienia elementu Runbook. Po zaimportowaniu przekonwertowanego elementu Runbook do Azure Automation należy zmodyfikować to działanie za pomocą informacji określonych w działaniu.

## <a name="related-articles"></a>Pokrewne artykuły:

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Automatyzacja zarządzania usługami](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md)
* [Działania standardowe programu Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Pobierz pakiet narzędzi do migracji programu System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

