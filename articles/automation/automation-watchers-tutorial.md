---
title: Śledzenie zaktualizowanych plików za pomocą zadania obserwatora Azure Automation
description: W tym artykule opisano sposób tworzenia zadania obserwatora na koncie Azure Automation, aby obejrzeć nowe pliki utworzone w folderze.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682756"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Śledzenie zaktualizowanych plików przy użyciu zadania obserwatora

Azure Automation używa zadania obserwatora, aby wyszukać zdarzenia i wyzwalać akcje przy użyciu elementów Runbook programu PowerShell. Zadanie obserwatora zawiera dwie części, obserwatora i akcję. Element Runbook obserwatora działa w interwale zdefiniowanym w zadaniu obserwatora i wyprowadza dane do elementu Runbook akcji.

> [!NOTE]
> Zadania obserwatora nie są obsługiwane w przypadku platformy Azure w Chinach Vianet 21.

> [!IMPORTANT]
> Począwszy od 2020 maja, zalecanym i obsługiwanym sposobem monitorowania zdarzeń, planowania cyklicznych zadań i wyzwalania akcji jest użycie Azure Logic Apps. Zobacz temat [Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

W tym artykule opisano tworzenie zadania obserwatora do monitorowania po dodaniu nowego pliku do katalogu. Omawiane kwestie:

* Importuj element Runbook obserwatora
* Tworzenie zmiennej automatyzacji
* Tworzenie elementu Runbook akcji
* Tworzenie zadania obserwatora
* Wyzwalanie obserwatora
* Sprawdzanie danych wyjściowych

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, wymagane są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](./index.yml) do przechowywania obserwatora i elementów Runbook akcji oraz zadania obserwatora.
* [Hybrydowy proces roboczy elementu Runbook](automation-hybrid-runbook-worker.md) , w którym jest uruchamiane zadanie obserwatora.
* Elementy Runbook programu PowerShell. Elementy Runbook przepływu pracy programu PowerShell nie są obsługiwane przez zadania obserwatora.

## <a name="import-a-watcher-runbook"></a>Importuj element Runbook obserwatora

W tym artykule jest wyszukiwane nowe pliki w katalogu przy użyciu elementu Runbook obserwatora o nazwie **Watch-NewFile** . Element Runbook obserwatora Pobiera ostatni znany czas zapisu do plików w folderze i szuka plików nowszych niż ten znak wodny.

Element Runbook można pobrać z [Azure Automation organizacji usługi GitHub](https://github.com/azureautomation).

1. Przejdź do strony Azure Automation organizacji usługi GitHub dla [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Aby pobrać element Runbook z usługi GitHub, wybierz pozycję **Code** z prawej strony, a następnie wybierz pozycję **Pobierz plik zip** , aby pobrać cały kod w pliku zip.
3. Wyodrębnij zawartość i [zaimportuj element Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Możesz również zaimportować ten element Runbook do konta usługi Automation z portalu, wykonując poniższe kroki.

1. Otwórz konto usługi Automation, a następnie kliknij stronę elementy Runbook.
2. Kliknij pozycję **Przeglądaj Galerię** i na liście rozwijanej **Źródło** wybierz pozycję **GitHub**.
3. Wyszukaj **element Runbook obserwatora**, wybierz **element Runbook obserwatora, który wyszukuje nowe pliki w katalogu**, a następnie kliknij pozycję **Importuj**.
4. Nadaj elementowi Runbook nazwę i opis, a następnie kliknij przycisk **OK** , aby zaimportować element Runbook do konta usługi Automation.
5. Wybierz pozycję **Edytuj** , a następnie kliknij pozycję **Publikuj**. Po wyświetleniu monitu kliknij przycisk **tak** , aby opublikować element Runbook.

## <a name="create-an-automation-variable"></a>Tworzenie zmiennej automatyzacji

[Zmienna automatyzacji](./shared-resources/variables.md) służy do przechowywania sygnatur czasowych, które poprzedni element Runbook odczytuje i zapisuje z każdego pliku.

1. Wybierz **zmienne** w obszarze **zasoby udostępnione** i kliknij pozycję **+ Dodaj zmienną**.
1. Dla nazwy wprowadź wartość **Watch-NewFileTimestamp** .
1. Dla tego typu wybierz pozycję Data i godzina.
1. Kliknij przycisk **Utwórz** , aby utworzyć zmienną automatyzacji.

## <a name="create-an-action-runbook"></a>Tworzenie elementu Runbook akcji

Element Runbook akcji jest używany w zadaniu obserwatora do działania na danych przesłanych do niego z elementu Runbook obserwatora. Należy zaimportować wstępnie zdefiniowany element Runbook Action o nazwie **Process-NewFile** z [organizacji usługi GitHub Azure Automation](https://github.com/azureautomation).

Aby utworzyć element Runbook akcji:

1. Przejdź do strony Azure Automation organizacji usługi GitHub dla [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
2. Aby pobrać element Runbook z usługi GitHub, wybierz pozycję **Code** z prawej strony, a następnie wybierz pozycję **Pobierz plik zip** , aby pobrać cały kod w pliku zip.
3. Wyodrębnij zawartość i [zaimportuj element Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Możesz również zaimportować ten element Runbook do konta usługi Automation z Azure Portal:

1. Przejdź do konta usługi Automation i wybierz pozycję **elementy Runbook** w obszarze **Automatyzacja procesów**.
1. Kliknij pozycję **Przeglądaj Galerię** i na liście rozwijanej **Źródło** wybierz pozycję **GitHub**.
1. Wyszukaj **akcję obserwatora**, wybierz **akcję obserwatora, która przetwarza zdarzenia wyzwalane przez element Runbook obserwatora**, a następnie kliknij przycisk **Importuj**.
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
1. Wybierz **WatchMyFolder** zadania obserwatora.
1. Kliknij pozycję **Wyświetl strumienie obserwatora** w obszarze **strumienie** , aby zobaczyć, że obserwator odnalazł nowy plik i uruchomił element Runbook akcji.
1. Aby wyświetlić zadania akcji elementu Runbook, kliknij przycisk **Wyświetl zadania akcji obserwatora**. Każde zadanie można wybrać, aby wyświetlić szczegółowe informacje o zadaniu.

   ![Zadania akcji obserwatora z interfejsu użytkownika](media/automation-watchers-tutorial/WatcherActionJobs.png)

Oczekiwane dane wyjściowe po znalezieniu nowego pliku można zobaczyć w następującym przykładzie:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia własnego elementu Runbook, zobacz [Tworzenie elementu Runbook programu PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
