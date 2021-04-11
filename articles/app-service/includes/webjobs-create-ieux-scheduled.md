---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5105df5793d37b166b017585a62c962933a0b019
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081136"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Tworzenie zaplanowanego zadania WebJob


1. Ma [Azure Portal](https://portal.azure.com).
1. Przejdź do **App Service** <abbr title="Zasób aplikacji może być aplikacją sieci Web, aplikacją interfejsu API lub aplikacją mobilną.">Zasób aplikacji</abbr>.
1. Wybierz pozycję **Zadania WebJob**.

   ![Wybieranie zadań WebJob](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

    ![Strona Zadania WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

    ![Dodawanie strony zadania WebJob](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Ustawienie      | Wartość przykładowa   |
    | ------------ | ----------------- | 
    | <abbr title="Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż `-` i `_` .">Nazwa</a> | myScheduledWebJob | 
    | <abbr title="Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu.">Przekazywanie plików</abbr> | ConsoleApp.zip |
    | <abbr title="Typy obejmują ciągły, wyzwolony.">Typ</abbr> | Wyzwalane |
    | <abbr title="Aby planowanie działało niezawodne, Włącz funkcję zawsze włączone. Opcję zawsze włączone są dostępne tylko w warstwach cenowych podstawowa, standardowa i Premium.">Wyzwalacze</a> | Zaplanowana |
    | Wyrażenie CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Dowiedz się więcej na temat wyrażeń firmy CRONUS</summary>
     <a name="#ncrontab-expressions"></a>
    
     Możesz wprowadzić [wyrażenie NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) w portalu lub umieścić `settings.job` plik w katalogu głównym pliku WebJob *. zip* , jak w poniższym przykładzie:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Aby dowiedzieć się więcej, zobacz [Planowanie wyzwalanego Zadania WebJob](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Kliknij przycisk **OK**.

    Nowe zadanie WebJob pojawia się na stronie **WebJobs** .
    
    ![Lista zadań WebJob](../media/web-sites-create-web-jobs/listallwebjobs.png)
