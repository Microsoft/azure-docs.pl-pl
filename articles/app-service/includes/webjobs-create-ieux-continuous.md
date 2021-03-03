---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746677"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Tworzenie ciągłego Zadania WebJob

1. Ma [Azure Portal](https://portal.azure.com).
1. Przejdź do **App Service** <abbr title="Zasób aplikacji może być aplikacją sieci Web, aplikacją interfejsu API lub aplikacją mobilną.">Zasób aplikacji</abbr>.
1. Wybierz pozycję **Zadania WebJob**.

   ![Wybieranie zadań WebJob](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na stronie **Zadania WebJob** wybierz pozycję **Dodaj**.

    ![Strona Zadania WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Użyj **Dodaj ustawienia zadania WebJob** określonych w tabeli.

   ![Zrzut ekranu pokazujący Dodawanie ustawień zadania WebJob, które należy skonfigurować.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ustawienie      | Wartość przykładowa   | 
   | ------------ | ----------------- | 
   | <abbr title="Nazwa, która jest unikatowa w ramach aplikacji App Service. Musi zaczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż `-` i `_` .">Nazwa</abbr> | myContinuousWebJob | 
   | <abbr title=" Plik *. zip* , który zawiera plik wykonywalny lub skrypt, a także wszystkie pliki pomocnicze potrzebne do uruchomienia programu lub skryptu.">Przekazywanie plików</abbr> | ConsoleApp.zip |
   | <abbr title="Typy obejmują ciągły, wyzwolony.">Typ</abbr> | Ciągłe | 
   | <abbr title="Dostępne tylko w przypadku ciągłych zadań WebJob. Określa, czy program lub skrypt jest uruchamiany we wszystkich wystąpieniach, czy tylko w jednym wystąpieniu. Opcja uruchamiania na wielu wystąpieniach nie ma zastosowania do warstw cenowych bezpłatna lub współdzielona.">Skalowanie</abbr> | Wiele wystąpień | 

1. Kliknij przycisk **OK**.

    Nowe zadanie WebJob pojawia się na stronie **WebJobs** .

    ![Lista zadań WebJob](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Aby zatrzymać lub uruchomić ponownie** ciągłe zadanie WebJob, kliknij prawym przyciskiem myszy zadanie WebJob na liście, a następnie kliknij przycisk **Zatrzymaj** lub **Uruchom**.

   ![Zatrzymywanie ciągłego Zadania WebJob](../media/web-sites-create-web-jobs/continuousstop.png)
