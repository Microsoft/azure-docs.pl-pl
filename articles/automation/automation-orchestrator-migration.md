---
title: Migrowanie z programu Orchestrator do usługi Azure Automation
description: W tym artykule opisano sposób migracji śmiób i pakietów integracyjnych z programu System Center Orchestrator do usługi Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: c7df6e31cd021fc61129131f9bd02acc7b96e2ad
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457556"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migracja z programu Orchestrator do usługi Azure Automation (beta)

Elementy runbook w [programie Program System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) są oparte na działaniach z pakietów integracji, które są napisane specjalnie dla programu Orchestrator, podczas gdy elementy runbook w usłudze Azure Automation są oparte na programie Windows PowerShell.  [Graficzne elementy runbook](automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation mają podobny wygląd do śmięty programu Orchestrator z ich działaniami reprezentującymi polecenia cmdlet programu PowerShell, podrzędne elementy runbook i zasoby.

[Zestaw narzędzi do migracji programu System Center Orchestrator zawiera](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) narzędzia ułatwiające konwertowanie śmięty z programu Orchestrator na usługę Azure Automation.  Oprócz konwersji samych śmięty czynów należy przekonwertować pakiety integracyjne z działaniami używanymi przez programy runbook na moduły integracji z poleceniami cmdlet programu Windows PowerShell.  

Poniżej przedstawiono podstawowy proces konwersji elementów runbook programu Orchestrator do usługi Azure Automation.  Każdy z tych kroków jest szczegółowo opisany w poniższych sekcjach.

1. Pobierz [zestaw narzędzi do migracji programu System Center Orchestrator,](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) który zawiera narzędzia i moduły omówione w tym artykule.
2. Importowanie [modułu działań standardowych](#standard-activities-module) do automatyzacji platformy Azure.  Obejmuje to przekonwertowane wersje standardowych działań programu Orchestrator, które mogą być używane przez przekonwertowane runbooki.
3. Importowanie [modułów integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) do usługi Azure Automation dla pakietów integracyjnych używanych przez elementy runbook, które uzyskują dostęp do centrum systemowego.
4. Konwertuj niestandardowe i zewnętrzne pakiety integracyjne przy użyciu [integration pack converter](#integration-pack-converter) i importuj do usługi Azure Automation.
5. Konwertuj elementy runbook programu Orchestrator przy użyciu [konwertera żyła](#runbook-converter) i instaluj w usłudze Azure Automation.
6. Ręcznie utwórz wymagane zasoby programu Orchestrator w usłudze Azure Automation, ponieważ konwerter uruchomieniu nie konwertuje te zasoby.
7. Skonfiguruj [hybrydowy proces roboczy żyła w](#hybrid-runbook-worker) lokalnym centrum danych, aby uruchamiały przekonwertowane programy runbook, które będą uzyskiwać dostęp do zasobów lokalnych.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="service-management-automation"></a>Automatyzacja zarządzania usługami

[Automatyzacja zarządzania usługami](https://technet.microsoft.com/library/dn469260.aspx) (SMA) przechowuje i uruchamia elementy runbook w lokalnym centrum danych, takim jak Orchestrator, i używa tych samych modułów integracji, co usługa Azure Automation. [Konwerter ekscesów](#runbook-converter) konwertuje runbooki programu Orchestrator na graficzne programy runbook, które nie są obsługiwane w SMA.  Nadal można zainstalować [moduły integracji standardowych działań](#standard-activities-module) i [modułów integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules) w sma, ale należy ręcznie [przepisać runbooks](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Hybrydowy proces roboczy elementu Runbook

Elementy runbook w programie Orchestrator są przechowywane na serwerze bazy danych i uruchamiane na serwerach umownie, zarówno w lokalnym centrum danych.  Elementy runbook w usłudze Azure Automation są przechowywane w chmurze platformy Azure i mogą działać w lokalnym centrum danych przy użyciu [hybrydowego procesu roboczego systemu runbook.](automation-hybrid-runbook-worker.md)  W ten sposób zwykle uruchamiane są księgi runbook konwertowane z programu Orchestrator, ponieważ są one przeznaczone do uruchamiania na serwerach lokalnych.

## <a name="integration-pack-converter"></a>Konwerter pakietów integracyjnych

Integration Pack Converter konwertuje pakiety integracji, które zostały utworzone przy użyciu [zestawu narzędzi integracji programu Orchestrator (OIT)](https://technet.microsoft.com/library/hh855853.aspx) na moduły integracji oparte na programie Windows PowerShell, które można zaimportować do usługi Azure Automation lub automatyzacji zarządzania usługami.  

Po uruchomieniu konwertera pakietu integracyjnego zostanie wyświetlony kreator, który pozwoli na wybranie pliku pakietu integracyjnego (oip).  Następnie kreator wyświetla listę działań zawartych w tym pakiecie integracyjnym i umożliwia wybranie, które zostaną zmigrowane.  Po zakończeniu pracy kreatora tworzy moduł integracji, który zawiera odpowiednie polecenie cmdlet dla każdego działania w oryginalnym pakiecie integracyjnym.

### <a name="parameters"></a>Parametry

Wszelkie właściwości działania w pakiecie integracyjnym są konwertowane na parametry odpowiedniego polecenia cmdlet w module integracji.  Polecenia cmdlet programu Windows PowerShell mają zestaw [typowych parametrów,](https://technet.microsoft.com/library/hh847884.aspx) które mogą być używane ze wszystkimi poleceniami cmdlet. Na przykład parametr -Verbose powoduje, że polecenie cmdlet powoduje, że szczegółowe informacje o jego działaniu są wyświetlane.  Żadne polecenie cmdlet nie może mieć parametru o takiej samej nazwie jak wspólny parametr. Jeśli działanie ma właściwość o takiej samej nazwie jak parametr wspólny, kreator wyświetli monit o podanie innej nazwy parametru.

### <a name="monitor-activities"></a>Monitorowanie działań

Monitorowanie śg w programie Orchestrator rozpoczyna się od [aktywności monitora](https://technet.microsoft.com/library/hh403827.aspx) i uruchamia stale oczekuje na wywołanie przez określone zdarzenie.  Usługa Azure Automation nie obsługuje śmięty monitorów, więc wszelkie działania monitora w pakiecie integracyjnym nie zostaną przekonwertowane.  Zamiast tego polecenie cmdlet zastępcze jest tworzony w module integracji dla działania monitora.  To polecenie cmdlet nie ma funkcji, ale umożliwia zainstalowanie dowolnego przekonwertowanego zestawu runbook, który go używa.  Ten system runbook nie będzie można uruchomić w usłudze Azure Automation, ale można go zainstalować, dzięki czemu można go zmodyfikować.

### <a name="integration-packs-that-cannot-be-converted"></a>Pakiety integracyjne, których nie można przekonwertować

Pakietów integracyjnych, które nie zostały utworzone za pomocą OIT nie można przekonwertować za pomocą integration pack converter. Istnieje również kilka pakietów integracyjnych dostarczonych przez firmę Microsoft, które obecnie nie mogą być konwertowane za pomocą tego narzędzia.  Przekonwertowane wersje tych pakietów integracyjnych zostały [dostarczone do pobrania,](#system-center-orchestrator-integration-modules) dzięki czemu można je zainstalować w usłudze Azure Automation lub automatyzacji zarządzania usługami.

## <a name="standard-activities-module"></a>Moduł działań standardowych

Orchestrator zawiera zestaw [standardowych działań,](https://technet.microsoft.com/library/hh403832.aspx) które nie są zawarte w pakiecie integracji, ale są używane przez wiele śmięty.  Standard Działania moduł jest moduł integracji, który zawiera odpowiednik polecenia cmdlet dla każdego z tych działań.  Należy zainstalować ten moduł integracji w usłudze Azure Automation przed zaimportowanie wszelkich przekonwertowanych żyjących niestandardowych niestandardowych, które używają działania standardowego.

Oprócz obsługi przekonwertowanych śmięty, polecenia cmdlet w module działań standardowych mogą być używane przez osobę zaznajomioną z programem Orchestrator do tworzenia nowych śmięty w usłudze Azure Automation.  Podczas gdy funkcjonalność wszystkich standardowych działań może być wykonywana za pomocą poleceń cmdlet, mogą one działać inaczej.  Polecenia cmdlet w module przekonwertowanych działań standardowych będą działać tak samo jak ich odpowiednie działania i będą używać tych samych parametrów.  Może to pomóc istniejącemu autorowi uruchomieniu programu Orchestrator w ich przejściu do śmięty systemu Azure Automation.

## <a name="system-center-orchestrator-integration-modules"></a>Moduły integracji programu System Center Orchestrator

Firma Microsoft udostępnia [pakiety integracyjne](https://technet.microsoft.com/library/hh295851.aspx) do tworzenia elementów runbook w celu automatyzacji składników programu System Center i innych produktów.  Niektóre z tych pakietów integracyjnych są obecnie oparte na OIT, ale obecnie nie można ich przekonwertować na moduły integracji z powodu znanych problemów.  [Moduły integracji programu System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) zawierają przekonwertowane wersje tych pakietów integracyjnych, które można zaimportować do automatyzacji automatyzacji automatyzacji automatyzacji automatyzacji usług Azure i zarządzania usługami.  

W wersji RTM tego narzędzia zostaną opublikowane zaktualizowane wersje pakietów integracyjnych opartych na OIT, które można przekonwertować za pomocą integration pack converter.  Wskazówki zostaną również dostarczone, aby pomóc w konwersji śmięty przy użyciu działań z pakietów integracyjnych nie opartych na OIT.

## <a name="runbook-converter"></a>Konwerter ekscesu

Konwerter ujeżdna jest konwertowany programem Runbook na [graficzne elementy runbook,](automation-runbook-types.md#graphical-runbooks) które można zaimportować do usługi Azure Automation.  

Runbook Converter jest zaimplementowany jako moduł programu `ConvertFrom-SCORunbook` PowerShell z poleceniem cmdlet wywołanym, który wykonuje konwersję.  Po zainstalowaniu narzędzia utworzy skrót do sesji programu PowerShell, która ładuje polecenie cmdlet.   

Poniżej przedstawiono podstawowy proces konwersji podręcznika runtora i zaimportowania go do usługi Azure Automation.  W poniższych sekcjach podano więcej informacji na temat korzystania z narzędzia i pracy z przekonwertowanymi grupami runbook.

1. Eksportuj jeden lub więcej śmięty śmiętu z programu Orchestrator.
2. Uzyskaj moduły integracji dla wszystkich działań w biełgocie.
3. Konwertuj runbooki programu Orchestrator w wyeksportowanym pliku.
4. Przejrzyj informacje w dziennikach, aby sprawdzić poprawność konwersji i określić wszelkie wymagane zadania ręczne.
5. Importowanie przekonwertowanych liczeń ekwiwalom do usługi Azure Automation.
6. Utwórz wszystkie wymagane zasoby w usłudze Azure Automation.
7. Edytuj projekt runbook w usłudze Azure Automation, aby zmodyfikować wszystkie wymagane działania.

### <a name="using-runbook-converter"></a>Korzystanie z konwertera żyła

Składnia **convertfrom-SCORunbook** jest następująca:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath — ścieżka do pliku eksportu zawierającego pliki runbook do konwersji.
* Moduł — rozdzielona przez przecinek lista modułów integracyjnych zawierających działania w podręcznikach runbook.
* OutputFolder - Ścieżka do folderu w celu utworzenia przekonwertowanych wiązek graficznych.

Poniższe przykładowe polecenie konwertuje elementy runbook w pliku eksportu o nazwie **MyRunbooks.ois_export**.  Te zestawy runbook używają pakietów integracyjnych Usługi Active Directory i Programu Ochrony Danych.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Pliki dziennika

Konwerter elementów Runbook utworzy następujące pliki dziennika w tej samej lokalizacji co przekonwertowany projekt runbook.  Jeśli pliki już istnieją, zostaną one zastąpione informacjami z ostatniej konwersji.

| Plik | Spis treści |
|:--- |:--- |
| Konwerter eksmisji — Progress.log |Szczegółowe kroki konwersji, w tym informacje dla każdego działania pomyślnie przekonwertowane i ostrzeżenie dla każdego działania nie konwertowane. |
| Konwerter ekscesu — Summary.log |Podsumowanie ostatniej konwersji, w tym ostrzeżenia i zadania uzupełniające, które należy wykonać, takie jak tworzenie zmiennej wymaganej dla przekonwertowanego żyjącego. |

### <a name="exporting-runbooks-from-orchestrator"></a>Eksportowanie ekscesów z programu Orchestrator

Konwerter kreślnika systemu działa z plikiem eksportu z programu Orchestrator, który zawiera co najmniej jeden element runbook.  Utworzy odpowiedni podręcznik runbook usługi Azure Automation dla każdego runbooka orchestrator w pliku eksportu.  

Aby wyeksportować projekt runbooka z programu Orchestrator, kliknij prawym przyciskiem myszy nazwę likwidatora w Projektancie liksu i wybierz polecenie **Eksportuj**.  Aby wyeksportować wszystkie programy runbook w folderze, kliknij prawym przyciskiem myszy nazwę folderu i wybierz polecenie **Eksportuj**.

### <a name="runbook-activities"></a>Działania elementu Runbook

Konwerter yskonwerterzy każdego działania w dzienniku runtora orchestrator do odpowiedniego działania w usłudze Azure Automation.  Dla tych działań, których nie można przekonwertować, w liczeniu runbook tworzone jest działanie zastępcze z tekstem ostrzegawczym.  Po zaimportowaniu przekonwertowanego uruchomienia do usługi Azure Automation należy zastąpić dowolną z tych działań prawidłowymi działaniami, które wykonują wymagane funkcje.

Wszystkie działania koordynatora w [module działania standardowe](#standard-activities-module) zostaną przekonwertowane.  Istnieją pewne standardowe działania Orchestrator, które nie są w tym module choć i nie są konwertowane.  Na przykład `Send Platform Event` nie ma odpowiednika usługi Azure Automation, ponieważ zdarzenie jest specyficzne dla programu Orchestrator.

[Monitorowanie działań](https://technet.microsoft.com/library/hh403827.aspx) nie są konwertowane, ponieważ nie ma odpowiednika z nich w usłudze Azure Automation.  Wyjątkiem są działania monitorowania w [przekonwertowanych pakietach integracyjnych,](#integration-pack-converter) które zostaną przekonwertowane na działanie zastępcze.

Każde działanie z [przekonwertowanego pakietu integracyjnego](#integration-pack-converter) zostanie przekonwertowane, jeśli podasz ścieżkę do modułu integracji z parametrem. `modules` W przypadku pakietów integracji systemu Center można użyć [modułów integracji programu System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Zasoby orkiestratora

Konwerter yskonwerterów tylko storonuje runbooki, a nie inne zasoby programu Orchestrator, takie jak liczniki, zmienne lub połączenia.  Liczniki nie są obsługiwane w usłudze Azure Automation.  Zmienne i połączenia są obsługiwane, ale należy je utworzyć ręcznie.  Pliki dziennika poinformuje Cię, jeśli runbook wymaga takich zasobów i określić odpowiednie zasoby, które należy utworzyć w usłudze Azure Automation dla przekonwertowanego systemu runbook do poprawnego działania.

Na przykład element runbook może użyć zmiennej do wypełniania określonej wartości w działaniu.  Przekonwertowany projekt runbook przekonwertuje działanie i określi zasób zmiennej w usłudze Azure Automation o takiej samej nazwie jak zmienna Orchestrator.  Zostanie to odnotowane w pliku **Runbook Converter - Summary.log,** który jest tworzony po konwersji.  Przed użyciem systemu runbook należy ręcznie utworzyć ten zasób zmiennej w usłudze Azure Automation.

### <a name="input-parameters"></a>Parametry wejściowe

Runbooks w Orchestrator akceptować `Initialize Data` parametry wejściowe z działania.  Jeśli element runbook konwertowane obejmuje to działanie, [parametr wejściowy](automation-graphical-authoring-intro.md#runbook-input-and-output) w uruchomieniu usługi Azure Automation jest tworzony dla każdego parametru w działaniu.  Działanie [kontroli skryptu przepływu pracy](automation-graphical-authoring-intro.md#activities) jest tworzone w przekonwertowanym uruchomieniu, który pobiera i zwraca każdy parametr.  Wszelkie działania w żyłaku runbook, które używają parametru wejściowego odnoszą się do danych wyjściowych z tego działania.

Powodem, dla którego ta strategia jest używana jest najlepsze dublowanie funkcji w podręczniku orchestrator.  Działania w nowych elementach runbook graficznych powinny odnosić się bezpośrednio do parametrów wejściowych przy użyciu źródła danych wejściowych programu Runbook.

### <a name="invoke-runbook-activity"></a>Wywoływanie działania żyłajnika

Runbooks w Orchestrator uruchomić inne `Invoke Runbook` runbooks z działania. Jeśli konwertowany system runbook `Wait for completion` zawiera to działanie i jest ustawiona opcja, w przekonwertowanym zestawieniu zostanie utworzone działanie zestawu runbook.  Jeśli `Wait for completion` opcja nie jest ustawiona, zostanie utworzone działanie skryptu przepływu pracy, które używa [książki Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) do uruchomienia księgi runbook. Po zaimportowaniu przekonwertowanego śmigieł do usługi Azure Automation należy zmodyfikować to działanie z informacjami określonymi w działaniu.

## <a name="related-articles"></a>Pokrewne artykuły:

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Automatyzacja zarządzania usługami](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md)
* [Standardowe działania orkiestratora](https://technet.microsoft.com/library/hh403832.aspx)
* [Pobierz zestaw narzędzi do migracji programu Program Orchestrator centrum systemu](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

