---
title: 'Samouczek: Rozpoczynanie analizy danych za pomocą puli SQL'
description: W tym samouczku poznasz przykładowe dane z NYC taksówkami, aby poznać możliwości analityczne puli SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 948de6968d336d16554fe0a0c2f7d0c5b7367f15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101627"
---
# <a name="analyze-data-with-sql-pools"></a>Analizowanie danych przy użyciu pul SQL

Usługa Azure Synapse Analytics umożliwia analizowanie danych za pomocą puli SQL. W tym samouczku poznasz przykładowe dane z NYC taksówkami, aby poznać możliwości analityczne puli SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Ładowanie przykładowych danych z NYC taksówki do bazy danych SQLDB1

1. W programie Synapse Studio, w górnym niebieskim menu, wybierz ikonę znaku zapytania (**?**).
1. Wybierz pozycję **wprowadzenie**— wprowadzenie do  >  **centrum**.
1. Na karcie oznaczone **przykładowe dane zapytania**wybierz pulę SQL o nazwie **SQLDB1**.
1. Wybierz pozycję **dane zapytania**. Zostanie wyświetlone powiadomienie "Ładowanie przykładowych danych". Jasnoniebieski pasek stanu w górnej części programu Synapse Studio wskazuje, że dane są ładowane do SQLDB1.
1. Gdy pasek stanu zmieni kolor na zielony, Odrzuć go.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Eksplorowanie danych NYC taksówki w puli SQL

1. W programie Synapse Studio przejdź do centrum **danych** .
1. Przejdź do **SQLDB1**  >  **tabel**SQLDB1. Zobaczysz kilka załadowanych tabel.
1. Kliknij prawym przyciskiem myszy obiekt **dbo. Tabelę podróży** i wybierz pozycję **Nowy skrypt SQL**  >  **Wybierz pierwsze 100 wierszy**.
1. Poczekaj, aż zostanie utworzony i uruchomiony nowy skrypt SQL.
1. Zwróć uwagę, że u góry skryptu SQL **połączenie z programem** jest automatycznie ustawiana na pulę SQL o nazwie **SQLDB1**.
1. Zastąp tekst skryptu SQL tym kodem i uruchom go.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    To zapytanie pokazuje, w jaki sposób łączny czas podróży i średnią odległość podróży odnoszą się do liczby osób.
1. W oknie wyników skryptu SQL Zmień **Widok** na **Wykres** , aby wyświetlić wizualizację wyników jako wykres liniowy.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie przy użyciu platformy Spark](get-started-analyze-spark.md)

