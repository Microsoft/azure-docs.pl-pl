---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 40e9e2b0e7437566a132f2402d3bed01b284d11a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081082"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Utwórz ręcznie wyzwolone zadanie WebJob

1. Ma [Azure Portal](https://portal.azure.com).
1. Przejdź do **App Service** <abbr title="Zasób aplikacji może być aplikacją sieci Web, aplikacją interfejsu API lub aplikacją mobilną.">Zasób aplikacji</abbr>.
1. Wybierz pozycję **Zadania WebJob**.

    ![Wybieranie zadań WebJob](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

   ![Strona Zadania WebJob](../media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

    ![Zrzut ekranu pokazujący ustawienia, które należy ustawić w celu utworzenia ręcznie wyzwalanego Zadania WebJob.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Ustawienie      | Wartość przykładowa   | 
    | ------------ | ----------------- | 
   | <abbr title="Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż `-` i `_` .">Nazwa</abbr> | myTriggeredWebJob | 
    | <abbr title="Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu.">Przekazywanie plików</abbr> | ConsoleApp.zip |
    | <abbr title="Typy obejmują ciągły, wyzwolony.">Typ</abbr> | Wyzwalane | 
    | <abbr title="Typy obejmują zaplanowane lub ręczne">Wyzwalacze</a> | Ręcznie | 

4. Kliknij przycisk **OK**. 

   Nowe zadanie WebJob pojawia się na stronie **WebJobs** .

   ![Lista zadań WebJob](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Aby uruchomić ręczne zadanie WebJob**, kliknij prawym przyciskiem myszy jego nazwę na liście i kliknij polecenie **Uruchom**.
   
    ![Uruchom zadanie WebJob](../media/web-sites-create-web-jobs/runondemand.png)

