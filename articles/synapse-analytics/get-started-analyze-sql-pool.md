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
ms.openlocfilehash: 363f2934bbeec266c16711572620e03e69785f94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007200"
---
# <a name="analyze-data-with-sql-pools"></a>Analizowanie danych przy użyciu pul SQL

Usługa Azure Synapse Analytics umożliwia analizowanie danych za pomocą puli SQL. W tym samouczku poznasz przykładowe dane z NYC taksówkami, aby poznać możliwości analityczne puli SQL.

## <a name="link-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Połącz przykładowe dane z NYC taksówkami z bazą danych SQLDB1

1. W programie Synapse Studio przejdź do centrum **danych** po lewej stronie.
1. Kliknij przycisk **+** , a następnie wybierz pozycję **Przeglądaj przykłady**. Spowoduje to otwarcie **przykładowego centrum** i otwarcie karty **zestawy danych** .
1. Wybierz kolejno **NYC taksówke & Limousine Commission-żółtej z podróży**. Ten zestaw danych zawiera ponad 1 500 000 000 wierszy.
1. Kliknij pozycję **Dodaj zestaw danych**
1. W centrum **danych** w obszarze **połączone** zostanie wyświetlony nowy zestaw danych w tej lokalizacji **BLOB Storage platformy Azure > przykładowych zestawach > nyc_tlc_yellow**   
1. Na karcie oznaczone **przykładowe dane zapytania**wybierz pulę SQL o nazwie **SQLDB1**.


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

