---
title: Wyświetl historię dzienników zadań WebJob
description: Wyświetl historię dzienników dla zadań zakończonych niepowodzeniem i zakończonych powodzeniem.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746690"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Wyświetl historię zadania WebJob w Azure Portal

Wyświetl historię dzienników dla zadań zakończonych niepowodzeniem i zakończonych powodzeniem.

1. Wybierz zadanie WebJob, dla którego ma zostać wyświetlona historia, a następnie wybierz przycisk **dzienniki** .

    ![Przycisk dzienniki](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. Na stronie **szczegóły zadania WebJob** wybierz godzinę, aby wyświetlić szczegółowe informacje o jednym przebiegu.

    ![Szczegóły zadania WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

1. Na stronie **szczegóły przebiegu Zadania WebJob** wybierz pozycję **Przełącz dane wyjściowe** , aby zobaczyć tekst zawartości dziennika.

    ![Szczegóły przebiegu zadania sieci Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

    Aby wyświetlić tekst wyjściowy w osobnym oknie przeglądarki, wybierz pozycję **Pobierz**. Aby pobrać sam tekst, kliknij prawym przyciskiem myszy pozycję **Pobierz** i użyj opcji przeglądarki, aby zapisać zawartość pliku.

1. Aby przejść do listy zadań WebJob, wybierz link do obszaru nawigacyjnego **Zadania WebJob** w górnej części strony.

    ![Łącza do stron WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![Lista zadań WebJob na pulpicie nawigacyjnym historia](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>Następne kroki

* Używanie [zestawu SDK zadań WebJob](https://github.com/Azure/azure-webjobs-sdk/wiki) do uproszczenia wielu zadań programistycznych

* Naucz się [opracowywać i wdrażać Zadania WebJob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md)