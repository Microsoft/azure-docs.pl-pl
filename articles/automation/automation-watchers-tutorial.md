---
title: Śledzenie zaktualizowanych plików za pomocą zadania obserwatora Azure Automation
description: W tym artykule opisano sposób tworzenia zadania obserwatora na koncie Azure Automation, aby obejrzeć nowe pliki utworzone w folderze.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 38963a8e1bfdbde50439ed871aa33e9aaa830d35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185657"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Śledzenie zaktualizowanych plików przy użyciu zadania obserwatora

Azure Automation używa zadania obserwatora, aby wyszukać zdarzenia i wyzwalać akcje przy użyciu elementów Runbook programu PowerShell. Zadanie obserwatora zawiera dwie części, obserwatora i akcję. Element Runbook obserwatora działa w interwale zdefiniowanym w zadaniu obserwatora i wyprowadza dane do elementu Runbook akcji. 

> [!NOTE]
> Zadania obserwatora nie są obsługiwane w przypadku platformy Azure w Chinach Vianet 21.

> [!IMPORTANT]
> Począwszy od maja 2020, używanie Azure Logic Apps jest obsługiwanym sposobem monitorowania zdarzeń, planowania cyklicznych zadań i wyzwalania akcji. Zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ten samouczek przeprowadzi Cię przez proces tworzenia zadania obserwatora do monitorowania po dodaniu nowego pliku do katalogu. Omawiane kwestie:

> [!div class="checklist"]
> * Importuj element Runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu Runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdzanie danych wyjściowych

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności przedstawionych w tym samouczku są wymagane następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md) , w którym jest uruchamiane zadanie obserwatora.
* Elementy Runbook programu PowerShell. Elementy Runbook przepływu pracy programu PowerShell nie są obsługiwane przez zadania obserwatora.

## <a name="import-a-watcher-runbook"></a>Importuj element Runbook obserwatora

Ten samouczek używa elementu Runbook obserwatora o nazwie **Watch-NewFile** , aby wyszukać nowe pliki w katalogu. Element Runbook obserwatora Pobiera ostatni znany czas zapisu do plików w folderze i szuka plików nowszych niż ten znak wodny.

Ten proces importowania można wykonać za pomocą [Galeria programu PowerShell](https://www.powershellgallery.com).

1. Przejdź do strony Galeria, aby uzyskać [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Na karcie **Azure Automation** kliknij pozycję **wdróż, aby Azure Automation**.

Możesz również zaimportować ten element Runbook do konta usługi Automation z portalu, wykonując poniższe kroki.

1. Otwórz konto usługi Automation, a następnie kliknij stronę elementy Runbook.
2. Kliknij pozycję **Przeglądaj Galerię**.
3. Wyszukaj **element Runbook obserwatora**, wybierz **element Runbook obserwatora, który wyszukuje nowe pliki w katalogu**, a następnie kliknij pozycję **Importuj**.
  ![Importuj element Runbook usługi Automation z interfejsu użytkownika](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Nadaj elementowi Runbook nazwę i opis, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.
5. Wybierz pozycję **Edytuj** , a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="create-an-automation-variable"></a>Tworzenie zmiennej automatyzacji

[Zmienna automatyzacji](./shared-resources/variables.md) służy do przechowywania sygnatur czasowych, które poprzedni element Runbook odczytuje i zapisuje z każdego pliku.

1. Wybierz **zmienne** w obszarze **zasoby udostępnione** i kliknij pozycję **+ Dodaj zmienną**.
1. Wprowadź Watch-NewFileTimestamp dla nazwy.
1. Dla tego typu wybierz pozycję Data i godzina.
1. Kliknij przycisk **Utwórz** , aby utworzyć zmienną automatyzacji.

## <a name="create-an-action-runbook"></a>Tworzenie elementu Runbook akcji

Element Runbook akcji jest używany w zadaniu obserwatora do działania na danych przesłanych do niego z elementu Runbook obserwatora. Należy zaimportować wstępnie zdefiniowany element Runbook Action o nazwie **Process-NewFile** z [Galeria programu PowerShell](https://www.powershellgallery.com). 

Aby utworzyć element Runbook akcji:

1. Przejdź do strony Galeria, aby uzyskać [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Na karcie **Azure Automation** kliknij pozycję **wdróż, aby Azure Automation**.

Możesz również zaimportować ten element Runbook do konta usługi Automation z Azure Portal:

1. Przejdź do konta usługi Automation i wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Kliknij pozycję **Przeglądaj Galerię**.
1. Wyszukaj **akcję obserwatora**, wybierz **akcję obserwatora, która przetwarza zdarzenia wyzwalane przez element Runbook obserwatora**, a następnie kliknij przycisk **Importuj**.
  ![Importuj element Runbook akcji z interfejsu użytkownika](media/automation-watchers-tutorial/importsourceaction.png)
1. Nadaj elementowi Runbook nazwę i opis, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.
1. Wybierz pozycję **Edytuj** , a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="create-a-watcher-task"></a>Tworzenie zadania obserwatora

W tym kroku należy skonfigurować zadanie obserwatora odwołujące się do obserwatora i elementów Runbook akcji zdefiniowanych w powyższych sekcjach.

1. Przejdź do konta usługi Automation i wybierz **zadania obserwatora** w obszarze **Automatyzacja procesu**.
1. Wybierz stronę zadania obserwatora i kliknij pozycję **+ Dodaj zadanie obserwatora**.
1. Wprowadź **WatchMyFolder** jako nazwę.

1. Wybierz pozycję **Konfiguruj obserwatora** i wybierz element Runbook **Watch-NewFile** .

1. Wprowadź następujące wartości parametrów:

   * **FOLDERPATH** — folder w hybrydowym procesie roboczym elementu Runbook, w którym są tworzone nowe pliki, na przykład **d:\examplefiles**.
   * **Rozszerzenie-rozszerzenie** dla konfiguracji. Pozostaw to pole puste, aby przetworzyć wszystkie rozszerzenia plików.
   * **Rekursywnie — operacja** cykliczna. Pozostaw tę wartość jako domyślną.
   * **Parametry uruchomieniowe** — ustawienie do uruchamiania elementu Runbook. Wybierz hybrydowy proces roboczy.

1. Kliknij przycisk **OK**, a następnie **Wybierz opcję** powrotu do strony obserwatora.
1. Wybierz pozycję **Konfiguruj akcję** i wybierz element Runbook **Process-NewFile** .
1. Wprowadź następujące wartości parametrów:

   * **EVENTDATA** — dane zdarzenia. Pozostaw to pole puste. Dane są przesyłane z elementu Runbook obserwatora.
   * **Parametry uruchomieniowe** — ustawienie do uruchamiania elementu Runbook. Pozostaw jako platformę Azure, ponieważ ten element Runbook działa w Azure Automation.

1. Kliknij przycisk **OK**, a następnie **Wybierz opcję** powrotu do strony obserwatora.
1. Kliknij przycisk **OK** , aby utworzyć zadanie obserwatora.

![Skonfiguruj akcję obserwatora z poziomu interfejsu użytkownika](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Wyzwalanie obserwatora

Należy uruchomić test zgodnie z poniższym opisem, aby upewnić się, że zadanie obserwatora działa zgodnie z oczekiwaniami. 

1. Zdalna w hybrydowym procesie roboczym elementu Runbook. 
2. Otwórz program **PowerShell** i Utwórz plik testowy w folderze.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

W poniższym przykładzie pokazano oczekiwane dane wyjściowe.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Sprawdzanie danych wyjściowych

1. Przejdź do konta usługi Automation i wybierz **zadania obserwatora** w obszarze **Automatyzacja procesu**.
1. Wybierz **WatchMyFolder**zadania obserwatora.
1. Kliknij pozycję **Wyświetl strumienie obserwatora** w obszarze **strumienie** , aby zobaczyć, że obserwator odnalazł nowy plik i uruchomił element Runbook akcji.
1. Aby wyświetlić zadania akcji elementu Runbook, kliknij przycisk **Wyświetl zadania akcji obserwatora**. Każde zadanie można wybrać, aby wyświetlić szczegółowe informacje o zadaniu.

   ![Zadania akcji obserwatora z interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe po znalezieniu nowego pliku można zobaczyć w następującym przykładzie:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importuj element Runbook obserwatora
> * Tworzenie zmiennej automatyzacji
> * Tworzenie elementu Runbook akcji
> * Tworzenie zadania obserwatora
> * Wyzwalanie obserwatora
> * Sprawdzanie danych wyjściowych

Skorzystaj z tego linku, aby dowiedzieć się więcej na temat tworzenia własnego elementu Runbook.

> [!div class="nextstepaction"]
> [Tworzenie elementu runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md)
