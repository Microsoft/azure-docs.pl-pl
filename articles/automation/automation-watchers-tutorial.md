---
title: Tworzenie zadania obserwatora na koncie usługi Azure Automation
description: Dowiedz się, jak utworzyć zadanie obserwatora na koncie usługi Azure Automation, aby obserwować nowe pliki utworzone w folderze.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617354"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Tworzenie zadań obserwatora usługi Azure Automation w celu śledzenia zmian w plikach na komputerze lokalnym

Usługa Azure Automation używa zadania obserwatora do wyszukiwania zdarzeń i wyzwalania akcji za pomocą śmięty programu PowerShell. Zadanie obserwatora składa się z dwóch części, obserwatora i akcji. Runbook obserwatora jest uruchamiany w odstępie czasu zdefiniowanym w zadaniu obserwatora i wyprowadza dane do kreślnika akcji. 

W tym samouczku można utworzyć zadanie obserwatora do monitorowania, gdy nowy plik zostanie dodany do katalogu. Omawiane kwestie:

> [!div class="checklist"]
> * Importowanie runbooka obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie śmiętu akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdź wyjście

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto automatyzacji](automation-offering-get-started.md) do przechowywania książki ręcznej i działania oraz zadania czujki.
* [Hybrydowy proces roboczy uruchomieniu,](automation-hybrid-runbook-worker.md) w którym jest uruchamiane zadanie obserwatora.
* Runbooki programu PowerShell. Runbooki przepływu pracy programu PowerShell nie są obsługiwane przez zadania obserwatora.

> [!NOTE]
> Zadania obserwatora nie są obsługiwane w Chinach platformy Azure.

## <a name="import-a-watcher-runbook"></a>Importowanie runbooka obserwatora

W tym samouczku użyto runbooka obserwatora o nazwie **Watch-NewFile** do wyszukiwania nowych plików w katalogu. Runbook obserwatora pobiera ostatni znany czas zapisu do plików w folderze i patrzy na wszystkie pliki nowsze niż ten znak wodny.

Ten proces importowania można wykonać za pośrednictwem [galerii programu PowerShell](https://www.powershellgallery.com).

1. Przejdź do strony galerii [watch-newfile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. W obszarze **karty Automatyzacja platformy Azure** kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

Można również zaimportować ten numer życiornik do konta automatyzacji z portalu, wykonując następujące kroki.

1. Otwórz konto automatyzacji i kliknij stronę Runbooks.
2. Kliknij **pozycję Przeglądaj galerię**.
3. Wyszukaj **runbooka Czujka**, wybierz pozycję **Runbook Watcher, który wyszukuje nowe pliki w katalogu,** a następnie kliknij przycisk **Importuj**.
  ![Importowanie interfejsu użytkownika z interfejsu użytkownika automatyzacji](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Nadaj zestawowi runbook nazwę i opis, a następnie kliknij przycisk **OK,** aby zaimportować program runbook do konta automatyzacji.
5. Wybierz **pozycję Edytuj,** a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu kliknij przycisk **Tak,** aby opublikować projekt runbook.

## <a name="create-an-automation-variable"></a>Tworzenie zmiennej automatyzacji

[Zmienna automatyzacji](automation-variables.md) służy do przechowywania sygnatur czasowych, które poprzedni element runbook odczytuje i przechowuje z każdego pliku.

1. Wybierz **pozycję Zmienne** w obszarze **Zasoby udostępnione** i kliknij pozycję + Dodaj **zmienną**.
1. Wprowadź Watch-NewFileTimestamp dla nazwy.
1. Wybierz DateTime dla typu.
1. Kliknij **przycisk Utwórz,** aby utworzyć zmienną Automatyzacja.

## <a name="create-an-action-runbook"></a>Tworzenie śmiętu akcji

Element runbook akcji jest używany w zadaniu obserwatora do działania na danych przekazanych do niego z runbooka obserwatora. Należy zaimportować wstępnie zdefiniowany projekt runbook akcji o nazwie **Process-NewFile** z [Galerii programu PowerShell](https://www.powershellgallery.com). 

Aby utworzyć element runbook akcji:

1. Przejdź do strony galerii [process-newfile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. W obszarze **karty Automatyzacja platformy Azure** kliknij pozycję **Wdrażanie w usłudze Azure Automation**.

Można również zaimportować ten numer ekwidu do konta automatyzacji z witryny Azure portal:

1. Przejdź do konta automatyzacji i wybierz **elementy runbook w** obszarze **Automatyzacja procesów**.
1. Kliknij **pozycję Przeglądaj galerię**.
1. Wyszukaj **akcję Czujka**, wybierz **akcję Czujka, która przetwarza zdarzenia wyzwalane przez runbook obserwatora,** a następnie kliknij przycisk **Importuj**.
  ![Importowanie umniejszania akcji z interfejsu użytkownika](media/automation-watchers-tutorial/importsourceaction.png)
1. Nadaj zestawowi runbook nazwę i opis, a następnie kliknij przycisk **OK,** aby zaimportować program runbook do konta automatyzacji.
1. Wybierz **pozycję Edytuj,** a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu kliknij przycisk **Tak,** aby opublikować projekt runbook.

## <a name="create-a-watcher-task"></a>Tworzenie zadania obserwatora

W tym kroku można skonfigurować zadanie obserwatora odwołujące się do obserwatora i runbooków akcji zdefiniowanych w poprzednich sekcjach.

1. Przejdź do konta automatyzacji i wybierz pozycję **Zadania obserwatora** w obszarze **Automatyzacja procesów**.
1. Wybierz stronę Zadania obserwatora i kliknij **przycisk + Dodaj zadanie obserwatora**.
1. Wprowadź **watchmyfolder** jako nazwę.

1. Wybierz **pozycję Konfiguruj obserwatora** i wybierz runbook **Watch-NewFile.**

1. Wprowadź następujące wartości parametrów:

   * **FOLDERPATH** — folder w hybrydowym ustroju roboczym stroju runbooka, w którym tworzone są nowe pliki, na przykład **d:\examplefiles**.
   * **ROZSZERZENIE** — rozszerzenie dla konfiguracji. Pozostaw puste miejsce, aby przetworzyć wszystkie rozszerzenia plików.
   * **RECURSE** - Operacja cykliczna. Pozostaw tę wartość jako wartość domyślną.
   * **USTAWIENIA URUCHAMIANIA** — ustawienie uruchamiania łańszka runbooka. Wybierz proces pracy hybrydowej.

1. Kliknij **przycisk OK**, a następnie **wybierz,** aby powrócić do strony Obserwator.
1. Wybierz **pozycję Konfiguruj akcję** i wybierz runbook **Process-NewFile.**
1. Wprowadź następujące wartości parametrów:

   * **EVENTDATA** - Dane zdarzeń. Pozostaw to pole puste. Dane są przekazywane z runbooka obserwatora.
   * **Uruchom ustawienia** — ustawienie uruchamiania łańszka runbooka. Pozostaw jako platforma Azure, ponieważ ten projekt runbook jest uruchamiany w usłudze Azure Automation.

1. Kliknij **przycisk OK**, a następnie **wybierz,** aby powrócić do strony Obserwator.
1. Kliknij **przycisk OK,** aby utworzyć zadanie obserwatora.

![Konfigurowanie akcji obserwatora z interfejsu użytkownika](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Wyzwalanie obserwatora

Należy uruchomić test, jak opisano poniżej, aby upewnić się, że zadanie obserwatora działa zgodnie z oczekiwaniami. 

1. Zdalnie do hybrydowego procesu roboczego uruchomieniu. 
2. Otwórz **program PowerShell** i utwórz plik testowy w folderze.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Poniższy przykład przedstawia oczekiwane dane wyjściowe.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Sprawdź wyjście

1. Przejdź do konta automatyzacji i wybierz pozycję **Zadania obserwatora** w obszarze **Automatyzacja procesów**.
1. Wybierz zadanie obserwatora **WatchMyFolder**.
1. Kliknij **wyświetl strumienie obserwatora** w obszarze **Strumienie,** aby zobaczyć, że obserwator znalazł nowy plik i rozpoczął runbook akcji.
1. Aby wyświetlić zadania likpoglądu akcji, kliknij **pozycję Wyświetl zadania akcji obserwatora**. Każde zadanie można wybrać, aby wyświetlić szczegóły zadania.

   ![Zadania działania obserwatora z interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe po znalezieniu nowego pliku można zobaczyć w poniższym przykładzie:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie runbooka obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie śmiętu akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdź wyjście

Kliknij ten link, aby dowiedzieć się więcej o tworzenie własnego autorskie.. .

> [!div class="nextstepaction"]
> [Mój pierwszy program runbook programu PowerShell](automation-first-runbook-textual-powershell.md).

