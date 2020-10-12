---
title: 'Samouczek: Rozpoczynanie pracy z usługą Azure Synapse Analytics — Monitorowanie obszaru roboczego Synapse'
description: W tym samouczku dowiesz się, jak monitorować działania w obszarze roboczym Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708176"
---
# <a name="monitor-your-synapse-workspace"></a>Monitorowanie obszaru roboczego Synapse

W tym samouczku dowiesz się, jak monitorować działania w obszarze roboczym Synapse. Możesz monitorować bieżące i historyczne działania dla SQL, Apache Spark. i potoki. 

## <a name="introduction-to-the-monitor-hub"></a>Wprowadzenie do centrum monitorów

Otwórz Synapse Studio i przejdź do centrum **monitora** . W tym miejscu można zobaczyć historię wszystkich działań wykonywanych w obszarze roboczym, które są teraz aktywne. 

* W obszarze **aranżacja**można monitorować potoki, wyzwalacze i środowiska Integration Runtime
* W obszarze **działania**można monitorować działania platformy Spark i SQL. 

## <a name="orchestration"></a>Aranżacja

1. Przejdź do **> potoku aranżacji**. W tym widoku można zobaczyć za każdym razem, gdy potok został uruchomiony w obszarze roboczym. 
1. Znajdź potok uruchomiony w poprzednim kroku i kliknij jego **nazwę potoku**.
1. Teraz można zobaczyć, jak działa poszczególne działania w ramach tego potoku.
1. Kliknij przycisk **pasek nawigacyjny** w górnej części programu Synapse Studio, kliknij pozycję **wszystkie uruchomienia potoków** , aby powrócić do poprzedniego widoku.

## <a name="apache-spark-activities"></a>Działania Apache Spark

1. Przejdź do **> działań aranżacji > aplikacji Apache Spark**. Teraz można zobaczyć wszystkie aplikacje Spark uruchomione lub uruchomione w obszarze roboczym.
1. Znajdź aplikację, która nie jest już uruchomiona, a następnie kliknij jej **nazwę aplikacji**. Teraz można zobaczyć szczegóły aplikacji platformy Spark.
1. Jeśli znasz Apache Spark, możesz znaleźć standardowy interfejs użytkownika serwera historii Apache Spark, klikając pozycję **serwer historii Spark**.

## <a name="sql-activities"></a>Działania SQL

1. Przejdź do **> działań związanych z aranżacją > żądania SQL**.
1. W tym widoku można zobaczyć żądania SQL.
1. Wybierz **pulę** do monitorowania. Teraz można zobaczyć wszystkie żądania SQL uruchomione lub uruchomione w obszarze roboczym w tej puli.
1. Znajdź określone żądanie SQL i umieść wskaźnik myszy na tym elemencie. Po umieszczeniu wskaźnika myszy zostanie wyświetlona ikona skryptu SQL.
1. Kliknij ikonę skryptu SQL, aby wyświetlić pełny tekst żądania SQL.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Eksplorowanie centrum wiedzy](get-started-knowledge-center.md)
