---
title: Uruchamianie zadań w tle za pomocą aplikacji WebJobs
description: Dowiedz się, jak używać funkcji WebJobs do uruchamiania zadań w tle w usłudze Azure App Service. Wybieraj spośród różnych formatów skryptów i uruchamiaj je za pomocą wyrażeń CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 028551f04b2e44e9456e2f7343159ad9b52fd25f
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085148"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Uruchamianie zadań w tle za pomocą aplikacji WebJobs w usłudze Azure App Service

W tym artykule pokazano, jak wdrożyć webjobs za pomocą [witryny Azure Portal](https://portal.azure.com) do przekazywania pliku wykonywalnego lub skryptu. Aby uzyskać informacje dotyczące tworzenia i wdrażania funkcji WebJobs przy użyciu programu Visual Studio, zobacz [Wdrażanie funkcji WebJobs przy użyciu programu Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Omówienie
WebJobs to funkcja [usługi Azure App Service,](index.yml) która umożliwia uruchamianie programu lub skryptu w tym samym wystąpieniu co aplikacja sieci web, aplikacja interfejsu API lub aplikacja mobilna. Korzystanie z funkcji WebJobs nie wiąże się z żadnymi dodatkowymi kosztami.

> [!IMPORTANT]
> WebJobs nie jest jeszcze obsługiwany dla usługi App Service w systemie Linux.

Zestaw SDK usługi Azure WebJobs może być używany z webjobs w celu uproszczenia wielu zadań programowania. Aby uzyskać więcej informacji, zobacz [Co to jest SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Usługa Azure Functions umożliwia uruchomienie programów i skryptów. Aby uzyskać porównanie między webjobs i funkcje, zobacz [Wybierz przepływ, aplikacje logiki, funkcje i WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy dżlochu sieci Web

W poniższej tabeli opisano różnice między *ciągłymi* i *wyzwalanych* webjobs.


|Ciągłe  |Wywołany  |
|---------|---------|
| Rozpoczyna się natychmiast po utworzeniu robota WebJob. Aby utrzymać zadanie przed zakończeniem, program lub skrypt zazwyczaj wykonuje swoją pracę wewnątrz nieskończonej pętli. Jeśli zadanie się zakończy, można go ponownie uruchomić. | Uruchamia się tylko wtedy, gdy jest wyzwalany ręcznie lub zgodnie z harmonogramem. |
| Działa na wszystkich wystąpieniach, na których działa aplikacja internetowa. Opcjonalnie można ograniczyć WebJob do jednego wystąpienia. |Uruchamia się w jednym wystąpieniu, które platforma Azure wybiera do równoważenia obciążenia.|
| Obsługuje zdalne debugowanie. | Nie obsługuje zdalnego debugowania.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Obsługiwane typy plików dla skryptów lub programów

Obsługiwane są następujące typy plików:

* .cmd, .bat, .exe (przy użyciu cmd systemu Windows)
* .ps1 (przy użyciu programu PowerShell)
* .sh (przy użyciu Bash)
* .php (przy użyciu PHP)
* .py (przy użyciu Pythona)
* .js (przy użyciu node.js)
* .jar (przy użyciu języka Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>Tworzenie ciągłego webjob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do strony **Usługi aplikacji** usługi app service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybieranie webjobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **WebJobs** wybierz pozycję **Dodaj**.

    ![Strona WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj ustawień **Dodaj webjob,** jak określono w tabeli.

   ![Dodaj stronę WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myContinuousWebJob | Nazwa unikatowa w aplikacji usługi App Service. Musi zaczynać się od litery lub liczby i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | Aplikacja ConsoleApp.zip | Plik *zip* zawierający plik wykonywalny lub plik skryptu, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych lub skryptów są wymienione w sekcji [Obsługiwane typy plików.](#acceptablefiles) |
   | **Typ** | Ciągłe | [Typy WebJob](#webjob-types) są opisane wcześniej w tym artykule. |
   | **Skalowanie** | Wystąpienie wielokrotne | Dostępne tylko dla ciągłych webjobs. Określa, czy program lub skrypt działa na wszystkich wystąpieniach, czy tylko w jednym wystąpieniu. Opcja uruchamiania w wielu wystąpieniach nie ma zastosowania do warstw cen bezpłatnych lub [udostępnionych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kliknij przycisk **OK**.

   Nowy webjob pojawi się na stronie **WebJobs.**

   ![Lista webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Aby zatrzymać lub ponownie uruchomić ciągłą pracę w sieci Web, kliknij prawym przyciskiem myszy robotę WebJob na liście, a następnie kliknij polecenie **Zatrzymaj** lub **Rozpocznij**.

    ![Zatrzymywać ciągłą chłodzkę](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>Tworzenie ręcznie wyzwalanego zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do strony **Usługi aplikacji** usługi app service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybieranie webjobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **WebJobs** wybierz pozycję **Dodaj**.

    ![Strona WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj ustawień **Dodaj webjob,** jak określono w tabeli.

   ![Dodaj stronę WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myTriggeredWebJob | Nazwa unikatowa w aplikacji usługi App Service. Musi zaczynać się od litery lub liczby i nie może zawierać znaków specjalnych innych niż "-" i "_".|
   | **Przekazywanie pliku** | Aplikacja ConsoleApp.zip | Plik *zip* zawierający plik wykonywalny lub plik skryptu, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych lub skryptów są wymienione w sekcji [Obsługiwane typy plików.](#acceptablefiles) |
   | **Typ** | Wywołany | [Typy WebJob](#webjob-types) są opisane wcześniej w tym artykule. |
   | **Wyzwalacze** | Ręcznie | |

4. Kliknij przycisk **OK**.

   Nowy webjob pojawi się na stronie **WebJobs.**

   ![Lista webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Aby uruchomić webjob, kliknij prawym przyciskiem myszy jego nazwę na liście i kliknij polecenie **Uruchom**.
   
    ![Uruchamianie pracy w sieci Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>Tworzenie zaplanowanego webjob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do strony **Usługi aplikacji** usługi app service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybieranie webjobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **WebJobs** wybierz pozycję **Dodaj**.

   ![Strona WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj ustawień **Dodaj webjob,** jak określono w tabeli.

   ![Dodaj stronę WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myScheduledWebJob | Nazwa unikatowa w aplikacji usługi App Service. Musi zaczynać się od litery lub liczby i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | Aplikacja ConsoleApp.zip | Plik *zip* zawierający plik wykonywalny lub plik skryptu, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych lub skryptów są wymienione w sekcji [Obsługiwane typy plików.](#acceptablefiles) |
   | **Typ** | Wywołany | [Typy WebJob](#webjob-types) są opisane wcześniej w tym artykule. |
   | **Wyzwalacze** | Zaplanowana | Aby planowanie działało niezawodnie, włącz funkcję Zawsze włączone. Always On jest dostępny tylko w warstwach cenowych Podstawowe, Standardowe i Premium.|
   | **Wyrażenie CRON** | 0 0/20 * * * * | [Wyrażenia CRON](#ncrontab-expressions) są opisane w poniższej sekcji. |

4. Kliknij przycisk **OK**.

   Nowy webjob pojawi się na stronie **WebJobs.**

   ![Lista webjobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Wyrażenia NCRONTAB

Wyrażenie [NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) można wprowadzić w portalu `settings.job` lub dołączyć plik w katalogu głównym pliku *.zip* webjob, jak w poniższym przykładzie:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Aby dowiedzieć się więcej, zobacz [Planowanie wyzwalanego robota WebJob](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>Wyświetlanie historii zadań

1. Wybierz robota WebJob, dla którego chcesz wyświetlić historię, a następnie wybierz przycisk **Dzienniki.**
   
   ![Przycisk Dzienniki](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na stronie **Szczegóły usługi WebJob** wybierz godzinę, aby wyświetlić szczegóły jednego uruchomienia.
   
   ![Szczegóły webjob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na stronie **Szczegóły uruchamiania w programie WebJob** wybierz pozycję **Przełącz dane wyjściowe,** aby wyświetlić tekst zawartości dziennika.
   
    ![Szczegóły uruchomienia zadania sieci Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Aby wyświetlić tekst wyjściowy w osobnym oknie przeglądarki, wybierz polecenie **Pobierz**. Aby pobrać sam tekst, kliknij prawym przyciskiem myszy opcję **Pobierz** i użyj opcji przeglądarki, aby zapisać zawartość pliku.
   
5. Wybierz łącze **WebJobs** w górnej części strony, aby przejść do listy aplikacji WebJobs.

    ![WebJob bułka tarta](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista webjobs na pulpicie nawigacyjnym historii](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Kolejne kroki

Zestaw SDK usługi Azure WebJobs może być używany z webjobs w celu uproszczenia wielu zadań programowania. Aby uzyskać więcej informacji, zobacz [Co to jest SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
