---
title: Uruchamianie zadań w tle za pomocą zadań WebJob
description: Dowiedz się, jak uruchamiać zadania w tle za pomocą zadań WebJob w Azure App Service. Wybieraj spośród różnych formatów skryptów i uruchamiaj je za pomocą wyrażeń firmy cronus.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723881"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service

W tym artykule przedstawiono sposób wdrażania zadań WebJob przy użyciu [Azure Portal](https://portal.azure.com) do przekazania pliku wykonywalnego lub skryptu. Aby uzyskać informacje na temat sposobu tworzenia i wdrażania zadań WebJob przy użyciu programu Visual Studio, zobacz [wdrażanie zadań WebJob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Omówienie
WebJobs to funkcja [Azure App Service](index.yml) , która umożliwia uruchamianie programu lub skryptu w tym samym wystąpieniu, w którym znajduje się aplikacja sieci Web, aplikacja interfejsu API lub aplikacja mobilna. Nie ma dodatkowych opłat za korzystanie z zadań WebJob.

> [!IMPORTANT]
> Usługi WebJobs nie są jeszcze obsługiwane w przypadku App Service w systemie Linux.

Zestawu SDK Azure WebJobs można używać z zadaniami WebJob w celu uproszczenia wielu zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions zapewnia inny sposób uruchamiania programów i skryptów. Aby uzyskać porównanie między zadaniami WebJob i funkcjami, zobacz [Wybieranie między przepływem, Logic Apps, funkcjami i zadaniami WebJob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy zadań WebJob

W poniższej tabeli opisano różnice między *ciągłymi* i *wyzwalanymi* zadaniami WebJob.


|Ciągłe  |Wyzwalane  |
|---------|---------|
| Uruchamiany natychmiast po utworzeniu zadania WebJob. Aby zachować zadanie od końca, program lub skrypt zwykle wykonuje swoją pracę wewnątrz pętli nieskończonej. Jeśli zadanie zakończy się, możesz uruchomić je ponownie. | Uruchamiany tylko w przypadku wyzwolenia ręcznie lub zgodnie z harmonogramem. |
| Działa na wszystkich wystąpieniach, na których działa aplikacja sieci Web. Opcjonalnie możesz ograniczyć zadanie WebJob do jednego wystąpienia. |Działa w ramach jednego wystąpienia, które wybierze platformę Azure do równoważenia obciążenia.|
| Obsługuje debugowanie zdalne. | Nie obsługuje debugowania zdalnego.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Obsługiwane typy plików dla skryptów lub programów

Obsługiwane są następujące typy plików:

* . cmd,. bat,. exe (przy użyciu polecenia Windows cmd)
* . ps1 (przy użyciu programu PowerShell)
* . sh (przy użyciu bash)
* . php (przy użyciu języka PHP)
* . PR (przy użyciu języka Python)
* . js (przy użyciu Node.js)
* . jar (przy użyciu języka Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Tworzenie ciągłego Zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Jeśli masz kontrolę źródła skonfigurowaną z aplikacją, Zadania WebJob powinny zostać wdrożone w ramach integracji kontroli źródła. Po skonfigurowaniu kontroli źródła za pomocą aplikacji Zadania WebJob nie można dodać do witryny Azure Portal.

1. W [Azure Portal](https://portal.azure.com)przejdź do strony **App Service** aplikacji sieci Web App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz pozycję **Zadania WebJob**.

   ![Wybieranie zadań WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

    ![Strona Zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

   ![Zrzut ekranu pokazujący Dodawanie ustawień zadania WebJob, które należy skonfigurować.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myContinuousWebJob | Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie plików** | ConsoleApp.zip | Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych i skryptów są wymienione w sekcji [obsługiwane typy plików](#acceptablefiles) . |
   | **Typ** | Ciągłe | [Typy zadań WebJob](#webjob-types) zostały opisane wcześniej w tym artykule. |
   | **Skalowanie** | Wiele wystąpień | Dostępne tylko w przypadku ciągłych zadań WebJob. Określa, czy program lub skrypt jest uruchamiany we wszystkich wystąpieniach, czy tylko w jednym wystąpieniu. Opcja uruchamiania na wielu wystąpieniach nie ma zastosowania do [warstw cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)bezpłatna lub współdzielona. | 

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob pojawia się na stronie **WebJobs** .

   ![Lista zadań WebJob](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Aby zatrzymać lub uruchomić ponownie ciągłe zadanie WebJob, kliknij prawym przyciskiem myszy zadanie WebJob na liście, a następnie kliknij przycisk **Zatrzymaj** lub **Uruchom**.

    ![Zatrzymywanie ciągłego Zadania WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Utwórz ręcznie wyzwolone zadanie WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [Azure Portal](https://portal.azure.com)przejdź do strony **App Service** aplikacji sieci Web App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz pozycję **Zadania WebJob**.

   ![Wybieranie zadań WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

    ![Strona Zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

   ![Zrzut ekranu pokazujący ustawienia, które należy ustawić w celu utworzenia ręcznie wyzwalanego Zadania WebJob.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myTriggeredWebJob | Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_".|
   | **Przekazywanie plików** | ConsoleApp.zip | Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych i skryptów są wymienione w sekcji [obsługiwane typy plików](#acceptablefiles) . |
   | **Typ** | Wyzwalane | [Typy zadań WebJob](#webjob-types) zostały opisane wcześniej w tym artykule. |
   | **Wyzwalacze** | Ręcznie | |

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob pojawia się na stronie **WebJobs** .

   ![Lista zadań WebJob](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Aby uruchomić zadanie WebJob, kliknij prawym przyciskiem myszy jego nazwę na liście i kliknij polecenie **Uruchom**.
   
    ![Uruchom zadanie WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Tworzenie zaplanowanego zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [Azure Portal](https://portal.azure.com)przejdź do strony **App Service** aplikacji sieci Web App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz pozycję **Zadania WebJob**.

   ![Wybieranie zadań WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

   ![Strona Zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

   ![Dodawanie strony zadania WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myScheduledWebJob | Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie plików** | ConsoleApp.zip | Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików wykonywalnych i skryptów są wymienione w sekcji [obsługiwane typy plików](#acceptablefiles) . |
   | **Typ** | Wyzwalane | [Typy zadań WebJob](#webjob-types) zostały opisane wcześniej w tym artykule. |
   | **Wyzwalacze** | Zaplanowana | Aby planowanie działało niezawodne, Włącz funkcję zawsze włączone. Opcję zawsze włączone są dostępne tylko w warstwach cenowych podstawowa, standardowa i Premium.|
   | **Wyrażenie CRON** | 0 0/20 * * * * | [Wyrażenia CRONUS](#ncrontab-expressions) są opisane w następnej sekcji. |

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob pojawia się na stronie **WebJobs** .

   ![Lista zadań WebJob](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Wyrażenia NCRONTAB

Możesz wprowadzić [wyrażenie NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) w portalu lub umieścić `settings.job` plik w katalogu głównym pliku WebJob *. zip* , jak w poniższym przykładzie:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Aby dowiedzieć się więcej, zobacz [Planowanie wyzwalanego Zadania WebJob](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Wyświetl historię zadania

1. Wybierz zadanie WebJob, dla którego ma zostać wyświetlona historia, a następnie wybierz przycisk **dzienniki** .
   
   ![Przycisk dzienniki](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na stronie **szczegóły zadania WebJob** wybierz godzinę, aby wyświetlić szczegółowe informacje o jednym przebiegu.
   
   ![Szczegóły zadania WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na stronie **szczegóły przebiegu Zadania WebJob** wybierz pozycję **Przełącz dane wyjściowe** , aby zobaczyć tekst zawartości dziennika.
   
    ![Szczegóły przebiegu zadania sieci Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Aby wyświetlić tekst wyjściowy w osobnym oknie przeglądarki, wybierz pozycję **Pobierz**. Aby pobrać sam tekst, kliknij prawym przyciskiem myszy pozycję **Pobierz** i użyj opcji przeglądarki, aby zapisać zawartość pliku.
   
5. Aby przejść do listy zadań WebJob, wybierz link do obszaru nawigacyjnego **Zadania WebJob** w górnej części strony.

    ![Łącza do stron WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista zadań WebJob na pulpicie nawigacyjnym historia](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Następne kroki

Zestawu SDK Azure WebJobs można używać z zadaniami WebJob w celu uproszczenia wielu zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK usługi WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
