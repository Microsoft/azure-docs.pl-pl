---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics — Monitorowanie obszaru roboczego Synapse'
description: W tym samouczku dowiesz się, jak monitorować działania w obszarze roboczym Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 7e8525dbebb42e1f387ee8f0c192efd5e64c9453
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426044"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorowanie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak monitorować działania w obszarze roboczym Synapse. Można monitorować bieżące i historyczne działania dla SQL, Apache Spark i potoków. 

## <a name="introduction-to-the-monitor-hub"></a>Wprowadzenie do centrum monitorów

Otwórz Synapse Studio i przejdź do centrum **monitora** . W tym miejscu można zobaczyć historię wszystkich działań wykonywanych w obszarze roboczym, które są teraz aktywne. 

* W obszarze **integracja** można monitorować potoki, wyzwalacze i środowiska Integration Runtime.
* W obszarze **działania** można monitorować działania platformy Spark i SQL. 

## <a name="integration"></a>Integracja

1. Przejdź do programu **Integration > potoku uruchomienia**. W tym widoku można zobaczyć za każdym razem, gdy potok został uruchomiony w obszarze roboczym. 
1. Znajdź potok uruchomiony w poprzednim kroku i kliknij jego **nazwę potoku** , aby wyświetlić szczegóły.
1. Kliknij przycisk **pasek nawigacyjny** w górnej części programu Synapse Studio, kliknij pozycję **wszystkie uruchomienia potoków** , aby powrócić do poprzedniego widoku.

## <a name="apache-spark-activities"></a>Działania Apache Spark

1. Przejdź do **działań > Apache Spark aplikacji**. Teraz można zobaczyć wszystkie aplikacje Spark uruchomione lub uruchomione w obszarze roboczym.
1. Znajdź aplikację, która nie jest już uruchomiona, a następnie kliknij jej **nazwę aplikacji**. Teraz można zobaczyć szczegóły aplikacji platformy Spark.
1. Jeśli znasz Apache Spark, możesz znaleźć standardowy interfejs użytkownika serwera historii Apache Spark, klikając pozycję **serwer historii Spark**.

## <a name="sql-activities"></a>Działania SQL

1. Przejdź do **działań > żądania SQL**.
1. W tym widoku można zobaczyć żądania SQL.
1. Wybierz **pulę** do monitorowania z poziomu filtru **puli** . Teraz można zobaczyć wszystkie żądania SQL uruchomione lub uruchomione w obszarze roboczym w tej puli.
1. Znajdź określone żądanie SQL i kliknij link **więcej** , aby wyświetlić pełny tekst żądania SQL.

    > [!NOTE] 
    > Żądania SQL przesłane za pośrednictwem programu Synapse Studio w ramach dedykowanej puli SQL z włączonym obszarem roboczym (dawniej SQL DW) można wyświetlić w centrum monitora. W przypadku wszystkich innych działań monitorowania można przejść do Azure Portal dedykowanej puli SQL (dawniej programu SQL DW).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie centrum wiedzy](get-started-knowledge-center.md)
